# raise-existing-vim-window

hacky as fuck shim around vim that asks vim to list all existing swap files,
matches them against its commandline arguments, and if there's running vim
processes for any argument, raises the containing terminal and removes them
from the arguments list. each remaining argument opens a `urxvt` with vim
editing that file in it.

usage:

  * `xvim`: just a new `urxvt` with vim
  * `xvim FILE`: activates vim for `FILE`, or else opens a new one.
  * `xvim DIR`: activates all vims with cwd `DIR` or a file in `DIR`
  * `xvim -r DIR`: same but also subdirectories of `DIR`
  * `xvim -g GLOB`:  activates all vims with a file matching `GLOB`/`**/GLOB`,
    ignoring our own cwd.

examples:

  * `xvim foo.rb`: opens/activates foo.rb in the current directory
  * `xvim -g foo.rb`: activates all vims editing a foo.rb anywhere
  * `xvim -g \*.rb`: all ruby files, anywhere
  * `xvim  \*.rb`: all ruby files, anywhere
  * `xvim -r /`: all your vims

note:

  * symlinks are confusing. given `ln -s ../a c/d`, vim editing `c/d/x` won't
    be found by `xvim -r c` because we only check for prefixes of the real
    path (`a/x`) not containing any symlinks instead of actually walking the
    directory tree.
  * given `mkdir foo` and no running vims, `xvim foo` will open vim pointing
    at the directory, `xvim foo/` will not open anything, so I have a way to
    check for running vims without ever wanting to edit a directory.
  * matching vim cwd by default might be a bit annoying, maybe that should
    be hidden behind another flag

we make at least the following questionable assumptions:

  * `vim` is in path
  * `wmctrl` is in path. and you're running x11 I guess.
  * vim processes helpfully have an environment variable named `WINDOWID`
    that we can just pass to `wmctrl`. I think `urxvt` sets this on my machine
    but I forgot where I read about that.
      * naturally this breaks if your vim is inside `screen` attached to a
        different terminal. following pty fds might pe fun.
  * the `vim -r` format is somewhat stable and also you didn't put newlines
    or other weird things into your filenames or hostnames.
