# tor-socks-pool.sh

A Bash script to launch and manage a pool of Tor-based SOCKS5 proxies on local ports, with country-specific exit nodes and automatic periodic IP rotation.

## Purpose

This script is designed to serve as a V2Ray outbound . Traffic from your V2Ray client flows into Xray core, then is routed through multiple Tor SOCKS5 proxies (e.g., UK, US, DE) on local ports. Each port represents a separate Tor exit node with configurable IP rotation intervals. This setup enhances your server security.

**Workflow:**

```
User V2Ray -> Xray Core -> Tor SOCKS5 (UK, US, DE) -> Internet
```

## Features

* Launch multiple Tor SOCKS5 instances with specified exit-node countries
* Assign each instance to a distinct local port for V2Ray outbound
* Automatic IP rotation using Tor's NEWNYM signal at configurable intervals
* Improved security and reduced server fingerprinting

## Prerequisites

* **tor**
* **tmux** (for persistent sessions)

## Installation

```bash
# Clone this repository
git clone https://github.com/paulGUZU/tor-socks-pool.git
cd tor-socks-pool

# Make the script executable
chmod +x tor_socks_pool.sh

```

### Running in tmux

To keep the proxies running after you close your terminal, use tmux (or screen):

```bash
tmux new -s tor-pool "./tor_socks_pool.sh -l UK,US,DE -p 1010,1020,1030 -i 5"
```

Then detach from the session with `Ctrl+B D`. You can reattach later with:

```bash
tmux attach -t tor-pool
```

## Usage

```bash
./tor_socks_pool.sh -l UK,US,DE -p 1010,1020,1030 -i 5
```

* `-l` Comma-separated list of ISO country codes for exit nodes (e.g., `UK,US,DE`)
* `-p` Comma-separated list of local SOCKS5 ports (one port per country)
* `-i` Interval in minutes to rotate IPs via Tor's NEWNYM signal

## Example

Launch three Tor proxies with exit nodes in the UK, US, and Germany, rotating every 10 minutes:

```bash
./tor_socks_pool.sh -l UK,US,DE -p 1010,1020,1030 -i 10
```

Use these ports in your V2Ray outbound configuration to distribute traffic across different exit nodes.

## Checking Current IP

To verify the current exit IP of a specific tunnel:

```bash
curl --socks5-hostname localhost:1010 https://api.ipify.org
```

## Troubleshooting

* Ensure Tor is installed and running without errors.
* Verify that required control and SOCKS ports are not occupied by other services.
* Inspect individual Tor logs in `/tmp/tor_instance_<idx>/tor.log` for details.

## TODO

* Provide a systemd service unit for automatic startup on boot
* Add IPv6 exit node support
* Implement graceful shutdown handling
* Add logging rotation for Tor instances

