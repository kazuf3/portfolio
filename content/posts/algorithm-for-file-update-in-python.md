---
title: "Algorithm for File Update in Python"
date: 2023-07-26T10:16:50-07:00
categories: ["cybersecurity", "portfolio", "Python"]
weight: 5
---
- [Back to index]({{< ref "cyber-security-portfolio-index" >}})
## Project description

This project describes an activity of updating a text file, based on the
necessity of the business.

It is designed for a process to maintain allow-list-based access control list.
In order to restrict access of unauthorized users, businesses are required to
review the list and remove access privileges on a need-to basis. With a given
list of IP addresses, the program will remove them from the allow-list and write
IP addresses back to the text file.

# Open the file that contains the allow list

Initially, the allow list text file contains IP addresses as follows.

```
ip address
192.168.25.60
192.168.205.12
192.168.97.225
192.168.6.9
192.168.52.90
192.168.158.170
192.168.90.124
192.168.186.176
192.168.133.188
192.168.203.198
192.168.201.40
192.168.218.219
192.168.52.37
192.168.156.224
192.168.60.153
192.168.58.57
192.168.69.116
```

## Read the file contents

The following code piece will read the list file `import_file`. `with` statement
will handle the execution of `open()` and `close()` automatically.

```
    # read the list from the file
    with open(import_file, "r") as file:
        ips = file.read()
```

## Convert the string into a list
Then it splits the whole string into a list of strings. Each string represents an IP address.

```
    # split IP addresses based on line breaks
    ip_addresses = ips.split()
```

## Iterate through the remove list

In order to remove all IP addresses from the list we want to maintain, it
iterates over remove targets and removes them from the list.
```
    # remove each target IP address from the list
    for element in remove_list:
        if element in ip_addresses:
            ip_addresses.remove(element)
```

## Update the file with the revised list of IP addresses

To write back to the file, we reformat the list into a line-break-separated
single string. Write the text back.

```
    # prepare updated string of list by inserting '\n' as separator
    ip_addresses = "\n".join(ip_addresses)

    # fully write back to the file
    with open(import_file, "w") as file:
        file.write(ip_addresses)
```

## Summary

This project explained how to automate the maintenance of allow-list which
contains IP addresses. For reusability, the whole process is formatted as a
function. The docstring contains the signature of the function. Appropriate
comments are inserted for future modifications or reviews.

```
def update_file(import_file, remove_list):
    """ Removes target IP addresses from the text file.
    
    If given target IP addresses are not in the text file, they are ignored.
    
    Parameters
    ----------
    import_file : str
        File path/name of the list we want to remove from
        
    remove_list : list (of str)
        Target IP addresses we want to remove
    """

    # read the list from the file
    with open(import_file, "r") as file:
        ips = file.read()

    # split IP addresses based on line breaks
    ip_addresses = ips.split()

    # remove each target IP address from the list
    for element in remove_list:
        if element in ip_addresses:
            ip_addresses.remove(element)

    # prepare updated string of list by inserting '\n' as separator
    ip_addresses = "\n".join(ip_addresses)

    # fully write back to the file
    with open(import_file, "w") as file:
        file.write(ip_addresses)
```

The following code piece is an example of its execution.

```
# sample execution:
# call the function
update_file("allow_list.txt", ["192.168.25.60", "192.168.90.124", "192.168.60.153"])

# Build `with` statement to read in the updated file

with open("allow_list.txt", "r") as file:
    # Read in the updated file and store the contents in `text`
    text = file.read()

# Display the contents of `text`

print(text)
```

The output is:
```
ip
address
192.168.205.12
192.168.97.225
192.168.6.9
192.168.52.90
192.168.158.170
192.168.186.176
192.168.133.188
192.168.203.198
192.168.201.40
192.168.218.219
192.168.52.37
192.168.156.224
192.168.58.57
192.168.69.116
```

For future maintenance, it is suggested to:

- Instead of reading while list from the text, we can perform a rewrite based on chunks, in order to handle a large list
- Sorting the IP addresses both in the list and the target list will reduce the complexity of execution.
