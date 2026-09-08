# Termucash Snap Packaging

Commands for building and uploading a Termucash Core Snap to the Snap Store. Anyone on amd64 (x86_64), arm64 (aarch64), or i386 (i686) should be able to build it themselves with these instructions. This would pull the official Termucash binaries from the releases page, verify them, and install them on a user's machine.

## Building Locally
```
sudo apt install snapd
sudo snap install --classic snapcraft
sudo snapcraft
```

### Installing Locally
```
snap install \*.snap --devmode
```

### To Upload to the Snap Store
```
snapcraft login
snapcraft register termucash-core
snapcraft upload \*.snap
sudo snap install termucash-core
```

### Usage
```
termucash-unofficial.cli # for termucash-cli
termucash-unofficial.d # for termucashd
termucash-unofficial.qt # for termucash-qt
termucash-unofficial.test # for test_termucash
termucash-unofficial.tx # for termucash-tx
```

### Uninstalling
```
sudo snap remove termucash-unofficial
```