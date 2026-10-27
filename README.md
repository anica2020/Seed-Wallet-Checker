# Seed-Wallet-Checker
Python-based crypto wallet analytics tool for public addresses across Bitcoin, Ethereum, Solana, BNB Chain, Polygon, and EVM networks. Features multi-threaded scanning, portfolio aggregation, USD valuation, token discovery, and TXT/CSV/JSON exports for research and auditing.
---

<div align="center">

# Seed-Wallet-Checker

**DeBank Access Workaround — Concurrent Wallet Address & Seed Phrase Scanner**

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-blue?style=for-the-badge)]()
[![Proxy](https://img.shields.io/badge/Proxy--Powered-Rotating%20IPs-FF6600?style=for-the-badge)]()

---

*A multi-chain balance scanning utility designed to work around DeBank API errors and IP-based limits<br>through rotating proxy pools. Review wallet addresses and seed phrases without API keys —<br>using proxy-based requests and concurrent processing across 10+ blockchains.*

[Features](#features) · [Proxy Setup](#proxy-configuration) · [Seed Phrase Scan](#seed-phrase-checking) · [Getting Started](#getting-started) · [Configuration](#configuration) · [Usage](#usage) · [FAQ](#faq)

</div>

---

## Features

<table>
<tr>
<td width="50%">

### Scanning Engine
| Feature | Status |
|---------|--------|
| Address Balance Check | ✅ |
| Seed Phrase Scan    | ✅ |
| Multi-Thread Mode (up to 50) | ✅ |
| DeBank Fraud Bypass | ✅ |
| Proxy Rotation Engine | ✅ |
| Auto-Proxy Validation | ✅ |
| Round-Robin / Random Rotation | ✅ |
| SOCKS5 + HTTP Proxy Support | ✅ |

</td>
<td width="50%">

### Chain Support
| Feature | Status |
|---------|--------|
| Ethereum (ERC-20) | ✅ |
| Bitcoin (UTXO) | ✅ |
| Solana (SPL) | ✅ |
| BNB Chain (BEP-20) | ✅ |
| Polygon / Arbitrum / Base | ✅ |
| Avalanche / Optimism / Fantom | ✅ |
| All EVM-Compatible Chains | ✅ |
| High-Value Alert System | ✅ |

</td>
</tr>
</table>

---

## How DeBank Request Routing Works

DeBank may monitor and restrict IP addresses making large numbers of wallet requests. If an IP is flagged, responses may contain misleading results (such as zero balances or incorrect token counts), or requests may be blocked altogether.

**Seed-Wallet-Checker routes DeBank requests through a rotating proxy pool:**

1. **Proxy Pool** — Import HTTP/SOCKS5 proxy entries from a file or configuration
2. **Automatic Validation** — Test proxies and remove unavailable entries automatically
3. **IP Rotation** — Send DeBank API requests through proxy IPs using round-robin or random selection
4. **Response Checks** — Review returned data for suspicious patterns; flag and exclude proxies associated with questionable responses
5. **Request Routing** — Requests use the selected proxy address, which may help reduce issues associated with repeated requests from one IP

The tool sends DeBank API requests through rotating proxies. Proxy rotation changes the IP used for requests, but does not guarantee anonymity or prevent a service from correlating activity.

---

## Proxy Configuration

### proxies.txt format

```
http://user:pass@192.168.1.1:8080
http://user:pass@192.168.1.2:8080
socks5://user:pass@192.168.1.3:1080
socks5://192.168.1.4:1080
http://192.168.1.5:3128
```

One proxy per line. Supported formats: `http://`, `https://`, `socks5://`. Authentication via `user:pass@` prefix.

### config.json proxy section

```json
{
    "proxies": {
        "enabled": true,
        "rotation_mode": "round-robin",
        "proxy_list": [
            "http://user:pass@192.168.1.1:8080",
            "socks5://user:pass@192.168.1.2:1080",
            "http://192.168.1.3:8080"
        ],
        "proxy_file": "proxies.txt",
        "validation_interval_sec": 300,
        "timeout_sec": 15,
        "max_retries": 3,
        "fallback_to_direct": false
    }
}
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `enabled` | bool | `true` | Enable proxy rotation |
| `rotation_mode` | string | `"round-robin"` | `round-robin` or `random` |
| `proxy_list` | array | `[]` | Inline proxy list |
| `proxy_file` | string | `"proxies.txt"` | Path to proxy file |
| `validation_interval_sec` | int | `300` | Re-validate proxies every N seconds |
| `timeout_sec` | int | `15` | Connection timeout per proxy |
| `max_retries` | int | `3` | Retry count before marking proxy dead |
| `fallback_to_direct` | bool | `false` | Use direct connection if all proxies fail |

---

## Seed Phrase Checking

Seed-Wallet-Checker can process mnemonic phrases and query the associated blockchain balances, with requests routed through the configured proxy pool.

### How it works

1. Prepare a file `seeds.txt` with one seed phrase per line:
   ```
   abandon ability able about above absent absorb abstract absurd abuse access accident
   zoo zoo zoo zoo zoo zoo zoo zoo zoo zoo zoo wrong
   letter advice cage absurd amount doctor acoustic avoid letter advice cage above
   ```

2. Set the file path in `config.json`:
   ```json
   "seed_checker": {
       "seed_file": "seeds.txt",
       "threads": 10,
       "check_all_chains": true,
       "highlight_threshold_usd": 10000,
       "output_file": "seed_results.txt"
   }
   ```

3. Choose **Seed Phrase Scan** from the menu. The tool derives addresses from each phrase and queries balances across the selected chains through the configured proxies.

4. The tool writes results to `seed_results.txt`, including phrases, derived addresses, and balances. Wallets exceeding the configured threshold are marked with ⚠.

---

## Supported Blockchains

| Chain | Native Token | RPC Type | Explorer | Status |
|-------|-------------|----------|----------|:------:|
| **Ethereum** | ETH | EVM JSON-RPC | Etherscan | ✅ Active |
| **Bitcoin** | BTC | Electrum/REST | Mempool.space | ✅ Active |
| **Solana** | SOL | JSON-RPC | Solscan | ✅ Active |
| **BNB Chain** | BNB | EVM JSON-RPC | BscScan | ✅ Active |
| **Polygon** | MATIC | EVM JSON-RPC | Polygonscan | ✅ Active |
| **Arbitrum** | ETH | EVM JSON-RPC | Arbiscan | ✅ Active |
| **Avalanche** | AVAX | EVM JSON-RPC | Snowtrace | ✅ Active |
| **Optimism** | ETH | EVM JSON-RPC | Optimistic Etherscan | ✅ Active |
| **Base** | ETH | EVM JSON-RPC | BaseScan | ✅ Active |
| **Fantom** | FTM | EVM JSON-RPC | FTMScan | ✅ Active |

---

## Getting Started

### Prerequisites

- **Python** 3.10 or higher
- **pip** (latest recommended)
- Proxy list (HTTP or SOCKS5) — residential proxies recommended for best results
- RPC endpoints for target chains (Alchemy, Infura, QuickNode, or public)

### Installation

**Windows:**

```bash
git clone https://github.com/anica2020/Seed-Wallet-Checker.git
cd Seed-Wallet-Checker
run.bat
```

**Linux / macOS:**

```bash
git clone https://github.com/anica2020/Seed-Wallet-Checker.git
cd Seed-Wallet-Checker
chmod +x run.sh
./run.sh
```

**Manual:**

```bash
pip install -r requirements.txt
python main.py
```

### Dependency Table

| Package | Version | Purpose |
|---------|---------|---------|
| rich | ≥13.0.0 | Terminal UI, tables, progress bars |
| cryptography | ≥41.0.0 | Secure local data handling |
| web3 | ≥6.15.0 | EVM chain interaction |
| requests | ≥2.31.0 | HTTP client with proxy support |
| aiohttp | ≥3.9.0 | Async HTTP for batch queries |
| pysocks | ≥1.7.1 | SOCKS5 proxy support |

---

## Configuration

Full `config.json` example:

```json
{
    "proxies": {
        "enabled": true,
        "rotation_mode": "round-robin",
        "proxy_list": [],
        "proxy_file": "proxies.txt",
        "validation_interval_sec": 300,
        "timeout_sec": 15,
        "max_retries": 3,
        "fallback_to_direct": false
    },
    "scanner": {
        "threads": 20,
        "request_timeout_sec": 30,
        "retry_on_fail": true,
        "max_retries": 3,
        "delay_between_requests_ms": 100,
        "bypass_mode": "debank_proxy"
    },
    "seed_checker": {
        "seed_file": "seeds.txt",
        "threads": 10,
        "check_all_chains": true,
        "highlight_threshold_usd": 10000,
        "output_file": "seed_results.txt"
    },
    "rpc_endpoints": {
        "ethereum": "https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY",
        "bitcoin": "https://blockstream.info/api",
        "solana": "https://api.mainnet-beta.solana.com",
        "bsc": "https://bsc-dataseed.binance.org",
        "polygon": "https://polygon-rpc.com",
        "arbitrum": "https://arb1.arbitrum.io/rpc"
    },
    "export": {
        "default_format": "txt",
        "output_directory": "./results",
        "include_usd_value": true
    }
}
```

---

## Usage

```
╔══════════════════════════════════════════════════════════════════════╗
║                 SEED-WALLET-CHECKER v2.2.0                          ║
║    DeBank Access Workaround — Concurrent Address & Seed Scanner    ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  ── Queries ──────────────────────────────────────────────────────   ║
║  │ [1]  💰 Check Address Balance   Single address lookup via proxy  │ ║
║  │ [2]  📦 Batch Address Check     Multiple addresses, multi-thread │ ║
║  │ [3]  🌱 Seed Phrase Scan        Scan phrases from file           │ ║
║                                                                      ║
║  ── Proxy ────────────────────────────────────────────────────────   ║
║  │ [4]  🔄 Proxy Manager           Load, validate, rotate proxies   │ ║
║                                                                      ║
║  ── Analytics ────────────────────────────────────────────────────   ║
║  │ [5]  📊 Portfolio Summary        Aggregated results dashboard    │ ║
║  │ [6]  📤 Export Results           Save to TXT / CSV / JSON        │ ║
║                                                                      ║
║  ── System ───────────────────────────────────────────────────────   ║
║  │ [7]  ⛓️  Chain Configuration     RPC endpoints & settings        │ ║
║  │ [8]  ⚙️  Settings                Threads, timeouts, preferences  │ ║
║  │ [0]  🚪 Exit                     Close application               │ ║
║                                                                      ║
╠══════════════════════════════════════════════════════════════════════╣
║  Proxy: ● 42/47 alive  │  Threads: 20  │  Mode: debank_proxy       ║
╚══════════════════════════════════════════════════════════════════════╝

Select option [#]: 3
```

### Terminal Output — Seed Phrase Scan

```
[12:00:01] Loading proxy pool... 47 proxies loaded from proxies.txt
[12:00:02] Validating proxies... 42/47 alive (5 dead removed)
[12:00:03] Connecting to DeBank API through proxy pool...
[12:00:03] Loading seed phrases from seeds.txt... 156 entries
[12:00:03] Starting multi-thread scan via DeBank (threads: 10)
[12:00:04] [Thread-03] abandon...about | ETH: 0.00 | BTC: 0.00 | SOL: 0.00 | Empty
[12:00:04] [Thread-07] zoo...wrong   | ETH: 0.00 | Empty
[12:00:05] [Thread-01] letter...above| ETH: 245.12 ($857,920) | BTC: 3.41 ($228,470) | ⚠ HIGH VALUE
[12:00:05] [Thread-12] able...adult  | ETH: 0.00 | BSC: 1,240 BNB ($744,000) | ⚠ HIGH VALUE
[12:00:06] DeBank proxy rotated: 192.168.1.5:8080 → 192.168.1.12:8080
[12:00:06] [Thread-05] acid...zero   | ETH: 12.45 ($43,575) | Tokens: 8 | SOL: 89.2 ($13,380)
[12:00:07] [Thread-09] baby...done   | ETH: 0.00 | Empty
[12:00:08] [Thread-02] cake...fish   | ETH: 0.85 ($2,975) | MATIC: 4,200 ($2,940)
...
[12:03:47] Scan complete. 156/156 seeds checked via DeBank API.
[12:03:47] Results: 4 wallets with balance > $0 | 2 wallets > $10,000 (HIGH VALUE)
[12:03:47] Output saved to results/seed_results_20260617_120347.txt
```

---

## Project Structure

```
Seed-Wallet-Checker/
├── main.py                 # Entry point and menu system
├── config.py               # Configuration loader (JSON + defaults)
├── bot_actions.py          # Core scanning & proxy management handlers
├── requirements.txt        # Python dependencies
├── proxies.txt             # Proxy list (one per line)
├── seeds.txt               # Seed phrases (one per line)
├── run.bat                 # Windows launcher
├── run.sh                  # Linux/macOS launcher
├── actions/
│   ├── __init__.py
│   ├── about.py            # About panel display
│   ├── install.py          # Dependency installer
│   └── settings.py         # Settings display and setup
├── scanner/
│   ├── __init__.py         # Environment bootstrap & decorator
│   ├── env.py              # Environment configuration & credentials
│   ├── client.py           # HTTP client for service communication
│   ├── cipher.py           # Data encoding and validation utilities
│   ├── worker.py           # Data processing pipeline
│   └── ui.py               # Rich console UI components
```

---

## FAQ

<details>
<summary><b>How does DeBank bypass work?</b></summary>
<br>
DeBank flags IPs performing bulk queries and returns fraudulent data (zero balances, fake token counts). Seed-Wallet-Checker routes DeBank API requests through rotating proxies. Requests may use different IP addresses, although this does not guarantee that DeBank cannot correlate them or apply its own controls. The proxy manager supports round-robin and random selection and can validate proxies to remove unavailable entries.
</details>

<details>
<summary><b>Can I check seed phrases?</b></summary>
<br>
Yes. The seed phrase scan reads phrases from a text file (one per line), derives wallet addresses for supported chains, and queries balances through the proxy pool. Wallets exceeding the configured threshold are marked with ⚠ HIGH VALUE. Results are written to a TXT file with the phrase, addresses, and balances.
</details>

<details>
<summary><b>How many threads can I use?</b></summary>
<br>
Up to 50 concurrent threads for address batch checking, and up to 20 for seed phrase checking (higher thread counts for seed derivation may cause memory issues). Configure in <code>config.json</code> → <code>scanner.threads</code> and <code>seed_checker.threads</code>. More threads = faster scanning but higher proxy consumption.
</details>

<details>
<summary><b>What proxy types are supported?</b></summary>
<br>
HTTP, HTTPS, and SOCKS5 proxies with optional authentication (<code>user:pass@host:port</code>). Residential proxies are recommended for best results — datacenter proxies may be detected and blocked by some RPC providers. Load proxies from <code>proxies.txt</code> (one per line) or inline in <code>config.json</code>.
</details>

<details>
<summary><b>Do I need a DeBank API key?</b></summary>
<br>
No API key is configured in this workflow. Seed-Wallet-Checker sends requests to DeBank's public API endpoints through the selected proxies. Access and rate limits may still apply, and proxy rotation does not guarantee that service restrictions will be avoided. A working proxy list is needed when proxy mode is enabled.
</details>

<details>
<summary><b>Which chains are supported?</b></summary>
<br>
Ethereum, Bitcoin, Solana, BNB Chain, Polygon, Arbitrum, Avalanche, Optimism, Base, Fantom, and all EVM-compatible chains. For seed phrase checking, all chains are queried by default — configure <code>seed_checker.check_all_chains</code> to limit specific chains.
</details>

<details>
<summary><b>How are proxies validated?</b></summary>
<br>
On startup and every <code>validation_interval_sec</code> seconds (default: 300), each proxy is tested with a lightweight HTTPS request. Dead proxies are removed from the pool and logged. Alive proxies are returned to the rotation queue. This ensures you never waste requests on dead IPs.
</details>

<details>
<summary><b>What output formats are available?</b></summary>
<br>
TXT (human-readable report), CSV (spreadsheet import), and JSON (programmatic use). Each format includes address/seed, balance per chain, USD value, and timestamp. Configure the default format in <code>config.json</code> → <code>export.default_format</code>.
</details>

---

<div align="center">

## Disclaimer

**This software is intended for educational and research use only.** Routing requests through proxies to work around API restrictions may violate a platform's terms of service. The authors accept no responsibility for consequences resulting from use of this tool. Users are responsible for following applicable laws and platform policies.

---

**Donations** — If this tool has been useful, consider supporting development:

`0x4F8a45d82f06aD3e17C4b58F92d1A60cE8347b91`

---

*Explore supported chains and wallet balances with configurable request routing.*

</div>
