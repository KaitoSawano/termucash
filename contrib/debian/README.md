
Debian
====================
This directory contains files used to package termucashd/termucash-qt
for Debian-based Linux systems. If you compile termucashd/termucash-qt yourself, there are some useful files here.

## termucash: URI support ##


termucash-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install termucash-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your termucash-qt binary to `/usr/bin`
and the `../../share/pixmaps/termucash128.png` to `/usr/share/pixmaps`

termucash-qt.protocol (KDE)

