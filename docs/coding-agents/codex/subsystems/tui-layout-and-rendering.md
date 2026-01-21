# Codex TUI Layout and Rendering (Comprehensive)

This document consolidates all known details about how the Codex TUI constructs
messages, tool/command blocks, plan checklists, MCP rows, status indicator, and
overall layout. It is intended as a single "full picture" reference that
combines the data model, event flow, and rendering rules.

If you are building a UI that mirrors the Codex CLI output, start here and then
cross-reference the specialized docs:

- `docs/coding-agents/codex/subsystems/tui-conversation-flow.md`
- `docs/coding-agents/codex/subsystems/tui-task-ui-elements.md`
- `docs/coding-agents/codex/subsystems/server-to-tui-event-pipeline.md`
- `docs/coding-agents/codex/subsystems/data-model.md`

## Scope and Sources

Primary implementation files:

- Layout + main rendering: `src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs`
- History cell definitions: `src/coding-agents/codex/codex-rs/tui/src/history_cell.rs`
- Exec/tool calls: `src/coding-agents/codex/codex-rs/tui/src/exec_cell/model.rs`,
  `src/coding-agents/codex/codex-rs/tui/src/exec_cell/render.rs`
- Status indicator: `src/coding-agents/codex/codex-rs/tui/src/status_indicator_widget.rs`
- Streaming: `src/coding-agents/codex/codex-rs/tui/src/streaming/controller.rs`,
  `src/coding-agents/codex/codex-rs/tui/src/markdown_stream.rs`
- Markdown rendering: `src/coding-agents/codex/codex-rs/tui/src/markdown.rs`
- Layout primitives: `src/coding-agents/codex/codex-rs/tui/src/render/renderable.rs`,
  `src/coding-agents/codex/codex-rs/tui/src/render/line_utils.rs`,
  `src/coding-agents/codex/codex-rs/tui/src/wrapping.rs`
- Scrollback insertion: `src/coding-agents/codex/codex-rs/tui/src/insert_history.rs`

## 1) Renderable Primitives (Layout Engine)

All TUI UI components implement the `Renderable` trait
(`render`, `desired_height`, optional `cursor_pos`), defined in
`render/renderable.rs`. Higher-level layout uses a small set of reusable
renderables:

- `RenderableItem`: wraps owned or borrowed renderables.
- `ColumnRenderable`: stacks children vertically using each child's
  `desired_height(width)`.
- `FlexRenderable`: vertical layout with flex factors; fixed-height children
  are allocated first, remaining height is divided by flex factor.
- `RowRenderable`: fixed-width children laid out horizontally.
- `InsetRenderable`: wraps a child and shrinks its usable area by insets.

Important rules:

- `FlexRenderable::allocate` always computes child rectangles from top to
  bottom and caps height at the available area.
- `desired_height` for flex containers is computed by calling `allocate` on a
  huge-height rect (u16::MAX) and returning the final bottom.
- `cursor_pos` walks children in layout order and returns the first child that
  reports a cursor.

## 2) Main Layout Tree (ChatWidget and Bottom Pane)

### ChatWidget layout

`ChatWidget::as_renderable()` builds the top-level layout for the chat screen:

```
ChatWidget (Renderable)
├─ active_cell (HistoryCell, live/in-flight)     [flex=1]
└─ BottomPane                                     [flex=0]
```

Both the `active_cell` and `bottom_pane` are wrapped with an inset:

```
Insets::tlbr(1, 0, 0, 0)
```

This inserts a one-row gap at the top of each region (a consistent visual
breathing line above transcript content and above the bottom pane).

Implementation: `ChatWidget::as_renderable` in
`src/coding-agents/codex/codex-rs/tui/src/chatwidget.rs`.

### BottomPane layout

`BottomPane::as_renderable()` lays out a vertical column that may include:

1) Status indicator (if a task is running)
2) Unified exec footer (if background terminals are running)
3) Queued user messages (if user input is queued)
4) Optional spacer line (if any of the above are visible)
5) Composer input (always visible unless a modal view replaces it)

Layout details:

- The status indicator and footer are fixed-height (1 or more rows).
- Queued messages can be multi-line based on wrap.
- A blank spacer row is inserted when any of status/footer/queue are visible.
- The composer is rendered at the bottom as the last child of a flex column.

Implementation: `src/coding-agents/codex/codex-rs/tui/src/bottom_pane/mod.rs`.

## 3) Transcript Model and History Cells

The transcript is a stream of `HistoryCell` objects. The trait is in
`history_cell.rs` and provides:

- `display_lines(width)`: what appears in the main UI.
- `transcript_lines(width)`: what appears in the transcript overlay and export.
- `desired_height(width)` and `desired_transcript_height(width)` for layout.
- Optional `is_stream_continuation()` and `transcript_animation_tick()` for
  streaming and animated overlays.

### Active cell vs committed history

`ChatWidget` maintains:

- `active_cell`: mutable in-flight cell (for streaming or active exec/MCP).
- Committed history cells (stored in a transcript buffer in the widget).

The transcript overlay (Ctrl+T) renders committed history plus a live tail
derived from `active_cell`. This is kept consistent by:

- `active_cell_transcript_key()` for cache invalidation.
- `active_cell_transcript_lines(width)` for the live tail content.

See `ChatWidget` top-of-file docs in `chatwidget.rs`.

## 4) Event to UI Mapping (High-Level)

The Codex TUI consumes `EventMsg` values emitted by core. The dispatch table is
`ChatWidget::dispatch_event_msg`.

Common event -> UI behavior:

- `TurnStarted` -> `on_task_started`
  - Sets running state, shows status indicator with "Working".
- `TurnComplete` -> `on_task_complete`
  - Stops running state, clears buffers, and may enqueue next user input.
- `AgentMessageDelta` -> `handle_streaming_delta`
  - Streams partial agent output into `StreamController`.
- `AgentMessage` -> `on_agent_message`
  - Finalizes stream (if active) and commits the final message.
- `AgentReasoningDelta` -> `on_agent_reasoning_delta`
  - Updates status header with first bold segment from reasoning text.
- `AgentReasoning` / `AgentReasoningRawContent` -> `on_agent_reasoning_final`
  - Builds a transcript-only reasoning summary cell if needed.
- `PlanUpdate` -> `on_plan_update` -> `PlanUpdateCell`
  - Inserts plan checkbox list immediately into transcript.
- `ExecCommandBegin/Output/End` -> `on_exec_command_begin/append/end`
  - Updates or completes the active exec cell.
- `McpToolCallBegin/End` -> `handle_mcp_begin_now` / `handle_mcp_end_now`
  - Updates or completes the active MCP tool call cell.
- `McpStartupUpdate/Complete` -> updates status header or warnings.
- `WebSearchBegin` -> `new_web_search_call` history cell.
- `ViewImageToolCall` -> `new_view_image_tool_call` history cell.
- `WarningEvent` -> warning cell.
- `ErrorEvent` -> error cell and finalize turn.

## 5) Agent Messages (Streaming + Final)

### Streaming pipeline

- Deltas flow into `StreamController` (`streaming/controller.rs`).
- `StreamController` uses `MarkdownStreamCollector` to render markdown and
  **only commits complete lines** (newline-gated).
- Each commit produces an `AgentMessageCell` with:
  - `is_first_line = true` for the first chunk (prefix "• ")
  - `is_first_line = false` for follow-up chunks (prefix "  ")
- Commit animation is paced by `AppEvent::CommitTick`, one line per tick.

### Final message vs streaming

If the final `AgentMessage` arrives while streaming is active, it is ignored,
because its content has already been streamed and committed.

Implementation: `ChatWidget::on_agent_message`,
`ChatWidget::handle_streaming_delta`, `StreamController`.

## 6) Reasoning and Status Header

Reasoning text is *not* rendered as a standard agent message. Instead:

1) Reasoning deltas append to `reasoning_buffer`.
2) The first bold segment (`**...**`) is extracted and shown as the status
   header via `set_status_header`.
3) At reasoning final, the accumulated content is stored as a
   `ReasoningSummaryCell` (transcript-only or visible, depending on content).

Key behaviors:

- Reasoning headers are suppressed if unified-exec wait is active
  (`unified_exec_wait_streak`).
- If reasoning content is empty or only a header, the summary cell is created
  but set `transcript_only = true` so it is not shown on-screen.

See: `chatwidget.rs` and `history_cell.rs`.

## 7) User Messages

`UserHistoryCell` renders user prompts with a distinct style:

- Blank line before and after the prompt block.
- Prefix `> ` (actually "› " dim/bold) for each line.
- Wraps with `word_wrap_lines` using a width reduced by
  `LIVE_PREFIX_COLS + 1` to preserve a right margin.
- Style is applied to the whole block (`user_message_style()`).

Implementation: `history_cell.rs`.

## 8) Tool/Command Cells (ExecCell)

`ExecCell` is the workhorse for commands and tool invocations.
It contains one or more `ExecCall` entries.

### ExecCall data

Fields include:

- `call_id`
- `command` (Vec<String>)
- `parsed` (Vec<ParsedCommand>)
- `source` (ExecCommandSource)
- `output` (CommandOutput: aggregated_output, formatted_output, exit_code)
- `start_time`, `duration`
- `interaction_input` for unified-exec interaction

### Exploring vs command display

`ExecCell` renders in one of two modes:

**Exploring/Explored group**

- Used if **all** calls are parsed as `Read`, `ListFiles`, or `Search`, and the
  source is not `UserShell`.
- Header is "Exploring" while active, "Explored" when complete.
- Consecutive Read calls are coalesced into one line with comma-separated
  filenames.
- Each line is indented with tree prefixes:
  - initial: "  └ "
  - subsequent: "    "

**Command display**

- Used for all other calls.
- Header line:
  - Spinner if active; green bullet on success; red bullet on failure.
  - Title is "Running", "Ran", or "You ran" (UserShell).
  - Unified exec interaction uses a special inline sentence instead of "Ran".
- Command formatting:
  - `strip_bash_lc_and_escape` removes `bash -lc` wrappers.
  - Highlighted with `highlight_bash_to_lines`.
  - Continuation lines use `EXEC_DISPLAY_LAYOUT.command_continuation`:
    initial + subsequent prefixes `"  │ "`, max 2 lines.
- Output formatting:
  - Aggregated output is split into lines with ANSI handling.
  - Output block uses `EXEC_DISPLAY_LAYOUT.output_block`:
    initial `"  └ "`, subsequent `"    "`.
  - Tool output line limits:
    - Non-user tool calls: 5 lines (`TOOL_CALL_MAX_LINES`)
    - User shell commands: 50 lines (`USER_SHELL_TOOL_CALL_MAX_LINES`)
  - Output is truncated in the **middle** with `... +N lines` when long.
  - `(no output)` is shown if empty and not a unified exec interaction.

Implementation: `exec_cell/model.rs`, `exec_cell/render.rs`.

## 9) MCP Tool Call Cells

`McpToolCallCell` renders MCP tool invocations inline in the transcript.

### Header

- Bullet spinner while running, green/red when complete.
- Title is "Calling" or "Called".
- Invocation is formatted as:
  `server.tool(<json args>)` via `format_mcp_invocation`.
- If invocation line fits, it is inline; otherwise it is rendered on a new
  line with tree prefix ("  └ ").

### Body

If results exist, each content block is rendered:

- `TextContent`: truncated to 5 lines and wrapped, dim.
- `ImageContent`: placeholder "<image content>".
- `EmbeddedResource`: "embedded resource: <uri>".
- `ResourceLink`: "link: <uri>".

If the first result block is an image, a separate image history cell is added
after completion (`try_new_completed_mcp_tool_call_with_image_output`).

Implementation: `history_cell.rs`, `chatwidget.rs`.

## 10) MCP Tools Listing (/mcp)

When `/mcp` is requested:

- `ChatWidget::add_mcp_output()` sends `Op::ListMcpTools` if any server is
  configured; otherwise `empty_mcp_output()` is shown.
- `new_mcp_tools_output()` renders a detailed list of servers:
  - enabled/disabled status
  - auth status
  - transport config (stdio command/args/env/cwd OR streamable HTTP URL/headers)
  - available tools list
  - resources list (with titles and URIs)
  - resource templates list

Sensitive header values are masked as `name=*****`.

Implementation: `history_cell.rs`, `chatwidget.rs`.

## 11) Plan/Todo Checklist (update_plan tool)

`PlanUpdateCell` renders `update_plan` tool output as a checkbox list:

- Header: "• Updated Plan"
- Optional explanation (dim, italic)
- Steps:
  - Completed: "✔ " + crossed-out + dim
  - In progress: "□ " + cyan + bold
  - Pending: "□ " + dim
- All items are indented under the header with "  └ " and "    ".

Implementation: `history_cell.rs`.

## 12) Status Indicator (Bottom Pane)

`StatusIndicatorWidget` displays the running status line:

Line 1:

- Spinner (animated bullet)
- Shimmered header text (default "Working")
- Elapsed time (compact format)
- Interrupt hint: "(Xm Ys • esc to interrupt)" when enabled

Line 2+:

- Optional details text, wrapped and prefixed with "  └ ".
- Max 3 lines; truncates with ellipsis.

Elapsed timing:

- `fmt_elapsed_compact` formats seconds into:
  `0s`, `59s`, `1m 03s`, `1h 05m 12s`, etc.

Lifecycle:

- `BottomPane::set_task_running(true)` creates the widget.
- `BottomPane::set_task_running(false)` hides it.
- `ChatWidget::set_status_header` updates its header.
- Timers pause/resume when modal overlays are shown.

Implementation: `status_indicator_widget.rs`, `bottom_pane/mod.rs`.

## 13) "Worked for Xm Ys" Separator

`FinalMessageSeparator` is inserted at the transition from tool/exec output to
the next agent message:

- Format: "─ Worked for Xm Ys ─"
- The duration is sourced from `StatusIndicatorWidget::elapsed_seconds()`.
- It is inserted when a new streaming agent message starts and a tool call
  just completed (via `needs_final_message_separator`).

Implementation: `history_cell.rs`, `chatwidget.rs`.

## 14) Unified Exec Footer (Background Processes)

If background terminal processes are running, the bottom pane shows a single
footer row, for example:

```
  2 background terminals running · /ps to view
```

Implementation: `bottom_pane/unified_exec_footer.rs`.

## 15) Queued User Messages (During Running Turns)

If the user submits while a turn is running:

- Messages are queued in `ChatWidget::queued_user_messages`.
- The bottom pane renders a queued list until the turn completes.
- On completion, the next queued message is sent automatically.

Implementation: `chatwidget.rs`, `bottom_pane/mod.rs`.

## 16) Scrollback Insertion (insert_history_lines)

The TUI can insert committed lines into terminal scrollback without redrawing
the entire buffer:

- `insert_history_lines` wraps lines to the viewport width so the terminal
  scrollback matches UI wrapping.
- Uses ANSI scroll regions and `Reverse Index (RI)` to shift the viewport down.

Implementation: `insert_history.rs`.

## 17) Snapshot Tests (Canonical Output Samples)

Snapshot files show exact rendering for core UI elements. Useful for matching
output in other UIs or for validation:

- `src/coding-agents/codex/codex-rs/tui/src/snapshots/*history_cell*`
  - Plan updates, MCP rows, exec cells, web search history, etc.
- `src/coding-agents/codex/codex-rs/tui/src/snapshots/*status_indicator*`
  - Status bar output.
- `src/coding-agents/codex/codex-rs/tui/src/chatwidget/snapshots/*`
  - Chat-level flows and approvals.

Use these to confirm exact spacing, prefixes, and truncation behaviors.

## 18) Summary: Reproducing Codex TUI Layout

To mirror Codex's TUI:

1) Build a transcript as a stream of `HistoryCell`-like renderables.
2) Keep a mutable active cell for in-flight tool calls or streaming output.
3) Use newline-gated streaming with a commit tick (one line per tick).
4) Render agent content via markdown, but do not stream raw reasoning to UI;
   use it only to update the status header.
5) Treat Read/List/Search exec calls as "Exploring/Explored" groups and use
   command display blocks for everything else.
6) Show a bottom status indicator while a turn or MCP startup is running.
7) Insert "Worked for Xm Ys" separators before new agent messages when a tool
   or exec block just finished.

All behaviors above are implemented directly in the Codex TUI code referenced
throughout this doc.
