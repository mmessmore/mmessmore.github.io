---
title: 'Shell Scripts/CLIs'
date: 2024-10-03T09:45:09-05:00
draft: false
---

## Some Principles

### Useless use of `cat`

The classic doc [Useless Use of Cat](https://porkmail.org/era/unix/award) is a classic resource.

### Use ShellCheck

[ShellCheck](https://www.shellcheck.net/) is an amazing static analysis tool that nags you about nearly everything that was taught to me by the elders of the internet.

### Don't fear `mktemp`

Proper use of `mktemp` is super useful, whether a temporary file or directory.  Just make sure that you handle cleanup on the very next line.

Don't invent your own tempfile creation like `mkdir .foo.$$`.  It's a scary race condition that there is no need for.  `mktemp` is there for you.  Just use it.

The following is very typical of my usage:

```bash
# Securely create a temp directory and capture the name
TDIR=$(mktemp -d "/tmp/${prog}.XXXXXX"
# this deletes the files on the pseudo-signal EXIT.
# I use outer single quotes and inner double.  The variable will be expanded 
# when it runs, and the double quotes handle accidental spaces.
trap 'rm -fr "$TDIR"' EXIT
```

## Use `getopts`

Inventing your own option handling is an exercise in pain.  Just use `getopts`.  You could use the similarly named `getopt`.  I don't.

## Pedantic Principles

### Don't get fancy

Support the least common denominator of shell features as possible.  Stick to Borne Shell (eg. `sh`) features when you can.  Use POSIX shell spec features next.  If I find myself having to wander into Bashisms, Zshisms, etc., it's time for me to use a "real" programming language.

Usually arrays can be avoided with structure or using temporary files.

### Don't use backticks

Seriously.  Stop it.  They've been deprecated for decades now and are so difficult to read.  Use `$()` instead.  You may violate the previous with it, but whatever.  If the shell doesn't support `$()`, it's stupid.

### Don't create unnecessary processes

If there's a common shell builtin, use it.

```bash
# POSIX string manipulation
prog="${0##*/}"
# unnecessary process
prog="$(basename "$0")"
```

It is almost never useful to pipe `grep` with `awk` or `sed`.

```bash
# boo
grep '^switch_[ab]' hosts.txt | awk '{print $2}' | tee ips.txt
# yay
awk '/^switch_[ab]{print $2}' | tee ips.txt
```

## Sample

This is an example of a simple script that demonstrates most of the above doing 87 lines of sanity checking before 1 line of "business logic."

```bash
{{% include "static/code/cli-example.sh" %}}
```
