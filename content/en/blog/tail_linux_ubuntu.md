+++
title = 'Linux command Tail: useful for monitoring log text files'
date = 2024-09-27T08:00:00+02:00
draft = true
lang = 'en'
+++

When monitoring any program logs in Ubuntu or in many Linux based operating systems one can use the tail command. The tail command outputs the last lines of a text file.

Enter the command in the shell program.

``` Shell
tail /<path>/logfile.log
```

By default, the tail command will show the last 10 lines. If you want to specify the number of lines, enter the command with the flag "-n", in the following way:

``` Shell
tail -n 15 /<path>/logfile.log
```
In this example, the argument for the parameter/flag -n is 15, thus tail output the last 15 lines of the log file (or any text file).

Let's say we want to monitor a log file in real time. We can observe the log lines as they are being added to the log file with the "-f" flag, in this way:

``` Shell
tail -f /<path>/logfile.log
```

For knowing more about the command tail, enter "tail --help" or "man tail" in the shell console; or visit the online manual in: https://www.man7.org/linux/man-pages/man1/tail.1.html.


# Conclusion

The command "tail" is useful for checking text in Linux and for monitoring log files. The bad news is that we cannot monitor all log files with tail, though. Since the inception of systemd, many log files are now binaries, which tail cannot read. For that kind of logs, we need to use journalctl. Soon, I will write about that one.

Iubilate!
