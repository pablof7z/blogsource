---
layout: post
title: "Compare permissions script"
date: 2011-03-15 23:15
comments: true
categories:
  - bash
  - programming
  - fedora
  - linux
---
Recently I needed to compare some permissions between two directories, I wrote this script that could be useful for someone else:

``` bash
#!/bin/bash
# This script will go through a list of files and directories and match their
# permissions against another directory, it can also copy the permissions from
# the first directory.
#
# Written by Pablo Fernandez
#

function showusage()
{
        echo "USAGE: $0 OPTS DIR1 DIR2"
        echo
        echo "OPTIONS:"
        echo "-a Apply changes (update permissions of DIR2"
        echo "-w Warning on missing files"
        echo "-q Quiet mode"
        echo "-h Show this help"
        echo "The directory DIR1 will be matched against DIR2. The directory DIR2 will"
        echo "be corrected with all permission differences."
}

apply_changes=""

while [ ! -z "$1" ]; do
        case "$1" in
                -a)
                        apply_changes=1
                        ;;
                -w)
                        warn_on_missing=1
                        ;;
                -q)
                        quiet=1
                        ;;
                -h)
                        showusage
                        exit 0
                        ;;
                *)
                        if [ -z "$orig" ]; then
                                orig=$1
                        elif [ -z "$targ" ]; then
                                targ=$1
                        else
                                echo "Unknown flag $1"
                                showusage
                                exit 1
                        fi
        esac
        shift
done

if [ -z "$orig" -o -z "$targ" ]; then
        showusage
        exit 1
fi

# Change into the directory and get a list of files and dirs
pushd . >/dev/null
cd $orig
list=`find .`
popd >/dev/null

# Go through the list
for i in $list; do
        # Get the permissions of the orig file and targ file
        p=`stat -c %a $orig/$i`
        p1=`stat -c %a $targ/$i 2>/dev/null`

        [ "$quiet" = "" ] && echo -n "$i: "

        if [ "$p1" = "" ]; then
                # No permissions for targ file, targ file doesn't exist
                [ "$quiet" = "" ] && echo "not present"

                if [ "$warn_on_missing" != "" -a "$quiet" != "" ]; then
                        echo "$targ/$i not present"
                fi
        else
                # Permission for targ file, compare
                if [ "$p" != "$p1" ]; then
                        # Permissions are different going to try to set permissions
                        if [ "$apply_changes" != "" ]; then
                                # Set permissions
                                e=`chmod $p $targ/$i 2>&1 >/dev/null`

                                if [ "$?" != "0" ]; then
                                        [ "$quiet" = "" ] && echo "different permission, change to $p failed, $e"
                                        [ "$quiet" != "" ] && echo "$e"
                                else
                                        [ "$quiet" = "" ] && echo "set to $p"
                                fi
                        else
                                # Running just for report
                                [ "$quiet" = "" ] && echo "different, orig is $p targ is $p1"
                                [ "$quiet" != "" ] && echo "Permission of $targ/$i is different than $orig/$i"
                        fi
                else
                        [ "$quiet" = "" ] && echo "Ok"
                fi
        fi
done
```