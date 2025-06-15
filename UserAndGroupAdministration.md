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
Each group can have multiple users. Users can also belong to multiple groups. The GID stored in the user’s entry in /etc/passwd is the user’s primary group.
## /etc/gshadow
Hashed group passwords are stored in this file. However, group passwords are rarely used. Here is an example of an entry in this file:
```
tail -1 /etc/gshadow 
test:!!::test
```
The last two fields are used to designate administrators and members.
# Adding a User Account
## useradd
Use the useradd command to add a user account. The syntax is:
```
useradd user_name
```
Also, by default, useradd creates a locked user account. To unlock the account and assign a password, run the passwd user_name command as root. The passwd user_name command prompts you for a new password. Depending on the complexity of the password, you may be notified that the password is bad (too short or too simple). Re-enter the same password to continue and unlock the user account. The same passwd command is used to change a password. The root user can always change a user’s password. Users are prompted to enter the current password first.
### Default Settings
The default settings for a new user can be viewed and modified by using the -D option. Example:
```
useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```
The INACTIVE directive sets the number of days after a password expires until the account is locked. A value of 0 locks the account as soon as the password expires. A value of -1 disables the feature. Contents of the SKEL (`/etc/skel` by default) are copied to a new user’s home directory when the user account is created. Default settings are stored in `/etc/default/useradd`. The following options, used with –D, change the useradd command defaults:
-  **-b default_home:** The initial path prefix for a new user’s home directory
-  **-e default_expire_date:** The date on which the user account is disabled
-  **-f default_inactive:** The number of days after a password has expired before the account is locked
-  **-g default_group:** The group name or ID for a new user’s initial group
-  **-s default_shell:** The new user’s login shell
For example, to change a new user’s login shell to the Bourne shell, enter the following:
```
useradd –D –s /bin/sh user_name
```
### useradd Options
Several options are available to the useradd command to override default settings. The following are some of the more commonly used options:
-  **-c comment:** The new user’s GECOS information, such as full name
-  **-d home_dir:** The initial path prefix for a new user’s home directory
-  **-e expire_date:** The date (format YYYY-MM-DD) when the user account is disabled
-  **-g initial_group:** The group name or number of the user’s initial login group. The group name must exist. A group number must refer to an already existing group.
-  **-G group:** A list of secondary groups that the user is also a member of. Each group is separated from the next by a comma, with no intervening whitespace.
-  **-p passwd:** Set the new user’s password.
-  **-s shell:** The name of the user’s login shell
For example, to create a new username of “john”, and include the user’s name, and change the login shell to the C shell, enter the following:
```
useradd –c "John Smith" –s /bin/csh john
```
# Modifying or Deleting User Accounts
## usermod
Use the usermod command to modify an existing user account. The syntax is:
```
usermod user_name
```
One of the most common uses of the usermod command is to add a user to another (secondary) group. Use the –a and –G options followed by a comma-separated list of the secondary groups to add the user to. The following example lists the contents of /etc/group before and after modifying a user and adding them to a secondary group:
```
grep 1017 /etc/group 
students:x:1017:
```
```
usermod –aG 1017 mary 
grep 1017 /etc/group students:x:1017:mary
```
## userdel
Use the userdel command to delete a user account.
```
userdel user_name
```

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
