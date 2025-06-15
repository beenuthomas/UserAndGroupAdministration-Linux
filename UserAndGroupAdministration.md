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
| Field  | Description |
| ------------- | ------------- |
| test  | Username  |
| x  | Indicates that shadow passwords are used  |
| 1001  | UID, these begin with 1000 and increment by 1 for each newly added user. UIDs below 1000 are reserved for system use.  |
| 1001  | GID of the user’s primary group. These begin with 1000 and increment by 1 for each new group. Users can belong to more than one group.  |
| test user  | GECOS (General Electric Comprehensive Operating System) information, used only for informational purposes such as full name  |
| /home/test  | Home directory for this user  |
| /bin/bash  | Default shell for this user  |

## /etc/shadow
With shadow passwords, a new entry is automatically added to /etc/shadow when a new user is created. This file can be viewed only by root. Here is an example of an entry in this file:
```
tail -1 /etc/shadow
test:$6$XBCDBQ...:17610:0:99999:7:::
```
| Field  | Description |
| ------------- | ------------- |
| test  | Username  |
| $6$XBCDBQ...  | Hashed password value (partial value shown). The plain text password itself is not stored on the disk. An algorithm creates a unique string from a password.  |
| 17610  | Number of days since password has changed (counted in days since Jan 1, 1970).  |
| 0  | Number of days that need to pass before the password must be changed by the user.  |
| 99999  | Maximum number of days since the password changed that the password can be used. After this amount of days, the password must be changed by the user.  |
| 7  | Number of days before expire date that the user is warned about the pending password change policy. If the password is not changed after this number of days, the user account is locked.  |

The next field is empty but is used to store the last date when the account is locked (counted in days since Jan 1, 1970). The last field is also empty but is not used.
## /etc/group
Because Oracle Linux uses a UPG scheme, a new entry is automatically created in /etc/group when a new user is added. The group name is the same as the username. Here is an example of an entry in this file:
```
tail -1 /etc/group
test:x:1000:test
```
| Field  | Description |
| ------------- | ------------- |
| test  | Username  |
| x  | Indicates that shadow passwords are used  |
| 1000  | GID  |
| test  | List of users that are members of the group  |
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
groups user_name
```
# Password Configuration
Password aging requires users to change their passwords periodically. Use the chage command to configure password expiration. The syntax is:
```
chage [options] user_name
```
Password aging information is stored in the /etc/shadow file.

# Task Commands
1. Create users and Groups
2. Set Passwords and Apply Password Policies
3. Sudo Access
4. Account expiry and lock
5. Verification
6. Cleanup

## Create users and Groups
```
root@beenu:~# groupadd engineers                                                         #created new group called engineers
root@beenu:~# useradd -m -s /bin/bash -g engineers -c "Developer one" devuser1           #add user named devuser1 to group engineers,set /bin/bash as default shell and set a comment
root@beenu:~# mkdir /customhome                                                          #created /customhome directory
root@beenu:~# useradd -m -d /customhome/devuser2 -s /bin/bash -g engineers devuser2      #add devuser2 to group engineers, with a custom home directory
root@beenu:~# groups devuser1           
devuser1 : engineers
root@beenu:~# groups devuser2
devuser2 : engineers

```

## Set Passwords and Apply Password Policies
```
root@beenu:~# passwd devuser1                       #set password
New password:
Retype new password:
passwd: password updated successfully
root@beenu:~# passwd devuser2
New password:
Retype new password:
passwd: password updated successfully
root@beenu:~# chage -m 2 -M 45 -W 7 devuser1        #change password aging policies
```

## Sudo Access
```
root@beenu:~# usermod -aG sudo devuser1
```

## Account Expiry and Lock
```
root@beenu:~# chage -E 2025-06-28 devuser2                                                   #set account expiry date
root@beenu:~# cat /etc/passwd | grep devuser
devuser1:x:1001:1001:Developer one:/home/devuser1:/bin/bash
devuser2:x:1002:1001::/customhome/devuser2:/bin/bash
root@beenu:~# chage -l devuser1
Last password change                                    : Jun 13, 2025
Password expires                                        : Jul 28, 2025
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 2
Maximum number of days between password change          : 45
Number of days of warning before password expires       : 7
root@beenu:~# sudo -l -U devuser1
Matching Defaults entries for devuser1 on beenu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User devuser1 may run the following commands on beenu:
    (ALL : ALL) ALL
root@beenu:~# usermod -L devuser2                                                             #lock devuser2 account temporarily
```

## Verification
```
root@beenu:~# groups devuser1
devuser1 : engineers sudo
root@beenu:~# groups devuser2
devuser2 : engineers
root@beenu:~# sudo -l -U devuser1
Matching Defaults entries for devuser1 on beenu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User devuser1 may run the following commands on beenu:
    (ALL : ALL) ALL
root@beenu:~# chage -l devuser1
Last password change                                    : Jun 13, 2025
Password expires                                        : Jul 28, 2025
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 2
Maximum number of days between password change          : 45
Number of days of warning before password expires       : 7
root@beenu:~# chage -l devuser2
Last password change                                    : Jun 13, 2025
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : Jun 28, 2025
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7

```
## Cleanup
```
root@beenu:~# usermod -U devuser2
root@beenu:~#
root@beenu:~# userdel -r devuser1
userdel: devuser1 mail spool (/var/mail/devuser1) not found
root@beenu:~# userdel -r devuser2
userdel: devuser2 mail spool (/var/mail/devuser2) not found
root@beenu:~# groupdel engineers
root@beenu:~# groups devuser1
groups: ‘devuser1’: no such user
root@beenu:~# cat /etc/passwd | grep devuser
root@beenu:~#
```
