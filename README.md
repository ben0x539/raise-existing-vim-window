# raise-existing-vim-window

hacky as fuck shim around vim that asks vim to list all existing swap files,
matches them against its commandline arguments, and if there's running vim
processes for any argument, raises the containing terminal and removes them
from the arguments list.

the remaining arguments are passed on to vim, unless we removed all of them,
then we just exit.

we make at least the following questionable assumptions:

  * `vim` is in path
  * `wmctrl` is in path. and you're running x11 I guess.
  * vim processes helpfully have an environment variable named `WINDOWID`
    that we can just pass to `wmctrl`. I think urxvt sets this on my machine
    but I forgot where I read about that.
  * the `vim -r` format is somewhat stable and also you didn't put newlines
    or other weird things into your filenames or hostnames.
