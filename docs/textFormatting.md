# Text processing

### [Sreenu Vattipally](https://www.gla.ac.uk/schools/infectionimmunity/staff/sreenuvattipally/)
* MRC-University of Glasgow Centre for Virus Research
* University of Glasgow, G61 1QH
* E-mail: Sreenu.Vattipally@glasgow.ac.uk


## grep

The grep (global regular expression print) command in Unix/Linux allows you to search for and match patterns in a file or multiple files. It can find specific strings, numbers, or regular expressions within the file(s).

### Searching for a specific string:
```
grep "string" file_name
```


This will search for the specified `string` in the file `file_name` and print the matching lines to the terminal.

### Searching for a number within a file:
```
grep -e '[0-9]' file_name
```

This will search for any numbers within the file "file_name" and print the matching lines to the terminal.

### Searching for multiple strings:
```
grep -e 'string1' -e 'string2' file_name
```

This will search for the specified strings, string1 and string2, in the file file_name and print lines that match any of the lines. 


### Searching for NOT matching line:
```
grep -v  string file_name
```

This will print all the lines that do NOT match the `string`.


### Case insensitive match:
Unix/Linux commands are case-sensitive. If you want to run case insensitive match

```
grep -i String file_name
```


### Print the file name:
```
grep -l string file_name
```

This will print the `file_name` only if the file contains the `string`. 

### Match the word:
In the above examples, grep will print the matching line if the pattern is a separate word or a part of another word. If you want to match only words
```
grep -w string file_name
```


### Print only matching word (not the whole line):
```
grep -o string file_name
```

This will print the `string` if it has a match in the file


### Count the number of matches:
```
grep -c string file_name
```
This will output the number of matches in the `file_name`

### Print the lines before and after the match:

```
grep -A 1 string file_name
grep -B 1 string file_name
grep -C 1 string file_name
```

Here `-A` will print the lines after the match. The number following the `-A` option will tell the number of lines to print. Similarly, `-B` will print lines before and `-C` will print before and after

---

## sort

The sort command in Linux is a utility program that takes one or more files as input and sorts their contents based on various criteria such as lines, words, or integers

examples of how you can use the sort command:

### Sort lines of text:

```
sort file_name
```

This will read the contents of the `file_name` and print them sorted in alphabetical order. This is the default sort type.

### Sort integers in a file:

```
$ sort -n file_name
```

This will sort the contents of `file_name` based on integers, with larger integers appearing first. The `-n` option tells sort to use numerical sorting.

  

### Sort lines in reverse order:

```
sort -r file_name
```

This will sort the contents of `file_name` in reverse order, with the last line appearing first.
  

### Sort files based on a specific column:

```
$ sort -k 2 file_name
```

This will sort the contents of `file_name` based on the second column (assuming that each line has three columns separated by spaces or a tab). The `-k` option tells sort to use a specific column for sorting. Option `-t` can be used to specify a column separator.

```
sort -t ":" -k 3 file_name
``` 

This will sort column 3, where columns are separated by `:`


### Removing duplicates and printing unique lines
```
sort -u file_name
```


---

## cut

The `cut` command in Linux extracts specific regions, columns or fields from text files.

### Cut based on characters:

```
cut -c 10-20 file_name
```

This will print characters 10 to 20 (inclusive) from each line of the `file_name` contents.

```
cut -c 10- file_name
```

Outputs the characters from 10 to the end of every line

```
cut -c -10 file_name
```

Outputs from beginning to character 10 of every line

### Cut based on a delimiter:
```
cut -d ',' -f2 file_name
```

This will print the second field from a comma-separated variable (CSV) file. The delimiter can be any character. 

---

## paste

The `paste` command in Linux allows you to combine the contents of two files or standard input and output streams into a single file or stream. It is often used for tasks such as 

combining output from multiple commands, merging files, and formatting data. Here are some examples of how to use the `paste` command:

### Merging files

```
paste file1 file2 > merged_file
```

This will merge contents from `file1` and `file2` side by side.

### Merging lines

```
paste - - < file_name
```

This will merge two consecutive lines into a single line (number of `-` indicate number of lines to merge). Each field is separated by a tab in a merger line. 

---
## tr

The `tr` command in Linux is used to translate or delete certain characters in a file or stream. It takes two or more arguments, where the first argument specifies the characters to be translated and the second argument specifies the replacement characters. Here are some examples of how you can use the `tr` command:

### To translate uppercase letters to lowercase letters:

```
tr [A-Z] [a-z] < filen_ame
```

This command will replace all uppercase letters in the file `file_name` with their corresponding lowercase letters.

### To delete all spaces in a file:

```
tr -d ' ' < file_name
```

This command will delete all spaces in the file `file_name`.


### To delete a newline character from a file (combine all the lines):

```
tr -d '\n' < file_name
```

This converts file contents into a single line `file_name`.

### To replace all tabs with spaces:

```
tr '\t' ' ' < file_name
```
This command will replace all tabs in the file `file_name` with spaces.


### To replace a set of characters:
```
tr '[ABCD]' '[WXYZ]' < file_name
```

This will replace all the `A`s with `W`s and `B`s with `X`s... so and so forth. 

---
## sed

The `sed` command, short for Stream Editor, is a powerful text processing tool in Linux that allows you to modify text by searching and replacing patterns within a file or stream of text.

### Search and replace a word in a file:

```
sed -i 's/old/new/g' file_name
```

This will search for all occurrences of `old` in the file `file_name` and replace them with `new`. The `-i` option tells `sed` to edit the file in place, so the changes will be made directly to the file.


### Extract line(s) from a file:

```
sed -n '4p' file_name
```

This will print the fourth line of the `file_name`. The `-n` option tells `sed` not to print anything by default, so we need to specify the line number followed by the `p` command to print the line.

```
sed -n '4,6p' file_name
```

Lines 4 to 6 will be printed.

### Deleting line(s) from a file:

```
sed -i '4,6d' file_name
```

This will delete lines 4, 5 and 6 from `file_name`


---

## uniq

The uniq command in Linux removes/counts duplicate lines from a file or output of another command. THIS COMMAND WORK ONLY ON A SORTED INPUT FILE.

### Print only unique lines
```
sort file_name |uniq -u
```

Will output all the unique lines

### Print only non-unique lines
```
sort file_name |uniq -d
```
This will print all non-unique lines

### Count the number of line repetitions
```
sort file_name |uniq -c 
```

This will print each line and how many times it is repeated.

---

## rev

This command will reverse the lines (right to left) in a file

## tac

This command will reverse the file from bottom to top

---


## Exercises
Copy data for exercise

```
cp -r ~bvv2t/exFiles . 
cd exFiles
```

1. Create a reverse complementary genome sequence of `Ebola.fa`
2. Print the genome composition (number of different nucleotides) and length of the above genome
3. Convert from fastq to fasta sequence from `SRR10968345_1.fastq.gz`
4. Convert from fastq to fasta sequence only if they have EcoRI (GAATTC) site in the above reads
5. In the file `Ebola.fa`,  there is an ORF sequence from position 67 to 1875. Print only ORF sequence from this file


