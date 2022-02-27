# The Linux Command Line

[toc]

## Important Definitions

| command  | description                                                  |
| -------- | ------------------------------------------------------------ |
| Command  | An instruction typed in the terminal and submitted to the shell for interpretation. |
| Shell    | A program that interprets commands for meaning.              |
| Terminal | A graphical window where commands can be typed and submitted to the shell. |

## Command Structure

- Each command follows the same overarching structure:  

  **`commandName –options arguments`**

  - **commandName**

    - Must be a valid program on the Shell’s Path. To check this, you can use the `which` command:  

      `which commandName`

    - If a path is returned, then the commandName is valid.

  - **options**

    - You can specify options for each command to customise the commands behaviour. These can be either “short-form” options or “long-form” options.

      - Short-form Options

        Short-form options are where a letter defines an option. Each option is prepended by a dash “**`-`**“ :

        `commandName –a –b –c args`
        *To save typing, you could join together the options:*
        `commandName –abc args`
        *Both of these formats are equivalent.*

      - Long-form Options

        For some commands, there are long-form options defined to make options easier to identify. Long form options are usually prepended by a double dash “**`--`**“.

        Long-form options **cannot** be joined together like short-form options can.
        Whether they are defined or not depends on each specific command, so consult the command’s manual page for more information.
        If long form options are defined for options “a”, “b” and “c”, then:
        `commandName –a –b -c arguments`
        *is equivalent to*
        `commandName --alpha --beta --charlie arguments`

  - **Command Line Arguments**

    - Are a type of input that commands operate on. Some commands can take an unlimited amount of inputs, some take a specific amount, and some take none at all. Consult the manual page for the specific command for more information.
      `cal 12 2017`
      *Here the cal command has 2 command line arguments. The number 12 and the number 2017.*

  - **Arguments for Options**
    - Sometimes, command options can also take their own arguments (inputs).
      `cal –A 1 –B 1 12 2017`
      *Here the cal command has 2 options; A and B.*
      *The A option has its own argument (1).*
      *The B option has its own argument (1).*
      *And the cal command has 2 command line arguments (12 and 2017).*

## Command Input and Output

![command_input_output](images/command_input_output.png)

Standard Data Streams can be redirected and are identified using their stream number.
Redirection of the standard output of one command to the standard input of another
command is known as piping.

- **Redirecting Standard Output:**

  Standard output is stream number 1. There are 2 methods to redirect standard output.
  The long form, using the stream number:
  `commandName –options arguments 1> destination`
  *Or the short form, with no stream number:*
  `commandName –options arguments > destination`

- **Redirecting Standard Error:**
  Standard error is stream number 2.
  Here is how to redirect standard error
  `commandName –options arguments 2> destination`
  *Standard error can be redirected at the same time as standard output:*
  `commandName –options arguments 1> output_destination 2> error_destination`

- **Redirecting Standard Input:**
  Standard Input is stream number 0. There are 2 methods to redirect standard Input.
  The long form, using the stream number:
  `commandName –options arguments 0< input_source`
  *Or the short form, with no stream number:*
  `commandName –options arguments < input_source`

## Piping

> Piping is the connection of the standard output of one command to the standard input of
> another command.

Piping using the pipe character **`|`** (*SHIFT + BACKSLASH*) .
Here is how you would pipe together commandOne and commandTwo:
`commandOne –options arguments | commandTwo –options arguments`
*Notice how both commands can have their own options and command line arguments as*
*usual.* This piping can go on for as long as is required with as many commands as is required.

- **Taking “Snapshots” of pipeline data using the tee command :**

  > Redirecting during a pipeline breaks the pipeline.

  ![tee command](images/tee_command.png)

  *For example, this wouldn’t work:*
  `commandOne –options arguments > snapshot.txt | commandTwo –options arguments`
  *Because redirection is processed by the shell before piping is, snapshot.txt would be created,*
  *but this locks up the standard output stream and therefore no data can be passed through the*
  *pipeline to commandTwo.*
  However, the tee command allows us to take a “snapshot” of the data in the pipeline without
  breaking the pipeline.
  `commandOne –options arguments | tee snapshot.txt | commandTwo –options arguments`
  *Here, a snapshot of the data coming out of commandOne is saved in snapshot.txt, but the data*
  *is also successfully piped through to commandTwo.*

- **Piping to commands that only accept command line arguments by using xargs :**

  Piping connects the standard output of one command to the standard input of another
  command.
  But what if the second command doesn’t accept standard input? e.g. the echo command.
  The key is to transform the data coming in, into command line arguments.
  This is possible using the **`xargs`** command.
  *For example, this would not work:*
  `commandOne –options arguments | echo`
  *This would work:*
  `commandOne –options arguments | xargs echo`
  
  **Note:** Commands you use with xargs can still have their own arguments.

## Aliases

> Aliases allow you to save your pipelines and commands with easy to remember nicknames so
> that they can be used later much easier.

1. You define aliases in your `.bash_aliases` file in your home directory. If it does not exist, you
   need to create it spelled exactly as shown. Note that the preceding period (.) must be included
   and there should be no file extension (such as .txt, or .pdf).
2. Here is how you define an alias in .bash_aliases:
   `alias aliasName=”THING YOU WANT TO ALIAS”`
   Notice that there are no spaces between the equals sign (=) and the aliasName and the quotes
   (“). The quotes can be double quote (“) or single quotes (‘).

*Let’s take an example:*
`alias calmagic=”cal –A 1 –B 1 12 2017”`
With this alias defined in our .bash_aliases file, whenever we run the calmagic command it is as if we ran the cal –A 1 –B 1 12 2017 command.
calmagic is now said to be an alias of “cal –A 1 –B 1 12 2017”.
NB: Aliases may contain either one command or an entire pipeline!

- **Piping to an alias**  
  
  > If the first command in an alias accepts standard input, then the alias can be piped to; even if it is an entire pipeline!  
  
  *Our alias is currently:*
  `alias calmagic=”cal –A 1 –B 1 12 2017”`
  *cal is the first command in this alias, but cal doesn’t accept standard input.*
  *Therefore, this would not work:*
  `commandOne –options arguments | calmagic`
  *However, if we adjust our alias so that it can accept standard input.*
  `alias calmagic=”xargs cal –A 1 –B 1 12 2017”`
  *This will now work:*
  `commandOne –options arguments | calmagic`
  And yes, you can pipe out of an alias as well, if the alias produces standard output.
  `commandOne –options arguments | calmagic | commandTwo –options arguments`
  Think of aliases as building blocks that you can use in more sophisticated pipelines.

## Using the Manual

| command               | description                                                  |
| --------------------- | ------------------------------------------------------------ |
| man –k \<search term> | Search the manual for pages matching \<search term>.         |
| man 5 \<page name>    | Open the man page called \<page name> in section 5 of the manual. (replace \<page name> and 5 as required) |
| man \<page name>      | Open the man page called \<page name> in section 1 of the manual. |

Symbols and their meaning in the manuale:

| section          | meaning                                                      |
| ---------------- | ------------------------------------------------------------ |
| [THING]          | THING is optional.                                           |
| \<THING>         | THING is mandatory (required)                                |
| THING …          | THING can be repeated (limitless)                            |
| THING1 \| THING2 | Use THING1 **OR** THING2. Not Both.                          |
| *THING*          | [Notice the Italics] Replace THING with whatever is appropriate. |

## Important Short Cuts & Commands

- **Opening and Closing the Terminal :**

| description          | short cut     |
| -------------------- | ------------- |
| Opening The Terminal | CTRL + ALT +T |
| Closing The Terminal | CTRL + D      |

- **Command History :**

| command    | description                                                  |
| ---------- | ------------------------------------------------------------ |
| history    | Show commands previously entered (command history).          |
| !!         | Run the previous command.                                    |
| !50        | Run the command that is on line 50 of the output from the history command. (replace “50” as needed). |
| history -c | clear the history list by deleting all of the entries        |

- **Key Commands for Navigating the File System**

| command              | description                                                  |
| -------------------- | ------------------------------------------------------------ |
| pwd                  | Print on standard output the absolute path to the shell’s current working directory. |
| cd [\<new location>] | Change the shell’s current working directory to the optional \<new location>. If no location is provided, return to the user’s home directory. |
| ls \[\<location>]    | List out the contents of the optional \<location> directory. If no \<location> is provided, print out the contents of the shell’s current working directory. |

- **Key Shortcuts when Navigating File System**

| command | description                                 |
| ------- | ------------------------------------------- |
| ~       | The current user’s home directory           |
| .       | The current folder.                         |
| ..      | The parent directory of the current folder. |

- **Wildcards and Regular Expressions**

> **Regular expressions** are patterns that can be used to match text. In Linux, they are used to allow a user to make rather generic expressions about what files they want a command to operate on.
> Creating regular expressions to match filenames is known as **globbing**.  

The regular expression patterns can be made using **special building blocks** known as **wildcards**.
**Wildcards** are symbols with specific meanings to the shell.

| command   | description                                         |
| --------- | --------------------------------------------------- |
| *         | Matches anything, regardless of length.             |
| ?         | Matches anything, but for one place only.           |
| [options] | Matches any of the options inside for 1 place only. |

- **Creating Files and Directories**

| command            | description                                             |
| ------------------ | ------------------------------------------------------- |
| touch \<file>      | Creates an empty file. e.g. `touch ~/Desktop/file1.txt` |
| mkdir \<directory> | Creates an empty directory. e.g. `mkdir ~/newdir`       |

- **Deleting Files and Directories**

| command                  | description                                                  |
| ------------------------ | ------------------------------------------------------------ |
| rm \<file>               | Remove a file e.g. `rm ~/Desktop/file1.txt`                  |
| rm -r \<directory>       | Removes a directory. e.g. `rm -r ~/newdir`                   |
| rm –i                    | Removes in an interactive manner. This is a good safety measure. |
| rmdir \<empty directory> | Only remove empty directories e.g. `rmdir ~/emptydir`        |

- **Editing Files with the Nano Editor**

| command | description                                                  |
| ------- | ------------------------------------------------------------ |
| ^       | This is the **CTRL** key on your keyboard. For example, **^O** is **CTRL + O**. |
| M-      | This is the “meta” key on your keyboard. Depending on your keyboard layout this may be the **ALT**, **ESC**, **CMD** key. Try it out Assuming M- is the ALT key, then M-X is ALT + X |

- **The Locate Command**

> The **`locate`** command searches a **database** on your file system for the files that match the text (or regular expression) that you provide it as a command line argument. If results are found, the locate command will return the **absolute path** to all matching files.  

*For example:*
`locate *.txt`
will find all files with filenames ending in .txt that are registered in the database.
The locate command is fast, but because it relies on a database it can be error prone if the database isn’t kept up to date.
Below are some commands to update the database and some reassuring procedures in case one cannot access administrator privileges.

| command           | description                                                  |
| ----------------- | ------------------------------------------------------------ |
| Locate -S         | Print information about the database file.                   |
| sudo updatedb     | Update the database. As the updatedb command is an administrator command, the sudo command is used to run updatedb as the root user (the administrator) |
| Locate --existing | Check whether a result actually exists before returning it.  |
| locate –limit 5   | Limit the output to only show 5 results                      |

- **The Find Command**

> The **`find`** command can be used for more sophisticated search tasks than the locate command. This is made possible due to the many powerful options that the **`find`** command has. The first thing to note is that the **`find`** command will list both files and directories, below the point the file tree that it is told to start at.  

*For example:*
`find .`
Will list all files and directories below the current working directory (which is denoted by the .)
`find /`
Will list all files and directories below the base directory (/); thereby listing everything on the entire file system! By default, the find command will list everything on the file system below its starting point, to an infinite depth. The search depth can however be limited using the **`–maxdepth`** option.
*For example:*
`find / -maxdepth 4`
Will list everything on the file system below the base directory, provided that it is within 4 levels of the base directory.

| command       | description                                                  |
| ------------- | ------------------------------------------------------------ |
| -type         | Only list items of a certain type. –type f restricts the search to file and –type d restricts the search to directories. |
| -name “*.txt” | Search for items matching a certain name. This name may contain a regular expression and **should be enclosed in double quotes** as shown. In this example, the find command will return all items with names ending in .txt. |
| -iname        | Same as –name but uppercase and lowercase do not matter.     |
| -size         | Find files based on their size. e.g –size +100k finds files over 100 KiB in size –size -5M finds files less than 5MiB in size. Other units include G for GiB and c for bytes[^footnote]. |

[^footnote]: Note: 1 Kibibyte (KiB) = 1024 bytes. 1 Mebibyte (MiB) = 1024 KiB. 1 Gibibyte = 1024 MiB.

A supremely useful feature of the find command is the ability to execute another command on each of the results.
*For example:*
`find /etc –exec cp {} ~/Desktop \;`
will copy every item below the `/etc` folder on the file system to the `~/Desktop directory`.
Commands are executed on each item using the **`–exec`** option.
The argument to the **`–exec`** option is the command you want to execute on each item found by the **`find`** command.
Commands should be written as they would normally, with **`{}`** used as a placeholder for the results of the **`find`** command.
Be sure to terminate the **`–exec`** option using **`\;`** (a backslash then a semicolon).
The **`–ok`** option can also be used, to prompt the user for permission before each action. This can be tedious for a large number of files, but provides an extra layer of security of a small number of files; especially when doing destructive processes such as deletion.
*For example:*
`find /etc –ok cp {} ~/Desktop \;`

- **Viewing File Content**

> There exist commands to open files and print their contents to standard output. One such example is the **`cat`** command. Let’s say we have a file called `hello.txt` on the Desktop.  

*By performing:*
`cat ~/Desktop/hello.txt`
This will print out the contents of `hello.txt` to standard output where it can be viewed or piped to other commands if required.
One such command to pipe to would be the **`less`** command. The **`less`** command is known as a “pager” program and excels at allowing a user to page through large amounts of output in a more user-friendly manner than just using the terminal.
*An example may be:*
`cat ~/Desktop/hello.txt | less`
*Or more simply:*
`less ~/Desktop/hello.txt`
By pressing the **`q`** key, the less command can be terminated and control regained over the shell.

| command                   | description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| tac <path/to/file>        | Print a file’s contents to standard output, reversed vertically. |
| rev <path/to/file>        | Print a file’s content to standard output, reversed horizontally (along rows). |
| head –n 15 <path/to/file> | Read the first 15 lines from a file (10 by default if -n option not provided.) |
| tail –n 15 <path/to/file> | Read the bottom 15 lines from a file (10 by default if -n option not provided.) |

- **Sorting Data**  

> A useful ability when working with file data is to be able to sort it either alphabetically or numerically. This behaviour is handled using the sort command. By default, the **`sort`** command sorts smallest first. So if sorting alphabetically, it will by default sort from a – z. If sorting numerically, it will put the smallest numbers first, and largest last.  

| command | description                              |
| ------- | ---------------------------------------- |
| sort -r | Reverse the default sorting order.       |
| sort -n | Sort in a numerical manner.              |
| sort -u | Sort data an only return unique entries. |

It is also possible to sort tabular data using the **`sort`** command using one of the columns. This is possible by providing a **`KEYDEF`** as an argument to the **`–k`** option.
`sort –k <KEYDEF>`
**`KEYDEFS`** are made using a column number and then additional options can be added (without dashes).
*As an example:*
`sort –k 5nr`
The **`KEYDEF`** is **`5nr`**. This will sort using column 5 of the data, and sort numerically (**`-n option`**) but in reverse (**`-r option`**).

- **Searching File Contents**  

> The ability to search for and filter out what you want from a file or standard output makes working with the command line a much more efficient process. The command for this is called the **`grep`** command. The grep command will return all lines that match the particular piece of text (or regular
> expression) provided as a search term.  

*For example:*
`grep hello myfile.txt`
Will return all lines containing the word “hello” in myfile.txt
*and*
`ls /etc | grep *.conf`
will return all lines with anything ending in “.conf” in data piped from the **`ls`** command.

| command | description                                                  |
| ------- | ------------------------------------------------------------ |
| grep -i | Search in a case insensitive manner (upper case and lowercase don’t matter). |
| grep -v | Invert the search. i.e return all lines that DON’T contain a certain search term. |
| grep -c | Return the number of lines (count) that match a search term rather than the lines themselves. |

