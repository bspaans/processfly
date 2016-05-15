processfly
==========

Processfly is a testing tool that captures the output of command line programs.
It's heavily inspired by SpectoLab's Hoverfly, which does capture and playback
for http(s) web services.

Processfly is written in Go and makes use of Boltdb for storage.  It was
initially built to facilitate testing of the KubeFuse program. It does not 
work with interactive programs at this point in time, but it's pretty baller
with the rest.


Usage
=====

Capture the output of `ls -al /`

```
processfly --capture ls -al /
```

Or by setting the CAPTURE environment variable:

```
CAPTURE=1 processfly ls -al /
```

Play back the output of `ls -al /`

```
processfly ls -al /
```

By writing a wrapper `ls` file and putting this on the PATH we can seamlessly
use processfly from our tests:

```
cat > ls <<EOF 
#!/bin/bash 
processfly "\$0" "\$@"
EOF

chmod +x ls  
export PATH="`pwd`:$PATH"
```


We can also specify a cheeky little alias to work with processfly from the
command line, which is less useful for automated testing, but still fun for the
whole family.

```
$ alias ls="processfly ls"
$ ls /
./processfly does not know this process. Run the command in capture mode first.
$ export CAPTURE=1
$ ls /
drwxr-xr-x  24 root root      4096 May 14 12:29 .
drwxr-xr-x  24 root root      4096 May 14 12:29 ..
drwxr-xr-x   2 root root     12288 Mar 20 12:37 bin
drwxr-xr-x   4 root root      4096 May 14 12:30 boot
drwxrwxr-x   2 root root      4096 Aug 10  2015 cdrom
drwxr-xr-x  19 root root      4880 May 14 10:17 dev
drwxr-xr-x 159 root root     12288 May 14 12:29 etc
drwxr-xr-x   4 root root      4096 Aug 10  2015 home
lrwxrwxrwx   1 root root        32 May 14 12:29 initrd.img -> boot/initrd.img-4.2.0-36-generic
lrwxrwxrwx   1 root root        32 Apr  6 18:58 initrd.img.old -> boot/initrd.img-4.2.0-35-generic
drwxr-xr-x  29 root root      4096 Feb 17 21:13 lib
drwxr-xr-x   2 root root      4096 Feb 17 21:13 lib32
drwxr-xr-x   2 root root      4096 Feb 17 21:13 lib64
drwx------   2 root root     16384 Aug 10  2015 lost+found
drwxr-xr-x   3 root root      4096 Aug 10  2015 media
drwxr-xr-x   2 root root      4096 Apr 17  2015 mnt
drwxr-xr-x   5 root root      4096 Apr  5 00:07 opt
dr-xr-xr-x 297 root root         0 May 13 22:34 proc
drwx------   8 root root      4096 Nov 12  2015 root
drwxr-xr-x  30 root root       960 May 15 03:05 run
drwxr-xr-x   2 root root     12288 Mar 20 12:37 sbin
drwxr-xr-x   2 root root      4096 Apr 22  2015 srv
dr-xr-xr-x  13 root root         0 May 15 03:31 sys
drwxrwxrwt  12 root root     12288 May 15 04:49 tmp
drwxr-xr-x  12 root root      4096 Jan 30 15:09 usr
drwxr-xr-x  13 root root      4096 Apr 22  2015 var
lrwxrwxrwx   1 root root        29 May 14 12:29 vmlinuz -> boot/vmlinuz-4.2.0-36-generic
lrwxrwxrwx   1 root root        29 Apr  6 18:58 vmlinuz.old -> boot/vmlinuz-4.2.0-35-generic
$ export CAPTURE=0
$ ls /
drwxr-xr-x  24 root root      4096 May 14 12:29 .
drwxr-xr-x  24 root root      4096 May 14 12:29 ..
drwxr-xr-x   2 root root     12288 Mar 20 12:37 bin
drwxr-xr-x   4 root root      4096 May 14 12:30 boot
drwxrwxr-x   2 root root      4096 Aug 10  2015 cdrom
drwxr-xr-x  19 root root      4880 May 14 10:17 dev
drwxr-xr-x 159 root root     12288 May 14 12:29 etc
drwxr-xr-x   4 root root      4096 Aug 10  2015 home
lrwxrwxrwx   1 root root        32 May 14 12:29 initrd.img -> boot/initrd.img-4.2.0-36-generic
lrwxrwxrwx   1 root root        32 Apr  6 18:58 initrd.img.old -> boot/initrd.img-4.2.0-35-generic
drwxr-xr-x  29 root root      4096 Feb 17 21:13 lib
drwxr-xr-x   2 root root      4096 Feb 17 21:13 lib32
drwxr-xr-x   2 root root      4096 Feb 17 21:13 lib64
drwx------   2 root root     16384 Aug 10  2015 lost+found
drwxr-xr-x   3 root root      4096 Aug 10  2015 media
drwxr-xr-x   2 root root      4096 Apr 17  2015 mnt
drwxr-xr-x   5 root root      4096 Apr  5 00:07 opt
dr-xr-xr-x 297 root root         0 May 13 22:34 proc
drwx------   8 root root      4096 Nov 12  2015 root
drwxr-xr-x  30 root root       960 May 15 03:05 run
drwxr-xr-x   2 root root     12288 Mar 20 12:37 sbin
drwxr-xr-x   2 root root      4096 Apr 22  2015 srv
dr-xr-xr-x  13 root root         0 May 15 03:31 sys
drwxrwxrwt  12 root root     12288 May 15 04:49 tmp
drwxr-xr-x  12 root root      4096 Jan 30 15:09 usr
drwxr-xr-x  13 root root      4096 Apr 22  2015 var
lrwxrwxrwx   1 root root        29 May 14 12:29 vmlinuz -> boot/vmlinuz-4.2.0-36-generic
lrwxrwxrwx   1 root root        29 Apr  6 18:58 vmlinuz.old -> boot/vmlinuz-4.2.0-35-generic
```


Workflow
========

For KubeFuse I have to generate more than one test case.  To keep this process
repeatable I added a `capture` step to my build system that I would run
whenever the command under test (`kubectl` in this case) was updated.

The build step looks something like this:

```
#!/bin/bash 

# Put processfly into capture mode 
export CAPTURE=1

# Remove the old processfly database if it exists
rm -f processes.db

# Run the commands I need for testing:
processfly kubectl get namespaces
processfly kubectl get pods --all-namespaces
processfly kubectl get svc --all-namespaces
processfly kubectl get rc --all-namespaces
....

# Export the results
processfly --export | python -m json.tool > kubectl.json
```

Before I start running my tests I need to create the kubectl shim and import
the definition:

```
cat > bin/kubectl <<EOF 
#!/bin/bash 

unset CAPTURE
processfly kubectl "\$@"
EOF

chmod +x bin/kubectl
processfly --import kubectl.json
```

And then I can start my tests with a modified PATH so that the tests pick 
up the shim first.

```
PATH="bin/:$PATH" nosetests
```

And that's it. Your uncle is Bob.