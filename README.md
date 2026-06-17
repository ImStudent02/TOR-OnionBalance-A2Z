# TOR-OnionBalance-A2Z
Beginner level TOR Onion Balance tutorial in simple steps.

# OnionBalance v3 Master Guide (Linux, Windows, macOS)

A complete practical guide for deploying OnionBalance v3 with Tor Hidden Services.

This guide covers:

* Fresh OnionBalance deployment
* Migrating an existing onion service
* Preserving an existing onion address
* Linux
* Windows
* macOS
* Tor Expert Bundle users
* Multiple backend hidden services
* Troubleshooting and best practices

---

# What is OnionBalance?

OnionBalance allows a single public `.onion` address to be backed by multiple hidden service instances.

Benefits:

* High availability
* Load distribution
* Redundancy
* Easier scaling
* Protection against single-node failures

Users only see one onion address while traffic is distributed across multiple backend hidden services.

---

Requirements: TOR, Python, OnionBalance

TOR : https://archive.torproject.org/tor-package-archive/torbrowser/

Python : https://www.python.org/downloads/

# Architecture Overview

Without OnionBalance:

```text
Users
  |
myservice.onion
  |
Tor Hidden Service
  |
Application
```

With OnionBalance:

```text
Users
  |
myservice.onion
  |
OnionBalance
  |
+------+------+------+
|      |      |      |
hs1    hs2    hs3    ...
  \      |    /
    Application
```

The public onion remains unchanged while traffic is distributed across multiple backend hidden services.

---

# Prerequisites

# Linux Installation

Ubuntu / Debian:

```bash
sudo apt update
sudo apt install tor python3 python3-pip
pip3 install onionbalance
```

Fedora:

```bash
sudo dnf install tor python3 python3-pip
pip3 install onionbalance
```

Arch Linux:

```bash
sudo pacman -S tor python python-pip
pip install onionbalance
```

---

# macOS Installation

Install Homebrew if needed:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Install Tor:

```bash
brew install tor
```

Install OnionBalance:

```bash
pip3 install onionbalance
```

---

# Windows Installation

Windows users should use:

* Tor Expert Bundle
* Python 3.x
* OnionBalance

## Step 1 — Install Python

Download:

```text
https://www.python.org/downloads/
```

During installation:

✅ Check:

```text
Add Python to PATH
```

Verify:

```cmd
python --version
pip --version
```

---

## Step 2 — Install Tor Expert Bundle

Download:

```text
https://www.torproject.org/download/tor/
```

Extract to:

```text
C:\Tor
```

Example:

```text
C:\Tor\tor.exe
```

Verify:

```cmd
C:\Tor\tor.exe --version
```

---

## Step 3 — Install OnionBalance

```cmd
pip install onionbalance
```

Verify:

```cmd
onionbalance --version
```

---

# Verify Installation

Linux/macOS:

```bash
tor --version
onionbalance --version
```

Windows:

```cmd
C:\Tor\tor.exe --version
onionbalance --version
```

---

# Recommended Port Layout

Avoid common Tor Browser ports.

Use:

```text
Backend 1
SOCKS   19051
CONTROL 19061

Backend 2
SOCKS   19052
CONTROL 19062

Backend 3
SOCKS   19053
CONTROL 19063
```

Avoid:

```text
9050
9051
9150
9151
```

These are commonly used by Tor and Tor Browser.

---

# Directory Structure

Linux/macOS:

```text
project/
├── hidden/
│   ├── hs1/
│   ├── hs2/
│   └── hs3/
│
├── tor-data/
│   ├── tor1/
│   ├── tor2/
│   └── tor3/
│
├── torrc-1
├── torrc-2
├── torrc-3
└── config.yaml
```

Windows:

```text
C:\OnionBalance\
│
├── hidden\
│   ├── hs1\
│   ├── hs2\
│   └── hs3\
│
├── tor-data\
│   ├── tor1\
│   ├── tor2\
│   └── tor3\
│
├── torrc-1
├── torrc-2
├── torrc-3
└── config.yaml
```

---

# Part 1 — Fresh OnionBalance Deployment

## Create Backend Directories

### Linux/macOS

```bash
mkdir -p hidden/hs1
mkdir -p hidden/hs2
mkdir -p hidden/hs3

mkdir -p tor-data/tor1
mkdir -p tor-data/tor2
mkdir -p tor-data/tor3
```

### Windows

```cmd
mkdir hidden\hs1
mkdir hidden\hs2
mkdir hidden\hs3

mkdir tor-data\tor1
mkdir tor-data\tor2
mkdir tor-data\tor3
```

---

# Create Tor Configurations

## torrc-1

```ini
SocksPort 19051
ControlPort 19061

DataDirectory tor-data/tor1

HiddenServiceDir hidden/hs1
HiddenServicePort 80 127.0.0.1:3000
```

## torrc-2

```ini
SocksPort 19052
ControlPort 19062

DataDirectory tor-data/tor2

HiddenServiceDir hidden/hs2
HiddenServicePort 80 127.0.0.1:3000
```

## torrc-3

```ini
SocksPort 19053
ControlPort 19063

DataDirectory tor-data/tor3

HiddenServiceDir hidden/hs3
HiddenServicePort 80 127.0.0.1:3000
```

---

# Start Tor Instances

## Linux/macOS

Open three terminals:

```bash
tor -f torrc-1
```

```bash
tor -f torrc-2
```

```bash
tor -f torrc-3
```

---

## Windows

Open three Command Prompt windows:

```cmd
C:\Tor\tor.exe -f torrc-1
```

```cmd
C:\Tor\tor.exe -f torrc-2
```

```cmd
C:\Tor\tor.exe -f torrc-3
```

---

Wait several minutes.

Tor will generate:

```text
hidden/hs1/hostname
hidden/hs2/hostname
hidden/hs3/hostname
```

Save all three backend onion addresses.

Example:

```text
abc111.onion
abc222.onion
abc333.onion
```

---

# Generate OnionBalance Configuration

```bash
onionbalance-config
```

Generated files:

```text
config.yaml
<public-onion>.key
```

Example:

```text
abcdefghijklmnopqrstuvwxyz.key
```

Public onion:

```text
abcdefghijklmnopqrstuvwxyz.onion
```

This is the address users will access.

---

# Configure Backends

Edit:

```text
config.yaml
```

Example:

```yaml
services:
- instances:
  - address: abc111.onion
    name: node1

  - address: abc222.onion
    name: node2

  - address: abc333.onion
    name: node3

  key: abcdefghijklmnopqrstuvwxyz.key
```

---

# Start OnionBalance

Linux/macOS:

```bash
onionbalance -c config.yaml
```

Windows:

```cmd
onionbalance -c config.yaml
```

Wait several minutes for descriptor publication.

---

# Test

Visit:

```text
public-onion.onion
```

Never use backend onions directly.

---

# Part 2 — Migrate Existing Onion Service

Goal:

Keep your existing onion address.

Example:

```text
myoldservicexxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion
```

No client updates required.

---

# Existing Structure

```text
hidden/DC/
├── hostname
├── hs_ed25519_secret_key
└── hs_ed25519_public_key
```

---

# Verify Existing Address

Linux/macOS:

```bash
cat hidden/DC/hostname
```

Windows:

```cmd
type hidden\DC\hostname
```

Output:

```text
myoldservicexxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion
```

---

# Create Backend Services

Create:

```text
hidden/hs1
hidden/hs2
hidden/hs3
```

using the same procedure described in Part 1.

---

# Generate OnionBalance Configuration

```bash
onionbalance-config
```

Generated:

```text
config.yaml
random-generated.key
```

---

# Replace Generated Key

Delete:

```text
random-generated.key
```

Copy:

```text
hidden/DC/hs_ed25519_secret_key
```

into the OnionBalance directory.

Rename it:

```text
myoldservicexxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.key
```

---

# Configure Backends

```yaml
services:
- instances:
  - address: backend1.onion
    name: node1

  - address: backend2.onion
    name: node2

  - address: backend3.onion
    name: node3

  key: myoldservicexxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.key
```

---

# Disable Original Hidden Service

Stop using:

```ini
HiddenServiceDir hidden/DC
```

The identity is now managed by OnionBalance.

Do not run both simultaneously.

---

# Start OnionBalance

Linux/macOS:

```bash
onionbalance -c config.yaml
```

Windows:

```cmd
onionbalance -c config.yaml
```

Wait several minutes.

---

# Result

Users continue connecting to:

```text
myoldservicexxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion
```

while OnionBalance distributes requests across:

```text
hs1
hs2
hs3
```

behind the scenes.

---

# Common Mistakes

## Wrong Key

Use:

```text
hidden/DC/hs_ed25519_secret_key
```

when migrating.

---

## Lost Secret Key

The onion address cannot be recovered.

A new onion service must be created.

Always keep backups.

---

## Running Old Service and OnionBalance Together

Incorrect:

```text
DC
hs1
hs2
hs3
```

Correct:

```text
OnionBalance
hs1
hs2
hs3
```

---

## Clients Using Backend Onions

Users should only know:

```text
public.onion
```

Backend addresses should remain private.

---

## Descriptor Propagation Delay

Changes are not immediate.

Wait several minutes after startup before testing.

---

# Troubleshooting

## Check Tor Processes

Linux:

```bash
ps aux | grep tor
```

Windows:

```cmd
tasklist | findstr tor
```

---

## Check Listening Ports

Linux:

```bash
netstat -tulpn | grep 190
```

Windows:

```cmd
netstat -ano | findstr 190
```

---

## OnionBalance Logs

```bash
onionbalance -v info -c config.yaml
```

---

## Debug Mode

```bash
onionbalance -v debug -c config.yaml
```

---

## Verify Backend Hostnames

Check:

```text
hidden/hs1/hostname
hidden/hs2/hostname
hidden/hs3/hostname
```

If these files do not exist, Tor has not finished generating the hidden services.

---

# Security Recommendations

* Keep backend onion addresses private.
* Backup all secret keys.
* Use separate servers for backend instances when possible.
* Restrict access to Tor control ports.
* Monitor logs regularly.
* Never expose hidden service directories publicly.

---

# Final Notes

OnionBalance does not proxy traffic.

Instead, it publishes descriptors that allow clients to reach multiple backend hidden services using a single public onion address.

This improves:

* Availability
* Reliability
* Redundancy
* Scalability

while preserving the same public onion identity.

For production deployments, use multiple backend servers located on different machines or geographic regions to maximize resilience and uptime.

