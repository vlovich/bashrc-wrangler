The purpose of this repository is to demonstrate a useful best-practice for managing
your bashrc environment.

This is composed of 3 pieces:
* bashrc - Symlink this into your `~/.bashrc`
* bash.d - Symlink this into `~/.bash.d`
* paths - Symlink this into `~/.paths`

This project is a culmination of more than 10 years experience maintaining my bash environment
in Silicon Valley jumping between many companies & regularly starting from scratch at the beginning
as I learned best-practices.

The purpose of this structure is to build a habit of decomposing your bashrc into small chunks
that offer the pieces of functionality that you need instead of 1 giant bashrc. This way you are
methodical about which parts of your bashrc setup depend on others or should be loaded before/after
others.

This bashrc project is also cross-platform working for bash running on Windows (under WSL), MacOS
(at the time either homebrew or builtin but now only homebrew since zsh has taken over), & Linux.

# Features

This project comes with some useful prebuilt functionality:

* Colorizes your `PS1` & various coreutils tools
* Prints the error code in red preceding your current working directory if the previous command failed.
* Version control support (git & mercurial). The current branch name is printed beside your directory and an attempt
is generally made to colorize it differently based on if you have a clean branch or local modifications.
* If your startup script takes >100ms a warning is printed.
* Helper utilities you can enable to help you find what's taking long
* Helper functionality to manage your `PATH`/`MANPATH` environment more easily

# How to clone this

This repository isn't likely to change often. I generally maintain my own personal version in dropbox and create symlinks
off that. You may want to do the same - in practice I've found myself putting things in my personal `~/.bash.d` that are
only relevant to me. This repository is an attempt to share some of the infrastructure pieces only. YMMV if you clone this directly
or you snapshot it into your dropbox. Please feel free to submit improvements that are likely to apply to many people.

# Features worth calling out

## Infinite reloadability
Generally the code is structured so that you can source your `~/.bashrc` or any component within `~/.bash.d` as many times as you want
and everything works correctly. Most notably the problem comes around management of appending variables. Which leads to...

## Paths

Building your `PATH` environment variable can be a brittle process, especially if you just append to `$PATH` (MANPATH suffers the
same problem albeit to a lesser extent due to usage differences).

That's why `003-paths` intelligently processes the contents of `~/.paths` and `~/.manpaths` to build up your environment variable. It does so
by caching your `PATH`/`MANPATH` on startup forever. Then it processes the directives in `~/.paths`/`~/.manpaths` in alphabetical order (hence the `###-<name>`
structure follows `~/.bash.d/`. This ensures that regardless of how many times you source you end up with the same `$PATH` environment
variable unless you modify the contents of `~/.paths/` - when you do modify only the incremental effective change is applied and
a diff is printed highlighting what has changed in your `PATH`. Additionally you may append the platform name (`-macos`, `-wsl`, `-linux`)
to the paths file so that you can have 1 set of configuration files to deploy across all platforms.

The format of a paths file is:
* 1 path per line
* May reference the `$HOME` environment variable (without braces) or use `~/` but no other variable/path expansion is currently supported
* May start the line with `^` to indicate it goes to the beginning of the `PATH` (default is end)
* `#!stop` is a special directive to stop process the rest of the current file
* Entries may be commented out by using `#` at the start of the line.

An extension point here would be to support any arbitary environment variable although I personally haven't encountered a need yet.
