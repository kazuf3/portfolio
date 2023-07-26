---
title: "Linux Commands"
date: 2023-07-18T18:05:44-07:00
categories: ["portfolio", "cybersecurity", "linux commands"]
weight: 5
---
- [Back to index]({{< ref "cyber-security-portfolio-index" >}})
- [Scenario(given)]({{< ref "#scenario" >}})
- [Current file permissions(given)]({{< ref "#current-file-permissions" >}})
- [File permissions in Linux]({{< ref "file-permissions-in-linux" >}})

## Scenario

You are a security professional at a large organization. You mainly work with
their research team. Part of your job is to ensure users on this team are
authorized with the appropriate permissions. This helps keep the system secure.

Your task is to examine existing permissions on the file system. You’ll need to
determine if the permissions match the authorization that should be given. If
they do not match, you’ll need to modify the permissions to authorize the
appropriate users and remove any unauthorized access.

## Current file permissions

This section displays the file structure of the `/home/researcher2/projects`
directory and the permissions of the files and subdirectory it contains.

In the `/home/researcher2/projects` directory, there are five files with the following names and permissions:
- `project_k.txt`
  - User = read, write
  - Group = read, write
  - Other = read, write

- `project_m.txt`
  - User = read, write
  - Group = read
  - Other = none

- `project_r.txt`
  - User= read, write
  - Group = read, write
  - Other = read

- `project_t.txt`
  - User = read, write
  - Group = read, write
  - Other = read

- `.project_x.txt`
  - User = read, write
  - Group = write
  - Other = none


There is also one subdirectory inside the projects directory named drafts. The permissions on drafts are:
- `./drafts`
  - User = read, write, execute
  - Group = execute
  - Other = none

