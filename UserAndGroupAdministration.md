# User and Group Administration in Linux
Each user in Linux has a unique user ID (**UID**), which is an ordinary integer number, and an associated username. Users log in by using their usernames, but the system uses the associated UIDs. Each user account also has a home directory and a login shell. When users log in, they are placed in their home directory, and their login shell executes. All of this user account information is stored in the `/etc/passwd` file.

Each user also belongs to one or more groups. Different users can be assigned to the same group. Access can be given to a group, and all members of the group are granted the same access privileges. Each group account in Linux has a unique group ID (**GID**) and an associated group name. Group information is stored in the `/etc/group` file.

RedHat Linux uses a **user private group (UPG)** scheme. When a new user account is added, a new user private group is also created. The user's private group has the same name as the user, and the new user is the only member of this group.

Both users and groups use shadow passwords. Passwords are hashed and stored in different files, `/etc/shadow` for users and `/etc/gshadow` for groups. Security improves by storing hashed passwords in “shadow” files, because these files are readable only by the root user. The use of shadow passwords also provides password aging parameters and allows security policies to be enforced, using the /etc/login.defs file. Only the root user can add, modify, or delete user and group accounts.

# User and Group Configuration Files
## /etc/passwd
When a new user is added, the information is stored as a single, colon-separated line in /etc/passwd. Here is an example of an entry in this file:
```
tail -1 /etc/passwd
test:x:1001:1001:test user:/home/test:/bin/bash
```
## /etc/shadow
With shadow passwords, a new entry is automatically added to /etc/shadow when a new user is created. This file can be viewed only by root. Here is an example of an entry in this file:
```
tail -1 /etc/shadow
test:$6$XBCDBQ...:17610:0:99999:7:::
```
## /etc/group
Because Oracle Linux uses a UPG scheme, a new entry is automatically created in /etc/group when a new user is added. The group name is the same as the username. Here is an example of an entry in this file:
```
tail -1 /etc/group
test:x:1000:test
```
Each group can have multiple users. Users can also belong to more than one group. The GID stored in the user’s entry in /etc/passwd is the user’s primary group.
## /etc/gshadow
Hashed group passwords are stored in this file. However, group passwords are rarely used. Here is an example of an entry in this file:
```
tail -1 /etc/gshadow 
test:!!::test
```
The last two fields are used to designate administrators and members.

# Group Account Administration
## groupadd
Use the groupadd command to add a group account. The syntax is:
```
groupadd group_name
```
For example, create a new group 'engineers'.
```
root@beenu:~# groupadd engineers
```
## groupmod
Use the groupmod command to modify a group account. The syntax is:
```
groupmod group_name
```
## groupdel
Use the groupdel command to delete a group account. The syntax is:
```
groupdel group_name
```
If we want to delete the group 'engineers',
```
groupdel engineers
```
You can remove groups even if there are members in the group. You cannot remove the primary group of any existing user. You must remove the user before removing the group.
## gpasswd
Use the gpasswd command to administer /etc/group and /etc/gshadow. Every group can have administrators, members, and a password. The syntax is:
```
gpasswd group_name
```
## groups
The groups command displays the groups that a user belongs to. The following example illustrates that user oracle belongs to two groups, oracle (primary group) and students (secondary group):
```
grep
```
