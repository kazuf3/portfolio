---
title: "File Permissions in Linux"
date: 2023-07-18T18:10:51-07:00
categories: ["portfolio", "cybersecurity", "linux commands"]
---

## Project description

This project repairs file permissions to its appropriate status.

## Check file and directory details

After performing the command, ls -la in the /home/researcher2/projects/ directory, we found the following status.

## Describe the permissions string

In the `/home/researcher2/projects` directory, there are five files with the following names and permissions: 

- `project_k.txt: -rw-rw-rw-`
  - User = read, write
  - Group = read, write
  - Other = read, write

- `project_m.txt: -rw-r-----`
  - User = read, write
  - Group = read
  - Other = none

- `project_r.txt: -rw-rw-r--`
  - User= read, write
  - Group = read, write
  - Other = read

- `project_t.txt: -rw-rw-r--`
  - User = read, write
  - Group = read, write
  - Other = read

- `.project_x.txt: -rw--w----`
  - User = read, write
  - Group = write
  - Other = none

For each file and directory, permissions are assigned with 3 sets (user, group,
other) of 3 (read, write, and execution) different permissions. Hyphen means the
file or directory lacks that permission according to the letter position in the
string.

## Change file permissions

First, we remove write permission for others from all the files, except for the
hidden file, other than the user. o-w means remove write permission from others

`$ chmod o-w p*.txt`

## Change file permissions on a hidden file

Second, we remove all the write permissions for the hidden file. ugo-w means
talking write permission away for any users.

`$ chmod ugo-w .project_x.txt`

## Change directory permissions

Lastly, we set the permission of the directory draft private to the researcher2
user. -r means this runs recursively with its subdirectories and files inside of
the directory. u=rwx means to give full permission to the user and go-rwx means
remove all the permissions from the group and others.

`$ chmod -r u=rwx,go=--- ./draft`

## Summary

During this project, we performed repairs on both directories and file access
permissions. First, we checked the current permissions, then repaired their
permissions based on the organization's policy. Since the draft directory is
designed for the user only, we also removed permissions for the group and
others.
