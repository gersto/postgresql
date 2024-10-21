## Virtualbox unter Debian

Download unter: [https://www.virtualbox.org/wiki/Linux_Downloads](https://www.virtualbox.org/wiki/Linux_Downloads)

Edit /etc/apt/sources.list:
```python
deb http://deb.debian.org/debian/ bookworm contrib main non-free-firmware non-free
deb http://deb.debian.org/debian/ bookworm-updates contrib main non-free-firmware non-free
deb http://deb.debian.org/debian/ bookworm-backports contrib main non-free-firmware non-free
```

Packages installieren:
```python
apt-get install libxcb-cursor0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-render-util0
```

Software installieren:
```python
dpkg -i virtualbox-7.1_7.1.4-165100~Debian~bookworm_amd64.deb
```

ifconfig package installieren:
```python
apt-get install net-tools
```
