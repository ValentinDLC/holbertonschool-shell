📂 README — Shell Permissions (Linux/Unix)

🧾 Introduction

This document explains the basics of file and directory permissions in Unix/Linux systems. It covers how to read, understand, and modify permissions using the Shell. Understanding permissions is critical for maintaining security and proper access control in any Linux environment.

📌 Permission Structure

Each file or directory has a set of permissions. You can view them using:

ls -l
Example output:

-rwxr-xr--
Breakdown:
Position	Meaning
1	File type (- = file, d = dir, l = link)
2-4	Owner (user) permissions
5-7	Group permissions
8-10	Others (world) permissions
Permission Characters:
Symbol	Meaning
r	Read
w	Write
x	Execute
-	No permission
🔧 Changing Permissions

Using chmod
chmod [permissions] [file]
Symbolic Mode:

chmod u+x file      # Add execute to user
chmod g-w file      # Remove write from group
chmod o=r file      # Set read-only for others
Numeric Mode:

Number	Permissions	Binary
0	---	000
1	--x	001
2	-w-	010
3	-wx	011
4	r--	100
5	r-x	101
6	rw-	110
7	rwx	111
Example:

chmod 755 script.sh   # rwxr-xr-x
👤 Ownership

View ownership:
ls -l
Shows the owner and group of the file.

Change owner:
chown username file
chown username:group file
🔒 Special Permissions

Permission	Symbol	Description
Setuid	s on user execute	Run as file owner
Setgid	s on group execute	Run as group
Sticky bit	t on others execute	Only owner can delete (e.g., /tmp)
Apply with:

chmod u+s file    # setuid
chmod g+s file    # setgid
chmod +t dir      # sticky bit
✅ Best Practices

Never give 777 unless absolutely necessary.
Use the least privilege principle.
Regularly audit file permissions with find and ls.
🧠 Quick Tips

Use umask to set default permissions for new files.
Use stat file for detailed file metadata.
Combine chown and chmod in scripts to automate permission management.
📚 References

man chmod
man chown
man umask
