---
title: 'Helpful Tools'
date: 2024-10-03T16:01:28-05:00
draft: false
---

These are helpful tools and usages I've wandered into.

## Unbuffer

This is included in the `expect` package.  It runs a command ensuring that the output is a TTY, even when piping or redirecting to a file.

The use case designed for is to ensure that output comes line-by-line vs buffered into blocks.

The other super useful feature is when you want colorized output to be piped to another tool e.g. `less`.
