---
title: 'General CLI Philosophy'
date: 2024-10-03T09:00:43-05:00
draft: false
weight: 1
---

These are principles and habits I have developed over many years of writing CLI tools in a variety of languages.  Examples of my personal methodologies per language are included here.

## What do I mean by CLI?

A CLI includes any script that is more than a disposable ad-hoc script as well as more formal tools intended to be general purpose or shared with others.

## Basic Principles I Follow

1. Revision control everything
2. Provide online documentation
3. Validate inputs/dependencies
4. Support human readable and machine readable output when possible
5. Log/output actionable error messages
6. Appropriately use options and arguments
7. Use proper return codes

Many of these will mean you have more lines of code dedicated to them than for the logic itself.  That may seem silly, but focusing on usability typically saves you work in the long run.  It also forces you to reevaluate what you are doing as you go.  That may reveal opportunities to make the tool more useful or ensure better logic.

### Revision Control

Hopefully this is obvious, but revision control all code and host it externally.

If it left locally or on another host, you will lose it.  You will likely want that code again one day for something to rip off.  If it's not worth having its own repository, just shove it in a blanket "scripts" repo.  That could be private on GitHub, kept for a dumping ground for your team's ad-hoc work, etc.

You can delete the file when it's superseded by a more formal tool in its own repository or a better version of the tool.  It's really common for me to move from a shell script to a proper programming language when the scope of the tool becomes awkward in shell.  Delete the file.  But by using version control, it still exists for when you want to rip off your prior work.

### Online Documentation

I mean this in the traditional sense of `-h/--help` output, `man` pages, etc.  Sticking a `README.md` file in a repo is nice, but being able to check basic usage is critical.

I *will not* remember what I did in 3 months.  I don't want to have to read the code just to see how to use it again.

This segues into the next item.

### Validate Inputs/Dependencies

Whether using a CLI framework or not, validate options and arguments whenever possible.  Like online documentation, this will save you from your future self.

If your tool has dependencies that are not part of the standard tooling, check for them and error out before doing anything.

### Human/Machine Readable Output

Make sure your default output is easily readable by human beings.  But also make sure you provide output that is easily machine readable so that you can redirect the output for further processing.  Following this typical Unix behavior opens up opportunities to do things you may not expect.

I typically check to see if STDOUT is a TTY.  If so, output human happy output.  If not, then provide a reasonable default machine readable output.  Usually that's just something `awk`able.  If I think it should be more structured, it may be JSON or provide options for different outputs like CSV, JSON, and YAML.

### Actionable logs

Make log messages show what should be done to resolve the error and not just state the internal problem whenever possible.

Dumping a stack trace is always a bug.

Ideally, write error messages to STDERR.  It's more "correct", but it also enables redirecting STDOUT without breaking further processing.

### Options and Arguments

Arguments are required input.  Options are... optional.  A missing argument is a usage error (`EINVAL`), a missing option should not necessarily result in an error.  The exception to that is if configuration may be provided through the command, an environment variable, or a configuration file, but is still required.  I typically use that as the order of precedence.

When inputs are incorrect/missing, exit appropriately, provide a message about what specifically is missing, and provide the usage (ie. `-h`/`--help`) text.

### Return Codes

Don't just exit 1 on all errors.  Use the appropriate error codes defined in [`errno.h`](https://android.googlesource.com/kernel/lk/+/dima/for-travis/include/errno.h).  That particular link is to the Android source, but more usefully defines all values in 1 file vs. the Linux kernel spreading them across multiple includes.

The example of `EINVAL` is 22.  I may define these as constants, use standard libraries, or just hardcode them out of laziness.  The big thing is that you want to be able to determine the specific error whenever possible in the context of something else running this CLI one day, even if you do not anticipate it now.  This is akin to a REST service using the appropriate HTTP response codes.

Fall back to exiting 1 only when there is no formal error code that applies.

When you are running another process, it fails, and that is a fatal error, just bubble the error status returned by it up (and provide an actionable log message).
