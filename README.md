# Packet Analyzer – DPI Engine

A deep packet inspection (DPI) tool built in C++ that captures and analyzes network traffic from PCAP files. It parses Ethernet, IP, and TCP headers, extracts TLS SNI and HTTP Host fields to identify which application or domain a connection belongs to, and supports filtering and blocking flows by IP, app, or domain.

---

## Features

- Parses Ethernet / IP / TCP packet headers
- Extracts TLS SNI from Client Hello messages to identify domains (e.g. YouTube, Facebook)
- Falls back to HTTP Host header for unencrypted traffic
- Blocks or filters flows by IP address, app name, or domain
- Outputs filtered traffic to a new PCAP file
- Includes a multi-threaded version with a load-balancer → fast-path → output-writer pipeline

---

## Project Structure

```
packet_analyzer/
├── include/          # Header files (.h)
├── src/              # Source files (.cpp)
├── CMakeLists.txt    # Build configuration
├── dpi_engine        # Compiled binary (Linux)
├── test_dpi.pcap     # Sample input PCAP for testing
├── output.pcap       # Filtered output PCAP
└── generate_test_pcap.py  # Script to generate test traffic
```

---

## How It Works

1. Reads a `.pcap` file using **libpcap**
2. Parses each packet's Ethernet → IP → TCP headers
3. Inspects the TCP payload for TLS Client Hello — extracts the **SNI field** to get the domain name
4. For HTTP traffic, reads the **Host header** instead
5. Matches the domain against a blocklist or app category
6. Writes allowed packets to a new output PCAP file

The multi-threaded version uses a **5-tuple hash** (src IP, dst IP, src port, dst port, protocol) to route all packets of the same connection to the same worker thread, ensuring consistent flow tracking.

---

## Tech Stack

- **Language:** C++17
- **Library:** libpcap
- **Build:** CMake
- **Concurrency:** std::thread, thread-safe queues

---

## Build & Run

### Linux

```bash
mkdir build && cd build
cmake ..
make
./dpi_engine ../test_dpi.pcap ../output.pcap
```

### Windows

See [WINDOWS_SETUP.md](WINDOWS_SETUP.md) for setup instructions.

---

## Author

Nivi  
[github.com/nivijainn](https://github.com/nivijainn)
