# Bandit Steps

This guide, written by Kendra Winhall, describes how to find the password for levels 1 through 12 of OverTheWire's Bandit wargame.

## Level 0 -> Level 1

Steps:

1. Type `ls` to list the contents of the current directory. Observe that there is a file named `readme`.
2. Type `cat readme` to view the contents of `readme`.
3. Note that the password is `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`.

Lesson: This level explores how to list the contents of the current directory using `ls` and read the contents of a specified file using `cat`.

## Level 1 -> Level 2

Steps:

1. Type `ls` to list the contents of the current directory. Observe that there is a file named `-`. Since `-` is sometimes used to represent standard input/output, the approach we used last time won't work. 
2. Instead, use the relative path. To specify that you want to view the contents of file `-` in the current directory (represented by `.`), type `cat ./-`.
3. Note that the password is `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`.

Lesson: This level explores how to use relative file paths when a file name is a symbol/word that has a special meaning for Unix commands.

## Level 2 -> Level 3

Steps:

1. Type `ls` to list the contents of the current directory. Observe that there is a file named `spaces in this filename`. Typing `cat spaces in this filename` won't work because of the spaces.
2. Instead, enclose the filename in single quotes --type `cat 'spaces in this filename'`.
3. Note that the password is `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`.

Lesson: This level explores how to deal with a file name that has spaces by enclosing it in single quotes.

## Level 3 -> Level 4

Steps:

1. Type `ls` to list the contents of the current directory. Observe that it contains a subdirectory named `inhere`.
2. Type `cd inhere` to change your current directory to `inhere`.
3. Type `ls` to list the contents of the current directory. Observe that no file names are listed. The directory might be empty--or maybe there are hidden files.
4. Type `ls -a` to see all files in the directory. Observe that there is a file named `...Hiding-From-You`. 
5. Type `cat ...Hiding-From-You` to view the contents of `...Hiding-From-You`.
6. Note that the password is `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`.

Lesson: This level explores how to change directories using `cd` and view hidden files using `ls -a`.

## Level 4 -> Level 5

Steps:

1. Type `ls` to list the contents of the current directory. Observe that it contains a subdirectory named `inhere`.
2. Type `cd inhere` to change your current directory to `inhere`.
3. Type `ls` to list the contents of the current directory. Observe that it contains 10 files, labeled `-file00, -file01, -file02 ... -file09`.
4. Type `cat ./-file00` to view the contents of `-file00`. Observe that it is not human-readable.
5. To check the type of all the files in the directory, type `file ./*`. Observe that all files are of type `data` except for `-file07`, which is labeled as `ASCII text`. This is the human-readable file!
6. Type `cat ./-file07` to view the contents of `-file07`.
7. Note that the password is `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`.

Lesson: This level explores how to check file types using the `file` command and how to use the wildcard character `*`.

## Level 5 -> Level 6

Steps:

1. Type `ls` to list the contents of the current directory. Observe that it contains a subdirectory named `inhere`.
2. Type `cd inhere` to change your current directory to `inhere`.
3. Type `ls` to list the contents of the current directory. Observe that there are many subdirectories.
4. Read the instructions for this level. Observe that the file must be human-readable, 1033 bytes in size, and not executable. Here is how to build the command to find it:
    1. To find all the files in this directory and its subdirectories, type `find .`
    2. To find files that are 1033 bytes and not executable, type `find . -size 1033c ! -executable`.
    3. To find files that are 1033 bytes, not executable, and human-readable, use a similar approach to the previous level--the `file` command. Use `exec` to execute the `file` command as follows: `find . -size 1033c ! -executable -exec file {} +` (using `+` is more efficient than `\;`). This command prints the file types of all 1033 byte non executable files. Use `|` (the pipe character) to pass the output of the previous command into the input of `grep` (which searches for specific text patterns). Recall from the previous level that human-readable files are of type `ASCII text`, so this is the desired text pattern. Then, the command is `find . -size 1033c ! -executable -exec file {} + | grep 'ASCII text'`.
5. After typing `find . -size 1033c ! -executable -exec file {} + | grep 'ASCII text'`, observe that `./maybehere07/.file2` meets all the requirements.
6. Type `cat ./maybehere07/.file2` to view the contents of `./maybehere07/.file2`.
7. Note that the password is `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`.

Lesson: This level explores how to use the `find` command to find files that meet specific requirements, how to use the pipe character `|` to pass the output of one command into the input of another, and how to use `grep` to search for text patterns.

## Level 6 -> Level 7

Steps:

1. Read the instructions, which state that the password is stored somewhere on the server, is owned by user `bandit7` and group `bandit6` and is 33 bytes. 
2. Type `cd /` to change your current directory to the root directory.
3. Type `find -user bandit7 -group bandit6 -size 33c` to find all files that meet the requirements. Note that there are many error messages from `stderr`, making it difficult to identify the file that contains the password.
4. To suppress the error messages, discard all output from `stderr`. Specifically, use the symbol `>` to redirect all output from `stderr` (file descriptor `2`) to `/dev/null` (the null device, which discards all data written to it). Then the command is `find . 2>/dev/null -user bandit7 -group bandit6 -size 33c`.
5. After typing `find . 2>/dev/null -user bandit7 -group bandit6 -size 33c` observe that `./var/lib/dpkg/info/bandit7.password` meets all the requirements.
6. Type `cat ./var/lib/dpkg/info/bandit7.password` to view the contents of `./var/lib/dpkg/info/bandit7.password`.
7. Note that the password is `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`.

Lesson: This level explores how to reference standard streams using their file descriptors, how to use the `>` character to redirect output, and how to discard data using `/dev/null`.

## Level 7 -> Level 8

Steps:

1. Read the instructions, which state that the password is in `data.txt` next to the word `millionth`. 
2. Use `grep` to search for `millionth` in `data.txt` by typing `grep millionth data.txt`.
3. Note that the password is `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`.

Lesson: This level explores how to use `grep` to search for a specific string in a given file.

## Level 8 -> Level 9

Steps:

1. Read the instructions, which state that the password is the only line that appears in `data.txt` just once.
2. To find the unique line in `data.txt`, use `uniq -u`, which prints the unique lines in a file. However, this command only detects repeated lines if they are adjacent. Therefore, sort `data.txt` first with the command `sort data.txt` and then pipe the output to  `uniq -u` by typing `sort data.txt | uniq -u`.
3. Note that the password is `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`.

Lesson: This level explores how to use `sort`, the pipe character `|`, and `uniq` to find unique lines in a given file.

## Level 9 -> Level 10

Steps:

1. Read the instructions, which state that the password is one of the only human-readable strings in `data.txt`, which follows several `=` characters. 
2. To find human-readable strings in `data.txt`, type `strings data.txt`. To find human-readable strings following several `=` characters, pipe the output of the previous command into `grep ==` (which finds text patterns containing `==`). To do this, type `strings data.txt | grep ==`.
3. Although this command prints several strings, only one is a 32-character SSH password. Note that the password is `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`.

Lesson: This level explores how to use `string` to find human-readable strings in a given file. Furthermore, this level combines this new information with concepts from earlier levels (pipes and `grep`) to find a specific string.

## Level 10 -> Level 11

Steps:

1. Read the instructions, which state that the password is encoded in base64 in `data.txt`. Indeed, if you type `cat data.txt`, you can see that the contents are `VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==`. That definitely isn't the password!
2. Luckily, there is a command called `base64` that is built to encode and decode this data. To decode the contents of `data.txt`, type `base64 --decode data.txt`.
3. Note that the password is `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`.

Lesson: This level explores how to use `base64` to encode and decode base64 data.

## Level 11 -> Level 12

Steps:

1. Read the instructions, which state that the password is in `data.txt`--except all letters have been rotated by 13 positions. This means that A and a are converted to N and n and so on until M and m are converted to Z and z. Then, loop back to the beginning of the alphabet, so that N and n are converted to A and a and so on until Z and z are converted to M and m.
2. To translate the contents of the file, type `cat data.txt`. 
3. Then, pipe the output of this command into the `tr` command. Given an input stream and two arrays of characters, `tr` translates each input character by finding its location in the first array and replacing it with the character in the same relative position in the second array. In this case, the first array consists of the letters in alphabetical order (in both uppercase and lowercase): `[A-Z]` and `[a-z]`. As described above, starting by converting A and a to N and n, move through the alphabet until M and m are converted to Z and z. Then, loop back to the beginning of the alphabet, so that N and n are converted to A and a and so on until Z and z are converted to M and m. Since the first array starts with A (which is converted to N) and ends with Z (which is converted to M), the second array also starts with N and ends with M. Specifically, it starts with N and continues to the end of the alphabet. Then, it loops back to the beginning of the alphabet and continues until M. This is the same for lowercase letters. Therefore, the second array consists of N-Z and A-M as well as n-z and a-m: `[N-ZA-M]` and `[n-za-m]`. Then the `tr` command is `tr '[A-Z][a-z]' '[N-ZA-M][n-za-m]'`. Combine this with the previous command by typing `cat data.txt | tr '[A-Z][a-z]' '[N-ZA-M][n-za-m]'`.
4. Note that the password is `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`.

Lesson: This level explores how to use `tr` to encode and decode simple substitution ciphers.