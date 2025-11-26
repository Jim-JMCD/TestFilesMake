# testFilesMake
### A test file generator that fills files with random bytes in a single directory

#### This repository only contains X86-64 excutable. For the ARM version go to [testFilesMake_for_ARM_aarch64](https://github.com/Jim-JMCD/TestFilesMake_for_ARM_aarch64/)

testFilesMake is a Linux portable executable created from the bash script [Test_Files_Make](https://github.com/Jim-JMCD/Test_Files_Make/) (private Github repository) using shc. 

Use [testFilesCreate](https://github.com/Jim-JMCD/testFilesCreate/) as an alternative : 
* If a tree of directories is required for test data;
* If random sized data files are required. Size range is user defined
* If all files created have to be identical.
* If you do not require sparse files. Sparse files only avalaible in testFilesMake 

### Dependency
This requires a Linux bash environment to run. Will run in Microsoft WSL2(Linux), TestFilesMake will not run in MSYS2, Gitbash and Cygwin environmants. testFilesMake is an exceutable created from the *shc* utility. More : [Github shc](https://github.com/neurobin/shc)   

### OPTIONS
__File Contents__ 
* Each file is filled with its own set of ramdom data, no files are copied.
* Binary (default) from /dev/urandom.  (Not compressible)
* __-P__ any printable characters from the ASCI set. Optional
* __-D__ Only the 0 to 9 digits. Optioal
* __-S__ Sparse file nulls. Only to be used in Linux, Unix and WSL2. The sparse file option is __NOT__ to be used on MS Windows filesystems and WSL1, MSYS2, Gitbash and Cygwin - see below

__Other Options__

__-f \<file size\>__   Manditory, minimum 1 byte. File sizes have to be designated by B, K, M or G. Example 2KiB = 2K, 3MiB = 3M 200GiB = 200G

__-o \<exisiting directory\>__  Optional, create file in a exisiting directory. Default is your current directory.

Files created will have names in the format of **tfm_YYYYMMDD-mmss_file\_\<file number\>**  

### NOTES
__File Contents: Digits__ 

__-D n__     Where n is a number 1 to 10.
   
This selects the numbers in the character pool used
   
* n=1 files will be filled with the character zero '0'
* n=2 files will be filled with 0 and 1
* n=3 files will be filled with 0,1 and 2
* n=7 files will be filled with 0 and numbers 1 to 6
* n=10 files will be filled with a randon numbers of 0 to 9 

__File Contents: Printable ASCI characters:___ 

__-P n__ Where n is a number 1 to 95.(the ASCI character set)
* n=1 file will filled with repeats of the character 'A'
* n>=2 or n<=26 all characters will be from the lower-case Latin alphabet
* n>=27 characters will be from the 95 printable character set.
  
_Examples:_
* n = 10 will select all characters stating at "a" to "j"
* n = 4 will select all characters "a" to "d"
* n = 60 will select all characters from "!" to "\\"
* n = 46 will select all characters from "!" to "M"

__File Contents: Sparse File__

__-S__ Null filled sparse files. Although disk usage is minimal their apparent size may be used to determine capacity usage.  Not to be used on Windows filesystems.

__VALIDATE FILE CONTENTS__

___Validate all Files:___
~~~
    od -N <bytes> -Ax -t x1z <file name>
~~~
* Where \<bytes\> is the number to check from beginning of file.
* Non-printable characters will appear as "dots"
* For sparse files omit \<bytes\> as it not required.

___Validate printable character distribution:___
~~~   
    od -a <file name>  | cut -b 9- | tr " " \\n | egrep -v "^$" | sort | uniq -c
~~~
_Output_
* Column 1 : Character count
* Column 2 : Character being counted. This column should only contain a single charcter, if not then file contents is binary data.
  
___Validate sparse fILes:___ 
  * _ls -lsh_ Shows disk usage at start of output, apparent size is in its usual place.
  * _du -b_ Shows disk usage in bytes.
  * _du -b --apparent-size_ Shows the size the user sees and used filesystem capacity calculations

__NOTES__
* Binary data may render storage and transmission data dedplication and compression ineffective.
* The smaller range of printable characters used the longer the script runs.
* Testing data duplication, the moost useful range is alphanumeric of 1 to 20 chracters i.e -P 1 to -P 20
* Testing compression, the most useful range is alphanumeric 1 to 40 i.e. -P 1 to -P 40

_EXAMPLES_

Create ten 5 MiB files containing a randonly distribution of the first 30 printable characters of the ASCII character set.   
~~~
$./testFilesMake -P 30 -f 5M -n 10
Creating 10x 5M files in /home/ted/tmp/
Files containing random selection from the 30 char set: !"#$%&'()*+,-./0123456789:;<=>

Creating file 1      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_1
Creating file 2      Done: 5.0M  /home/ted/tmp//tfm_20251019-1916-53_file_2
Creating file 3      Done: 5.0M  /home/ted/tmp//tfm_20251019-1916-53_file_3
Creating file 4      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_4
Creating file 5      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_5
Creating file 6      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_6
Creating file 7      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_7
Creating file 8      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_8
Creating file 9      Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_9
Creating file 10     Done: 5.0M  /home/ted/tmp/tfm_20251019-1916-53_file_10

Total Directory Usage of output directory: /home/ted/tmp
          102435.066 KiB
             100.427 MiB
               0.098 GiB

Total disk usage (du -sk) : 101M

NOTE: Totals are for the entire directory contents not just test files made by this script run
$ 
~~~
Create five 1 GiB **sparse files** 
~~~
$./testFilesMake -S -f 1G -n 5
Creating 5x 1G SPARSE files in /home/ted/tmp

Creating file 1       DONE: Apparent Size: 1.0G Disk Usage: 0  /home/ted/tmp/tfm_20251019-1937-01_Sfile_1
Creating file 2       DONE: Apparent Size: 1.0G Disk Usage: 0  /home/bob/tmp/tfm_20251019-1937-01_Sfile_2
Creating file 3       DONE: Apparent Size: 1.0G Disk Usage: 0  /home/bob/tmp/tfm_20251019-1937-01_Sfile_3
Creating file 4       DONE: Apparent Size: 1.0G Disk Usage: 0  /home/bob/tmp/tfm_20251019-1937-01_Sfile_4
Creating file 5       DONE: Apparent Size: 1.0G Disk Usage: 0  /home/bob/tmp/tfm_20251019-1937-01_Sfile_5

Total Directory Usage of output directory: /home/ted/tmp
         5242907.188 KiB
            5140.105 MiB
               5.020 GiB

Total disk usage (du -sk) : 32K

NOTE: Totals are for the entire directory contents not just test files made by this script run
$
~~~
Create fifty 5GiB files containing only the randonly distributed digits 0, 1 , 2, 3 and 4   

    testFilesMake -D 5 -f 5G -n 50
     
Create a single 1MiB file containing these characters randomly distributed __!"#$%&'()*+,-./0123456789:;<=>?\@ABC__  

    testFilesMake -P 35 -f 1M -n 1
     
Same as previous, but with files being generated in the _test_files_ directory within the user's home directory. 

    testFilesMake -P 35 -f 1M -n 1 -o ~/test_files

Create 1,000 1KiB files containing these characters randomly distributed __abcdefghij__  

    testFilesMake -P 10 -f 1K -n 1000
   
_________________________________________________________________
__WINDOWS SPARSE FILES - How to make:__

On Windows run the following as administrator

    fsutil File CreateNew  <file name> <size in bytes>
    fsutil Sparse SetFlag  <file name>
    fsutil Sparse SetRange <file name> 0 <size in bytes>


WSL2-Linux cannot run windows commands and programs that require administrator privilege.
   
