---
title: Bash grouping commands
tags: bash
datetime: 2020-06-07T22:32:11Z
---
Something I was struggling when creating the static site generator in bash was:

1. How I can temporary change directory execute some tasks and back to the previous working directory.
2. How I had no idea how to append different output together to form a file content

Regarding the directory change I remember using `pushd` and `popd`, but I had no luck and no way to figure it out why it wasn't working as I expected.

For some luck change, I didn't know what to look for, but found this page about [Grouping Commands](https://www.gnu.org/software/bash/manual/html_node/Command-Grouping.html), which are exactly what I was looking for, and solve both of my problems.

There are two ways to group commands; one it's using parenthesis or curly braces. Using the parenthesis creates a subshell, allowing us to change directory inside it, but on outside the shell the working directory is maintained.

    pwd
    (
        cd /path/to/directory
        pwd
    )
    pwd

    # Output
    /home/user
    /path/to/directory
    /home/user

Using curly braces helps me put everything between them and whatever was the output I can redirect them to the file.

I went from something like the code below:

    echo "<styles>" >> "$tmp"
    echo "$styles" >> "tmp"
    echo "</style>" >> "$tmp"

To something like this:

    {
        echo "<styles>"
        echo "$styles"
        echo "</styles>"
    } > "$tmp"
