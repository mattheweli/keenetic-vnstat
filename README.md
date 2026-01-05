# keenetic-vnstat
A Bash script designed for embedded systems (like Keenetic routers with Entware) that generates visual network traffic reports using `vnstati`.

It creates specific PNG graphs for a chosen network interface (Summary, Daily, Monthly, Top 10) and generates a dedicated HTML dashboard for easy viewing.

## Features

- 🎯 **Per-Interface Support:** Can be run multiple times for different interfaces (e.g., WAN `ppp0` and LAN `br0`).
- 🖼️ **Image Merging:** Uses ImageMagick (if installed) to stack all graphs into a single, clean overview image.
- 📄 **Auto HTML:** Generates a `INTERFACE-vnstat.html` file with auto-refresh (every 10 mins).

## Prerequisites

You need Entware installed.
```bash
opkg update
opkg install vnstat2 vnstati2 imagemagick-convert
```
Note: imagemagick-convert is optional but recommended for the merged view.

Ensure the vnstat daemon is running and collecting data:
```bash
/opt/etc/init.d/S26vnstatd start
```

## Installation

1. **Download the script** to /opt/bin:
```bash
curl -L -o /opt/bin/vnstat-gen.sh [https://raw.githubusercontent.com/YOUR_USERNAME/vnstat-dashboard-gen/main/vnstat-gen.sh](https://raw.githubusercontent.com/YOUR_USERNAME/vnstat-dashboard-gen/main/vnstat-gen.sh)
chmod +x /opt/bin/vnstat-gen.sh
```


2. **Web Server Setup** (Lighttpd) Ensure your web server points to /opt/var/www. If you use Lighttpd (default on Entware):

* Config file: /opt/etc/lighttpd/lighttpd.conf
* Document root: server.document-root = "/opt/var/www"
* Port: Usually 81.

## Usage
Run the script specifying the interface you want to graph:

```bash
# Syntax: vnstat-gen.sh <INTERFACE_NAME>
/opt/bin/vnstat-gen.sh ppp0
```

This will create:

* /opt/var/www/ppp0-vnstat.html
* /opt/var/www/vnstat_ppp0_all.png

You can view it at: http://YOUR_ROUTER_IP:81/ppp0-vnstat.html

## Automation (Cron)
To keep graphs updated, add a cron job. You can add one line for each interface you want to monitor.

1. Open Crontab:
```bash
nano /opt/etc/crontab
```

2. Add lines to update every 30 minutes:
```bash
# Update WAN stats
*/30 * * * * root /opt/bin/vnstati-allpng ppp0 > /dev/null 2>&1

# Update LAN stats (optional)
*/30 * * * * root /opt/bin/vnstati-allpng.sh br0 > /dev/null 2>&1
```
