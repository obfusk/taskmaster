[]: {{{1

    File        : README.md
    Maintainer  : Felix C. Stegerman <flx@obfusk.net>
    Date        : 2013-03-15

    Copyright   : Copyright (C) 2013  Felix C. Stegerman
    Version     : 0.0.1

[]: }}}1

## Description
[]: {{{1

  taskmaster - manage tasks using a Taskfile

  Similar to {f,sh}oreman, taskmaster uses a Taskfile (or $TASKFILE)
  to specify tasks to be run.  Exports, concurrency and other
  configuration can be specified in a .taskmaster file (or
  $TASKMASTERRC).  You can specify a global or task-specific logdir,
  or let taskmaster log to stdout.  A task's command can be prefixed
  with SIG\* to use that signal to kill it.  You can also specify a
  global or task-specific working directory.

  See \*.sample for examples.

[]: }}}1

## Be careful with backround processes and signals
[]: {{{1

  The commands in the Taskfile are passed to `sh -c`.  To prevent most
  problems with backround processes, taskmaster will kill all
  subprocesses of `sh -c` as well as the process itself, but will
  leave any grandchildren alone.  Nonetheless, some care must be taken
  with commands that use shell features.

  `SIGINT sleep 37` will not be killed by the SIGINT, even though
  sleep normally responds to SIGINT.

  `sleep 42 &` will not die because it's parent process (`sh -c`) is
  dead; `sleep 42 & wait` does die.

[]: }}}1

## Usage
[]: {{{1

    $ cd .../app
    $ vim Taskfile .taskmaster
    $ taskmaster # [ foo=2 bar=1 ]

[]: }}}1

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
