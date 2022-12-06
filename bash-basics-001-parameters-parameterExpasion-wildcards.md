# bash Basics for Bioinformatics

## Terminal, Shell, bash ?
The **terminal** is the GUI window that you see on the screen. It takes commands and shows output.

The **shell** is the software that interprets and executes the various commands that we type in the terminal.

**Bash** is a particular shell for UNIX and a command language.  It stands for Bourne Again Shell. Some other examples of shell are sh(bourne shell), csh(c shell), tcsh(turbo c shell) etc.

CLI (Command Line Interface) refers to a user interface in computers where users type in commands and see the results printed on the screen.

## Repeat the Most Basic commands 
You should know these basic linux commands. There are lots of tutorials that cover this
- `cd`   
- `mkdir`   
- `cp` and `mv`
- `ls`
- `cat`
- `less`
- `wget`

most commands accept additional parameters, that can be viewed with `man` (for example `man ls`)
## Wildcards
Use wildcards to grep multiple files with a certain pattern.

List all text files with ending `.txt`<br>
- `ls *.txt`

List all forward Read files:
- `ls *R1*` 


## Parameters

One core functionality of bash ist the management of parameters. A parameter stores values, like a name, filepath or number. Calling the variable gives access to this stored value. 
- parameters referenced by a name are called variables 
- parameters referenced by a number are called positional parameters and reflect the arguments given to a shell


### Define a parameter
using an `=` sign
```
parameter=/path/to/my/file
```
###  Call the parameter with a `$` sign

```
$parameter
${parameter} #can be followed by other characters
```
### print to screen
```
echo $parameter
```

Careful with this difference:

```
myvar="This is a full sentence stored a variable"
myvar=This is a full sentence stored a variable
```
String with whitespace need to be in qotes

**Where could you find this?** 

See Qiime2 tutorial: import with manifest file <br>
https://docs.qiime2.org/2022.8/tutorials/importing/ 


<details>
<summary>TASK: get an example dataset from Qiime2 tutorials</summary>

First get this small example dataset to play around with
```
# dowload folder with sequences
wget  "https://data.qiime2.org/2022.8/tutorials/importing/pe-64.zip"
# download manifest file
wget \
  -O "pe-64-manifest" \
  "https://data.qiime2.org/2022.8/tutorials/importing/pe-64-manifest"
# unpack 
unzip -q pe-64.zip
  ```

Print manifest file to screen: where is the parameter?
<details>
<summary>Solution</summary>
```cat pe-64-manifest```

--> the $PWD 
</details>

Print only sample ids to screen (use cut ...)

<details>
<summary>Solution</summary>
```cut -f1 pe-64-manifest```
Prints only the first column of tab delimited file
</details>

</details>

### Parameter expansions 
With parameter expansion you can manipulate the parameter value. You can cut parts from the end or beginning of a file name, which is useful for renaming, sorting, or pipeing input and output of commands

#### Parameter expansions overview

(see this nice cheatsheet --> https://devhints.io/bash)
```
str="/path/to/foo.cpp"
echo "${str%.cpp}"    # /path/to/foo
echo "${str%.cpp}.o"  # /path/to/foo.o
echo "${str%/*}"      # /path/to

echo "${str##*.}"     # cpp (extension)
echo "${str##*/}"     # foo.cpp (basepath)

echo "${str#*/}"      # path/to/foo.cpp
echo "${str##*/}"     # foo.cpp

echo "${str/foo/bar}" # /path/to/bar.cpp
```
Little side note: `#` sign is used for comments, anything that starts with a `#` is not read as command

####  Example: renaming files with parameter expansions
If you are using the small sample dataset from the Qiime2 tutorial agains, inside the pe-64 folder you have the floowing files

- s1-phred64-r2.fastq.gz
- s1-phred64-r1.fastq.gz
- s2-phred64-r2.fastq.gz
- s2-phred64-r1.fastq.gz

r1 and r2 refers to the forward and reverse reads, of sample s1 and s2.


```
# define on sample name as variable
myvar=s1-phred64-r1.fastq.gz
# check variable
echo $myvar
# unpack all fasta files
gzip -d *.fastq.gz
```
Use parameter expansions
```
echo ${myvar%%.fastq}
```
<details>
<summary>TASK: Use parameter exansion to rename the file stred as $myvar from `fastq` to the shorter format `fq`</summary>

Remember: renaming is done with `mv old-file newfile`

If files are in zipped format `.fastq.gz`, rename to `fq.gz`

<details>
<summary>Solution</summary>

```
mv $myvar ${myvar%%.fastq.gz}.fq.gz
```

Hint: 
This becomes powerful when combined with loops

```
for file in *.fastq.gz;  mv $file ${file%%.fastq.gz}.fq.gz; done
```

</details>
</details>




## Pipe `|`

Pipeing the output of one bash command directly to the next bash command.

Example: for a given fastq file (use `s1-phred64-r1.fastq` , get the ids of sequences that start with "TACGNAG" (can be for example a primer etc)

```
cat s1-phred64-r1.fastq | grep -B1 "^TACGNAG" | grep @
``` 

<details>
<summary>Details for that command</summary>

try the commands in order to see what happens

First read file:
```
cat s1-phred64-r1.fastq
```
now search for patterns with grep 
```
cat s1-phred64-r1.fastq | grep "^TACGNAG"   # the ^ sign means: only search for string that have this pattern at the beginning
```
but this only print the line with the pattren, to get the id, add the line above
```
cat s1-phred64-r1.fastq | grep -B1 "^TACGNAG" 
```
now keep only the id (starts with `@` sign )

```
cat s1-phred64-r1.fastq | grep -B1 "^TACGNAG" | grep @
```
</details>

