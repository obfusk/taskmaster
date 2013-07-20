[]: {{{1

    File        : README.md
    Maintainer  : Felix C. Stegerman <flx@obfusk.net>
    Date        : 2013-07-20

    Copyright   : Copyright (C) 2013  Felix C. Stegerman
    Version     : 0.3.0.dev

[]: }}}1

## TODO

  * TEST!

## Description
[]: {{{1

  taskmaster - manage tasks using a Taskfile

  Taskmaster uses a `Taskfile` to specify tasks to be run.  A
  `.taskmaster` file can be used for additional configuration.  You
  can specify global or task-specific environments, working
  directories, log dirs (w/ log files for STDOUT and STDERR), etc.
  You can also set default concurrency here.  Tasks that do not have a
  log dir, will have their output shown by taskmaster (w/ info and
  colours).

  A task's command can be prefixed w/ `SIG*` to use that signal to
  kill it (instead of `SIGTERM`).  Some programs, like rackup, will
  not respond to `SIGTERM` properly when backgrounded, but will quit
  nicely when sent a `SIGINT`.

  When using a log dir, the process' STDOUT and STDERR will be
  redirected to `<logdir>/<task>.<n>-stdout.log` and
  `<logdir>/<task>.<n>-stderr.log`, respectively.

  See `*.sample` for configuration examples.

  You can override which files and shell taskmaster uses by setting
  `$TASKFILE`, `$TASKMASTERRC`, and `$TASKMASTER_SHELL`.

[]: }}}1

## Usage
[]: {{{1

    $ cd some/dir
    $ vim Taskfile .taskmaster    # see *.sample
    $ taskmaster                  # run, or:
    $ taskmaster foo=2 bar=1      # run and override concurrency

  Now wait for all tasks to finish, or kill taskmaster w/ SIGTERM or
  SIGINT (^C)

[]: }}}1

## Supervision
[]: {{{1

  In its default configuration, taskmaster starts some processes, then
  waits for them to end, or taskmaster to be killed.  It can also be
  used as an erlang-inspired supervisor, which will watch its children
  and possibly restart them when they die.

### Worker Types

  * `temporary` (the default) - the process' death is ignored.
  * `transient` - will restart the process if it returned non-zero.
  * `permanent` - will always restart the process when it dies.

### Restart Strategies

  * `one_for_one` (the default) - restarts the process that died.
  * `one_for_all` - restarts all processes when one dies.

  NB: `temporary` processes' death will always be ignored and not
  result in any restarts, even with the `one_for_all` restart
  strategy.

### Maximum Restarts

  To prevent runaway restarts, you can specify how many restarts
  (`maxR`) are allowed in a time interval (`maxT`).  When more
  restarts occur within this interval, taskmaster will exit.  The
  default is a `maxR` of 10 and a `maxT` of 60 (minutes).  Restarts
  are counted globally, not per task.

[]: }}}1

## CAVEATS: Killing, Backround Processes, and Signals
[]: {{{1

  The commands in the `Taskfile` are passed to `bash -c`.

  To prevent runaway backround processes, taskmaster will kill the
  `bash -c` process as well as its direct children.  Nonetheless, some
  care must still be taken.

  For example, `sleep 42 &` will not be killed by taskmaster because
  it's parent process (`bash -c`) is no longer alive; `sleep 42 &
  wait` will be killed as expected.  Processes w/o parents will also
  not play well w/ supervision.

  Also, `SIGINT sleep 37` will not be killed by the SIGINT, even
  though sleep responds to SIGINT when used in an interective shell.

[]: }}}1

## TODO

  * always send SIGTERM to `bash -c`?
  * make sure `kill || true` and `wait 2>/dev/null` are OK?
  * more efficient implementation of restarts buffer?
  * use fifo + SIGUSR2 to allow adding additional workers?
  * use ^^^ to scale up/down?
  * --color=always|auto|never?

## License
[]: {{{1

  GPLv2 [1].

[]: }}}1

## References
[]: {{{1

  [1] GNU General Public License, version 2
  --- http://www.opensource.org/licenses/GPL-2.0

[]: }}}1

[]: ! ( vim: set tw=70 sw=2 sts=2 et fdm=marker : )
