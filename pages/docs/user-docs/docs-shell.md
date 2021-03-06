---
title: Singularity Shell
sidebar: user_docs
permalink: docs-shell
folder: docs
---

## Usage
The `shell` Singularity sub-command will automatically spawn a shell within a container. The default and the only requirement of any Singularity container is that /bin/sh must be present, and thus /bin/sh is also used as the default shell.

The usage is as follows:

```bash
$ singularity shell
USAGE: singularity (options) shell [container image] (options)
```

Additionally any arguments passed to the Singularity command (after the container name) will be passed to the called shell within the container.

## Options
The shell sub-command allows you to set or change the default shell which is used by using the `--shell` argument. As of Singularity version 2.2, you can also use the environment variable SINGULARITY_SHELL which will use that as your shell entry point into the container.

### Notes about dot files
Some shells (e.g. /bin/bash) will automatically try to execute the user's dot files and system login scripts. This carries with it several caveats that must be noted:
- Singularity automatically tries to set the prompt (PS1 environment variable) to something related to the container, but many cases the dot files override this and reset the prompt.
- Custom commands inside a user's dot file maybe parsed and executed when the container is called (a common example of this is environment modules) and that may lead to errors or unexpected behaviors when spawning a shell inside a container.
- For these reasons (and if you experience issues like this) you may want to consider also telling the shell inside the container not to parse dot files and login scripts. Read the documentation on your desired shell (for Bash check into `--norc` and `--noprofile`).

## Examples

```bash
$ singularity shell container.img 
Singularity/container.img> echo $SHELL
/bin/sh
Singularity/container.img> exit
$ 
```


```bash
$ echo world > hello
$ singularity shell /tmp/Centos7-ompi.img 
Singularity: Invoking an interactive shell within container...

Singularity.Centos7-ompi.img> pwd
/home/gmk/demo
Singularity.Centos7-ompi.img> ls
hello
Singularity.Centos7-ompi.img> cat hello 
world
Singularity.Centos7-ompi.img> exit
```
You can see from the above example, we were able to have access to our current working directory. That is because one of the default bind paths that is included and enabled by default in Singularity is the binding of the user's home directory. In this example, you can see that we created a file called `hello` in the current directory and after we entered the Singularity container we landed in the same directory and thus `hello` is accessible to us here as we would expect.


## Help

```bash
$ singularity shell container.img --help
GNU bash, version 4.3.30(1)-release-(x86_64-pc-linux-gnu)
Usage:  /bin/sh [GNU long option] [option] ...
        /bin/sh [GNU long option] [option] script-file ...
GNU long options:
        --debug
        --debugger
        --dump-po-strings
        --dump-strings
        --help
        --init-file
        --login
        --noediting
        --noprofile
        --norc
        --posix
        --rcfile
        --restricted
        --verbose
        --version
Shell options:
        -ilrsD or -c command or -O shopt_option         (invocation only)
        -abefhkmnptuvxBCHP or -o option
Type `/bin/sh -c "help set"' for more information about shell options.
Type `/bin/sh -c help' for more information about shell built in commands.
Use the `bashbug' command to report bugs.
$ 
```

And thus we should be able to do:

```bash
$ singularity shell container.img -c "echo hello world"
hello world
$ 
```

