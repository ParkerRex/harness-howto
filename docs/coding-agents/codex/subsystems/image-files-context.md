# Codex Image + File-Mention Context Handling (Tech Spec)

This document describes how Codex handles image files in context and how file mentions (`@...`) work, with concrete I/O behavior and code anchors.

## Scope
- Covers TUI flows (paste, file-mention selection, clipboard), CLI `--image`, protocol serialization, model input construction, tool outputs, and history/compaction interactions.
- Focuses on image files and file mentions, including path normalization, resizing/encoding, and placeholders.

## Core Input Model
- `UserInput` supports `Text`, `Image` (data URL), `LocalImage` (path), and `Skill`; `LocalImage` is converted to `Image` during request serialization. `src/coding-agents/codex/codex-rs/protocol/src/user_input.rs:10`
- CLI accepts `--image/-i` paths and builds `UserInput::LocalImage` entries for the initial prompt. `src/coding-agents/codex/codex-rs/exec/src/cli.rs:13`
- TUI submits `UserInput::LocalImage` for each attached image path (before text). `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:2166`
- App-server protocol exposes `UserInput::Image` and `UserInput::LocalImage` in v2, mapping to core inputs. `src/coding-agents/codex/codex-rs/app-server-protocol/src/protocol/v2.rs:1547`

## Serialization: Local and Remote Images
- `UserInput::Image` serializes as:
  - `InputText("<image>")`, `InputImage`, `InputText("</image>")`. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:344`
- `UserInput::LocalImage` serializes as:
  - `InputText("<image name=[Image #n]>")`, `InputImage`, `InputText("</image>")`. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:358` `src/coding-agents/codex/codex-rs/protocol/src/models.rs:545`
- Local image label numbering is assigned sequentially based on the order of `UserInput::LocalImage` items in the turn. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:545`
- The UI placeholder label uses the same helper as serialization (`[Image #n]`). `src/coding-agents/codex/codex-rs/protocol/src/models.rs:358` `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:521`

## Image Processing Pipeline (LocalImage)
- `load_and_resize_to_fit` reads bytes, caches by SHA1 (LRU size 32), decodes with `image`, and returns an `EncodedImage` with bytes + MIME. `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:40`
- Reads are synchronous; inside Tokio runtime it uses `block_in_place` to avoid worker-thread panics. `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:102`
- Resize bounds: `MAX_WIDTH=2048`, `MAX_HEIGHT=768`, filter `Triangle`. `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:18`
- If the source is PNG/JPEG and within bounds, original bytes are reused; otherwise the image is re-encoded as PNG (or JPEG if preferred) before embedding. `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:51`
- JPEG encoding uses quality 85; PNG uses RGBA8. `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:120`
- Encoded images are converted to data URLs (`data:<mime>;base64,....`). `src/coding-agents/codex/codex-rs/utils/image/src/lib.rs:33`
- Errors from read/decode/encode become `InputText` placeholders rather than `InputImage`. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:329` `src/coding-agents/codex/codex-rs/utils/image/src/error.rs:7`

## TUI Attachment Flow
- `attach_image` inserts a placeholder element and tracks `PathBuf` in `attached_images`. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:519`
- `take_recent_submission_images` drains attachment paths per submission. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:530`
- TUI submission builds items: image paths first, then text, then skill mentions. `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:2166`

## File Mentions (`@...`) and File Search
- `/mention` inserts `@` in the composer; command description is “mention a file.” `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:2002` `src/coding-agents/codex/codex-rs/tui/src/slash_command.rs:55`
- Tokens are ASCII-whitespace delimited; `current_at_token` extracts the active `@token` under cursor. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1078`
- If an `@token` exists, the file-search popup is preferred over slash-command popup. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1955`
- File search is triggered via `StartFileSearch(query)` when the `@token` is non-empty. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1991`
- Selected paths replace the `@token`, quote whitespace paths, and append a trailing space. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1226`

## File Mention -> Image Attachment
- `is_image_path` is extension-based (`.png/.jpg/.jpeg`). `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1065`
- When a selected file path looks like an image, the composer tries `image::image_dimensions`:
  - success: removes the `@token`, attaches image placeholder, adds trailing space. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:952`
  - failure: falls back to inserting the path as text. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:985`
- Inference: this selection path only validates image metadata (dimensions) and does not read/encode the full file; full bytes are read later during serialization. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:355` `src/coding-agents/codex/codex-rs/protocol/src/models.rs:411`

## Paste Handling and Path Normalization
- `handle_paste` routes pasted text:
  - large paste → placeholder element + pending buffer.
  - image-path paste → attach image + trailing space.
  - otherwise → insert text. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:328`
- `handle_paste_image_path` uses `normalize_pasted_path` and `image::image_dimensions` to validate. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:355`
- `normalize_pasted_path` supports:
  - `file://` URLs,
  - Windows/UNC paths,
  - shell-escaped single paths,
  - WSL drive-letter conversion. `src/coding-agents/codex/codex-rs/tui/src/clipboard_paste.rs:239` `src/coding-agents/codex/codex-rs/tui/src/clipboard_paste.rs:277`

## Clipboard Image Paste
- `paste_image_as_png` uses `arboard` and prefers clipboard file paths over raw image bytes; it encodes to PNG and returns dimensions. `src/coding-agents/codex/codex-rs/tui/src/clipboard_paste.rs:49`
- `paste_image_to_temp_png` writes PNG bytes to a persisted temp file prefixed `codex-clipboard-`. `src/coding-agents/codex/codex-rs/tui/src/clipboard_paste.rs:119`
- WSL fallback runs PowerShell to dump the clipboard image to a Windows temp PNG and maps it to `/mnt/<drive>`. `src/coding-agents/codex/codex-rs/tui/src/clipboard_paste.rs:152`
- TUI binds Ctrl/Alt+V to attempt clipboard image paste; failures show an error and recommend saving to file path. `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:1737` `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:1838`

## Tool: `view_image`
- `view_image(path)` is defined as a tool that attaches a local image if the user provided a full filepath and the image is not already attached. `src/coding-agents/codex/codex-rs/core/src/tools/spec.rs:406`
- Handler:
  - resolves `path` relative to the turn `cwd`,
  - verifies it exists and is a file,
  - injects a user `Message` containing image content items (no `<image name=...>` wrapper because `label_number=None`). `src/coding-agents/codex/codex-rs/core/src/tools/handlers/view_image.rs:51`
- Emits a `ViewImageToolCallEvent` so the UI can render a “viewed image” cell. `src/coding-agents/codex/codex-rs/protocol/src/protocol.rs:1661` `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs:1091`
- Config flag: `tools.view_image` enables/disables the tool; reviews explicitly disable it. `src/coding-agents/codex/codex-rs/core/src/config/mod.rs:995` `src/coding-agents/codex/codex-rs/core/src/codex.rs:2392`

## Tool Output Images (MCP/Custom Tools)
- Tool outputs with `ContentBlock::ImageContent` become `FunctionCallOutputContentItem::InputImage` and are preserved as data URLs. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:734`
- If any image exists, `content_items` is set; otherwise it is `None`. `src/coding-agents/codex/codex-rs/protocol/src/models.rs:737`
- Truncation only omits text items; `InputImage` items are never dropped by budget. `src/coding-agents/codex/codex-rs/core/src/truncate.rs:100`

## History, Context, and Compaction
- History parsing strips image wrapper tags (`<image...>` / `</image>`) so only real text and `UserInput::Image` remain. `src/coding-agents/codex/codex-rs/core/src/event_mapping.rs:41`
- Compaction summaries ignore images (`InputImage` not included in summary text). `src/coding-agents/codex/codex-rs/core/src/compact.rs:192`
- User-turn boundary detection allows `InputImage` content to count as a user turn. `src/coding-agents/codex/codex-rs/core/src/context_manager/history.rs:326`
- Invalid image request handling:
  - replaces tool-output image content with a placeholder if possible,
  - otherwise emits a user-facing error. `src/coding-agents/codex/codex-rs/core/src/codex.rs:2629` `src/coding-agents/codex/codex-rs/core/src/context_manager/history.rs:127`

## Path Resolution and I/O Boundaries
- Tool paths are resolved by joining the provided path to the turn `cwd`. `src/coding-agents/codex/codex-rs/core/src/codex.rs:404`
- File mention UI only inserts paths or placeholders; actual file bytes are read during `LocalImage` serialization or via `view_image`. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:1198` `src/coding-agents/codex/codex-rs/protocol/src/models.rs:411`
- Image-path detection relies on `image::image_dimensions` (metadata), not full decode/encode, during selection or paste. `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/chat_composer.rs:355`

## Expected Behaviors (Summary)
- Local images are always converted into data URLs and wrapped with `<image ...>` tags before model submission.
- PNG/JPEG are preserved when within bounds; larger images are resized to `2048x768` and re-encoded.
- File mentions (`@path`) are purely text until a file is selected; if the selection looks like an image and dimensions can be read, an image attachment is created instead of inserting the path.
- Clipboard image paste writes a temporary PNG file and attaches it as a local image path.
- `view_image` injects image content into the model context without UI placeholders, and is disabled for review runs.
- Compaction and history summaries are text-only; images are not summarized.

