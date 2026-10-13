# Custom Shell

A small Unix-style shell written in C to explore how command interpreters parse input, launch programs, and handle process signals.

The shell displays a `SillyShell>` prompt, dispatches built-in commands, and executes external programs using `fork()` and `execvp()`.

## Features

- Whitespace-based command and argument parsing.
- External command lookup through `PATH`.
- Parent/child process management with `waitpid()`.
- Exit-status and signal reporting.
- Built-in directory navigation, environment display, and prompt customization.
- Basic pause/resume handling for a tracked child process.

## Build and run

Use a POSIX environment such as Linux or WSL with GCC.

```bash
git clone https://github.com/stevenrashid/Custom_Shell.git
cd Custom_Shell
gcc -Wall -Wextra MY_SHELL.c -o my_shell
./my_shell
```

## Built-in commands

| Command | Behavior |
| --- | --- |
| `pwd` | Prints the current working directory. |
| `cd path` | Changes directory to the supplied path. |
| `printenv` | Prints environment variables. |
| `newprompt name` | Changes the prompt; omitting the name restores `SillyShell`. |
| `cont` | Sends SIGCONT to the tracked child. Use only after pausing a child. |
| `exit`, `quit`, `done` | Exits the shell. |

Example session:

```text
SillyShell> pwd
SillyShell> ls -l
SillyShell> newprompt demo
demo> cd /tmp
demo> exit
```

## Signal handling

The shell ignores Ctrl+C for itself while an executed child restores the default SIGINT behavior. Ctrl+Z sends SIGSTOP to the tracked child; `waitpid(..., WUNTRACED)` reports the stop, and `cont` resumes that child.

This is basic single-child handling rather than complete shell job control. The `cont` implementation does not validate its stored PID or wait again after resuming a process.

## Scope and limits

The parser splits on whitespace and does not implement quoted arguments, pipes, redirection, wildcard expansion, or background-job syntax. Input uses fixed-size buffers: keep commands short, use fewer than 64 argument tokens, and keep custom prompts under 15 characters.

The implementation is in [MY_SHELL.c](MY_SHELL.c). It is an educational systems-programming project, not a replacement for a full shell.
