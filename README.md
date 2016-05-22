9x
==

An attempt to bring [Plan 9-style namespaces](http://www.cs.bell-labs.com/sys/doc/names.html) to unix as a FUSE file system.

Key features of Plan 9 namespaces:

- A per-process view of the namespace.
- Selective inheritance and sharing via process groups.
- Access to namespace manipulation by non-priviliged users.

basic features
--------------

- A FUSE filesystem that can be mounted by unpriviliged users on Linux and OS X.

- A [mount](http://man.cat-v.org/plan_9/1/bind) command that mounts 9p servers in the namespace.

- A [bind](http://man.cat-v.org/plan_9/1/bind) command that maps one name to another.

- A command to launch child processes and control if they inherit the parent's namespace, and if so, whether their modificiations will be seen by the parent.

wishlist features
-----------------

- directory unions with before, after, and bsd-style recursive merge
- mount fuse filesystems in addition to 9p servers
- mount standard kernel-provided filesystems as well
- language bindings for mount, bind, and rfork in C, rust, python, etc, that allow easily integration into programs
- integration with a window manager or tmux to facilitate namespace sharing in the manner of `rio`
- facilities for exporting namespace over a network, along with an auth mechanism
- an `ls` command that's able to deal with infinite namespaces by paging results
- arbitrary byte-sequence names including `/` and the null byte
- some way of distingushing between a namespace containing the named items `0`, `1`, `2`, and a numbered list (for example, a hypothetical json namespace would have a hard time representing a list vs an object indexed with '0', '1', '2')

Many of these are probably more trouble than they're worth.

non-goals
---------

- Compatiblity with Plan 9 or Plan 9 from User Space. The goal is to bring The ideas to unix in such a way that they seamlessly interoperate with unix, not to try to build a full Plan 9 user space or system.

open questions
--------------

- What is the best machanism for controlling the FUSE filesystem? Some ideas:
  . a unix domain socket in a known location
  . ioctl calls on the mount point
  . extended attributes on the mount point
  . unix signals
  . named pipes
  . system V message queue

- What language should we implement the FUSE filesystem in? It is a C API, so C is an obvious choice. Rust is a possibility, since it's performant and interoperates well with C.

- In Plan 9, as in Unix, you must create a directory to serve as the mountpoint for a `mount` command. This seems like poor ergonomics, but perhaps there is some reason we should reproduce this behavior.

- Child processes in Plan 9 can share a parent's namespace, or acquire a copy of it. If they get a copy, then they will not see subsequent changes to the namespace by the parent. Should they? This seems like an easy feature to add, but I'm not sure whether it's useful or not.

- What should we call it? `9x` was chosen because it's short and implies some combination of Plan 9 and unix. `dyn` might be a good name, as it implies a dynamic nature. Also, it suggests `/dyn` as the mount point, which is quite unixy sounding.

9p server ideas
---------------

- `env`: Plan 9 style environment variables
- `me`: information about the calling process
  - `me/pid`: process id
  - `me/tid`: process thread id
  - `me/ns`: process namespace as a list of mount and bind commands
- `xml`: Read and write XML documents
  - `/tag` node tag
  - `/attr/ATTR_NAME` - atter value
  - `/children` - sub nodes
  - `/text` - read the serialized document
- `soup`: Like `xml`, but allow for processing tag soup, a la Beautiful Soup.
- `json`: Like `xml`, but for json
- `cons`: a console server
- `win`: a window server
- `blit`: a graphics server
- `gpu`: gpu accelerated whatever. probably no sane interface is possible.
- `srv`: service registry
- `etc`: configuration namespace. Get and set structured data.
- `cache`: Caches another namespace, so subsequent reads get the cached data. Could do cache timers or just cache forever
- `ssh`: SSH as a mountable namespace
- `ftp`: FTP as a mountable namespace
- `git`: Serve a git repo as a namespace. Would serve files copy on write out of a bare repo, so checkouts don't waste space. Stats would be instant, even for huge repos, since the server would know all modifications to files.
- `tmp`: RAM backed namespace that goes away when unmounted.
- `sql`: Somehow abstract over an SQL database. Coming up with a sensible interface will be challenging.
- `text`: An editor-agnostic backend that serves buffers backed by files and provides for fast editing commands, for use with an front end that provides a nice user interface.
- `record`: A record oriented filesystem as a throwback to the bad old days
- `debug`: Some kind of view on a running process, so that it can be easily manipluated by the shell or by other programs. Read the value of variables, set break points, etc.
- `rest`: Serve a REST json API
- `oeis`: Serve The On-Line Encyclopedia of Integer Sequences
- `vim`: Serve R/W vim buffers.
- `mut`: Serve a mutable version of another namespace, where all changes are temporary.
- `retry`: Serve a non-flakey version of a flakey namespace via automatic retries.
- `ro`: Serve a read-only copy of a namespace
- `btc`: Serve the blockchain and all transactions
- `elf`: Serve elf binaries, sections, symbols, disassembly
- `sexp`: S expressions
- `c`: Serve C build artifacts. Would take a path to a project containing, for example, C files, and then serve the built artifacts on demand
