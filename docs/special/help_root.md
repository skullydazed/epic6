# EPIC help collection: comprehensive historical index

[Welcome](welcome.md) to the help collection. For a short current introduction, start with [getting started](getting_started.md), [commands](commands_overview.md), [functions](functions_overview.md), and [scripting](epic_scripting.md).

This page migrates the original master index in full. Its breadth includes older EPIC4/EPIC5 names and removed features. The **Current source status** column was checked against this EPIC6 checkout's registration tables and retained script files. A registration establishes that a name exists, not that every old behavior or optional integration works. Linked references explain current arguments, limits, and build requirements.

The **Historical description / migration note** column preserves the original index's purpose and replacement suggestions, with explicit corrections below. It is not a current API specification. In particular, an old “use X instead” note does not establish that X remains available in EPIC6; use the status column and the target's reference. Retained scripts may depend on removed facilities, and a script definition does not mean the script has been loaded. Code-style entries without links preserve references for which no migrated local page exists.

## Corrections applying throughout the index

- DCC commands, DCC transports/events/settings, external message encryption, CPU saver, Perl, Ruby, and incoming flood detection have been removed. Entries describing them are historical. Current TLS server connections are a separate facility.
- CTCP handlers other than ACTION in this index are supplied by `script/ctcp`. The retained outgoing reply-throttle branch does not establish a working limit or a supported `no_ctcp_flood` setting.
- `notify`, `ignore`, mail checking, and many compatibility commands/settings are supplied through scripts rather than their former built-in interfaces. Script availability must be checked alongside dependencies.
- `set client_information` supplies the VERSION responder's configurable text, while `set user_information` supplies USERINFO; neither is the CLIENTINFO command-description source.
- `switch` selects wildcard patterns, not regular expressions. `bless` exposes the oldest enclosing locked waiting frame under its documented scope rules; it does not automatically expose the immediate caller when no frame is locked.
- IRC network operations remain subject to server support and permissions. KICK, INVITE, MODE, WHO, WHOIS, and similar requests do not guarantee their requested result. TRACE asks for IRC routing diagnostics, not an IP traceroute. KNOCK requests network-specific channel entry assistance rather than guaranteeing an invitation.
- Hook names describe callback opportunities, not guarantees that every operation succeeds or that all state is already complete. In particular, CHANNEL_SYNC has the cache-completeness limitations in its reference, and suppression normally controls the caller's default handling rather than undoing the network event.


## The most common commands

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [away](../commands/away.md) | Tell the server you are not paying attention | C command registered; see linked reference for behavior. |
| [channel](../commands/channel.md) | Join or associate a channel with a window. | C command registered; see linked reference for behavior. |
| [clear](../commands/clear.md) | Do a [window clear](../window/clear.md); Display nothing in the window. | C command registered; see linked reference for behavior. |
| [ctcp](../commands/ctcp.md) | **C**lient **T**o **C**lient **P**rotocol: ask other clients for information. | C command registered; see linked reference for behavior. |
| [dcc](../disambiguation/dcc.md) | **D**irect **C**lient **C**onnection: talking with another user outside of irc. | Removed or unregistered built-in interface. |
| [dcc get](../disambiguation/ctcp_get.md) | Accept a copy of someone else's file. | Removed DCC interface; no current built-in command or transport. |
| [dcc resume](../disambiguation/ctcp_resume.md) | Agree to continue downloading a file you partially downloaded already. | Removed DCC interface; no current built-in command or transport. |
| [dcc send](../disambiguation/ctcp_send.md) | Extend an offer to copy a file to someone else. | Removed DCC interface; no current built-in command or transport. |
| [disconnect](../commands/disconnect.md) | Close the connection to a server | C command registered; see linked reference for behavior. |
| [encrypt](../disambiguation/encrypt.md) | Initiate transparent encryption over any transport protocol. | Removed or unregistered built-in interface. |
| [exit](../commands/exit.md) | Tell the client to shut down. | C command registered; see linked reference for behavior. |
| [ignore](../disambiguation/ignore.md) | Suppress display of stuff coming from people who you don't want to be bothered with. | No C command; script alias in `script/ignore`. |
| [invite](../commands/invite.md) | Ask someone to join a channel. | C command registered; see linked reference for behavior. |
| [join](../commands/join.md) | Join or associate a channel with a window. | C command registered; see linked reference for behavior. |
| [kick](../commands/kick.md) | Remove someone from a channel. | C command registered; see linked reference for behavior. |
| [knock](../commands/knock.md) | Ask to be [invite](../commands/invite.md)d to a channel. | C command registered; see linked reference for behavior. |
| [lastlog](../commands/lastlog.md) | Redisplay stuff that has previously shown up in a window. | C command registered; see linked reference for behavior. |
| [log](../commands/log.md) | General purpose logfiles. | C command registered; see linked reference for behavior. |
| [me](../commands/me.md) | Tell the channel what you are doing with a [ctcp action](../ctcp/action.md). | C command registered; see linked reference for behavior. |
| [mode](../commands/mode.md) | Change the mode of a channel. | C command registered; see linked reference for behavior. |
| [msg](../commands/msg.md) | Send a message to someone. | C command registered; see linked reference for behavior. |
| [nick](../commands/nick.md) | Change your nickname. | C command registered; see linked reference for behavior. |
| [notify](../disambiguation/notify.md) | Make the client periodically ask the server if certain nicknames are on the network. | No C command; script alias in `script/notify`, `script/notify.jm`. |
| [part](../commands/part.md) | Leave a channel. | C command registered; see linked reference for behavior. |
| [query](../commands/query.md) | Change the current target of a window to a nickname. | C command registered; see linked reference for behavior. |
| [quit](../commands/quit.md) | Same as [exit](../commands/exit.md). | C command registered; see linked reference for behavior. |
| [reconnect](../commands/reconnect.md) | Reconnect to a server. | C command registered; see linked reference for behavior. |
| [server](../commands/server.md) | Connect to an irc server. | C command registered; see linked reference for behavior. |
| [topic](../commands/topic.md) | Change the topic of the channel. | C command registered; see linked reference for behavior. |
| [who](../commands/who.md) | Ask the server for a list of who is on the channel. | C command registered; see linked reference for behavior. |
| [whois](../commands/whois.md) | Ask the server for detailed information about one person. | C command registered; see linked reference for behavior. |
| [whowas](../commands/whowas.md) | Ask the server for info about someone who is not on irc any more. | C command registered; see linked reference for behavior. |


## Less common IRC commands

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [admin](../commands/admin.md) | Ask the server how to contact the server admin. | C command registered; server enforces protocol/permissions. |
| [connect](../commands/connect.md) | Ask a server on the network to link to another server not on the network (oper only). | C command registered; server enforces protocol/permissions. |
| [ctcp action](../ctcp/action.md) | Action protocol used by ME/DESCRIBE; direct CTCP sending bypasses SEND_ACTION. | C ACTION handler registered. |
| [ctcp clientinfo](../ctcp/clientinfo.md) | Ask another client what ctcp commands they support. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp echo](../ctcp/echo.md) | Ask another client to send you a string back. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp finger](../ctcp/finger.md) | Ask another client about the man behind the mask. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp ping](../ctcp/ping.md) | Ask another client to respond, to measure lag time. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp time](../ctcp/time.md) | Ask another client what time they think it is. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp userinfo](../ctcp/userinfo.md) | Ask another client for its user-customizable string. | Script request handler in `script/ctcp`; requires loading. |
| [ctcp utc](../ctcp/utc.md) | Render a supplied seconds-since-epoch timestamp in the receiver's local timezone. | Script request/response conversion in `script/ctcp`. |
| [ctcp version](../ctcp/version.md) | Ask for client identification; replies may be customized or falsified. | Script request handler in `script/ctcp`; requires loading. |
| [dcc chat](../disambiguation/ctcp_chat.md) | Initiate a private conversation with someone outside of irc. | Removed DCC interface; no current built-in command or transport. |
| [dcc close](../disambiguation/ctcp_close.md) | Terminate a dcc session. | Removed DCC interface; no current built-in command or transport. |
| [dcc closeall](../disambiguation/ctcp_closeall.md) | Terminate every dcc session. | Removed DCC interface; no current built-in command or transport. |
| [dcc list](../disambiguation/ctcp_list.md) | Show all of the dcc sessions. | Removed DCC interface; no current built-in command or transport. |
| [dcc raw](../disambiguation/ctcp_raw.md) | Send data to a dcc socket connection. | Removed DCC interface; no current built-in command or transport. |
| [dcc rename](../disambiguation/ctcp_rename.md) | Change the nickname associated with a dcc session. | Removed DCC interface; no current built-in command or transport. |
| [deop](../commands/deop.md) | Revoke your own irc operatorship privileges (oper only) | C command registered; see linked reference for behavior. |
| [describe](../commands/describe.md) | Tell another nick what you are doing with a [ctcp action](../ctcp/action.md). | C command registered; see linked reference for behavior. |
| [die](../commands/die.md) | Tell your server to shut down (oper only) | C command registered; server enforces protocol/permissions. |
| [flush](../disambiguation/flush.md) | Suppress display of stuff coming from the server when you're being flooded. | Removed or unregistered built-in interface. |
| [hostname](../commands/hostname.md) | Change your virtual hostname. | C command registered; see linked reference for behavior. |
| [info](../commands/info.md) | Ask the client and server about the software you're using. | C command registered; see linked reference for behavior. |
| [ison](../commands/ison.md) | Ask the server if nicknames in a list are on the network. | C command registered; see linked reference for behavior. |
| [kill](../commands/kill.md) | Remove someone from the irc network. (oper only) | C command registered; see linked reference for behavior. |
| [links](../commands/links.md) | Ask the server what servers are on the network. | C command registered; server enforces protocol/permissions. |
| [list](../commands/list.md) | Ask the server to tell you about the network's public channels. | C command registered; server enforces protocol/permissions. |
| [load](../commands/load.md) | Import and run commands from a file. | C command registered; see linked reference for behavior. |
| [lusers](../commands/lusers.md) | Ask the server how many users and servers are on the network. | C command registered; server enforces protocol/permissions. |
| [map](../commands/map.md) | Ask the server to show you the topology of the network. | C command registered; server enforces protocol/permissions. |
| [mesg](../disambiguation/mesg.md) | Permit or revoke global write access to your pty. | Removed or unregistered built-in interface. |
| [motd](../commands/motd.md) | Ask the server for the Message Of The Day. | C command registered; server enforces protocol/permissions. |
| [names](../commands/names.md) | Ask the server about who is on what channels. | C command registered; server enforces protocol/permissions. |
| [note](../commands/note.md) | Leave a message for someone when they are away (rare) | C command registered; server enforces protocol/permissions. |
| [notice](../commands/notice.md) | Send a response to someone's message. | C command registered; see linked reference for behavior. |
| [oper](../commands/oper.md) | Assume operatorship privileges (oper only). | C command registered; see linked reference for behavior. |
| [ping](../commands/ping.md) | Figure out how lagged you are. | C command registered; see linked reference for behavior. |
| [redirect](../disambiguation/redirect.md) | Redirect output that would go to a window to someone over irc. | Removed or unregistered built-in interface. |
| [rehash](../commands/rehash.md) | Ask the server to re-read its config files (oper-only) | C command registered; server enforces protocol/permissions. |
| [restart](../commands/restart.md) | Ask the server to shut down and restart (oper-only) | C command registered; server enforces protocol/permissions. |
| [rping](../commands/rping.md) | Send an RPING to the server. | C command registered; server enforces protocol/permissions. |
| [servlist](../commands/servlist.md) | Send a SERVLIST to the server. | C command registered; server enforces protocol/permissions. |
| [squery](../commands/squery.md) | Send an SQUERY to someone. | C command registered; see linked reference for behavior. |
| [squit](../commands/squit.md) | Remove a server from the network. (oper only) | C command registered; see linked reference for behavior. |
| [stats](../commands/stats.md) | Ask the server for statistics (oper only) | C command registered; server enforces protocol/permissions. |
| [time](../commands/time.md) | Ask the server what time it thinks it is. | C command registered; server enforces protocol/permissions. |
| [trace](../commands/trace.md) | Request IRC network routing diagnostics from the server. | C command registered; server enforces protocol/permissions. |
| [uping](../commands/uping.md) | Ping the server. | C command registered; server enforces protocol/permissions. |
| [userhost](../commands/userhost.md) | Ask the server for a nick's userhost. | C command registered; see linked reference for behavior. |
| [userip](../commands/userip.md) | Ask the server for a nick's ip address. | C command registered; see linked reference for behavior. |
| [usrip](../commands/usrip.md) | Ask the server for a nick's ip address. | C command registered; see linked reference for behavior. |
| [version](../commands/version.md) | Ask the server what software it is using. | C command registered; see linked reference for behavior. |
| [wallchops](../commands/wallchops.md) | Send a message to channel operators. | C command registered; see linked reference for behavior. |
| [wallops](../commands/wallops.md) | Send a message to all irc operators (oper only). | C command registered; see linked reference for behavior. |


## Commands

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [abort](../commands/abort.md) | Kill the client with SIGABRT (trap to gdb) | C command registered; see linked reference for behavior. |
| [alias](../commands/alias.md) | Create your own commands | C command registered; see linked reference for behavior. |
| [allocdump](../disambiguation/allocdump.md) | Write debug info about malloc()s to file | No C command registered under this name; may be script-defined. |
| [assign](../commands/assign.md) | Create your own variables | C command registered; see linked reference for behavior. |
| [beep](../commands/beep.md) | Make your terminal beep | C command registered; see linked reference for behavior. |
| [bind](../commands/bind.md) | Tell the client what to do when you press a key | C command registered; see linked reference for behavior. |
| [bind_altcharset](../bind/altcharset.md) | Insert the char that switches your font between character sets. | C binding action registered. |
| [bind_backspace](../bind/backspace.md) | Delete the character before the cursor | C binding action registered. |
| [bind_backward_character](../bind/backward_character.md) | Move the cursor left one position | C binding action registered. |
| [bind_backward_word](../bind/backward_word.md) | Move the cursor to the start of the previous word | C binding action registered. |
| [bind_beginning_of_line](../bind/beginning_of_line.md) | Move the cursor to the start of input line | C binding action registered. |
| [bind_blink](../bind/blink.md) | Insert the char that toggles blinking | C binding action registered. |
| [bind_bold](../bind/bold.md) | Insert the char that toggles bold video | C binding action registered. |
| [bind_clear_screen](../bind/clear_screen.md) | Do a [window clear](../window/clear.md) | C binding action registered. |
| [bind_cpu_saver](../disambiguation/bind_cpu_saver.md) | Ask the client to save cpu by doing less housekeeping | No C binding action registered; scripts may provide an action. |
| [bind_delete_character](../bind/delete_character.md) | Delete the character under the cursor | C binding action registered. |
| [bind_delete_next_word](../bind/delete_next_word.md) | Delete from the cursor to the end of the next word | C binding action registered. |
| [bind_delete_previous_word](../bind/delete_previous_word.md) | Delete from the cursor to the start of the previous word. | C binding action registered. |
| [bind_delete_to_previous_space](../bind/delete_to_previous_space.md) | Delete from the cursor to the first space before the cursor. | C binding action registered. |
| [bind_end_of_line](../bind/end_of_line.md) | Move the cursor to the end of the input line. | C binding action registered. |
| [bind_erase_line](../bind/erase_line.md) | Delete the entire input line. | C binding action registered. |
| [bind_erase_to_beg_of_line](../bind/erase_to_beg_of_line.md) | Delete from the cursor to the start of input. | C binding action registered. |
| [bind_erase_to_end_of_line](../bind/erase_to_end_of_line.md) | Delete from the cursor to the end of input. | C binding action registered. |
| [bind_forward_character](../bind/forward_character.md) | Move the cursor right one position. | C binding action registered. |
| [bind_forward_word](../bind/forward_word.md) | Move the cursor to the next word. | C binding action registered. |
| [bind_highlight_off](../bind/highlight_off.md) | Insert the character that turns all highlights off. | C binding action registered. |
| [bind_next_window](../bind/next_window.md) | Move down one visible ("split") window. | C binding action registered. |
| [bind_nothing](../bind/nothing.md) | Perform no action (unbinds a sequence) | Special unbind selector; not an action. |
| [bind_parse_command](../bind/parse_command.md) | Execute a block of code (provided as argument) | C binding action registered. |
| [bind_previous_window](../bind/previous_window.md) | Move up one visible ("split") window. | C binding action registered. |
| [bind_quit_irc](../disambiguation/bind_quit_irc.md) | Ask the user if they really want to [exit](../commands/exit.md). | No C binding action registered; scripts may provide an action. |
| [bind_quote_character](../bind/quote_character.md) | Tell the client the next keypress should ignore its keybinding. | C binding action registered. |
| [bind_refresh_inputline](../bind/refresh_inputline.md) | Tell the client to redraw the input line. | C binding action registered. |
| [bind_refresh_screen](../bind/refresh_screen.md) | Tell the client to redraw the entire screen. | C binding action registered. |
| [bind_refresh_status](../bind/refresh_status.md) | Tell the client to redraw the status bars. | C binding action registered. |
| [bind_reset_line](../bind/reset_line.md) | Delete the entire input line but don't save it in the cutbuffer. | C binding action registered. |
| [bind_reverse](../bind/reverse.md) | Insert the character that toggles reverse video. | C binding action registered. |
| [bind_scroll_backward](../bind/scroll_backward.md) | Move backwards (older) in the scrollback buffer. | C binding action registered. |
| [bind_scroll_end](../bind/scroll_end.md) | End scrolling back | C binding action registered. |
| [bind_scroll_forward](../bind/scroll_forward.md) | Move forwards (newer) in the scrollback buffer. | C binding action registered. |
| [bind_scroll_start](../bind/scroll_start.md) | Move to the start (oldest) of the scrollback buffer. | C binding action registered. |
| [bind_self_insert](../bind/self_insert.md) | Insert the character itself into the input buffer. | C binding action registered. |
| [bind_send_line](../bind/send_line.md) | Process the contents of the input buffer and reset the input buffer. | C binding action registered. |
| [bind_stop_irc](../disambiguation/bind_stop_irc.md) | Suspend the client with SIGTSTP, returning to your shell. | No C binding action registered; scripts may provide an action. |
| [bind_swap_last_window](../bind/swap_last_window.md) | Do a [window last](../window/last.md). | C binding action registered. |
| [bind_swap_next_window](../bind/swap_next_window.md) | Do a [window next](../window/next.md). | C binding action registered. |
| [bind_swap_previous_window](../bind/swap_previous_window.md) | Do a [window previous](../window/previous.md). | C binding action registered. |
| [bind_switch_channels](../bind/switch_channels.md) | Cycle through the channels in a window. | C binding action registered. |
| [bind_switch_query](../bind/switch_query.md) | Cycle through the queries in a window. | C binding action registered. |
| [bind_toggle_insert_mode](../bind/toggle_insert_mode.md) | Toggle between inserting before the cursor and overwriting what is under the cursor. | C binding action registered. |
| [bind_toggle_stop_screen](../bind/toggle_stop_screen.md) | Do a [window hold_mode](../window/hold_mode.md) TOGGLE. | C binding action registered. |
| [bind_transpose_characters](../disambiguation/bind_transpose_characters.md) | Swap the character the cursor is on with the character before the cursor. | No C binding action registered; scripts may provide an action. |
| [bind_type_text](../bind/type_text.md) | Insert some text (provided as argument) into the input buffer. | C binding action registered. |
| [bind_unclear_screen](../bind/unclear_screen.md) | Do a [window unclear](../window/unclear.md). | C binding action registered. |
| [bind_underline](../bind/underline.md) | Insert the character that toggles underline video. | C binding action registered. |
| [bind_unstop_all_windows](../bind/unstop_all_windows.md) | Do a [window hold_mode](../window/hold_mode.md) OFF in all windows. | C binding action registered. |
| [bind_yank_from_cutbuffer](../bind/yank_from_cutbuffer.md) | Insert into the input line whatever we last deleted into the cutbuffer. | C binding action registered. |
| [bless](../commands/bless.md) | Expose the oldest enclosing locked waiting frame; no enclosing scope is attached when none is locked. | C command registered; see linked reference for behavior. |
| [botmode](../disambiguation/botmode.md) | Detach from the pty run the client in the background.  You are returned to your shell. | Removed or unregistered built-in interface. |
| [break](../commands/break.md) | Early termination of the loop; advance to the next command in the block. | C command registered; see linked reference for behavior. |
| [call](../commands/call.md) | Dump the current script call stack to the window. | C command registered; see linked reference for behavior. |
| [cd](../commands/cd.md) | Change the client's current working directory | C command registered; see linked reference for behavior. |
| [comment](../commands/comment.md) | The no-op command. | C command registered; see linked reference for behavior. |
| [continue](../commands/continue.md) | Early termination of the current iteration of a loop; go back to the top and start the next iteration. | C command registered; see linked reference for behavior. |
| [defer](../commands/defer.md) | Run a command only when the client is not doing something. | C command registered; see linked reference for behavior. |
| [do](../commands/do.md) | Post-test looping command | C command registered; see linked reference for behavior. |
| [dump](../commands/dump.md) | Bulk-removal of aliases, assigns, ons, etc | C command registered; see linked reference for behavior. |
| [echo](../commands/echo.md) | Display something to a window. | C command registered; see linked reference for behavior. |
| [eval](../commands/eval.md) | Expand variables in a statement again and execute it. | C command registered; see linked reference for behavior. |
| [exec](../commands/exec.md) | Run unix commands in a subshell. | C command registered; see linked reference for behavior. |
| [fe](../commands/fe.md) | Iterate over a word list | C command registered; see linked reference for behavior. |
| [fec](../commands/fec.md) | Iterate over the characters in a string | C command registered; see linked reference for behavior. |
| [for](../commands/for.md) | Multi-purpose looping command. | C command registered; see linked reference for behavior. |
| [foreach](../commands/foreach.md) | Iterate over aliases or assign names in a structure. | C command registered; see linked reference for behavior. |
| [hook](../commands/hook.md) | Throw an [on hook](../on/hook.md) event. | C command registered; see linked reference for behavior. |
| [if](../commands/if.md) | Conditional command. | C command registered; see linked reference for behavior. |
| [input](../commands/input.md) | Get the user to answer a question with a modal prompt. | C command registered; see linked reference for behavior. |
| [input_char](../commands/input_char.md) | Get the user to type a key with a modal prompt. | C command registered; see linked reference for behavior. |
| [license](../commands/license.md) | Display the copyright and legal disclaimers. | C command registered; see linked reference for behavior. |
| [local](../commands/local.md) | Create an assign that goes away when the alias is over. | C command registered; see linked reference for behavior. |
| [on](../commands/on.md) | Register event handlers for certain types of hooks | C command registered; see linked reference for behavior. |
| [on_action](../on/action.md) | Hooked when people do [me](../commands/me.md) or [describe](../commands/describe.md). | C hook type registered. |
| [on_channel_lost](../on/channel_lost.md) | Hooked when you stop being on a channel. | C hook type registered. |
| [on_channel_nick](../on/channel_nick.md) | Hooked when people change their nickname. | C hook type registered. |
| [on_channel_signoff](../on/channel_signoff.md) | Hooked when people leave a channel. | C hook type registered. |
| [on_channel_sync](../on/channel_sync.md) | Channel synchronization notification; not a guarantee that every possible fact is known. | C hook type registered. |
| [on_connect](../on/connect.md) | Hooked when a server accepts you. | C hook type registered. |
| [on_ctcp](../on/ctcp.md) | Hooked when a [ctcp](../commands/ctcp.md) message needs to be displayed. | C hook type registered. |
| [on_ctcp_reply](../on/ctcp_reply.md) | Hooked when someone response to your [ctcp](../commands/ctcp.md). | C hook type registered. |
| [on_ctcp_request](../on/ctcp_request.md) | Hooked when someone sends you a [ctcp](../commands/ctcp.md). | C hook type registered. |
| [on_dcc_activity](../disambiguation/on_dcc_activity.md) | Hooked when you send or receive a packet via dcc. | No C hook type registered; historical event. |
| [on_dcc_chat](../disambiguation/on_dcc_chat.md) | Hooked when someone sends you a message over dcc chat. | No C hook type registered; historical event. |
| [on_dcc_connect](../disambiguation/on_dcc_connect.md) | Hooked when a dcc connection starts. | No C hook type registered; historical event. |
| [on_dcc_list](../disambiguation/on_dcc_list.md) | Hooked for each dcc connection when you do [dcc list](../disambiguation/ctcp_list.md). | No C hook type registered; historical event. |
| [on_dcc_lost](../disambiguation/on_dcc_lost.md) | Hooked when a dcc connection ends. | No C hook type registered; historical event. |
| [on_dcc_offer](../disambiguation/on_dcc_offer.md) | Hooked when you offer someone a dcc connection. | No C hook type registered; historical event. |
| [on_dcc_raw](../disambiguation/on_dcc_raw.md) | Hooked when you get data from a raw socket. | No C hook type registered; historical event. |
| [on_dcc_request](../disambiguation/on_dcc_request.md) | Hooked when someone offers you a dcc connection. | No C hook type registered; historical event. |
| [on_disconnect](../on/disconnect.md) | Hooked when you're not connected to a server. | C hook type registered. |
| [on_encrypted_notice](../disambiguation/on_encrypted_notice.md) | Hooked when someone sends you an [encrypt](../disambiguation/encrypt.md)ed notice over irc. | No C hook type registered; historical event. |
| [on_encrypted_privmsg](../disambiguation/on_encrypted_privmsg.md) | Hooked when someone sends you an [encrypt](../disambiguation/encrypt.md)ed message over irc. | No C hook type registered; historical event. |
| [on_error](../on/error.md) | Hooked when the server sends you an ERROR. | C hook type registered. |
| [on_exec](../on/exec.md) | Hooked when an [exec](../commands/exec.md) process writes to stdout. | C hook type registered. |
| [on_exec_errors](../on/exec_errors.md) | Hooked when an [exec](../commands/exec.md) process writes to stderr. | C hook type registered. |
| [on_exec_exit](../on/exec_exit.md) | Hooked whan [exec](../commands/exec.md) process terminates. | C hook type registered. |
| [on_exec_prompt](../on/exec_prompt.md) | Hooked when [exec](../commands/exec.md) process writes a partial line. | C hook type registered. |
| [on_exit](../on/exit.md) | Hooked when the client is shutting down | C hook type registered. |
| [on_flood](../disambiguation/on_flood.md) | Hooked when the client thinks you're being flooded. | No C hook type registered; historical event. |
| [on_general_notice](../on/general_notice.md) | Hooked when you receive any kind of [notice](../commands/notice.md). | C hook type registered. |
| [on_general_privmsg](../on/general_privmsg.md) | Hooked when you receive any kind of msg. | C hook type registered. |
| `on_help` | No description in original index. | No C hook type registered; historical event. |
| [on_hook](../on/hook.md) | Hooked when you use the [hook](../commands/hook.md) command. | C hook type registered. |
| [on_idle](../on/idle.md) | Hooked once a minute when you're not pressing keys. | C hook type registered. |
| [on_input](../on/input.md) | Hooked when you use [bind send_line](../bind/send_line.md). | C hook type registered. |
| [on_invite](../on/invite.md) | Hooked when someone invites you to a channel. | C hook type registered. |
| [on_join](../on/join.md) | Hooked when you join a channel. | C hook type registered. |
| [on_keybinding](../on/keybinding.md) | Hooked each time a keybinding is executed. | C hook type registered. |
| [on_kick](../on/kick.md) | Hooked when someone is kicked from a channel. | C hook type registered. |
| [on_kill](../on/kill.md) | Hooked when someone is kicked off of irc. | C hook type registered. |
| [on_list](../on/list.md) | Hooked for each line when you do [list](../commands/list.md). | C hook type registered. |
| [on_mail](../disambiguation/on_mail.md) | Hooked whenever you receive new email. | No C hook type registered; historical event. |
| [on_mode](../on/mode.md) | Hooked when someone changes the mode on a channel. | C hook type registered. |
| [on_mode_stripped](../on/mode_stripped.md) | Hooked once for each mode change on a channel. | C hook type registered. |
| [on_msg](../on/msg.md) | Hooked when someone sends you a private message. | C hook type registered. |
| [on_msg_group](../on/msg_group.md) | Hooked when someone sends a message to everyone. | C hook type registered. |
| [on_names](../on/names.md) | Hooked for each line when you do [names](../commands/names.md). | C hook type registered. |
| [on_new_nickname](../on/new_nickname.md) | Hooked when the client needs a new nickname from you. | C hook type registered. |
| [on_nickname](../on/nickname.md) | Hooked when someone changes nickname | C hook type registered. |
| [on_note](../disambiguation/on_note.md) | Hooked when someone has sent you an offline message (rare). | No C hook type registered; historical event. |
| [on_notice](../on/notice.md) | Hooked when someone has sent you a [notice](../commands/notice.md). | C hook type registered. |
| [on_notify_signoff](../disambiguation/on_notify_signoff.md) | Hooked when someone you are watching leaves irc. | No C hook type registered; historical event. |
| [on_notify_signon](../disambiguation/on_notify_signon.md) | Hooked when someone you are watching joins irc. | No C hook type registered; historical event. |
| [on_numeric](../on/numeric.md) | Hooked when you receive a numeric reply from server. | C hook type registered. |
| [on_odd_server_stuff](../on/odd_server_stuff.md) | Hooked when the server sends something that can't be parsed. | C hook type registered. |
| [on_oper_notice](../on/oper_notice.md) | Hooked when the server sends a message to opers. | C hook type registered. |
| [on_operwall](../on/operwall.md) | Hooked when someone sends a message to all opers. | C hook type registered. |
| [on_part](../on/part.md) | Hooked when someone leave the channel. | C hook type registered. |
| [on_pong](../on/pong.md) | Hooked when the server responds to your ping request. | C hook type registered. |
| [on_public](../on/public.md) | Hooked when someone talks on a current channel. | C hook type registered. |
| [on_public_msg](../on/public_msg.md) | Hooked when someone outside the channel talks. | C hook type registered. |
| [on_public_notice](../on/public_notice.md) | Hooked when someone sends a [notice](../commands/notice.md) to the channel. | C hook type registered. |
| [on_public_other](../on/public_other.md) | Hooked when someoen talks to a non-current channel. | C hook type registered. |
| [on_raw_irc](../on/raw_irc.md) | Hooked for every line we get from the server. | C hook type registered. |
| [on_redirect](../disambiguation/on_redirect.md) | Hooked for each line of output being redirected back to irc. | No C hook type registered; historical event. |
| [on_send_action](../on/send_action.md) | Hooked for every [me](../commands/me.md) or [describe](../commands/describe.md) you do. | C hook type registered. |
| [on_send_ctcp](../on/send_ctcp.md) | Hooked for every [ctcp](../commands/ctcp.md) you send out. | C hook type registered. |
| [on_send_dcc_chat](../disambiguation/on_send_dcc_chat.md) | Hooked for every [dcc chat](../disambiguation/ctcp_chat.md) message you send out. | No C hook type registered; historical event. |
| [on_send_msg](../on/send_msg.md) | Hooked for every msg you send to someone else. | C hook type registered. |
| [on_send_notice](../on/send_notice.md) | Hooked for every notice you send out. | C hook type registered. |
| [on_send_public](../on/send_public.md) | Hooked for every msg you send to a channel. | C hook type registered. |
| [on_send_to_server](../on/send_to_server.md) | Hooked for every line you send to the server. | C hook type registered. |
| [on_server_established](../on/server_established.md) | Hooked when we connect to a server, before we register. | C hook type registered. |
| [on_server_lost](../on/server_lost.md) | Hooked when we lose a server connection for any reason. | C hook type registered. |
| [on_server_notice](../on/server_notice.md) | Hooked when the server sends you a notice. | C hook type registered. |
| [on_server_status](../on/server_status.md) | Hooked when the status of a server changes. | C hook type registered. |
| [on_set](../on/set.md) | Hooked when you use the [set](../commands/set.md) command. | C hook type registered. |
| [on_signal](../on/signal.md) | Hooked when an OS signal is caught by the client. | C hook type registered. |
| [on_signoff](../on/signoff.md) | Hooked when someone leaves irc. | C hook type registered. |
| [on_silence](../on/silence.md) | Hooked when someone has silenced you. | C hook type registered. |
| [on_ssl_server_cert](../on/ssl_server_cert.md) | Hooked when an ssl server sends us its certificate. | C hook type registered. |
| [on_status_update](../on/status_update.md) | Hooked every time a status bar changes. | C hook type registered. |
| [on_switch_channels](../on/switch_channels.md) | Hooked every time you change the current channel. | C hook type registered. |
| [on_switch_windows](../on/switch_windows.md) | Hooked every time you change the current window. | C hook type registered. |
| [on_timer](../on/timer.md) | Hooked at the top of every minute. | C hook type registered. |
| [on_topic](../on/topic.md) | Hooked when someone changes the topic of a channel. | C hook type registered. |
| [on_unknown_command](../on/unknown_command.md) | Hooked when you run a command that isn't builtin or an alias. | C hook type registered. |
| [on_unload](../on/unload.md) | Hooked when the user does an [unload](../commands/unload.md). | C hook type registered. |
| [on_wall](../disambiguation/on_wall.md) | Hooked when someone does a WALL (rare). | No C hook type registered; historical event. |
| [on_wallop](../on/wallop.md) | Hooked when someone does a wallop. | C hook type registered. |
| [on_who](../on/who.md) | Hooked for each line of a [who](../commands/who.md) reply. | C hook type registered. |
| [on_window](../on/window.md) | Hooked for each line of output to a window. | C hook type registered. |
| [on_window_beforekill](../on/window_beforekill.md) | Hooked just before a window is killed. | C hook type registered. |
| [on_window_command](../on/window_command.md) | Hooked for each operation of a [window](../commands/window.md) command. | C hook type registered. |
| [on_window_create](../on/window_create.md) | Hooked when you create a new window. | C hook type registered. |
| [on_window_kill](../on/window_kill.md) | Hooked after a window has been destroyed. | C hook type registered. |
| [on_window_notified](../on/window_notified.md) | Hooked when a hidden window that is notifying has output. | C hook type registered. |
| [on_window_server](../on/window_server.md) | Hooked when a window changes its server association. | C hook type registered. |
| [on_yell](../on/yell.md) | Hooked when the client outputs a diagnostic message. | C hook type registered. |
| [package](../commands/package.md) | Tag aliases, assigns, ons, etc, as belonging to a package group that can be [unload](../commands/unload.md)ed later. | C command registered; see linked reference for behavior. |
| [parsekey](../commands/parsekey.md) | Execute a keybinding by name. | C command registered; see linked reference for behavior. |
| [pause](../commands/pause.md) | Nonblocking sleep for a time, or until the user presses a key. | C command registered; see linked reference for behavior. |
| [perl](../disambiguation/perl.md) | Run something in a perl interpreter. | Removed or unregistered built-in interface. |
| [pop](../commands/pop.md) | Remove the last word from a variable. | C command registered; see linked reference for behavior. |
| [pretend](../commands/pretend.md) | Pretend the server just sent the client a string. | C command registered; see linked reference for behavior. |
| [push](../commands/push.md) | Add a word to the end of a variable. | C command registered; see linked reference for behavior. |
| [queue](../commands/queue.md) | Add a command to a named queue for executing later. | C command registered; see linked reference for behavior. |
| [quote](../commands/quote.md) | Send a string directly to the server. | C command registered; see linked reference for behavior. |
| [rbind](../commands/rbind.md) | Display all of the sequences bound to a keybinding. | C command registered; see linked reference for behavior. |
| [repeat](../commands/repeat.md) | Execute some commands multiple times. | C command registered; see linked reference for behavior. |
| [return](../commands/return.md) | Early termination of the current atomic scope. | C command registered; see linked reference for behavior. |
| [ruby](../disambiguation/ruby.md) | Run something in a ruby interpreter. | Removed or unregistered built-in interface. |
| [say](../commands/say.md) | Say something to the current channel. | C command registered; see linked reference for behavior. |
| [send](../commands/send.md) | Say something to the current target. | C command registered; see linked reference for behavior. |
| [sendline](../commands/sendline.md) | Pretend the user just typed this string at the input prompt. | C command registered; see linked reference for behavior. |
| [set](../commands/set.md) | Change a configuration variable. | C command registered; see linked reference for behavior. |
| [set_allow_c1_chars](../disambiguation/set_allow_c1_chars.md) | Whether to filter out chars 128-159 or not. | No C setting registered; historical/unsupported built-in name. |
| [set_always_split_biggest](../set/always_split_biggest.md) | Whether to split current window or biggest window for [window new](../window/new.md). | C setting registered. |
| [set_banner](../set/banner.md) | The thing to display before system messages (ie, `***`) | C setting registered. |
| [set_banner_expand](../set/banner_expand.md) | Whether to run [set banner](../set/banner.md) through the $-expander. | C setting registered. |
| [set_beep](../set/beep.md) | Whether to allow beeps or eat them. | C setting registered. |
| [set_channel_name_width](../set/channel_name_width.md) | How wide channel name should be on status bar. | C setting registered. |
| [set_client_information](../set/client_information.md) | Configurable text returned by the supplied CTCP VERSION responder. | C setting registered. |
| [set_clock](../set/clock.md) | Whether to run the system clock (turning off breaks stuff!) | C setting registered. |
| [set_clock_24hour](../set/clock_24hour.md) | Whether the system clock should be 12 hour or 24 hour. | C setting registered. |
| [set_clock_format](../set/clock_format.md) | What strftime() format to use to make the system clock. | C setting registered. |
| [set_clock_interval](../set/clock_interval.md) | How often to update the system clock. | C setting registered. |
| [set_cmdchars](../set/cmdchars.md) | What characters are used before a command (ie, `/`) | C setting registered. |
| [set_comment_hack](../set/comment_hack.md) | Whether to allow comments in the standard loader in the middle of lines. | C setting registered. |
| [set_continued_line](../set/continued_line.md) | What to prefix before broken-up physical lines. | C setting registered. |
| [set_cpu_saver_after](../disambiguation/set_cpu_saver_after.md) | Turn on cpu saver mode after this many minutes idle. | No C setting registered; historical/unsupported built-in name. |
| [set_cpu_saver_every](../disambiguation/set_cpu_saver_every.md) | Perform housekeeping this many seconds when in cpu saver mode.  (Values < 60 are pointless.) | No C setting registered; historical/unsupported built-in name. |
| [set_current_window_level](../set/current_window_level.md) | Assume the current window always owns these levels, overruling the window levels. | C setting registered. |
| [set_dcc_auto_send_rejects](../disambiguation/set_dcc_auto_send_rejects.md) | Send a DCC REJECT to the other peer when you close a dcc. | No C setting registered; historical/unsupported built-in name. |
| [set_dcc_dequote_filenames](../disambiguation/set_dcc_dequote_filenames.md) | De-urlifiy filenames offered; this permits filenames with spaces. | No C setting registered; historical/unsupported built-in name. |
| [set_dcc_long_pathnames](../disambiguation/set_dcc_long_pathnames.md) | Show the full path, or just the filename, in [dcc list](../disambiguation/ctcp_list.md). | No C setting registered; historical/unsupported built-in name. |
| [set_dcc_sliding_window](../disambiguation/set_dcc_sliding_window.md) | Tweak speed of [dcc sends](../disambiguation/ctcp_send.md). | No C setting registered; historical/unsupported built-in name. |
| [set_dcc_store_path](../disambiguation/set_dcc_store_path.md) | Where to save files others offer you. | No C setting registered; historical/unsupported built-in name. |
| [set_dcc_use_gateway_addr](../disambiguation/set_dcc_use_gateway_addr.md) | Whether your client is in your router's DMZ. | No C setting registered; historical/unsupported built-in name. |
| [set_debug](../set/debug.md) | Get help debugging your script. | C setting registered. |
| [set_default_realname](../set/default_realname.md) | The value we send to the server for your IRCNAME | C setting registered. |
| [set_default_username](../set/default_username.md) | The value we sent to the server for your username | C setting registered. |
| [set_dispatch_unknown_commands](../set/dispatch_unknown_commands.md) | Whether to bother the server with your typos. | C setting registered. |
| [set_display](../set/display.md) | Whether to suppress output to windows. | C setting registered. |
| [set_do_notify_immediately](../disambiguation/set_do_notify_immediately.md) | Whether to immediately [ison](../commands/ison.md) when changing your notify list. | No C setting registered; historical/unsupported built-in name. |
| [set_floating_point_math](../set/floating_point_math.md) | Whether to do math in floating point. | C setting registered. |
| [set_floating_point_precision](../set/floating_point_precision.md) | How many decimal places to return for floating point numbers. | C setting registered. |
| [set_flood_after](../disambiguation/set_flood_after.md) | How many lines to receive before checking flooding rate | No C setting registered; historical/unsupported built-in name. |
| [set_flood_ignore](../disambiguation/set_flood_ignore.md) | Whether to ignore people flooding you | No C setting registered; historical/unsupported built-in name. |
| [set_flood_maskuser](../disambiguation/set_flood_maskuser.md) | How to determine what one flooder is | No C setting registered; historical/unsupported built-in name. |
| [set_flood_rate](../disambiguation/set_flood_rate.md) | How many lines per second constitutes a flood (numerator) | No C setting registered; historical/unsupported built-in name. |
| [set_flood_rate_per](../disambiguation/set_flood_rate_per.md) | How many lines per second constitutes a flood (denominator) | No C setting registered; historical/unsupported built-in name. |
| [set_flood_users](../disambiguation/set_flood_users.md) | How many different flooders to check | No C setting registered; historical/unsupported built-in name. |
| [set_flood_warning](../disambiguation/set_flood_warning.md) | Whether to output a message when being flooded. | No C setting registered; historical/unsupported built-in name. |
| [set_hide_private_channels](../set/hide_private_channels.md) | Whether to hide the channel name on the status bar. | C setting registered. |
| [set_high_bit_escape](../disambiguation/set_high_bit_escape.md) | Whether to treat 8 bit chars as escape+7 bit char. | No C setting registered; historical/unsupported built-in name. |
| [set_hold_interval](../set/hold_interval.md) | The default [window hold_interval](../window/hold_interval.md) value. | No C setting; script definition in `script/hold_mode`. |
| [set_hold_mode](../set/hold_mode.md) | The default [window hold_mode](../window/hold_mode.md) value. | No C setting; script definition in `script/hold_mode`. |
| [set_hold_slider](../set/hold_slider.md) | The default [window hold_slider](../window/hold_slider.md) value. | C setting registered. |
| [set_indent](../set/indent.md) | Whether to indent broken lines. | C setting registered. |
| [set_input_prompt](../set/input_prompt.md) | What prompt to use in the input line. | C setting registered. |
| [set_insert_mode](../set/insert_mode.md) | Whether to insert before or overwrite the char under the cursor. | C setting registered. |
| [set_key_interval](../set/key_interval.md) | How many milliseconds until ambiguous keybindings time out. | C setting registered. |
| [set_lastlog](../set/lastlog.md) | What the default [window lastlog](../window/lastlog.md) size should be. | C setting registered. |
| [set_lastlog_level](../set/lastlog_level.md) | What the default [window lastlog_level](../window/lastlog_level.md) should be. | C setting registered. |
| [set_lastlog_rewrite](../set/lastlog_rewrite.md) | Set a default value for [LASTLOG](../commands/lastlog.md) -REWRITE | C setting registered. |
| [set_load_path](../set/load_path.md) | Where to look for [load](../commands/load.md) files. | C setting registered. |
| [set_log](../set/log.md) | Whether to write everything displayed to the global logfile. | C setting registered. |
| [set_log_rewrite](../set/log_rewrite.md) | Rewrite stuff before it is written to the global logfile. | C setting registered. |
| [set_logfile](../set/logfile.md) | The default global logfile name. | C setting registered. |
| [set_mail](../set/mail.md) | How your mail should be checked. | No C setting registered; historical/unsupported built-in name. |
| [set_mail_interval](../set/mail_interval.md) | How often your mail should be checked. | No C setting registered; historical/unsupported built-in name. |
| [set_mail_type](../disambiguation/set_mail_type.md) | What kind of mailbox you have. | No C setting registered; historical/unsupported built-in name. |
| [set_mangle_display](../set/mangle_display.md) | How to mangle stuff before we write it to the screen. | C setting registered. |
| [set_mangle_inbound](../set/mangle_inbound.md) | How to mangle stuff we receive from the server. | C setting registered. |
| [set_mangle_logfiles](../set/mangle_logfiles.md) | How to mangle stuff we are writing to a logfile. | C setting registered. |
| [set_mangle_outbound](../set/mangle_outbound.md) | How to mangle stuff we are sending to the server. | C setting registered. |
| [set_metric_time](../set/metric_time.md) | Whether to use regular (minute) or metric (milliday) units for the clock. | C setting registered. |
| [set_mirc_broken_dcc_resume](../disambiguation/set_mirc_broken_dcc_resume.md) | Whether to turn on [dcc resume](../disambiguation/ctcp_resume.md). | No C setting registered; historical/unsupported built-in name. |
| [set_mode_stripper](../set/mode_stripper.md) | Whether to turn on [on mode_stripped](../on/mode_stripped.md). | C setting registered. |
| [set_new_server_lastlog_level](../set/new_server_lastlog_level.md) | What level to give a window when you do [window server](../window/server.md) to a new server. | C setting registered. |
| [set_no_control_log](../set/no_control_log.md) | Whether control character should be stripped from stuff being written to logfiles. | C setting registered. |
| [set_no_ctcp_flood](../disambiguation/set_no_ctcp_flood.md) | Whether the client should ignore ctcps that come too fast. | No C setting registered; historical/unsupported built-in name. |
| [set_no_fail_disconnect](../set/no_fail_disconnect.md) | Whether the client should disconnect from server when writes fail. | C setting registered. |
| [set_notify](../disambiguation/set_notify.md) | Whether the client should check occasionally for nicknames on irc. | No C setting registered; historical/unsupported built-in name. |
| [set_notify_interval](../disambiguation/set_notify_interval.md) | How often the client should check for [notify](../disambiguation/notify.md) nicknames. | No C setting registered; historical/unsupported built-in name. |
| [set_notify_level](../set/notify_level.md) | What the default [window notify_level](../window/notify_level.md) should be. | C setting registered. |
| [set_notify_on_termination](../set/notify_on_termination.md) | Whether to tell you that an [exec](../commands/exec.md) process has finished. | C setting registered. |
| [set_notify_userhost_automatic](../disambiguation/set_notify_userhost_automatic.md) | Whether the notify system should ask for userhosts before telling you a nick is online. | No C setting registered; historical/unsupported built-in name. |
| [set_num_of_whowas](../set/num_of_whowas.md) | How many whowas entries to ask the server for. | No C setting; script definition in `script/builtins`. |
| [set_old_math_parser](../disambiguation/set_old_math_parser.md) | Turn on the old (legacy) math parser. | No C setting registered; historical/unsupported built-in name. |
| [set_old_server_lastlog_level](../set/old_server_lastlog_level.md) | Resolve level conflicts when merging a window onto an already connected server. | C setting registered. |
| [set_output_rewrite](../set/output_rewrite.md) | How output should be rewritten before it's displayed. | C setting registered. |
| [set_pad_char](../set/pad_char.md) | What char we should use for $[num]var expandos. | C setting registered. |
| [set_quit_message](../set/quit_message.md) | What the default quit message should be. | C setting registered. |
| [set_random_source](../disambiguation/set_random_source.md) | What random number generator we should use. | No C setting registered; historical/unsupported built-in name. |
| [set_realname](../disambiguation/set_realname.md) | What the realname is we send to the server when you connect. | No C setting registered; historical/unsupported built-in name. |
| [set_screen_options](../set/screen_options.md) | What flags should be passed to gnu screen. | C setting registered. |
| [set_scroll_lines](../set/scroll_lines.md) | How many lines the display should scroll when new output occurs in a full window. | C setting registered. |
| [set_scrollback](../set/scrollback.md) | How big the default window scrollback size is. | C setting registered. |
| [set_scrollback_ratio](../set/scrollback_ratio.md) | How much (percentage) each scroll back or scroll forward moves the display | C setting registered. |
| [set_shell](../set/shell.md) | Which shell should be used for [exec](../commands/exec.md) | C setting registered. |
| [set_shell_flags](../set/shell_flags.md) | Which flags should be passed to your shell. | C setting registered. |
| [set_shell_limit](../set/shell_limit.md) | How many lines of output from an [exec](../commands/exec.md) process before we shut it down. | C setting registered. |
| [set_show_channel_names](../set/show_channel_names.md) | Whether to show the channel roster when joining a channel. | C setting registered. |
| [set_show_end_of_msgs](../set/show_end_of_msgs.md) | Whether to hook end-of-message events. | No C setting; script definition in `script/builtins`. |
| [set_show_numerics](../set/show_numerics.md) | Whether to show the numeric or [set banner](../set/banner.md) in numeric replies. | C setting registered. |
| [set_show_status_all](../set/show_status_all.md) | Whether some status expandos show only in current window status or all window status. | C setting registered. |
| [set_show_who_hopcount](../set/show_who_hopcount.md) | Whether to show the server hopcount in who replies. | No C setting; script definition in `script/builtins`. |
| [set_status_away](../set/status_away.md) | How %A looks when you are away. | C setting registered. |
| [set_status_channel](../set/status_channel.md) | How %C looks when you are on a channel. | C setting registered. |
| [set_status_chanop](../set/status_chanop.md) | How %@ looks when you are a chanop. | C setting registered. |
| [set_status_clock](../set/status_clock.md) | How %T looks when the clock is on. | C setting registered. |
| [set_status_cpu_saver](../disambiguation/set_status_cpu_saver.md) | How %L looks when cpu saver is on. | No C setting registered; historical/unsupported built-in name. |
| [set_status_does_expandos](../set/status_does_expandos.md) | Whether to $-expand the status format. | C setting registered. |
| [set_status_format](../set/status_format.md) | What the status bar should look like. | C setting registered. |
| [set_status_format1](../set/status_format1.md) | What the top line of double status bar should be. | C setting registered. |
| [set_status_format2](../set/status_format2.md) | What the bottom line of double status bar should be. | C setting registered. |
| [set_status_halfop](../set/status_halfop.md) | What %@ looks like when you are a half-op | C setting registered. |
| [set_status_hold](../set/status_hold.md) | What %H looks like when youre in hold mode. | C setting registered. |
| [set_status_hold_lines](../set/status_hold_lines.md) | How many lines you're holding in hold mode. | C setting registered. |
| [set_status_insert](../set/status_insert.md) | What %I looks like when youre in insert mode. | C setting registered. |
| [set_status_mail](../set/status_mail.md) | What %M looks like when you have mail. | C setting registered. |
| [set_status_mode](../set/status_mode.md) | What %+ looks like for your channel mode. | C setting registered. |
| [set_status_nickname](../set/status_nickname.md) | What %N looks like for your nickname. | C setting registered. |
| [set_status_no_repeat](../set/status_no_repeat.md) | Don't repeat the last character of status format. | C setting registered. |
| [set_status_noswap](../set/status_noswap.md) | What appears when [window swap](../window/swap.md) is turned off. | C setting registered. |
| [set_status_notify](../set/status_notify.md) | The list of hidden windows that have output. | C setting registered. |
| [set_status_oper](../set/status_oper.md) | What %* looks like if you are an irc oper. | C setting registered. |
| [set_status_overwrite](../set/status_overwrite.md) | What %I looks like when you're in overwite mode. | C setting registered. |
| [set_status_query](../set/status_query.md) | What appears when you have a query. | C setting registered. |
| [set_status_scrollback](../set/status_scrollback.md) | What appears when you are in scrollback. | C setting registered. |
| [set_status_server](../set/status_server.md) | What the server name looks like. | C setting registered. |
| [set_status_ssl_off](../set/status_ssl_off.md) | What appears when server does not do ssl. | C setting registered. |
| [set_status_ssl_on](../set/status_ssl_on.md) | What appears when server does ssl. | C setting registered. |
| [set_status_umode](../set/status_umode.md) | What your user mode looks like. | C setting registered. |
| [set_status_user](../set/status_user.md) | What %U looks like. | C setting registered. |
| [set_status_voice](../set/status_voice.md) | What %@ looks like when you're a channel voice. | C setting registered. |
| [set_status_window](../set/status_window.md) | What the window refnum looks like. | C setting registered. |
| [set_suppress_from_remote_server](../disambiguation/set_suppress_from_remote_server.md) | Whether to append "(from remote.server)" when getting a server notice from a server other than your local server. | No C setting registered; historical/unsupported built-in name. |
| [set_switch_channels_between_windows](../set/switch_channels_between_windows.md) | Whether to allow switch windows to steal another window's channels. | C setting registered. |
| [set_term_does_bright_blink](../set/term_does_bright_blink.md) | Whether the terminal does bright blink. | C setting registered. |
| [set_translation](../disambiguation/set_translation.md) | Which translation to use between your server and your terminal emulator. | No C setting registered; historical/unsupported built-in name. |
| [set_translation_path](../disambiguation/set_translation_path.md) | Where the translation files live. | No C setting registered; historical/unsupported built-in name. |
| [set_user_information](../set/user_information.md) | Text returned by the supplied CTCP USERINFO responder. | C setting registered. |
| [set_verbose_ctcp](../set/verbose_ctcp.md) | Whether to tell you if someone [ctcp](../commands/ctcp.md)s you. | No C setting; script definition in `script/builtins`. |
| [set_word_break](../set/word_break.md) | What characters constitute breaks between words. | C setting registered. |
| [set_wserv_path](../set/wserv_path.md) | Where to find the wserv program. | C setting registered. |
| [set_wserv_type](../set/wserv_type.md) | Whether wserv should create gnu screens or xterms. | C setting registered. |
| [set_xterm](../set/xterm.md) | Which program you use as xterm (ie, rxvt) | C setting registered. |
| [set_xterm_options](../set/xterm_options.md) | What options you needed passed to your xterm. | C setting registered. |
| [setenv](../commands/setenv.md) | Change an environment variable. | C command registered; see linked reference for behavior. |
| [shift_command](../commands/shift.md) | Remove the first word from a variable. | C command registered; see linked reference for behavior. |
| [shook](../commands/shook.md) | Dispatch any arbitrary hook event. | C command registered; see linked reference for behavior. |
| [sleep](../commands/sleep.md) | Blocking sleep of the client. | C command registered; see linked reference for behavior. |
| [stack](../commands/stack.md) | Save and restore copies of an alias, assign, on, set, etc | C command registered; see linked reference for behavior. |
| [stub](../commands/stub.md) | On-demand [load](../commands/load.md)ing for an alias or assign. | C command registered; see linked reference for behavior. |
| [subpackage](../commands/subpackage.md) | Add a segment to the namespace for stuff you [load](../commands/load.md). | C command registered; see linked reference for behavior. |
| [switch](../commands/switch.md) | Select a command block by wildcard pattern. | C command registered; see linked reference for behavior. |
| [tcl](../disambiguation/tcl.md) | Run some command in a tcl interpreter. | Removed or unregistered built-in interface. |
| [silence](../commands/silence.md) | Server-side ignores (not on all servers) | C command registered; server enforces protocol/permissions. |
| [timer](../commands/timer.md) | Run a command later. | C command registered; see linked reference for behavior. |
| [type](../commands/type.md) | Pretend the user just typed a string. | C command registered; see linked reference for behavior. |
| [unclear](../commands/unclear.md) | Pull down the scrollback so it fills the screen. | C command registered; see linked reference for behavior. |
| [unless](../commands/unless.md) | Reverse of [if](../commands/if.md). | C command registered; see linked reference for behavior. |
| [unload](../commands/unload.md) | Remove alias, assigns, ons, sets, etc from a [package](../commands/package.md). | C command registered; see linked reference for behavior. |
| [unshift_command](../commands/unshift.md) | Add a word to the start of a variable. | C command registered; see linked reference for behavior. |
| [until](../commands/until.md) | Reverse of [while](../commands/while.md). | C command registered; see linked reference for behavior. |
| [usleep](../commands/usleep.md) | Blocking sleep of the client, less than a second. | C command registered; see linked reference for behavior. |
| [wait](../commands/wait.md) | Nonblocking suspending of execution until previous server request finished. | C command registered; see linked reference for behavior. |
| [which](../commands/which.md) | Find the full path of a [load](../commands/load.md)ed file. | C command registered; see linked reference for behavior. |
| [while](../commands/while.md) | Execute a block while some expression is true. | C command registered; see linked reference for behavior. |
| [window](../commands/window.md) | Manipulate the EPIC on-screen display areas known as "windows". | C command registered; see linked reference for behavior. |
| [window add](../window/add.md) | Add a nickname to your query list. | C window operation registered. |
| [window back](../window/back.md) | Go back to the previous current window, swapping it in if necessary. | C window operation registered. |
| [window balance](../window/balance.md) | Make all the windows the same size. | C window operation registered. |
| [window beep_always](../window/beep_always.md) | Beep even if the window is hidden. | C window operation registered. |
| [window channel](../window/channel.md) | Change the current channel. | C window operation registered. |
| [window clear](../window/clear.md) | Scroll down so nothing shows in the window. | C window operation registered. |
| [window create](../window/create.md) | Create a new screen. | C window operation registered. |
| [window delete](../window/delete.md) | Remove a screen you don't want any more. | C window operation registered. |
| [window describe](../window/describe.md) | Let me tell you about your window. | C window operation registered. |
| [window discon](../window/discon.md) | Disassociate the window from its server. | C window operation registered. |
| [window double](../window/double.md) | Turn on the double status bar. | C window operation registered. |
| [window echo](../window/echo.md) | Output something to the window. | C window operation registered. |
| [window fixed](../window/fixed.md) | Forbid changing the window's size. | C window operation registered. |
| [window flush](../window/flush.md) | Delete the scrollback below what you see. | C window operation registered. |
| [window flush_scrollback](../window/flush_scrollback.md) | Delete the entire scrollback. | C window operation registered. |
| [window goto](../window/goto.md) | Switch to the window, counting from the top of screen | C window operation registered. |
| [window grow](../window/grow.md) | Increase the size of the window. | C window operation registered. |
| [window hide](../window/hide.md) | Remove a window from the screen. | C window operation registered. |
| [window hide_others](../window/hide_others.md) | Make this window the only one on the screen. | C window operation registered. |
| [window hold_interval](../window/hold_interval.md) | How often the status bar updates when holding stuff in this window. | C window operation registered. |
| [window hold_mode](../window/hold_mode.md) | Stop displaying stuff to the window, but save it in the scrollback. | C window operation registered. |
| [window hold_slider](../window/hold_slider.md) | How much to scroll down when you hit <enter>. | C window operation registered. |
| [window indent](../window/indent.md) | Overrule [set indent](../set/indent.md) for this window. | C window operation registered. |
| [window kill](../window/kill.md) | Delete a window you don't want any more. | C window operation registered. |
| [window kill_all_hidden](../window/kill_all_hidden.md) | Delete all of the hidden (invisible) windows. | C window operation registered. |
| [window kill_others](../window/kill_others.md) | Make this the only window left for the whole client. | C window operation registered. |
| [window killable](../window/killable.md) | Control whether the window may be killed. | C window operation registered. |
| [window killswap](../window/killswap.md) | Kill this window, and swap in an invisible window in its place. | C window operation registered. |
| [window last](../window/last.md) | Go back to the previous current window, but not if it's hidden. | C window operation registered. |
| [window lastlog](../window/lastlog.md) | How many logical lines of output to save. | C window operation registered. |
| [window lastlog_level](../window/lastlog_level.md) | What levels of output should be saved to the lastlog. | C window operation registered. |
| [window level](../window/level.md) | What levels of output should be sent to this window. | C window operation registered. |
| [window list](../window/list.md) | Describe all the windows. | C window operation registered. |
| [window log](../window/log.md) | Turn on or off the window's log file. | C window operation registered. |
| [window logfile](../window/logfile.md) | Change the window's log filename. | C window operation registered. |
| [window move](../window/move.md) | Move the window up or down on the screen. | C window operation registered. |
| [window move_to](../window/move_to.md) | Move the window to the Nth place, counting from the top of the screen. | C window operation registered. |
| [window name](../window/name.md) | Change the window's name. | C window operation registered. |
| [window new](../window/new.md) | Create a new window, splitting a visible window in half. | C window operation registered. |
| [window new_hide](../window/new_hide.md) | Create a new hidden window. | C window operation registered. |
| [window next](../window/next.md) | Swap the current window with the hidden window with the next higher refnum. | C window operation registered. |
| [window noserv](../window/noserv.md) | Disassociate the window from the server. | C window operation registered. |
| [window notified](../window/notified.md) | Toggle whether the window is notifying that it has output since it's been hidden. | C window operation registered. |
| [window notify](../window/notify.md) | Toggle whether the window should notify you when it has output while it's hidden. | C window operation registered. |
| [window notify_level](../window/notify_level.md) | Toggle what types of output cause the window to notify you while it's hidden. | C window operation registered. |
| [window notify_name](../window/notify_name.md) | What the window's name looks like on the status bar when it's notifying you. | C window operation registered. |
| [window number](../window/number.md) | Change the window's refnum. | C window operation registered. |
| [window pop](../window/pop.md) | Go back to the window you last [window push](../window/push.md)ed. | C window operation registered. |
| [window previous](../window/previous.md) | Swap the current window with the hidden window with the next lower refnum. | C window operation registered. |
| [window prompt](../disambiguation/window_prompt.md) | What the input prompt should be for this window, if we are [query](../commands/query.md)ing an [exec](../commands/exec.md)ed process. | No C window operation registered under this name. |
| [window push](../window/push.md) | Save the current window's refnum onto a stack. | C window operation registered. |
| [window query](../window/query.md) | Change the window's current query target. | C window operation registered. |
| [window rebuild_scrollback](../window/rebuild_scrollback.md) | Throw away the scrollback buffer, and rebuild it from the lastlog. | C window operation registered. |
| [window refnum](../window/refnum.md) | Change the current window to another visible window. | C window operation registered. |
| [window refnum_or_swap](../window/refnum_or_swap.md) | Change the current window to another visible window, swapping it in if necessary. | C window operation registered. |
| [window refresh](../window/refresh.md) | Redraw the window. | C window operation registered. |
| [window rejoin](../window/rejoin.md) | Join a channel on a window's server, putting it in whichever window claims it. | C window operation registered. |
| [window remove](../window/remove.md) | Remove a nick from the window's query list. | C window operation registered. |
| [window scrolladj](../window/scrolladj.md) | Whether to insert space at the bottom, or pull scrollback back down from the top when a window grows in size. | C window operation registered. |
| [window scrollback](../window/scrollback.md) | How big the window's scrollback buffer should be. | C window operation registered. |
| [window scroll_backward](../window/scroll_backward.md) | Go back some number of lines in scrollback. | C window operation registered. |
| [window scroll_end](../window/scroll_end.md) | Go to the end (newest) of the scrollback. | C window operation registered. |
| [window scroll_forward](../window/scroll_forward.md) | Go forward some number of lines in scrollback. | C window operation registered. |
| [window scroll_start](../window/scroll_start.md) | Go to the start (oldest) of the scrollback. | C window operation registered. |
| [window search_back](../window/search_back.md) | Go backwards in scrollback to find a string. | C window operation registered. |
| [window search_forward](../window/search_forward.md) | Go forwards in scrollback to find a string. | C window operation registered. |
| [window server](../window/server.md) | Change which server this window is associated with. | C window operation registered. |
| [window show](../window/show.md) | Make a hidden window visible (by splitting a visible window) | C window operation registered. |
| [window show_all](../window/show_all.md) | Make all hidden windows visible (by splitting visible windows) | C window operation registered. |
| [window shrink](../window/shrink.md) | Make a window smaller | C window operation registered. |
| [window size](../window/size.md) | Set the window size to an absolute value. | C window operation registered. |
| [window skip](../window/skip.md) | Can [bind next_window](../bind/next_window.md) move to this window? | C window operation registered. |
| [window stack](../window/stack.md) | Show the refnums saved on the stack. | C window operation registered. |
| [window status_format](../window/status_format.md) | The status line for a single line status bar. | C window operation registered. |
| [window status_format1](../window/status_format1.md) | The bottom line of a double line status bar. | C window operation registered. |
| [window status_format2](../window/status_format2.md) | The top line of a double line status bar. | C window operation registered. |
| [window status_special](../window/status_special.md) | What %. should be on this window's status bar. | C window operation registered. |
| [window swap](../window/swap.md) | Make this window invisible, and make a hidden window visible, taking its place. | C window operation registered. |
| [window swappable](../window/swappable.md) | Change you change the visibility of this window? (Ie, hide a visible window) | C window operation registered. |
| [window topline](../window/topline.md) | Set the text of a selected topline. | C window operation registered. |
| [window toplines](../window/toplines.md) | Set how many toplines a window reserves. | C window operation registered. |
| [window unclear](../window/unclear.md) | Push down the scrollback so it's visible in the window. | C window operation registered. |
| [xdebug](../commands/xdebug.md) | Change some internal debugging flags. | C command registered; see linked reference for behavior. |
| [xecho](../commands/xecho.md) | Output to a window, with flags | C command registered; see linked reference for behavior. |
| [xeval](../commands/xeval.md) | Run some commands, with flags. | C command registered; see linked reference for behavior. |
| [xquote](../commands/xquote.md) | Send a string to the server, with flags. | C command registered; see linked reference for behavior. |
| [xtype](../commands/xtype.md) | Type some text, with flags. | C command registered; see linked reference for behavior. |


## Functions

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [abs](../functions/abs.md) | Absolute Value of number | C function registered; see linked reference for restrictions. |
| [acos](../functions/acos.md) | Arc Cosine of number | C function registered; see linked reference for restrictions. |
| [acosh](../functions/acosh.md) | Inverse Hyperbolic Cosine of number | C function registered; see linked reference for restrictions. |
| [after](../functions/after.md) | Return string after a character | C function registered; see linked reference for restrictions. |
| [afterw](../functions/afterw.md) | Return words in list after a word. | C function registered; see linked reference for restrictions. |
| [aliasctl](../functions/aliasctl.md) | Low level manipulation on aliases and assigns. | C function registered; see linked reference for restrictions. |
| [ascii](../functions/ascii.md) | Convert strings to codepoints (numbers). | C function registered; see linked reference for restrictions. |
| [asciiq](../functions/asciiq.md) | Convert url-encoded strings to codepoints. | C function registered; see linked reference for restrictions. |
| [asin](../functions/asin.md) | Arc Sine of number | C function registered; see linked reference for restrictions. |
| [asinh](../functions/asinh.md) | Inverse Hyperbolic Sine of number | C function registered; see linked reference for restrictions. |
| [atan](../functions/atan.md) | Arctangent of number | C function registered; see linked reference for restrictions. |
| [atanh](../functions/atanh.md) | Inverse Hyperbolic Sine of number. | C function registered; see linked reference for restrictions. |
| [b64decode](../disambiguation/b64decode.md) | Convert Base64 into a string. | No C function; script alias in `script/builtins`. |
| [b64encode](../disambiguation/b64encode.md) | Covnert a string into Base64. | No C function; script alias in `script/builtins`. |
| [before](../functions/before.md) | Return string before a character. | C function registered; see linked reference for restrictions. |
| [beforew](../functions/beforew.md) | Return words in list before a word. | C function registered; see linked reference for restrictions. |
| [bindctl](../functions/bindctl.md) | Low level manipulation of keybindings. | C function registered; see linked reference for restrictions. |
| [builtin_expando](../functions/builtin_expando.md) | Call a builtin-expando directly. | C function registered; see linked reference for restrictions. |
| [ceil](../functions/ceil.md) | Ceiling of number | C function registered; see linked reference for restrictions. |
| [center](../functions/center.md) | Pad a string with spaces to center it. | C function registered; see linked reference for restrictions. |
| [cexist](../functions/cexist.md) | Does a command exist? | C function registered; see linked reference for restrictions. |
| [chanmode](../functions/chanmode.md) | What is the mode of a channel? | C function registered; see linked reference for restrictions. |
| [channel](../functions/channel.md) | Summarize users on channel (op, voice, nick) | C function registered; see linked reference for restrictions. |
| [chanusers](../functions/chanusers.md) | Who is on a channel? | C function registered; see linked reference for restrictions. |
| [chanwin](../functions/chanwin.md) | What window is this channel in? | C function registered; see linked reference for restrictions. |
| [chmod](../functions/chmod.md) | Change a file's permissions. | C function registered; see linked reference for restrictions. |
| [chngw](../functions/chngw.md) | Change a word in a word list. | C function registered; see linked reference for restrictions. |
| [chop](../functions/chop.md) | Remove chars from end of string. | C function registered; see linked reference for restrictions. |
| [chops](../functions/chops.md) | Who is a chanop on a channel? | C function registered; see linked reference for restrictions. |
| [chr](../functions/chr.md) | Convert codepoints (numbers) into strings. | C function registered; see linked reference for restrictions. |
| [chrq](../functions/chrq.md) | Convert codepoints (numbers) into url-encoded strings. | C function registered; see linked reference for restrictions. |
| [cipher](../functions/cipher.md) | What cipher is the (ssl) server connection using? | C function registered; see linked reference for restrictions. |
| [close](../functions/close.md) | Close an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [cofilter](../functions/cofilter.md) | Return words from 2nd variable, based on which words in 1st variable aren't matched by a pattern. | C function registered; see linked reference for restrictions. |
| [common](../functions/common.md) | Return words common to two word lists. | C function registered; see linked reference for restrictions. |
| [connect](../functions/connect.md) | Establish a [dcc raw](../disambiguation/ctcp_raw.md) socket connection. | Registered stub; active implementation returns empty. |
| [convert](../functions/convert.md) | Convert a hostname to paddr, or paddr to hostname. | C function registered; see linked reference for restrictions. |
| [copattern](../functions/copattern.md) | Return words from 2nd variable, based on which words in 1st variable are matched by a pattern. | C function registered; see linked reference for restrictions. |
| [corfilter](../functions/corfilter.md) | Return words from 2nd variable, based on which patterns in 1st variable don't match a string. | C function registered; see linked reference for restrictions. |
| [corpattern](../functions/corpattern.md) | Return words from 2nd variable, based on which patterns in 1st variable match a string. | C function registered; see linked reference for restrictions. |
| [cos](../functions/cos.md) | Cosine of number. | C function registered; see linked reference for restrictions. |
| [cosh](../functions/cosh.md) | Hyperbolic Cosine of number. | C function registered; see linked reference for restrictions. |
| [count](../functions/count.md) | Count the number of instances of character in string. | C function registered; see linked reference for restrictions. |
| [cparse](../functions/cparse.md) | Convert markup codes into color codes. | C function registered; see linked reference for restrictions. |
| [crypt](../disambiguation/crypt.md) | Encrypt a string with crypt(3) (ie, DES) | No C function registered under this name; historical or script-defined. |
| [curcmd](../functions/curcmd.md) | The current ircII command or alias that is running. | C function registered; see linked reference for restrictions. |
| [curpos](../functions/curpos.md) | Where the cursor is in $L (the input line) | C function registered; see linked reference for restrictions. |
| [currchans](../functions/currchans.md) | All current channels for the server. | C function registered; see linked reference for restrictions. |
| [dbmctl](../functions/dbmctl.md) | External hash table support. | C function registered; see linked reference for restrictions. |
| [dccctl](../disambiguation/dccctl.md) | Low level manipulation of your dccs. | No C function registered under this name; historical or script-defined. |
| [decode](../disambiguation/decode.md) | Recover a string passed to [encode](../disambiguation/encode.md). | No C function; script alias in `script/builtins`. |
| [delarray](../functions/delarray.md) | Delete an entire Karll array. | C function registered; see linked reference for restrictions. |
| [delitem](../functions/delitem.md) | Remove an item from a Karll array by item number. | C function registered; see linked reference for restrictions. |
| [delitems](../functions/delitems.md) | Remove items from a Karll array by item numbers. | C function registered; see linked reference for restrictions. |
| [deuhc](../functions/deuhc.md) | Remove the wildcard part of a nick!user@host string. | C function registered; see linked reference for restrictions. |
| [diff](../functions/diff.md) | Return the words that exist only in one of two word lists. | C function registered; see linked reference for restrictions. |
| [encode](../disambiguation/encode.md) | Convert any string into something you can use in a variable name. | No C function; script alias in `script/builtins`. |
| [encryptparm](../disambiguation/encryptparm.md) | Return info about one of your [crypt](../disambiguation/crypt.md) sessions. | No C function registered under this name; historical or script-defined. |
| [eof](../functions/eof.md) | Is the [open](../functions/open.md) file at end-of-file? | C function registered; see linked reference for restrictions. |
| [epic](../functions/epic.md) | Returns 1, because you're using epic! | C function registered; see linked reference for restrictions. |
| [exec](../functions/exec.md) | Run a unix command in a subshell, returning three [open](../functions/open.md) fds. | C function registered; see linked reference for restrictions. |
| [exp](../functions/exp.md) | Exponent operation (x `**` y) | C function registered; see linked reference for restrictions. |
| [ferror](../functions/ferror.md) | Does the [open](../functions/open.md) file have an error condition? | C function registered; see linked reference for restrictions. |
| [fexist](../functions/fexist.md) | Does a filename exist? | C function registered; see linked reference for restrictions. |
| [filter](../functions/filter.md) | Return all words that don't match a pattern. | C function registered; see linked reference for restrictions. |
| [finditem](../functions/finditem.md) | The item number of any instance of an item in a Karll array. | C function registered; see linked reference for restrictions. |
| [finditems](../functions/finditems.md) | The item number of all instances of an item in a Karll array. | C function registered; see linked reference for restrictions. |
| [findw](../functions/findw.md) | Return the first [word](../functions/word.md) index of a word in a list. | C function registered; see linked reference for restrictions. |
| [findws](../functions/findws.md) | Return all of the [word](../functions/word.md) indexes of a word in a list. | C function registered; see linked reference for restrictions. |
| [fix_arglist](../functions/fix_arglist.md) | Convert a string into something you can use as an arglist to an alias or on. | C function registered; see linked reference for restrictions. |
| [floodinfo](../disambiguation/floodinfo.md) | Get information about floods being tracked. | No C function registered under this name; historical or script-defined. |
| [floor](../functions/floor.md) | Largest integral number less than or equal to number. | C function registered; see linked reference for restrictions. |
| [fnexist](../functions/fnexist.md) | Does a built in function exist? | C function registered; see linked reference for restrictions. |
| [frewind](../functions/frewind.md) | Reset an [open](../functions/open.md) file back to the beginning. | C function registered; see linked reference for restrictions. |
| [fromw](../functions/fromw.md) | Return a words in a list starting with a word. | C function registered; see linked reference for restrictions. |
| [fseek](../functions/fseek.md) | Change the read/write position of an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [fsize](../functions/fsize.md) | How many bytes big is a file? | C function registered; see linked reference for restrictions. |
| [fskip](../functions/fskip.md) | Skip lines in an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [ftell](../functions/ftell.md) | Where in an [open](../functions/open.md) file am I? | C function registered; see linked reference for restrictions. |
| [ftime](../functions/ftime.md) | When was a file last modified? (mtime) | C function registered; see linked reference for restrictions. |
| [ftruncate](../functions/ftruncate.md) | Delete the contents of a file, but not the file itself. | C function registered; see linked reference for restrictions. |
| [functioncall](../functions/functioncall.md) | Is this alias being called as a function or as a command? | C function registered; see linked reference for restrictions. |
| [geom](../functions/geom.md) | What is the size of the screen? | C function registered; see linked reference for restrictions. |
| [getarrays](../functions/getarrays.md) | Get the name of all active Karll arrays. | C function registered; see linked reference for restrictions. |
| [getcap](../functions/getcap.md) | Low-level capability information. | C function registered; see linked reference for restrictions. |
| [getcommands](../functions/getcommands.md) | Pattern match command names. | C function registered; see linked reference for restrictions. |
| [getenv](../functions/getenv.md) | Get an environment variable. | C function registered; see linked reference for restrictions. |
| [getfunctions](../functions/getfunctions.md) | Pattern match function names. | C function registered; see linked reference for restrictions. |
| [getgid](../functions/getgid.md) | What is my primary user group? (GID) | C function registered; see linked reference for restrictions. |
| [getitem](../functions/getitem.md) | Get an item number from a Karll array. | C function registered; see linked reference for restrictions. |
| [getlogin](../functions/getlogin.md) | What username am I running under? | C function registered; see linked reference for restrictions. |
| [getmatches](../functions/getmatches.md) | A word list of all item numbers that match in a Karll array of strings. | C function registered; see linked reference for restrictions. |
| [getopt](../functions/getopt.md) | Process command line arguments. | C function registered; see linked reference for restrictions. |
| [getpgrp](../functions/getpgrp.md) | What process group am I in? | C function registered; see linked reference for restrictions. |
| [getrmatches](../functions/getrmatches.md) | A word list of all item numbers that match in a Karll array of patterns. | C function registered; see linked reference for restrictions. |
| [getserial](../functions/getserial.md) | Get a serial number that isn't in use. | C function registered; see linked reference for restrictions. |
| [getset](../functions/getset.md) | Get the value of a [set](../commands/set.md). | C function registered; see linked reference for restrictions. |
| [getsets](../functions/getsets.md) | Pattern match set names. | C function registered; see linked reference for restrictions. |
| [gettmatch](../functions/gettmatch.md) | Return the best item match from a Karll array. | C function registered; see linked reference for restrictions. |
| [getuid](../functions/getuid.md) | What is my user id? (UID) | C function registered; see linked reference for restrictions. |
| [glob](../functions/glob.md) | Pattern match filenames. | C function registered; see linked reference for restrictions. |
| [globi](../disambiguation/globi.md) | Pattern match filenames, case insensitively. | No C function registered under this name; historical or script-defined. |
| [hash_32bit](../functions/hash_32bit.md) | Convert a string to a 32 bit integer. | C function registered; see linked reference for restrictions. |
| [hookctl](../functions/hookctl.md) | Low-level manipulation of your [on](../commands/on.md)s. | C function registered; see linked reference for restrictions. |
| [idle](../functions/idle.md) | How many seconds since a key was last pressed? (Same as $E) | C function registered; see linked reference for restrictions. |
| [ifindfirst](../functions/ifindfirst.md) | Return the **first** item number of an item in a Karll array. | C function registered; see linked reference for restrictions. |
| [ifinditem](../functions/ifinditem.md) | The index number of the first instance of an item in a Karll array. | C function registered; see linked reference for restrictions. |
| [ifinditems](../functions/ifinditems.md) | The index number of all instances of an item in a Karll array. | C function registered; see linked reference for restrictions. |
| [igetitem](../functions/igetitem.md) | Get an index number from a Karll array. | C function registered; see linked reference for restrictions. |
| [igetmatches](../functions/igetmatches.md) | A word list of all index numbers that match in a Karll array of strings. | C function registered; see linked reference for restrictions. |
| [igetrmatches](../functions/igetrmatches.md) | A word list of all index numbers that match in a Karll array of patterns. | C function registered; see linked reference for restrictions. |
| [ignorectl](../disambiguation/ignorectl.md) | Low-level manipulation of your [ignore](../disambiguation/ignore.md)s. | No C function registered under this name; historical or script-defined. |
| [index](../functions/index.md) | Return the position of a char in a string. | C function registered; see linked reference for restrictions. |
| [indextoitem](../functions/indextoitem.md) | Convert a Karll array index number to an item number. | C function registered; see linked reference for restrictions. |
| [indextoword](../functions/indextoword.md) | What word in list contains the 'index'th character? | C function registered; see linked reference for restrictions. |
| [info](../functions/info.md) | Return structured information about the client. | C function registered; see linked reference for restrictions. |
| [insert](../functions/insert.md) | Insert a word into a string at the "index"th character. | C function registered; see linked reference for restrictions. |
| [insertw](../functions/insertw.md) | Insert a word into a list. | C function registered; see linked reference for restrictions. |
| [iptolong](../functions/iptolong.md) | Convert a p-addr to a 32 bit long integer (for dcc). | C function registered; see linked reference for restrictions. |
| [iptoname](../functions/iptoname.md) | Resolve the canonical hostname of a p-addr. | C function registered; see linked reference for restrictions. |
| [irclib](../functions/irclib.md) | Where is the standard irc library directory? | C function registered; see linked reference for restrictions. |
| [isalpha](../functions/isalpha.md) | Does this string start with an a-z A-Z character? | C function registered; see linked reference for restrictions. |
| [isaway](../functions/isaway.md) | Am I away on a server? | C function registered; see linked reference for restrictions. |
| [ischannel](../functions/ischannel.md) | Is this a valid channel name on a server? | C function registered; see linked reference for restrictions. |
| [ischanop](../functions/ischanop.md) | Is this person a chanop (+o) on a channel? | C function registered; see linked reference for restrictions. |
| [ischanvoice](../functions/ischanvoice.md) | Is this person a channel voice (+v) on a channel? | C function registered; see linked reference for restrictions. |
| [isconnected](../functions/isconnected.md) | Am I registered to this server? | C function registered; see linked reference for restrictions. |
| [iscurchan](../functions/iscurchan.md) | Is this channel a current channel on this server? | C function registered; see linked reference for restrictions. |
| [isdigit](../functions/isdigit.md) | Does this string start with a digit? | C function registered; see linked reference for restrictions. |
| [isdisplaying](../functions/isdisplaying.md) | Is output being suppressed (ie, with `^)?` | C function registered; see linked reference for restrictions. |
| [isencrypted](../functions/isencrypted.md) | Am I connected to this server using ssl? | C function registered; see linked reference for restrictions. |
| [isfilevalid](../functions/isfilevalid.md) | Can I use this fd in file manipulation functions? | C function registered; see linked reference for restrictions. |
| [ishalfop](../functions/ishalfop.md) | Is this person a channel half-op (+h) on a channel? | C function registered; see linked reference for restrictions. |
| [isnumber](../functions/isnumber.md) | Is this string a number that is valid in this base? | C function registered; see linked reference for restrictions. |
| [itemtoindex](../functions/itemtoindex.md) | Convert a Karll array item number to an index number. | C function registered; see linked reference for restrictions. |
| [jn](../functions/jn.md) | Bessel function of the first kind of the order N.  Um, yea. | C function registered; see linked reference for restrictions. |
| [joinstr](../functions/joinstr.md) | Create a word list by joining words in each of 2 variables. | C function registered; see linked reference for restrictions. |
| [jot](../functions/jot.md) | Create a list of numbers, suitable for iterating over. | C function registered; see linked reference for restrictions. |
| [json_error](../functions/json_error.md) | Return error string set by the last call to [json_explode](../functions/json_explode.md). | C function registered; see linked reference for restrictions. |
| [json_explode](../functions/json_explode.md) | Parse a JSON string and expand it into an assign variable structure. | C function registered; see linked reference for restrictions. |
| [json_implode](../functions/json_implode.md) | Serialise an assign variable structure into a JSON string. | C function registered; see linked reference for restrictions. |
| [key](../functions/key.md) | What is the channel key of a channel? | C function registered; see linked reference for restrictions. |
| [killpid](../functions/killpid.md) | Kill a unix process. | C function registered; see linked reference for restrictions. |
| [lastlog](../functions/lastlog.md) | Search the lastlog and return matching lines as values I can pass to [line](../functions/line.md). | C function registered; see linked reference for restrictions. |
| [left](../functions/left.md) | Return the first N characters of a string. | C function registered; see linked reference for restrictions. |
| [leftpc](../functions/leftpc.md) | Return the longest initial part of the string that contains N printable characters. | C function registered; see linked reference for restrictions. |
| [leftw](../functions/leftw.md) | Return the first N words of a word list. | C function registered; see linked reference for restrictions. |
| [levelctl](../functions/levelctl.md) | Low-level manipulation of window/lastlog/flood/ignore levels. | C function registered; see linked reference for restrictions. |
| [levelwindow](../functions/levelwindow.md) | Which window owns this level? | C function registered; see linked reference for restrictions. |
| [line](../functions/line.md) | Return a line from the lastlog. | C function registered; see linked reference for restrictions. |
| [listarray](../functions/listarray.md) | Describe the contents of a Karll array. | C function registered; see linked reference for restrictions. |
| [listen](../functions/listen.md) | Open a server tcp socket. | Registered stub; active implementation returns empty. |
| [loadinfo](../functions/loadinfo.md) | What file is loading right now? | C function registered; see linked reference for restrictions. |
| [log](../functions/log.md) | Natural logarithm of a number. | C function registered; see linked reference for restrictions. |
| [log10](../functions/log10.md) | Base-10 logarithm of a number. | C function registered; see linked reference for restrictions. |
| [logctl](../functions/logctl.md) | Low-level manipulation of logfiles. | C function registered; see linked reference for restrictions. |
| [longtoip](../functions/longtoip.md) | Convert a 32 bit integer (from DCC) into a p-addr. | C function registered; see linked reference for restrictions. |
| [mask](../functions/mask.md) | Normalize a string into a nick!user@host pattern for banning. | C function registered; see linked reference for restrictions. |
| [match](../functions/match.md) | Return the string that is best matched by a pattern. | C function registered; see linked reference for restrictions. |
| [matchitem](../functions/matchitem.md) | The item number of the best string match in a Karll array. | C function registered; see linked reference for restrictions. |
| [maxlen](../functions/maxlen.md) | Return the [strlen](../functions/strlen.md) of the longest word in list. | C function registered; see linked reference for restrictions. |
| [metric_time](../functions/metric_time.md) | Return the current time in (days, millidays) | C function registered; see linked reference for restrictions. |
| [mid](../functions/mid.md) | Return a substring of a string. | C function registered; see linked reference for restrictions. |
| [midw](../functions/midw.md) | Return a subset of the words in a list. | C function registered; see linked reference for restrictions. |
| [mkdir](../functions/mkdir.md) | Make a directory. | C function registered; see linked reference for restrictions. |
| [mktime](../functions/mktime.md) | Convert (Year,month.day.hour.minute.second) to a [time](../functions/time.md). | C function registered; see linked reference for restrictions. |
| [msar](../functions/msar.md) | Multiple Search And Replace on a string. | C function registered; see linked reference for restrictions. |
| [mychannels](../functions/mychannels.md) | What channels am I on this server? | C function registered; see linked reference for restrictions. |
| [myservers](../functions/myservers.md) | What severs am I connected to? | C function registered; see linked reference for restrictions. |
| [nametoip](../functions/nametoip.md) | Convert a hostname to its canonical p-addr (ip address) | C function registered; see linked reference for restrictions. |
| [nochops](../functions/nochops.md) | Who is not a channel operator? | C function registered; see linked reference for restrictions. |
| [nohighlight](../functions/nohighlight.md) | Convert highlight characters to reverse chars, the way it's done on the input prompt. | C function registered; see linked reference for restrictions. |
| [notify](../disambiguation/notify.md) | Low level manipulation of your notifies. | No C function; script alias in `script/notify`, `script/notify.jm`. |
| [notifywindows](../functions/notifywindows.md) | Which hidden windows are notifying they have output? | C function registered; see linked reference for restrictions. |
| [notw](../functions/notw.md) | Remove a word from a list. | C function registered; see linked reference for restrictions. |
| [numarrays](../functions/numarrays.md) | How many Karll arrays are active? | C function registered; see linked reference for restrictions. |
| [numitems](../functions/numitems.md) | The number of items in a Karll array. | C function registered; see linked reference for restrictions. |
| [numlines](../functions/numlines.md) | How many lines on the display would this string take up if you [echo](../commands/echo.md)d it? | C function registered; see linked reference for restrictions. |
| [numonchannel](../functions/numonchannel.md) | How many people are on the channel> | C function registered; see linked reference for restrictions. |
| [numsort](../functions/numsort.md) | Sort a string, honoring numerical order. | C function registered; see linked reference for restrictions. |
| [numwords](../functions/numwords.md) | How many words are in this word list? | C function registered; see linked reference for restrictions. |
| [onchannel](../functions/onchannel.md) | Is this person on the channel? | C function registered; see linked reference for restrictions. |
| [open](../functions/open.md) | Open a file for reading or writing. | C function registered; see linked reference for restrictions. |
| [outputinfo](../functions/outputinfo.md) | What is the current output target and output level? | C function registered; see linked reference for restrictions. |
| [pad](../functions/pad.md) | Add chars to a string to make it a certain length. | C function registered; see linked reference for restrictions. |
| [pass](../functions/pass.md) | Remove all but certain characters from a string. | C function registered; see linked reference for restrictions. |
| [pattern](../functions/pattern.md) | Return all words from list that are matched by pattern. | C function registered; see linked reference for restrictions. |
| [perl](../disambiguation/perl.md) | Run some code in perl. | No C function registered under this name; historical or script-defined. |
| [perlcall](../disambiguation/perlcall.md) | Call a perl function (simple form) | No C function registered under this name; historical or script-defined. |
| [perlxcall](../disambiguation/perlxcall.md) | Call a perl function (advanced form) | No C function registered under this name; historical or script-defined. |
| [pid](../functions/pid.md) | What is the process id? | C function registered; see linked reference for restrictions. |
| [pop](../functions/pop.md) | Remove the last word from a variable. | C function registered; see linked reference for restrictions. |
| [ppid](../functions/ppid.md) | What is my parent's process id? | C function registered; see linked reference for restrictions. |
| [prefix](../functions/prefix.md) | What is the common leading substring on all of the words in a list? | C function registered; see linked reference for restrictions. |
| [printlen](../functions/printlen.md) | How many columns on the screen does this string take up? | C function registered; see linked reference for restrictions. |
| [push](../functions/push.md) | Add a word to the end of a variable. | C function registered; see linked reference for restrictions. |
| [querywin](../functions/querywin.md) | Which window claims this nick as a query? | C function registered; see linked reference for restrictions. |
| [qword](../functions/qword.md) | Get a word from a list as a qword (so it can be used as a dword) | C function registered; see linked reference for restrictions. |
| [rand](../functions/rand.md) | Get a random number. | C function registered; see linked reference for restrictions. |
| [randread](../functions/randread.md) | Read a random line from a file. | C function registered; see linked reference for restrictions. |
| [read](../functions/read.md) | Read a line from an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [realpath](../functions/realpath.md) | What is the canonical pathname for this file? | C function registered; see linked reference for restrictions. |
| [regcomp](../functions/regcomp.md) | Compile a regular expression. | C function registered; see linked reference for restrictions. |
| [regcomp_cs](../functions/regcomp_cs.md) | Compile a regular expression, case insensitively. | C function registered; see linked reference for restrictions. |
| [regerror](../functions/regerror.md) | Did this regular expression fail? | C function registered; see linked reference for restrictions. |
| [regexec](../functions/regexec.md) | Match a compiled regular expression against a string. | C function registered; see linked reference for restrictions. |
| [regfree](../functions/regfree.md) | Release a compiled regular expression. | C function registered; see linked reference for restrictions. |
| [regmatches](../functions/regmatches.md) | Match a compiled regular expression against a string, returning all backreferences. | C function registered; see linked reference for restrictions. |
| [remw](../functions/remw.md) | Remove a certain word from a word list. | C function registered; see linked reference for restrictions. |
| [remws](../functions/remws.md) | Remove several certain words from a word list. | C function registered; see linked reference for restrictions. |
| [rename](../functions/rename.md) | Rename a file. | C function registered; see linked reference for restrictions. |
| [repeat](../functions/repeat.md) | Repeat a string several times. | C function registered; see linked reference for restrictions. |
| [rest](../functions/rest.md) | Return a string starting with a certain character position. | C function registered; see linked reference for restrictions. |
| [restw](../functions/restw.md) | Return a word list starting with a certain word. | C function registered; see linked reference for restrictions. |
| [reverse](../functions/reverse.md) | Reverse the order of characters in a string. | C function registered; see linked reference for restrictions. |
| [revw](../functions/revw.md) | Reverse the order of words in a string. | C function registered; see linked reference for restrictions. |
| [rfilter](../functions/rfilter.md) | Return all of the patterns from a list that don't match a string. | C function registered; see linked reference for restrictions. |
| [right](../functions/right.md) | Return the last few characters in a string. | C function registered; see linked reference for restrictions. |
| [rightw](../functions/rightw.md) | Return the last few words in a word list. | C function registered; see linked reference for restrictions. |
| [rindex](../functions/rindex.md) | Return the character position of the last instance of a char in a string. | C function registered; see linked reference for restrictions. |
| [rmatch](../functions/rmatch.md) | Return the best pattern from a list that matches a string. | C function registered; see linked reference for restrictions. |
| [rmatchitem](../functions/rmatchitem.md) | The item number of the best pattern in a Karll array. | C function registered; see linked reference for restrictions. |
| [rmdir](../functions/rmdir.md) | Remove a directory. | C function registered; see linked reference for restrictions. |
| [rpattern](../functions/rpattern.md) | Return all of the patterns from a list that matches a string. | C function registered; see linked reference for restrictions. |
| [rsubstr](../functions/rsubstr.md) | Find the last instance of a substring in a larger string. | C function registered; see linked reference for restrictions. |
| [ruby](../disambiguation/ruby.md) | Run some code using ruby. | No C function registered under this name; historical or script-defined. |
| [sar](../functions/sar.md) | Search And Replace a string in a larger string. | C function registered; see linked reference for restrictions. |
| [sedcrypt](../disambiguation/sedcrypt.md) | Crypt/Decrypt a string as though sending it to a person. | No C function registered under this name; historical or script-defined. |
| [serverctl](../functions/serverctl.md) | Low-level manipulation of servers. | C function registered; see linked reference for restrictions. |
| [serverwin](../functions/serverwin.md) | What is the current window for a server? | C function registered; see linked reference for restrictions. |
| [servports](../functions/servports.md) | Return the remote and local portnum for a server connection. | C function registered; see linked reference for restrictions. |
| [setitem](../functions/setitem.md) | Set an item in a Karll Array. | C function registered; see linked reference for restrictions. |
| [sha256](../disambiguation/sha256.md) | SHA256 message digest of a string (Strong crypto hashing). | No C function; script alias in `script/builtins`. |
| [shift](../functions/shift.md) | Remove the first word from a variable. | C function registered; see linked reference for restrictions. |
| [shiftbrace](../functions/shiftbrace.md) | Remove and return the {...} at the start of a variable. | C function registered; see linked reference for restrictions. |
| [sin](../functions/sin.md) | Sine of number. | C function registered; see linked reference for restrictions. |
| [sinh](../functions/sinh.md) | Hyperbolic Sine of number. | C function registered; see linked reference for restrictions. |
| [sort](../functions/sort.md) | Sort a word list case insensitively. | C function registered; see linked reference for restrictions. |
| [splice](../functions/splice.md) | Remove some words, and insert new words, into a word list. | C function registered; see linked reference for restrictions. |
| [split](../functions/split.md) | Convert characters to spaces in a string. | C function registered; see linked reference for restrictions. |
| [srand](../functions/srand.md) | Seed the random number generator. | C function registered; see linked reference for restrictions. |
| [ssl](../functions/ssl.md) | Was this client compiled with ssl support? | C function registered; see linked reference for restrictions. |
| [startupfile](../functions/startupfile.md) | What file did the client load at startup? | C function registered; see linked reference for restrictions. |
| [stat](../functions/stat.md) | Return info about a file. | C function registered; see linked reference for restrictions. |
| [status](../functions/status.md) | Get the status bar. | C function registered; see linked reference for restrictions. |
| [stime](../functions/stime.md) | Human readable form of the time. | C function registered; see linked reference for restrictions. |
| [strftime](../functions/strftime.md) | Format the current time any way you want. | C function registered; see linked reference for restrictions. |
| [strptime](../functions/strptime.md) | Convert a string into a unix timestamp. | C function registered; see linked reference for restrictions. |
| [strip](../functions/strip.md) | Remove characters from a string. | C function registered; see linked reference for restrictions. |
| [stripansi](../functions/stripansi.md) | Convert codepoints 14-33 except 15 and 22 in a string to "safe" codepoints. | C function registered; see linked reference for restrictions. |
| [stripansicodes](../functions/stripansicodes.md) | The same as $stripcrap(NORMALIZE $*) | C function registered; see linked reference for restrictions. |
| [stripc](../functions/stripc.md) | The same as $stripcrap(COLOR $*) | C function registered; see linked reference for restrictions. |
| [stripcrap](../functions/stripcrap.md) | [Mangle](mangle_types.md) a string. | C function registered; see linked reference for restrictions. |
| [strlen](../functions/strlen.md) | The number of characters in a string. | C function registered; see linked reference for restrictions. |
| [strtol](../functions/strtol.md) | Convert a baseN number to base10, 2 <= N <= 36. | C function registered; see linked reference for restrictions. |
| [substr](../functions/substr.md) | Find a substring in a larger string. | C function registered; see linked reference for restrictions. |
| [symbolctl](../functions/symbolctl.md) | Low level manipulation of aliases, assigns, builtin commands, builtin functions, builtin expandos, and builtin variables ([set](../commands/set.md)s). | C function registered; see linked reference for restrictions. |
| [tan](../functions/tan.md) | Tangent of number. | C function registered; see linked reference for restrictions. |
| [tanh](../functions/tanh.md) | Hyperbolic Tangent. | C function registered; see linked reference for restrictions. |
| [tcl](../disambiguation/tcl.md) | Run some code using tcl. | No C function registered under this name; historical or script-defined. |
| [tdiff](../functions/tdiff.md) | Number converted to hours-minutes-seconds (long form) | C function registered; see linked reference for restrictions. |
| [tdiff2](../functions/tdiff2.md) | Number converted to hours-minutes-seconds (short form) | C function registered; see linked reference for restrictions. |
| [time](../functions/time.md) | What is the current time, in whole seconds? | C function registered; see linked reference for restrictions. |
| [timerctl](../functions/timerctl.md) | Low-level manipulation of [timer](../commands/timer.md)s. | C function registered; see linked reference for restrictions. |
| [tobase](../functions/tobase.md) | Convert a base10 number to another base. | C function registered; see linked reference for restrictions. |
| [tolower](../functions/tolower.md) | Convert a string to lowercase. | C function registered; see linked reference for restrictions. |
| [toupper](../functions/toupper.md) | Convert a string to uppercase. | C function registered; see linked reference for restrictions. |
| [tow](../functions/tow.md) | Return a word list up to and including a word. | C function registered; see linked reference for restrictions. |
| [tr](../functions/tr.md) | Change characters in a string. | C function registered; see linked reference for restrictions. |
| [trunc](../functions/trunc.md) | Round a floating point numbers to a certain number of decimal places. | C function registered; see linked reference for restrictions. |
| [ttyname](../functions/ttyname.md) | What tty am I using? | C function registered; see linked reference for restrictions. |
| [twiddle](../functions/twiddle.md) | Expand a pathnames that contains a ~ for homedirs. | C function registered; see linked reference for restrictions. |
| [uhc](../disambiguation/uhc.md) | Convert a string into a full nick!user@host format. | No C function registered under this name; historical or script-defined. |
| [umask](../functions/umask.md) | Change the default umask for newly created files. | C function registered; see linked reference for restrictions. |
| [uname](../functions/uname.md) | Tell me about the unix I'm using. | C function registered; see linked reference for restrictions. |
| [uniq](../functions/uniq.md) | Remove duplicates from a word list. | C function registered; see linked reference for restrictions. |
| [unlink](../functions/unlink.md) | Remove a file. | C function registered; see linked reference for restrictions. |
| [unshift](../functions/unshift.md) | Add a string to the start of a string. | C function registered; see linked reference for restrictions. |
| [unsplit](../functions/unsplit.md) | Convert a dword list into a uword list using a seperator. | C function registered; see linked reference for restrictions. |
| [urldecode](../disambiguation/urldecode.md) | Convert a string from url format into normal. | No C function; script alias in `script/builtins`. |
| [urlencode](../disambiguation/urlencode.md) | Convert a string into url format. | No C function; script alias in `script/builtins`. |
| [userhost](../functions/userhost.md) | What is the userhost of a nickname or current message? | C function registered; see linked reference for restrictions. |
| [usermode](../functions/usermode.md) | What is my usermode? | C function registered; see linked reference for restrictions. |
| [usetitem](../functions/usetitem.md) | Set an item in a Karll array (faster) | C function registered; see linked reference for restrictions. |
| [utime](../functions/utime.md) | What is the current time, including usec? | C function registered; see linked reference for restrictions. |
| [version](../functions/version.md) | What is the server version? | C function registered; see linked reference for restrictions. |
| [which](../functions/which.md) | Path search for a file. | C function registered; see linked reference for restrictions. |
| [winchan](../functions/winchan.md) | Translate between current channel and window refnum. | C function registered; see linked reference for restrictions. |
| [windowctl](../functions/windowctl.md) | Low-level manipulation of windows. | C function registered; see linked reference for restrictions. |
| [word](../functions/word.md) | Get a word from a word list. | C function registered; see linked reference for restrictions. |
| [wordtoindex](../functions/wordtoindex.md) | Which [index](../functions/index.md) position does this word start at? | C function registered; see linked reference for restrictions. |
| [write](../functions/write.md) | Write a line to an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [writeb](../functions/writeb.md) | Write some binary data to an [open](../functions/open.md) file. | C function registered; see linked reference for restrictions. |
| [xdebug](../functions/xdebug.md) | Manipulate the xdebug flags. | C function registered; see linked reference for restrictions. |
| [xform](../functions/xform.md) | Transform a string from one form to another. | C function registered; see linked reference for restrictions. |
| [yn](../functions/yn.md) | Bessel function of the second kind for integer order n. | C function registered; see linked reference for restrictions. |


## Scripts

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [2.8script](../scripts/2.8script.md) | Full featured join/leave aliases, and an on 367 handler. | Script file retained; availability is not a compatibility guarantee. |
| [activity](../scripts/activity.md) | An example of how to use generalized activity handling. | Script file retained; availability is not a compatibility guarantee. |
| [addset](../scripts/addset.md) | Create and destroy your own [set](../commands/set.md)s. | Script file retained; availability is not a compatibility guarantee. |
| [altchan](../scripts/altchan.md) | Implement <alt>-<number> window switching like bx. | Script file retained; availability is not a compatibility guarantee. |
| [autoget](../disambiguation/scripts_autoget.md) | Control what kinds of [dcc get](../disambiguation/ctcp_get.md)s you want to auto-get. | Retained script; references removed DCC facilities. |
| [autojoin](../scripts/autojoin.md) | Automatically join channels when you (re-)connect to server. | Script file retained; availability is not a compatibility guarantee. |
| [autoquery](../scripts/autoquery.md) | Automatically create a new querywin when someone msgs you. | Script file retained; availability is not a compatibility guarantee. |
| [builtins](../scripts/builtins.md) | Top-level script for compatibility with EPIC4.  Must load! | Retained script; references removed DCC facilities. |
| [chanmonitor](../scripts/chanmonitor.md) | Displays output of non-current channels in hidden windows to a special visible window. | Script file retained; availability is not a compatibility guarantee. |
| [commandqueues](../scripts/commandqueues.md) | Time-delay processing queues. | Script file retained; availability is not a compatibility guarantee. |
| [country](../scripts/country.md) | Convert between country codes and country names. | Script file retained; availability is not a compatibility guarantee. |
| [data_array](../scripts/data_array.md) | This is a work in progress. | Script file retained; availability is not a compatibility guarantee. |
| [data_struct](../scripts/data_struct.md) | This is a work in progress. | Script file retained; availability is not a compatibility guarantee. |
| [dcc_ports](../disambiguation/scripts_dcc_ports.md) | Specify a port range to be used automatically for [DCC](../disambiguation/dcc.md)s | Removed DCC interface; no current built-in command or transport. |
| [debug.irc](../scripts/debug.irc.md) | Aliases to help you debug/tune your script. | Script file retained; availability is not a compatibility guarantee. |
| [dump](../scripts/dump.md) | Simulate the dump command from EPIC4. | Retained script; references removed DCC facilities. |
| [edit_topic](../scripts/edit_topic.md) | Edit the topic of the current channel. | Script file retained; availability is not a compatibility guarantee. |
| [epic-crypt-gpg](../disambiguation/scripts_epic-crypt-gpg.md) | A shell script to use with [crypt](../disambiguation/crypt.md). | No bundled script file under this name; historical reference. |
| [epic-crypt-gpg-aa](../disambiguation/scripts_epic-crypt-gpg-aa.md) | A shell script to use with [crypt](../disambiguation/crypt.md). | No bundled script file under this name; historical reference. |
| [files](../scripts/files.md) | Examples of what you can do with [open](../functions/open.md) files. | Script file retained; availability is not a compatibility guarantee. |
| [floodprot](../scripts/floodprot.md) | Implement outbound flood protection. | Script file retained; availability is not a compatibility guarantee. |
| [functions](../scripts/functions.md) | Some utility commands and aliases. | Retained script; references removed DCC facilities. |
| [global](../scripts/global.md) | The script you should load in your epicrc. | Script file retained; availability is not a compatibility guarantee. |
| [grep](../scripts/grep.md) | An example of how to grep a file with [open](../functions/open.md). | Script file retained; availability is not a compatibility guarantee. |
| [guh](../scripts/guh.md) | A few aliases for userhost, ison, and channels. | Script file retained; availability is not a compatibility guarantee. |
| [help](../disambiguation/scripts_help.md) | Peruse the old style help files. | Script file retained; availability is not a compatibility guarantee. |
| [highlight](../scripts/highlight.md) | Imlement something like the old "highlight ignores". | Script file retained; availability is not a compatibility guarantee. |
| [history](../scripts/history.md) | Command history.  Must load! | Script file retained; availability is not a compatibility guarantee. |
| [hold_mode](../scripts/hold_mode.md) | Fully enable ircII-like hold mode behavior. | Script file retained; availability is not a compatibility guarantee. |
| [ison](../scripts/ison.md) | Low-level interface to the ison queue. | Script file retained; availability is not a compatibility guarantee. |
| [less](../scripts/less.md) | Peruse a file page by page. | Script file retained; availability is not a compatibility guarantee. |
| [less-pipe](../scripts/less-pipe.md) | Peruse an [exec](../disambiguation/exec.md) process output page by page. | Script file retained; availability is not a compatibility guarantee. |
| [loadformats](../scripts/loadformats.md) | A theming facility. | Script file retained; availability is not a compatibility guarantee. |
| [local.examp](../scripts/local.examp.md) | An example of the local customization file. | Script file retained; availability is not a compatibility guarantee. |
| [mail](../scripts/mail.md) | Check multiple mboxes at once. | Script file retained; availability is not a compatibility guarantee. |
| [man](../disambiguation/scripts_man.md) | A front end to the old help command. | Script file retained; availability is not a compatibility guarantee. |
| [map](../scripts/map.md) | A client-side implementation of the [map](../scripts/map.md) command. | Script file retained; availability is not a compatibility guarantee. |
| [meta-compat](../scripts/meta-compat.md) | Backwards compatibility for scripts that bound META keybindings. | Script file retained; availability is not a compatibility guarantee. |
| [motd_script](../scripts/motd.md) | A client side message-of-the-day script. | Script file retained; availability is not a compatibility guarantee. |
| [netsplit](../scripts/netsplit.md) | Handle netsplits and netjoins without flooding your screen. | Script file retained; availability is not a compatibility guarantee. |
| [newaway](../scripts/newaway.md) | Only see other people's away messages once. | Script file retained; availability is not a compatibility guarantee. |
| [newnick](../scripts/newnick.md) | Automatically generate new nicknames when server requires you to change your nickname. | Script file retained; availability is not a compatibility guarantee. |
| [nickcomp](../scripts/nickcomp.md) | Automatic nickname completion for channel text. | Script file retained; availability is not a compatibility guarantee. |
| [nopaste](../scripts/nopaste.md) | Prohibit accidental paste flooding. | Script file retained; availability is not a compatibility guarantee. |
| [notify](../scripts/notify.md) | A scripted implementation of [notify](../scripts/notify.md) | Script file retained; availability is not a compatibility guarantee. |
| [paste](../scripts/paste.md) | Allow a special paste mode for paste flooding. | Script file retained; availability is not a compatibility guarantee. |
| [pipe](../scripts/pipe.md) | Implement the function [pipe](../disambiguation/pipe.md) that returns the output from an [exec](../disambiguation/exec.md) command. | Script file retained; availability is not a compatibility guarantee. |
| [reconnect](../scripts/reconnect.md) | Automatically reconnect to servers when disconnected. | Script file retained; availability is not a compatibility guarantee. |
| [renumber](../scripts/renumber.md) | Automatically renumber your window refnums so there are no gaps. | Script file retained; availability is not a compatibility guarantee. |
| [revdcc](../disambiguation/scripts_revdcc.md) | Reverse dcc for those behind a firewall. | Retained script; references removed DCC facilities. |
| [save](../scripts/save.md) | Save your settings to a file you can [load](../commands/load.md) later. | Script file retained; availability is not a compatibility guarantee. |
| [scan](../scripts/scan.md) | Summarize the current channel. | Script file retained; availability is not a compatibility guarantee. |
| [screen](../scripts/screen.md) | Switch between windows like gnu screen does. | Script file retained; availability is not a compatibility guarantee. |
| [setmail](../disambiguation/scripts_setmail.md) | Support multiple status formats for mail. | Script file retained; availability is not a compatibility guarantee. |
| [slowcat](../scripts/slowcat.md) | Dump a file to current target, very slowly. | Script file retained; availability is not a compatibility guarantee. |
| [snip](../scripts/snip.md) | Remove substrings from the start or end of a string. | Script file retained; availability is not a compatibility guarantee. |
| [sping](../scripts/sping.md) | Ping a server. | Script file retained; availability is not a compatibility guarantee. |
| [suppressmotd](../scripts/suppressmotd.md) | Don't be bothered with the server's motd. | Script file retained; availability is not a compatibility guarantee. |
| `tabkey.ce` | Crazyeddy's and Fudd's tabkey script. | Retained script; references removed DCC facilities. |
| [tabkey.jm](../scripts/tabkey.jm.md) | Jm's tabkey script. | Retained script; references removed DCC facilities. |
| [utime](../scripts/utime.md) | Functions for manipulating [utime](../scripts/utime.md)s. | Script file retained; availability is not a compatibility guarantee. |


## Special Topics

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [about_epic](about_epic.md) | Information about the EPIC project and software. | Documentation topic; not a command registration. |
| [arglist](arglist.md) | How alias argument lists look and work. | Documentation topic; not a command registration. |
| [arrays](arrays.md) | How Karll arrays work. | Documentation topic; not a command registration. |
| [atomic_scope](atomic_scope.md) | Atomic scope and local variables. | Documentation topic; not a command registration. |
| [block](block.md) | The basis of ircII syntax is the block. | Documentation topic; not a command registration. |
| [command_line](command_line.md) | What options can I pass at the command line? | Documentation topic; not a command registration. |
| [commands_overview](commands_overview.md) | What are all the commands? | Documentation topic; not a command registration. |
| [compile_opts](compile_opts.md) | How can I tell what options were set at compiled time? | Documentation topic; not a command registration. |
| [copyright](copyright.md) | What is the copyright and license for the client? | Documentation topic; not a command registration. |
| [debugging](debugging.md) | How do I debug my script? | Documentation topic; not a command registration. |
| [default_keybindings](default_keybindings.md) | Information about what [BIND](../commands/bind.md) -DEFAULT does | Documentation topic; not a command registration. |
| [disambiguation](disambiguation.md) | A template file we use for disambiguation pages. | Documentation topic; not a command registration. |
| [environment_variables](environment_variables.md) | What environment variables does EPIC honor? | Documentation topic; not a command registration. |
| [etiquette](etiquette.md) | How should I behave on irc? | Documentation topic; not a command registration. |
| [expando_substitution](expando_substitution.md) | How do $'s get expanded? | Documentation topic; not a command registration. |
| [expandos](expandos.md) | Even more about how $'s get expanded. | Documentation topic; not a command registration. |
| [expressions](expressions.md) | How math expressions work. | Documentation topic; not a command registration. |
| [flood_control](../disambiguation/special_flood_control.md) | How does flood control work? | Historical topic; corresponding built-in facility removed. |
| [functions_overview](functions_overview.md) | What are the built in functions? | Documentation topic; not a command registration. |
| [glossary](glossary.md) | This is a work in progress. | Documentation topic; not a command registration. |
| [graphics_chars](graphics_chars.md) | How to use alternate character set to echo box chars | Documentation topic; not a command registration. |
| [input_handling](input_handling.md) | How the client handles your keypresses. | Documentation topic; not a command registration. |
| [introduction](introduction.md) | A crash course on using irc. | Documentation topic; not a command registration. |
| [ipv6](ipv6.md) | Things you need to know about using ipv6. | Documentation topic; not a command registration. |
| [ircii_syntax](ircii_syntax.md) | How the ircII language works. | Documentation topic; not a command registration. |
| [ircuser](ircuser.md) | Why can't I just set my username to anything I want? | Documentation topic; not a command registration. |
| [karll_arrays](karll_arrays.md) | What karll arrays are and how they work. | Documentation topic; not a command registration. |
| [key_bindings](key_bindings.md) | How you can represent keybindings. | Documentation topic; not a command registration. |
| [levels](levels.md) | All about window/lastlog/ignore/flood levels. | Documentation topic; not a command registration. |
| [lval](lval.md) | About lvalues, things you can assign values to (variable names) | Documentation topic; not a command registration. |
| [mangle_types](mangle_types.md) | All about removing highlights in strings. | Documentation topic; not a command registration. |
| [math_operators](math_operators.md) | All operators and their priority and associativity | Documentation topic; not a command registration. |
| [memory_usage](memory_usage.md) | What causes high memory usage and what you can do about it. | Documentation topic; not a command registration. |
| [new_user](new_user.md) | A crash course to the absolute beginner to irc. | Documentation topic; not a command registration. |
| [output-help](output-help.md) | How the client sends output to your emulator. | Documentation topic; not a command registration. |
| [pass-through_command](pass-through_command.md) | Commands that are set to the server directly. | Documentation topic; not a command registration. |
| [patterns](patterns.md) | How wildcard pattern matching works. | Documentation topic; not a command registration. |
| [pf_loader](pf_loader.md) | All about using the pf (pre-formatted) loader. | Documentation topic; not a command registration. |
| [programming](programming.md) | A crash course in ircII programming. | Documentation topic; not a command registration. |
| [quoting_hell](quoting_hell.md) | What is quoting hell and how do I make it work for me? | Documentation topic; not a command registration. |
| [random_numbers](random_numbers.md) | How the client generates random numbers. | Documentation topic; not a command registration. |
| [regex](regex.md) | How regular expressions work in EPIC. | Documentation topic; not a command registration. |
| [referring_to_servers](referring_to_servers.md) | How am I allowed to refer to servers? | Documentation topic; not a command registration. |
| [rules_about_number_arguments](rules_about_number_arguments.md) | How numbers passed to built-in functions are handled. | Documentation topic; not a command registration. |
| [security](security.md) | Thinking about security in your script. | Documentation topic; not a command registration. |
| [serial_numbers](serial_numbers.md) | How serial numbers work for on hooks. | Documentation topic; not a command registration. |
| [server_description](server_description.md) | What constitutes a server description? | Documentation topic; not a command registration. |
| [server_list](server_list.md) | How to build your own server list file. | Documentation topic; not a command registration. |
| [server_numerics](server_numerics.md) | How the client handles server numerics. | Documentation topic; not a command registration. |
| [server_state](server_state.md) | All about the various server status states. | Documentation topic; not a command registration. |
| [signals](signals.md) | Signals that the client handles and what they do. | Documentation topic; not a command registration. |
| [space_character](space_character.md) | What constitutes a space character? | Documentation topic; not a command registration. |
| [special_vars](special_vars.md) | Info about $ expandos, and a list of the inline expandos. | Documentation topic; not a command registration. |
| [standard_loader](standard_loader.md) | All about using the std (standard) loader. | Documentation topic; not a command registration. |
| [start](help_root.md) | This comprehensive index. | Documentation topic; not a command registration. |
| [status_line](status_line.md) | All you need to know about the status bar. | Documentation topic; not a command registration. |
| [text_hilight](text_hilight.md) | Bold, and Underline, and Color, and Reverse, oh my! | Documentation topic; not a command registration. |
| [using_perl](../disambiguation/special_using_perl.md) | What you need to know to use perl. | Historical topic; corresponding built-in facility removed. |
| [using_symbolctl](using_symbolctl.md) | Create your own sets, command completion, other neat stuff | Documentation topic; not a command registration. |
| [welcome](welcome.md) | Welcome to EPIC! | Documentation topic; not a command registration. |
| [what_is_a_word](what_is_a_word.md) | When something is a "word", what does that mean? | Documentation topic; not a command registration. |
| [window_activity_handling](window_activity_handling.md) | How hidden windows decide when to tell you they have output. | Documentation topic; not a command registration. |
| [window_targets](window_targets.md) | What is a "target" and how do windows use them? | Documentation topic; not a command registration. |


## Disambiguation

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [alias](../disambiguation/alias.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [away](../disambiguation/away.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [channel](../disambiguation/channel.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [connect](../disambiguation/connect.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [exec](../disambiguation/exec.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [fe](../disambiguation/fe.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [info](../disambiguation/info.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [lastlog](../disambiguation/lastlog.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [list](../disambiguation/list.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [local](../disambiguation/local.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [log](../disambiguation/log.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [map](../disambiguation/map.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [motd](../disambiguation/motd.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [notify](../disambiguation/notify.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [perl](../disambiguation/perl.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [pipe](../disambiguation/pipe.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [pop](../disambiguation/pop.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [prefix](../disambiguation/prefix.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [push](../disambiguation/push.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [reconnect](../disambiguation/reconnect.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [repeat](../disambiguation/repeat.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [ruby](../disambiguation/ruby.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [shift](../disambiguation/shift.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [stat](../disambiguation/stat.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [tcl](../disambiguation/tcl.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [time](../disambiguation/time.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [unshift](../disambiguation/unshift.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [userhost](../disambiguation/userhost.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [utime](../disambiguation/utime.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [version](../disambiguation/version.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [which](../disambiguation/which.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [window](../disambiguation/window.md) | No description in original index. | Name/namespace guide; follow the specific reference. |
| [xdebug](../disambiguation/xdebug.md) | No description in original index. | Name/namespace guide; follow the specific reference. |


## Historical EPIC4-to-EPIC5 compatibility lists


### Help files exist for non-existent scripts

The original category title is historical. Current registration and retained-script evidence are shown separately; some names now exist in another namespace.

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| [alias_script](../disambiguation/alias.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `altchan.bj` | No description in original index. | No bundled script file under this name; historical reference. |
| `autokick` | No description in original index. | No bundled script file under this name; historical reference. |
| `autoop` | No description in original index. | No bundled script file under this name; historical reference. |
| [away_script](../disambiguation/away.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `basical` | No description in original index. | No bundled script file under this name; historical reference. |
| `columns` | No description in original index. | No bundled script file under this name; historical reference. |
| `dcc_spacefix` | No description in original index. | Removed DCC interface; no current built-in command or transport. |
| `deban` | No description in original index. | No bundled script file under this name; historical reference. |
| `dns` | No description in original index. | No bundled script file under this name; historical reference. |
| `efnext` | No description in original index. | No bundled script file under this name; historical reference. |
| `events.hop` | No description in original index. | No bundled script file under this name; historical reference. |
| `events` | No description in original index. | No bundled script file under this name; historical reference. |
| `fake-dcc` | No description in original index. | No bundled script file under this name; historical reference. |
| [fe_script](../disambiguation/fe.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `genalias` | No description in original index. | No bundled script file under this name; historical reference. |
| `hybrid6` | No description in original index. | No bundled script file under this name; historical reference. |
| `imap` | No description in original index. | No bundled script file under this name; historical reference. |
| `ip-to-int` | No description in original index. | No bundled script file under this name; historical reference. |
| `keybinds` | No description in original index. | No bundled script file under this name; historical reference. |
| `killpath` | No description in original index. | No bundled script file under this name; historical reference. |
| `kpstat` | No description in original index. | No bundled script file under this name; historical reference. |
| `langtrans` | No description in original index. | No bundled script file under this name; historical reference. |
| [list_script](../disambiguation/list.md) | No description in original index. | No bundled script file under this name; historical reference. |
| [local_script](../disambiguation/local.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `mkpdir` | No description in original index. | No bundled script file under this name; historical reference. |
| `more` | No description in original index. | No bundled script file under this name; historical reference. |
| `mudirc` | No description in original index. | No bundled script file under this name; historical reference. |
| `netsplit.env` | No description in original index. | No bundled script file under this name; historical reference. |
| `newformat` | No description in original index. | No bundled script file under this name; historical reference. |
| `old-dcc` | No description in original index. | No bundled script file under this name; historical reference. |
| [prefix_script](../disambiguation/prefix.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `recursion` | No description in original index. | No bundled script file under this name; historical reference. |
| [repeat_script](../disambiguation/repeat.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `scandir` | No description in original index. | No bundled script file under this name; historical reference. |
| `sdiff` | No description in original index. | No bundled script file under this name; historical reference. |
| `silent` | No description in original index. | No bundled script file under this name; historical reference. |
| `sound` | No description in original index. | No bundled script file under this name; historical reference. |
| `starutils` | No description in original index. | No bundled script file under this name; historical reference. |
| [stat_script](../disambiguation/stat.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `status_lag` | No description in original index. | No bundled script file under this name; historical reference. |
| `tabkey.th` | No description in original index. | No bundled script file under this name; historical reference. |
| [time_script](../disambiguation/time.md) | No description in original index. | No bundled script file under this name; historical reference. |
| `tls` | No description in original index. | No bundled script file under this name; historical reference. |
| `vi-binds` | No description in original index. | No bundled script file under this name; historical reference. |
| `webster` | No description in original index. | No bundled script file under this name; historical reference. |
| `window_script` | No description in original index. | No C window operation registered under this name. |


### Help files exist for non-existent commands

The original category title is historical. Current registration and retained-script evidence are shown separately; some names now exist in another namespace.

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| `bind_command_completion` | Command completion handled by tabkey scripts | No C binding action registered; scripts may provide an action. |
| `bind_backward_history` | Implemented by [history script](../scripts/history.md). | No C binding action registered; scripts may provide an action. |
| `bind_forward_history` | Implemented by [history script](../scripts/history.md). | No C binding action registered; scripts may provide an action. |
| `bind_shove_to_history` | Implemented by [history script](../scripts/history.md). | No C binding action registered; scripts may provide an action. |
| `bye` | Use [quit](../commands/quit.md) instead. | No C command; script alias in `script/builtins`. |
| `set_alt_charset` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| `set_auto_new_nick` | Use the [newnick](../scripts/newnick.md) script instead. | No C setting; script definition in `script/newnick`. |
| `set_auto_reconnect` | Implemented by [reconnect script](../scripts/reconnect.md). | No C setting; script definition in `script/reconnect`, `script/reconnect.old`. |
| `set_auto_reconnect_delay` | Implemented by [reconnect script](../scripts/reconnect.md). | No C setting; script definition in `script/reconnect`, `script/reconnect.old`. |
| `set_auto_rejoin` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_auto_rejoin_connect` | Handled differently by [reconnect script](../scripts/reconnect.md). | No C setting registered; historical/unsupported built-in name. |
| `set_auto_rejoin_delay` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_auto_unmark_away` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_auto_whowas` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_bad_style` | This was never implemented. | No C setting registered; historical/unsupported built-in name. |
| `set_beep_max` | The client only ever beeps once anyways. | No C setting registered; historical/unsupported built-in name. |
| `set_beep_on_msg` | The [builtins](../scripts/builtins.md) script implements this. | No C setting; script definition in `script/builtins`. |
| `set_beep_when_away` | The [builtins](../scripts/builtins.md) script implements this. | No C setting registered; historical/unsupported built-in name. |
| `set_blink_video` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| `set_bold_video` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| `set_color` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting; script definition in `script/set_color`. |
| `set_command_mode` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_connect_timeout` | Obsoleted by nonblocking connects. | No C setting registered; historical/unsupported built-in name. |
| `set_dcc_timeout` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_display_ansi` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| `set_display_pc_characters` | The client no longer supports non-8-bit terminals. | No C setting registered; historical/unsupported built-in name. |
| `set_eight_bit_characters` | The client no longer supports non-8-bit terminals. | No C setting registered; historical/unsupported built-in name. |
| `set_full_status_line` | Implemented by [builtins](../scripts/builtins.md). | No C setting; script definition in `script/builtins`. |
| `set_help_pager` | Implemented by [help](../disambiguation/scripts_help.md) script. | No C setting registered; historical/unsupported built-in name. |
| `set_help_path` | Implemented by [help](../disambiguation/scripts_help.md) script. | No C setting registered; historical/unsupported built-in name. |
| `set_help_prompt` | Implemented by [help](../disambiguation/scripts_help.md) script. | No C setting registered; historical/unsupported built-in name. |
| `set_help_window` | Implemented by [help](../disambiguation/scripts_help.md) script. | No C setting registered; historical/unsupported built-in name. |
| `set_highlight_char` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| [set_history](../scripts/history.md) | Implemented by the [history](../scripts/history.md) script. | No C setting; script definition in `script/history`. |
| `set_history_circleq` | Implemented by the [history](../scripts/history.md) script. | No C setting; script definition in `script/history`. |
| `set_input_aliases` | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_inverse_video` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| `set_max_reconnects` | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_meta_states` | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_nd_space_max` | There was never a point to this. | No C setting registered; historical/unsupported built-in name. |
| `set_notify_handler` | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_random_local_ports` | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_reverse_status_line` | Implemented by [builtins](../scripts/builtins.md). | No C setting registered; historical/unsupported built-in name. |
| [set_scroll](../window/scroll.md) | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| [set_security](security.md) | An obsolete setting. | No C setting registered; historical/unsupported built-in name. |
| `set_ssl_certfile` | This was never implemented. | No C setting registered; historical/unsupported built-in name. |
| `set_ssl_keyfile` | This was never implemented. | No C setting registered; historical/unsupported built-in name. |
| `set_ssl_path` | This was never implemented. | No C setting registered; historical/unsupported built-in name. |
| `set_status_truncate_rhs` | This feature never really worked. | No C setting registered; historical/unsupported built-in name. |
| `set_suppress_server_motd` | Implemented by the [suppressmotd](../scripts/suppressmotd.md) script. | No C setting; script definition in `script/suppressmotd`. |
| `set_switch_channel_on_part` | Channel stacking makes this a moot feature. | No C setting registered; historical/unsupported built-in name. |
| `set_tab` | There was never a point to this. | No C setting registered; historical/unsupported built-in name. |
| `set_tab_max` | There was never a point to this. | No C setting registered; historical/unsupported built-in name. |
| `set_underline_video` | Use [set mangle_display](../set/mangle_display.md) instead. | No C setting registered; historical/unsupported built-in name. |
| [window_bind](../commands/bind.md) | Auto-rejoin feature no longer built-in | No C window operation registered under this name. |
| `window_unbind` | Auto-rejoin feature no longer built-in | No C window operation registered under this name. |
| [window_scratch](../window/scratch.md) | An obsolete operation.  All windows are scratch windows. | C window operation registered. |
| [window_scroll](../window/scroll.md) | An obsolete operation.  All windows are scrollable. | C window operation registered. |


### Help files exist for non-existent functions

The original category title is historical. Current registration and retained-script evidence are shown separately; some names now exist in another namespace.

| Reference | Historical description / migration note | Current source status |
| --- | --- | --- |
| `fabs` | Use [abs](../functions/abs.md) instead. | No C function registered under this name; historical or script-defined. |
| `getppid` | This was a mistake; the function is [pid](../functions/pid.md). | No C function registered under this name; historical or script-defined. |
| `igmask` | Use [ignorectl](../disambiguation/ignorectl.md) instead. | No C function; script alias in `script/builtins`. |
| `igtype` | Use [ignorectl](../disambiguation/ignorectl.md) instead. | No C function; script alias in `script/builtins`. |
| `lastserver` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `rigmask` | Use [ignorectl](../disambiguation/ignorectl.md) instead. | No C function; script alias in `script/builtins`. |
| `rigtype` | Use [ignorectl](../disambiguation/ignorectl.md) instead. | No C function; script alias in `script/builtins`. |
| `servergroup` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `servername` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `servernick` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `servernum` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `serverourname` | Use [serverctl](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `servertype` | Use [serverct](../functions/serverctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winbound` | Auto-rejoin feature no longer built-in | No C function; script alias in `script/builtins`. |
| `wincursorline` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winlevel` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winline` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winnam` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winnicklist` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winnum` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winquery` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winrefs` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winscreen` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winscrollbacksize` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winserv` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winsize` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winstatsize` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |
| `winvisible` | Use [windowctl](../functions/windowctl.md) instead. | No C function; script alias in `script/builtins`. |

The end!

## Validation and source references

All 1,111 original table entries are retained. Status checks cover 138 named C command entries, 308 C function entries, 150 built-in settings, 91 named hook types, 49 binding actions, and 97 window operations. This is a snapshot of the current source, not a full runtime test of every operation.

- [source/commands.c](../../source/commands.c): `irc_command` registration.
- [source/functions.c](../../source/functions.c): `built_in_functions` registration and disabled raw-connection stubs.
- [source/vars.c](../../source/vars.c): `init_variables_stage1()` registration, rather than retained identifiers alone.
- [source/hook.c](../../source/hook.c): `hook_function_templates`.
- [source/keys.c](../../source/keys.c): `init_binds()` and the special NOTHING selector.
- [source/window.c](../../source/window.c): `windowcmd_options`.
- [script](../../script): retained files and explicit alias/addset definitions; generated definitions and dependencies require the individual script reference.
- [KNOWNBUGS](../../KNOWNBUGS) and [UPDATES](../../UPDATES): retired facilities and migration context.
