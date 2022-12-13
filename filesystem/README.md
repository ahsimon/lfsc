# FileSystem Features

Extended attributes associate metadata not interpreted directly by filesystem with files. Four namespaces exist: user, trusted, security and system. The system namespace is used for Access Control List(ACLs) and security namespace is used by SELinux

Flag values are stored in the file inode and may be modified and set only by the root user. They are viewed with **lsattr** and set with **charrt**. Flags may be set for files

Flags

|   |   |
|---|---|
| i: Inmutable| It cant be modified, deleted or renamed even by root. Not hardlink can be created on it, and no data can be written to the file. Only super user can set or clear this attribute  | 
| a: Append-only | A file with append-only attribute can set only be opened in append mode for writing. Only the super user can set or clir this attribute  | 
| d: No-dump  |  A file with the no-dump attribute set is ignored when the dump program is run, this is useful for swap and cache files that you dont want to waste time backing up |   
A: no atime update| A file with no atime update will not modify it atime(access time)  recored when the file is accessed but not otherwise modified. This can increase the performance on some systems because it reduces the amount of disk I/O|


```sh
chattr +|-|=mode filename

# makes /etc/resolv.conf inmutable
sudo chattr +i /etc/resolv.conf

# makes /etc/resolv.conf inmutable
chattr +A test.txt

lsattr filename
```