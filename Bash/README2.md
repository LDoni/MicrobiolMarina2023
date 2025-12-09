# Bash

## Paths, files and directories
Open linux or mac terminal
```
pwd #print working directory
cd #change directory (relative or absolute path)
cd .. # bring you back one directory up
cd ~ #cd home
ls #print list of elements in directory
ls –lh # print sizes in human readable format
man list # (or any command, gives the manual for that command)
history #dispay last commands
ls *.fasta #list all fasta files
mkdir newfoldername# make new directory
```
## Edit files
` nano nomefile #create new empty file`

Ctrl+S to save

Ctrl+X to exit

Ctrl+W to search
```
cp filename pathwheretocopy #copy file somewhere using absolute or relative path of where to copy
mv filename pathwheretocopy #mv file somewhere using absolute or relative path of where to copy
mv filename new_filename #rename file
less filename #see file
cat filename #similar to less
head filename #see first 10 rows of the file
tail filename #see last 10 rows of the file
head –n5 filename #(or tail –n 5) see only first 5 (or last) 5 rows
wc filename #outputs the number of words, lines, and characters
wc –l filename #outputs the number of lines
rm file #remove file
rm * #remove every file in that directory
cp –r foldername #copy folder
mv foldername pathwheretomove #move folder
rm –r foldername #remove folder
```
> tip: be VERY careful with rm, once you removed something there is no way to undo it; remember bash is case sensitive, the file, folder or scritp "Data" is different from "data".
---
## Merge and sort files
```
cat file1 file2 file3 … #merge multiple files in 1  
cat file1 file2 file3 > newfilename #redirect output to new file
sort file #sort the file, careful to computational sorting of file
sort –h file #human numeric sort
sort -t file #specify field separator (e.g., -t",")
sort -k1,1 -k2,2n file #sort by first column adn then numerically by second column
sort -k1,1 -k2,2nr file #sort by first column adn then numerically by second column in reversed order
sort -k1,1V -k2,2n file #as before but human sorted
join -1 1 -2 1 sorted_file1 sorted_file2 #join two files according to first column of each file
join -1 1 -2 1 -a 1 sorted_file1 sorted_file2 #keep also non joined rows
paste file1 file2 #merge lines of files
```
## Grep
```
grep "word" file #print all rows that contains "word"
grep -w "word" file #print all rows that contains exactly the pattern "word"
grep -V "word" file #inverted match, print all rows that not contain the patter "word"
grep -i "word" file #ignore case distinctions, grep both "word" and "WORD"
grep -c "word" file #count how many rows contain the patter "word"
grep –A10 "word" file # print rows containing pattern "word" and the 10 rows after
grep –B10 "word" file # print rows containing pattern "word" and the 10 rows before
grep –C10 "word" file # print rows containing pattern "word" and the 10 rows after and before
grep "Locus10[12]" file #print Locus101 and Locus102 
greo -E "(Locus101|Locus102)" file #print Locus101 and Locus102 
```
> special characters: 
> * ^ starting with ; grep "^>" file #print lines starting with ">"
> * $ ending with ; grep ">$" file #print lines ending with ">"

> Regular Expressions: a sequence of characters that specifies a pattern
---
## Awk
```
awk '{print $1}' file #print first column
awk '{print $0}' file #print all columns
awk '{print $NF}' file #print last column
awk '{print $4"\t"$1}' file #change orders of column and use tab as field separator in the output
awk -F";" '{print $3,$4}' file #fiels separator is ";"
awk '$1==$2 {print}' file #if first column = second column, print all columns
awk '$1 ~ /chr2|chr3/ { print $0 "\t" $3 - $2 }' file #if first column contain "chr2" or "chr3", print all columns, and a column with the difference between $3 and $2
awk '$1 ~ /chr1/ && $3 - $2 > 10 '{print}' file #if both the first column  contain "chr1" AND $3-$2>0 , print all columns
awk '{if ($1~">") print $0; else print length}' "fasta_file #print length instead of sequence in fasta file
```
![awk](https://raw.githubusercontent.com/MariangelaIannello/didattica/main/images/awk.png)
---

## Sed
```
sed 's/Locus/Transcript/' file #for each line subtitute "Locus" with "Transcripts" at first occurrance
sed 's/Locus/Transcript/g' file #for each line subtitute "Locus" with "Transcripts" at first occurrance
sed -i 's/Locus/Transcript/g' file # overwrite input with the output
sed '/Locus/d' file #delete any row containing "Locus"
```

> $ echo "chr1:28427874-28425431" | \
sed -E 's/^(chr[^:]+):([0-9]+)-([0-9]+)/\1\t\2\t\3/'

> chr1 28427874 28425431

The first component of this
regular expression is ^\(chr[^:]+\):. This matches the text that begins at the start of
the line (the anchor ^ enforces this), and then captures everything between \( and \).
The pattern used for capturing begins with “chr” and matches one or more characters
that are not “:”, our delimiter. We match until the first “:” through a character class
defined by everything that’s not “:”, [^:]+.
The second and third components of this regular expression are the same: match and
capture more than one number. Finally, our replacement is these three captured
groups, interspersed with tabs, \t.

> $ echo "chr1:abRsZtjf-dhThdbUdj" | \
sed -E 's/^(chr[^:]+):([a-zA-Z]+)-([a-zA-Z]+)/\1\t\2\t\3/'

> chr1 abRsZtjf dhThdbUdj
## Concatenate commands and programs
### Pipe | 
| connects the standard output of one process to the standard input of another

```
grep "word" file1 | sed 's/ /\t/g' | program1 > file2 #sed use as input the output of grep; when using pipe the "original" input has to be specified only in the first command
```
## Variables
A variable in bash can be anything, a number, a character, a string of characters, a file, a folder.
Variables are in bash are indicated with $
```
var1="ciao"
echo $var1 #print ciao
echo "$var1" #print ciao
echo '$var1' #print var1
```
---

## Command substitution 
Command substitution runs a Unix command inline and returns the output as a string that can be used in another command.
```
echo "$(cat file.fasta)"
echo "There are $(grep -c '^>' input.fasta) entries in my FASTA file." # show the string "There are 416 entries in my FASTA file."
```
---
## For loop
```
for i in *.fasta; do echo $i; done
for i in *.fasta; do mv $i ${i::-5}”_for_trimming”; done
for i in *.fasta; do mv $i ${i:1:3}”.fasta” ; done
for i in *fasta; do sed ‘s/>Locus/>/’ > $i”_editname” ; done
for i in *fasta; do grep –c”>” $i ; done > counts
for i in *fasta; do program1 $i > “output_”$i; done
for i in */ ; do cd $i; cp *.fasta ../; cd ..; done
```
## Bash script (VEDIAMO)

Bash scripts are indicated with the .sh extention (python scripts with .py, perl scripts with .pl). 

Create bash script with vi:

```
#!/bin/bash
mkdir test
for i in *fasta; do mv $i test; done
cd test
for i in *fasta; do program1 $i > $i"_output"; done
for i in *output; do grep ">" $i; done > list_of_sequences
```
We need now to make the .sh file executable
```
chmod 777 namescript.sh
```
To execute the bash script
```
bash namescript.sh
```
## Bash script with variables

fake_script.sh
```
#!/bin/bash
#$1=fasta file

cp "$1" ../data
```
To execute it
```
bash fake_script.sh file1.fasta
```

This is the script get_sequences_from_list_of_loci.sh
```
#!/bin/bash
#retrieve sequences from a list of loci 
#$1=list of loci to grep in a fasta file
#$2=fasta file


file="$1" ; name=$(cat $file) ; for locus in $name ; do grep -w -A1 $locus "$2" ; done > "$1".fasta
```
Execute the script
```
bash get_sequences_from_list_of_loci.sh list_file M_musculus.fasta
```
## Conda
```
conda init bash # initialize conda
conda env list # see list of environments
conda activate "environment_name" # activate environment_name
conda list # see packages installed in that environment_name
conda deactivate # close environment 
```
Some usefull conda commands:

```
conda create --name <ENV_NAME> #Create a target conda environment
conda activate <ENV_NAME> #Activate a target environment
conda deactivate #Deactivate your current environment
conda info --envs #print a list of conda environments
conda list #print a lost of packages installed in your current environment
conda install -c <CONDA_CHANNEL> <PACKAGE_NAME> #install a conda package
```

A good practice is to create AND install necessary packages at the same moment :

```
conda create --name <ENV_NAME> -c <CONDA_CHANNEL> <PACKAGE_NAME>
```

Conda is a package manager based on python. Each conda environment can only have **ONE** specific version of python installed. Now the default version is the latest 3.X but some old software can be run only in python 2.7. To create a python 2.7 environment :

```
conda create --name <ENV_NAME> python=2.7
```
### Good practices in bioinformatics :

  1. **Work in a robust and reproducible way**
  2. **Document each step**
  3. **Check everything between computational steps, errors can be silent**
  4. **Code should be readable and organized in a logical way**
  5. **Files, file names and folders organized in a logical way**
  6. **Humans doing rote activities tend to make many mistakes, have your computer do as much of this rote work as possible**
  7. **Internet is your best friend and mentor, google everything that you don't understand!**
  8. **If an error rise first of all try to solve the problem by yourself:** a) read the error message carefully; b) read again the help of the software; c) check for typos, they are everywhere; d) GOOGLE it!


### Some usefull online resources :

  1. **Stack Overflow**
  2. **BioStars**
  3. **Issue page of GitHub**.
  
---
