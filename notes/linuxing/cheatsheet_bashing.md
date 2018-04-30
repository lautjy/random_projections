# Linux commands

Ghostscript PDF to PNG

    C:/opt/gs/gs9.22/bin/gswin64c.exe -dNOPAUSE -dBATCH -sDEVICE=pngalpha -sOutputFile=mak.png -r600 C:/2_Downloads/1484_001.pdf

Recursively change folder and file permissions:

    find ./videos -type d -exec chmod 775 {} \;
    find ./videos -type f -exec chmod 664 {} \;
    # or
    chmod -R a+rX *
    # where capital X ignores files


Remove poop left by OSX

    sudo find /path/to/folder -depth -name ".DS_Store" -exec rm {} \;


Remember `ALT+.` : gives tail of previous command.

    ls -lah /opt/makkara
    sudo rm -rf "ALT+."


How is it going:

    disk_usage = df -kP
    mem_usage = cat /proc/meminfo
    cpu_usage = cat /proc/stat


Find a file + with wildcard:

    find . -name foo
    find . -name 'nlp*'
    find -iname '*.gif'  # finds *.gif and *.GIF, etc.
    # NOTE: the ' -characters. W/out them this will not work.


Find how much these files take space:

    du -sh

Replace text in file:

    # "-i" for in-place, "g" for change every instance found
    sed -i 's/ugly/beautiful/g' /home/bruno/old-friends/sue.txt

Grep recursively

    grep -R "something" <path>

Grep and run some command for each line (I want to delete all listed files but also all files having the same file name but a different extension: .extension2.):

    grep --null -l '<pattern>' directory/*.extension1 | \
    xargs -n 1 -0 -I{} bash -c 'rm "$1" "${1%.*}.extension2"' -- {}

Add files to existing TAR file:

    mkdir ext
    cd ext
    wget files
    cd ..
    cp existing*.tar{,.old}
    tar uf existing*tar ext
    tar tvf existing*tar # verify file list

TAR and untar folder:

    tar -czvf archivename.tar.gz dirname
    tar -xzvf archivename.tar.gz
    tar -cjvf archname.tar.bz2
    tar -czvf doupdmodel.tar.gz -C ~/folder_uwannapack .

  * c - create
  * v - verbose file name list
  * f - following is archive file name
  * -C - change to this folder
  * z - run through gzip
  * j - run throuh bzip2


SCP files:

    # FROM remote TO local
    scp your_username@remotehost.edu:foobar.txt /some/local/directory
    # FROM local TO remote
    scp foo.txt bar.txt your_username@remotehost.edu:~

SSH bastion-hop using *alias*:

    alias goto='ssh -A -t gateway_bastions.domain.com ssh -A'
    # Then
    goto server.behind.bastion.com

Send file over bastion:

    tar c foobar | ssh | tar x
    # or using scp
    scp -o "ProxyCommand ssh -A gateway_bastion.domain.com nc -w 1 %h 22" $*
    # or using rsync
    alias orsync='rsync --progress -av -e "ssh -A gateway_bastion.domain.com ssh"'
    orsync somefile.tar.gz server.behind.bastion.com:~


Git with SSH:

    ssh-agent bash -c 'ssh-add /home/USER/.ssh/BEST_KEY; git clone git@github.com:markokr/pghashlib.git'
    git@github.com:markokr/pghashlib.git

Get IP:

    ip a
    ip link show

Restart network interface:

    ifdown eth0  # or wlan0
    ifup eth0  # or wlan0
    # you can see available interfaces in /etc/network/interfaces

Add user to group:

    # existing user
    sudo usermod -aG groupname username
    # new user
    sudo usermod -G groupname username

Blank screen is annoying:

    xset dpms 0 0 0 && xset s noblank && xset s off


## Python
Location of Python site-packages

    python -c "import site; print(site.getsitepackages())"

List `mock` calls:

    assert False, '\n{0}'.format('\n'.join([str(x) for x in self.winppsvc.sdlib.samples.get_sample_files.mock_calls]))


## Docker
Remove all containers, images, and volumes:

    docker rm $(docker ps -aq)
    docker rmi $(docker images -aq)
    sudo ls /var/lib/docker/volumes | sudo xargs -IVOL rm -rf /var/lib/docker/volumes/VOL

Docker netstats to find those IPs:
```
    > sysctl net.ipv6.bindv6only
    net.ipv6.bindv6only = 0
    > sysctl net.ipv6.conf.all.forwarding
    net.ipv6.conf.all.forwarding = 1
    > sudo netstat -tapn
```


## Stuff from other sources
(source ??)
I have marked with a * those which I think are absolutely essential
Items for each section are sorted by oldest to newest. Come back soon for more!


PSEUDO ALIASES FOR COMMONLY USED LONG COMMANDS
- function lt() { ls -ltrsa "$@" | tail; }
- function psgrep() { ps axuf | grep -v grep | grep "$@" -i --color=auto; }
- function fname() { find . -iname "*$@*"; }
- function remove_lines_from() { grep -F -x -v -f $2 $1; }
  removes lines from $1 if they appear in $2
- alias pp="ps axuf | pager"
- alias sum="xargs | tr ' ' '+' | bc" ## Usage: echo 1 2 3 | sum
- function mcd() { mkdir $1 && cd $1; }


VIM
- ':set spell' activates vim spellchecker. Use ']s' and '[s' to move between
  mistakes, 'zg' adds to the dictionary, 'z=' suggests correctly spelled words
- check my .vimrc http://tiny.cc/qxzktw and here http://tiny.cc/kzzktw for more


TOOLS
- Use 'apt-file' to see which package provides that file you're missing
* 'trash-cli' sends files to the trash instead of deleting them forever.
  - Be very careful with 'rm' or maybe make a wrapper to avoid deleting `*` by
  - accident (e.g. you want to type `rm tmp*` but type `rm tmp *`)
- 'file' gives information about a file, as image dimensions or text encoding
- 'sort -u' to check for duplicate lines
- 'echo start_backup.sh | at midnight' starts a command at the specified time
- Pipe any command over 'column -t' to nicely align the columns
* Google 'magic sysrq' to bring a Linux machine back from the dead
- 'diff --side-by-side fileA.txt fileB.txt | pager' to see a nice diff
* 'j.py' http://tiny.cc/62qjow remembers your most used folders and is an
  incredible substitute to browse directories by name instead of 'cd'
- 'dropbox_uploader.sh' http://tiny.cc/o2qjow is a fantastic solution to upload by commandline via Dropbox's API if you can't use the official client
- learn to use 'pushd' to save time navigating folders (j.py is better though)
- if you liked the 'psgrep' alias, check 'pgrep' as it is far more powerful
* never run `chmod o+x * -R`, capitalize the X to avoid executable files.

* If you want _only_ executable folders: `find . -type d -exec chmod g+x {} \;`
  - 'xargs' gets its input from a pipe and runs some command for each argument

* run jobs in parallel easily: `ls *.png | parallel -j4 convert {} {.}.jpg`
- grep has a '-c' switch that counts occurences, aka don't pipe grep to 'wc -l'.
- 'man hier' explains the filesystem folders for new users
- 'tree' instead of 'ls -R'
* Recover corrupt zip files: First, make copies and **ALWAYS WORK ON A COPY**
    - First: 'zip -F  corrupt_copy1.zip --out recover1.zip'
    - Then:  'zip -FF corrupt_copy2.zip --out recover2.zip'
    - Last:  'ditto -x -k corrupt_copy3.zip --out out_folder/'
    - Merge the contents of the two recovered zipfiles and the out_folder. You will be able to recover most of the data.
* Use GNU datamash for basic numerical, textual and statistical operations
  on text files: 'seq 10 | datamash sum 1 mean 1'


NETWORKING
- Don't know where to start? SMB is usually better than NFS for newbies. If really you know what you are doing, then NFS is the way to go.
* If you use 'sshfs_mount' and suffer from disconnects, use `-o reconnect,workaround=truncate:rename`
- 'python -m SimpleHTTPServer 8080' or 'python3 -mhttp.server localhost 8080' shares all the files in the current folder over HTTP.
* 'ssh -R 12345:localhost:22 -N server.com' forwards server.com's port 12345 to your local ssh port, even if you machine is behind a firewall/NAT. 'ssh localhost -p 12345' from server.com will get you in your machine.
* Read on 'ssh-agent' to strengthen your ssh connections using private keys, while avoiding typing passwords every time you ssh.
- 'socat TCP4-LISTEN:1234,fork TCP4:192.168.1.1:22' forwards your port 1234 to another machine's port 22. Very useful for quick NAT redirection.
- Some tools to monitor network connections and bandwith:
  - 'lsof -i' monitors network connections in real time
  - 'iftop' shows bandwith usage per *connection*
  - 'nethogs' shows the bandwith usage per *process*
* Use this trick on .ssh/config to directly access 'host2' which is on a private network, and must be accessed by ssh-ing into 'host1' first
```
  Host host2
      ProxyCommand ssh -T host1 'nc %h %p'
      HostName host2
```
* Pipe a compressed file over ssh to avoid creating large temporary .tgz files `tar cz folder/ | ssh server "tar xz"` or even better, use 'rsync'
* ssmtp can use a Gmail account as SMTP and send emails from the command line. 'echo "Hello, User!" | mail user@domain.com' ## Thanks to Adam Ziaja.
* Configure your /etc/ssmtp/ssmtp.conf:
```
      root=***E-MAIL***
      mailhub=smtp.gmail.com:587
      rewriteDomain=
      hostname=smtp.gmail.com:587
      UseSTARTTLS=YES
      UseTLS=YES
      AuthUser=***E-MAIL***
      AuthPass=***PASSWORD***
      AuthMethod=LOGIN
      FromLineOverride=YES
```

(CC) by-nc, Carlos Fenollosa <carlos.fenollosa@gmail.com>
Retrieved from http://cfenollosa.com/misc/tricks.txt
Last modified: Fri Jul 11 10:07:34 CEST 2014


## File permissions reminder

* 4 – read (r)
* 2 – write (w)
* 1 – execute (x)

Practical Examples:

    chmod 400 mydoc.txt – read by owner
    chmod 040 mydoc.txt – read by group
    chmod 004 mydoc.txt – read by anybody (other)
    chmod 200 mydoc.txt – write by owner
    chmod 020 mydoc.txt – write by group
    chmod 002 mydoc.txt – write by anybody
    chmod 100 mydoc.txt – execute by owner
    chmod 010 mydoc.txt – execute by group
    chmod 001 mydoc.txt – execute by anybody

Wait! I don't get it... there aren't enough permissions to do what I want!
Good call. You need to add up the numbers to get other types of permissions...

So, try wrapping your head around this:

    7 = 4+2+1 (read/write/execute)
    6 = 4+2 (read/write)
    5 = 4+1 (read/execute)
    4 = 4 (read)
    3 = 2+1 (write/execute)
    2 = 2 (write)
    1 = 1 (execute)

Basics:

    chmod 666 mydoc.txt – read/write by anybody! (the devil loves this one!)
    chmod 755 mydoc.txt – rwx for owner, rx for group and rx for the world
    chmod 777 mydoc.txt – read, write, execute for all! (may not be the best plan in the world...)



## SED oneliners

Handy one-liners for SED

HANDY ONE-LINERS FOR SED (Unix stream editor)               Mar. 23, 2001
compiled by Eric Pement <pemente@northpark.edu>               version 5.1
Latest version of this file is usually at:
   http://www.student.northpark.edu/pemente/sed/sed1line.txt


FILE SPACING:
```
 # double space a file
 sed G

 # double space a file which already has blank lines in it. Output file
 # should contain no more than one blank line between lines of text.
 sed '/^$/d;G'

 # triple space a file
 sed 'G;G'

 # undo double-spacing (assumes even-numbered lines are always blank)
 sed 'n;d'
```

NUMBERING:
```
 # number each line of a file (simple left alignment). Using a tab (see
 # note on '\t' at end of file) instead of space will preserve margins.
 sed = filename | sed 'N;s/\n/\t/'

 # number each line of a file (number on left, right-aligned)
 sed = filename | sed 'N; s/^/     /; s/ *\(.\{6,\}\)\n/\1  /'

 # number each line of file, but only print numbers if line is not blank
 sed '/./=' filename | sed '/./N; s/\n/ /'

 # count lines (emulates "wc -l")
 sed -n '$='
```

TEXT CONVERSION AND SUBSTITUTION:
```
 # IN UNIX ENVIRONMENT: convert DOS newlines (CR/LF) to Unix format
 sed 's/.$//'               # assumes that all lines end with CR/LF
 sed 's/^M$//'              # in bash/tcsh, press Ctrl-V then Ctrl-M
 sed 's/\x0D$//'            # gsed 3.02.80, but top script is easier

 # IN UNIX ENVIRONMENT: convert Unix newlines (LF) to DOS format
 sed "s/$/`echo -e \\\r`/"            # command line under ksh
 sed 's/$'"/`echo \\\r`/"             # command line under bash
 sed "s/$/`echo \\\r`/"               # command line under zsh
 sed 's/$/\r/'                        # gsed 3.02.80

 # IN DOS ENVIRONMENT: convert Unix newlines (LF) to DOS format
 sed "s/$//"                          # method 1
 sed -n p                             # method 2

 # IN DOS ENVIRONMENT: convert DOS newlines (CR/LF) to Unix format
 # Cannot be done with DOS versions of sed. Use "tr" instead.
 tr -d \r <infile >outfile            # GNU tr version 1.22 or higher

 # delete leading whitespace (spaces, tabs) from front of each line
 # aligns all text flush left
 sed 's/^[ \t]*//'                    # see note on '\t' at end of file

 # delete trailing whitespace (spaces, tabs) from end of each line
 sed 's/[ \t]*$//'                    # see note on '\t' at end of file

 # delete BOTH leading and trailing whitespace from each line
 sed 's/^[ \t]*//;s/[ \t]*$//'

 # insert 5 blank spaces at beginning of each line (make page offset)
 sed 's/^/     /'

 # align all text flush right on a 79-column width
 sed -e :a -e 's/^.\{1,78\}$/ &/;ta'  # set at 78 plus 1 space

 # center all text in the middle of 79-column width. In method 1,
 # spaces at the beginning of the line are significant, and trailing
 # spaces are appended at the end of the line. In method 2, spaces at
 # the beginning of the line are discarded in centering the line, and
 # no trailing spaces appear at the end of lines.
 sed  -e :a -e 's/^.\{1,77\}$/ & /;ta'                     # method 1
 sed  -e :a -e 's/^.\{1,77\}$/ &/;ta' -e 's/\( *\)\1/\1/'  # method 2

 # substitute (find and replace) "foo" with "bar" on each line
 sed 's/foo/bar/'             # replaces only 1st instance in a line
 sed 's/foo/bar/4'            # replaces only 4th instance in a line
 sed 's/foo/bar/g'            # replaces ALL instances in a line
 sed 's/\(.*\)foo\(.*foo\)/\1bar\2/' # replace the next-to-last case
 sed 's/\(.*\)foo/\1bar/'            # replace only the last case

 # substitute "foo" with "bar" ONLY for lines which contain "baz"
 sed '/baz/s/foo/bar/g'

 # substitute "foo" with "bar" EXCEPT for lines which contain "baz"
 sed '/baz/!s/foo/bar/g'

 # change "scarlet" or "ruby" or "puce" to "red"
 sed 's/scarlet/red/g;s/ruby/red/g;s/puce/red/g'   # most seds
 gsed 's/scarlet\|ruby\|puce/red/g'                # GNU sed only

 # reverse order of lines (emulates "tac")
 # bug/feature in HHsed v1.5 causes blank lines to be deleted
 sed '1!G;h;$!d'               # method 1
 sed -n '1!G;h;$p'             # method 2

 # reverse each character on the line (emulates "rev")
 sed '/\n/!G;s/\(.\)\(.*\n\)/&\2\1/;//D;s/.//'

 # join pairs of lines side-by-side (like "paste")
 sed '$!N;s/\n/ /'

 # if a line ends with a backslash, append the next line to it
 sed -e :a -e '/\\$/N; s/\\\n//; ta'

 # if a line begins with an equal sign, append it to the previous line
 # and replace the "=" with a single space
 sed -e :a -e '$!N;s/\n=/ /;ta' -e 'P;D'

 # add commas to numeric strings, changing "1234567" to "1,234,567"
 gsed ':a;s/\B[0-9]\{3\}\>/,&/;ta'                     # GNU sed
 sed -e :a -e 's/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/;ta'  # other seds

 # add commas to numbers with decimal points and minus signs (GNU sed)
 gsed ':a;s/\(^\|[^0-9.]\)\([0-9]\+\)\([0-9]\{3\}\)/\1\2,\3/g;ta'

 # add a blank line every 5 lines (after lines 5, 10, 15, 20, etc.)
 gsed '0~5G'                  # GNU sed only
 sed 'n;n;n;n;G;'             # other seds
```

SELECTIVE PRINTING OF CERTAIN LINES:
```
 # print first 10 lines of file (emulates behavior of "head")
 sed 10q

 # print first line of file (emulates "head -1")
 sed q

 # print the last 10 lines of a file (emulates "tail")
 sed -e :a -e '$q;N;11,$D;ba'

 # print the last 2 lines of a file (emulates "tail -2")
 sed '$!N;$!D'

 # print the last line of a file (emulates "tail -1")
 sed '$!d'                    # method 1
 sed -n '$p'                  # method 2

 # print only lines which match regular expression (emulates "grep")
 sed -n '/regexp/p'           # method 1
 sed '/regexp/!d'             # method 2

 # print only lines which do NOT match regexp (emulates "grep -v")
 sed -n '/regexp/!p'          # method 1, corresponds to above
 sed '/regexp/d'              # method 2, simpler syntax

 # print the line immediately before a regexp, but not the line
 # containing the regexp
 sed -n '/regexp/{g;1!p;};h'

 # print the line immediately after a regexp, but not the line
 # containing the regexp
 sed -n '/regexp/{n;p;}'

 # print 1 line of context before and after regexp, with line number
 # indicating where the regexp occurred (similar to "grep -A1 -B1")
 sed -n -e '/regexp/{=;x;1!p;g;$!N;p;D;}' -e h

 # grep for AAA and BBB and CCC (in any order)
 sed '/AAA/!d; /BBB/!d; /CCC/!d'

 # grep for AAA and BBB and CCC (in that order)
 sed '/AAA.*BBB.*CCC/!d'

 # grep for AAA or BBB or CCC (emulates "egrep")
 sed -e '/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d    # most seds
 gsed '/AAA\|BBB\|CCC/!d'                        # GNU sed only

 # print paragraph if it contains AAA (blank lines separate paragraphs)
 # HHsed v1.5 must insert a 'G;' after 'x;' in the next 3 scripts below
 sed -e '/./{H;$!d;}' -e 'x;/AAA/!d;'

 # print paragraph if it contains AAA and BBB and CCC (in any order)
 sed -e '/./{H;$!d;}' -e 'x;/AAA/!d;/BBB/!d;/CCC/!d'

 # print paragraph if it contains AAA or BBB or CCC
 sed -e '/./{H;$!d;}' -e 'x;/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d
 gsed '/./{H;$!d;};x;/AAA\|BBB\|CCC/b;d'         # GNU sed only

 # print only lines of 65 characters or longer
 sed -n '/^.\{65\}/p'

 # print only lines of less than 65 characters
 sed -n '/^.\{65\}/!p'        # method 1, corresponds to above
 sed '/^.\{65\}/d'            # method 2, simpler syntax

 # print section of file from regular expression to end of file
 sed -n '/regexp/,$p'

 # print section of file based on line numbers (lines 8-12, inclusive)
 sed -n '8,12p'               # method 1
 sed '8,12!d'                 # method 2

 # print line number 52
 sed -n '52p'                 # method 1
 sed '52!d'                   # method 2
 sed '52q;d'                  # method 3, efficient on large files

 # beginning at line 3, print every 7th line
 gsed -n '3~7p'               # GNU sed only
 sed -n '3,${p;n;n;n;n;n;n;}' # other seds

 # print section of file between two regular expressions (inclusive)
 sed -n '/Iowa/,/Montana/p'             # case sensitive
```

SELECTIVE DELETION OF CERTAIN LINES:
```
 # print all of file EXCEPT section between 2 regular expressions
 sed '/Iowa/,/Montana/d'

 # delete duplicate, consecutive lines from a file (emulates "uniq").
 # First line in a set of duplicate lines is kept, rest are deleted.
 sed '$!N; /^\(.*\)\n\1$/!P; D'

 # delete duplicate, nonconsecutive lines from a file. Beware not to
 # overflow the buffer size of the hold space, or else use GNU sed.
 sed -n 'G; s/\n/&&/; /^\([ -~]*\n\).*\n\1/d; s/\n//; h; P'

 # delete the first 10 lines of a file
 sed '1,10d'

 # delete the last line of a file
 sed '$d'

 # delete the last 2 lines of a file
 sed 'N;$!P;$!D;$d'

 # delete the last 10 lines of a file
 sed -e :a -e '$d;N;2,10ba' -e 'P;D'   # method 1
 sed -n -e :a -e '1,10!{P;N;D;};N;ba'  # method 2

 # delete every 8th line
 gsed '0~8d'                           # GNU sed only
 sed 'n;n;n;n;n;n;n;d;'                # other seds

 # delete ALL blank lines from a file (same as "grep '.' ")
 sed '/^$/d'                           # method 1
 sed '/./!d'                           # method 2

 # delete all CONSECUTIVE blank lines from file except the first; also
 # deletes all blank lines from top and end of file (emulates "cat -s")
 sed '/./,/^$/!d'          # method 1, allows 0 blanks at top, 1 at EOF
 sed '/^$/N;/\n$/D'        # method 2, allows 1 blank at top, 0 at EOF

 # delete all CONSECUTIVE blank lines from file except the first 2:
 sed '/^$/N;/\n$/N;//D'

 # delete all leading blank lines at top of file
 sed '/./,$!d'

 # delete all trailing blank lines at end of file
 sed -e :a -e '/^\n*$/{$d;N;ba' -e '}'  # works on all seds
 sed -e :a -e '/^\n*$/N;/\n$/ba'        # ditto, except for gsed 3.02*

 # delete the last line of each paragraph
 sed -n '/^$/{p;h;};/./{x;/./p;}'
```

SPECIAL APPLICATIONS:
```
 # remove nroff overstrikes (char, backspace) from man pages. The 'echo'
 # command may need an -e switch if you use Unix System V or bash shell.
 sed "s/.`echo \\\b`//g"    # double quotes required for Unix environment
 sed 's/.^H//g'             # in bash/tcsh, press Ctrl-V and then Ctrl-H
 sed 's/.\x08//g'           # hex expression for sed v1.5

 # get Usenet/e-mail message header
 sed '/^$/q'                # deletes everything after first blank line

 # get Usenet/e-mail message body
 sed '1,/^$/d'              # deletes everything up to first blank line

 # get Subject header, but remove initial "Subject: " portion
 sed '/^Subject: */!d; s///;q'

 # get return address header
 sed '/^Reply-To:/q; /^From:/h; /./d;g;q'

 # parse out the address proper. Pulls out the e-mail address by itself
 # from the 1-line return address header (see preceding script)
 sed 's/ *(.*)//; s/>.*//; s/.*[:<] *//'

 # add a leading angle bracket and space to each line (quote a message)
 sed 's/^/> /'

 # delete leading angle bracket & space from each line (unquote a message)
 sed 's/^> //'

 # remove most HTML tags (accommodates multiple-line tags)
 sed -e :a -e 's/<[^>]*>//g;/</N;//ba'

 # extract multi-part uuencoded binaries, removing extraneous header
 # info, so that only the uuencoded portion remains. Files passed to
 # sed must be passed in the proper order. Version 1 can be entered
 # from the command line; version 2 can be made into an executable
 # Unix shell script. (Modified from a script by Rahul Dhesi.)
 sed '/^end/,/^begin/d' file1 file2 ... fileX | uudecode   # vers. 1
 sed '/^end/,/^begin/d' "$@" | uudecode                    # vers. 2

 # zip up each .TXT file individually, deleting the source file and
 # setting the name of each .ZIP file to the basename of the .TXT file
 # (under DOS: the "dir /b" switch returns bare filenames in all caps).
 echo @echo off >zipup.bat
 dir /b *.txt | sed "s/^\(.*\)\.TXT/pkzip -mo \1 \1.TXT/" >>zipup.bat
```

TYPICAL USE: Sed takes one or more editing commands and applies all of
them, in sequence, to each line of input. After all the commands have
been applied to the first input line, that line is output and a second
input line is taken for processing, and the cycle repeats. The
preceding examples assume that input comes from the standard input
device (i.e, the console, normally this will be piped input). One or
more filenames can be appended to the command line if the input does
not come from stdin. Output is sent to stdout (the screen). Thus:
```
 cat filename | sed '10q'        # uses piped input
 sed '10q' filename              # same effect, avoids a useless "cat"
 sed '10q' filename > newfile    # redirects output to disk
```
For additional syntax instructions, including the way to apply editing
commands from a disk file instead of the command line, consult "sed &
awk, 2nd Edition," by Dale Dougherty and Arnold Robbins (O'Reilly,
1997), "UNIX Text Processing," by Dale Dougherty
and Tim O'Reilly (Hayden Books, 1987) or the tutorials by Mike Arst
distributed in U-SEDIT2.ZIP (many sites). To fully exploit the power
of sed, one must understand "regular expressions." For this, see
"Mastering Regular Expressions" by Jeffrey Friedl (O'Reilly, 1997).
The manual ("man") pages on Unix systems may be helpful (try "man
sed", "man regexp", or the subsection on regular expressions in "man
ed"), but man pages are notoriously difficult. They are not written to
teach sed use or regexps to first-time users, but as a reference text
for those already acquainted with these tools.

QUOTING SYNTAX: The preceding examples use single quotes ('...')
instead of double quotes ("...") to enclose editing commands, since
sed is typically used on a Unix platform. Single quotes prevent the
Unix shell from intrepreting the dollar sign ($) and backquotes
(`...`), which are expanded by the shell if they are enclosed in
double quotes. Users of the "csh" shell and derivatives will also need
to quote the exclamation mark (!) with the backslash (i.e., \!) to
properly run the examples listed above, even within single quotes.
Versions of sed written for DOS invariably require double quotes
("...") instead of single quotes to enclose editing commands.

USE OF `\t` IN SED SCRIPTS: For clarity in documentation, we have used
the expression `\t` to indicate a tab character (0x09) in the scripts.
However, most versions of sed do not recognize the `\t` abbreviation,
so when typing these scripts from the command line, you should press
the TAB key instead. `\t` is supported as a regular expression
metacharacter in awk, perl, and HHsed, sedmod, and GNU sed v3.02.80.

VERSIONS OF SED: Versions of sed do differ, and some slight syntax
variation is to be expected. In particular, most do not support the
use of labels (:name) or branch instructions (b,t) within editing
commands, except at the end of those commands. We have used the syntax
which will be portable to most users of sed, even though the popular
GNU versions of sed allow a more succinct syntax. When the reader sees
a fairly long command such as this:
```
   sed -e '/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d
```
it is heartening to know that GNU sed will let you reduce it to:
```
   sed '/AAA/b;/BBB/b;/CCC/b;d'      # or even
   sed '/AAA\|BBB\|CCC/b;d'
```
In addition, remember that while many versions of sed accept a command
like "/one/ s/RE1/RE2/", some do NOT allow "/one/! s/RE1/RE2/", which
contains space before the 's'. Omit the space when typing the command.

OPTIMIZING FOR SPEED: If execution speed needs to be increased (due to
large input files or slow processors or hard disks), substitution will
be executed more quickly if the "find" expression is specified before
giving the "s/.../.../" instruction. Thus:
```
   sed 's/foo/bar/g' filename         # standard replace command
   sed '/foo/ s/foo/bar/g' filename   # executes more quickly
   sed '/foo/ s//bar/g' filename      # shorthand sed syntax
```
On line selection or deletion in which you only need to output lines
from the first part of the file, a "quit" command (q) in the script
will drastically reduce processing time for large files. Thus:
```
   sed -n '45,50p' filename           # print line nos. 45-50 of a file
   sed -n '51q;45,50p' filename       # same, but executes much faster
```
