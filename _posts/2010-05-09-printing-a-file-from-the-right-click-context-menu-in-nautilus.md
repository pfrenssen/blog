---
layout: post
title: Printing a file from the right-click context menu in Nautilus
created: 1273426898
categories:
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob printing
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob printing
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob printing
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob printing
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob printing
- !ruby/string:Sequel::SQL::Blob gnome
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob printing
---
By default, Gnome does not provide a print option in the right-click menu of the file browser. Luckily it is very easy to add more functions to this menu.

Nautilus will look into the `~/.gnome2/nautilus-scripts` folder and add the files it finds there to the 'scripts'-entry in the context menu that pops up whenever your right-click a file.

Create a file called `print.sh` in this folder and paste the following script in it:

```shell
#!/bin/bash
#
# print.sh
#
# Print files from the right-click context menu in Nautilus.
# Place this script in ~/.gnome2/nautilus-scripts.

# The printer to use (as shown in the Printer Configuration
# gui or in /etc/cups/printers.conf).
printer=HP-Color-LaserJet-CP1215

echo "$NAUTILUS_SCRIPT_SELECTED_FILE_PATHS" | while read file
	do
		lpr -P "$printer" "$file"
done
exit 0
```

Replace "HP-Color-LaserJet-CP1215" with your own printer model. You can find the exact model name in the CUPS printer configuration GUI (Ubuntu: System > Administration > Printing).

Save the file, and make it executable:

```shell
$ sudo chmod u+x ~/.gnome2/nautilus-scripts/print.sh
```

From now on you can easily print multiple files directly from the file manager without having to open them first.


Reference:
<a href="http://aloshbennett.wordpress.com/2008/08/12/ubuntu-printing-with-a-mouse-right-click/">Ubuntu: Printing with a mouse right-click</a> - the inspiration for this article.
