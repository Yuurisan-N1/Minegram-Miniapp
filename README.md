<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=Minegram%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Daily%20Check-in%20%7C%20Mining%20Code%20%7C%20Miners%20%7C%20Tasks%20%7C%20Multi-Account&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Auto+Daily+Check-in+%7C+Earn+DUST;Auto+Fetch+%26+Submit+Mining+Code+from+Channel;Auto+Claim+Mining+Trickles+When+Ready;Auto+Claim+%2F+Activate+%2F+Buy+Best+Miner;Auto+Complete+All+Eligible+Tasks+%7C+Earn+DUST;Per-Account+Device+Fingerprint+Cached"/>

<p>
  <img alt="platform" src="https://img.shields.io/badge/Platform-Mining%20GRAM%20Miniapp-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>Minegram Bot</b> is a full automation bot for the Mining GRAM Telegram Miniapp.<br/>
  It handles the complete cycle: claiming the daily check-in, fetching and submitting the latest mining code from the official channel, claiming all ready mining trickles, managing owned miners by claiming done ones and activating idle ones, buying the most affordable new miner if budget allows, and completing all eligible social tasks, all running automatically across multiple accounts with per-account device fingerprint caching, proxy support, and a live countdown between cycles.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Ruby `3.0+` (only needed to run the downloader script)

---

## Installation

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/Minegram-Miniapp.git
cd Minegram-Miniapp
```

**Install downloader dependencies:**

```bash
gem install yuurisan
```

**Download the binary for your platform:**

```bash
ruby bot.rb
```

The script shows a numbered menu:

```
1. Minegram Linux ARM64
2. Minegram Linux AMD64
3. Windows (PowerShell / CMD)
```

Enter the number for your platform. The binary downloads with a live progress bar and is set to executable automatically on Linux.

Or download manually from the Releases page:
https://github.com/Yuurisan-N1/Minegram-Miniapp/releases/latest

| File | Platform |
|---|---|
| `Minegram.exe` | Windows x86_64 |
| `Minegram-linux-amd64` | Linux x86_64 |
| `Minegram-linux-arm64` | Linux ARM64 |

**Linux after manual download:**

```bash
chmod +x Minegram-linux-amd64
```

Place the binary in the same folder as your `data.txt`, `proxy.txt`, `config.json`, and `device.json` before running.

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with Telegram WebApp `initData` for each account, one per line. The `tgWebAppData=` prefix is stripped automatically if present:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456
```

> `initData` can be obtained from the browser DevTools when opening Mining GRAM on Telegram Web.

### 2. Proxy (proxy.txt)

Fill `proxy.txt` with proxies, one per line (optional, leave empty to run without proxy):

```
host:port
host:port:user:pass
http://user:pass@host:port
```

Proxies are assigned to accounts by index in round-robin order.

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. If `config.json` is missing, the bot falls back to a default of `3600` seconds.

---

## Running the Bot

**Linux:**

```bash
./Minegram-linux-amd64
```

**Linux ARM64:**

```bash
./Minegram-linux-arm64
```

**Windows:**

```bash
.\Minegram.exe
```

Press `Ctrl+C` at any time to stop the bot cleanly.

---

## Features

### Daily Check-in
The bot fetches the check-in status and checks the `alreadyCheckedInToday` flag. If not yet claimed, it submits the check-in and logs the DUST credited. If already claimed today, it is skipped.

### Auto Mining Code
The bot fetches the official Mining GRAM Telegram channel page and parses all mining code patterns from the latest posts. Up to 3 codes are tried in order from newest to oldest. For each code, it submits a mine request and logs the DUST credited on success. If the code is already redeemed, the capacity is full, or conditions are not met, the reason is logged and the next code is tried.

### Mining Trickle Claim
The bot fetches all pending mining trickles for the account. For each trickle that is ready to claim, it reads the DUST balance before and after the claim to verify the credit, then logs the trickle name and DUST earned. Trickles still unlocking are logged and skipped.

### Auto Miners
The bot fetches all owned miners and their states. For each miner in `done` state, it claims the reward and verifies the DUST credit before logging the amount. For miners in `inventory` state, it activates them. Miners still running are logged and skipped. After processing owned miners, the bot checks the miner catalog and buys the cheapest miner the account can currently afford. Each buy, activation, and claim is logged by miner name.

### Auto Tasks
The bot fetches all social tasks. Tasks already completed, requiring proof, boost-type, and join-type are skipped automatically. For tasks with a timer, the bot sends a start request then waits the required duration with a live countdown before claiming. For all tasks, the DUST balance is read before and after the claim to verify the credit. Each verified task logs the DUST earned.

### Referral Report
At the end of each account cycle, the bot fetches the referral stats and logs the total friend count and total DUST earned from referrals.

### Per-Account Device Fingerprint
Each account gets a deterministic device profile derived from the account ID, including a User-Agent from a pool of 3 devices (Samsung SM-S918B, Pixel 7, Redmi Note 11), a random screen resolution from 5 options, a timezone offset from 8 options, and a WebGL renderer from 4 options. The profile and its SHA256 fingerprint are saved to `device.json`. If the fingerprint is already cached, it is reused on all subsequent cycles.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. DUST balance and GRAM wallet balance are logged at sign-in for each account. The cycle number is logged at the start and end of each round.

### Proxy Support
Proxies are loaded from `proxy.txt` and assigned to accounts by position in round-robin order. Proxy credentials are masked in log output. Running without proxies is fully supported.

### Auto Countdown
After all accounts complete a cycle, the bot displays a live `HH:MM:SS` countdown until the next cycle starts.

---

## File Structure

```text
Minegram-Miniapp/
├── Minegram.exe             # Windows binary
├── Minegram-linux-amd64     # Linux x86_64 binary
├── Minegram-linux-arm64     # Linux ARM64 binary
├── bot.rb                   # Interactive downloader script
├── config.json              # Sleep duration between cycles
├── data.txt                 # Account initData, one per line
├── proxy.txt                # Proxy list, one per line (optional)
├── device.json              # Per-account device fingerprint cache (auto-generated)
├── LICENSE                  # License file
└── utils/
    └── banner.rb            # Banner using yuurisan module
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>