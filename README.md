# ApacheBench (ab) Load Testing on Kali Linux

A professional and visually enhanced **README.md** for performing load testing with **ApacheBench (ab)** on **Kali Linux**. This file includes installation steps, test types, example commands, monitoring guidance, and result interpretation — perfect for GitHub documentation.

---

## 📘 Table of Contents

1. 🔍 Overview
2. ⚙️ Requirements
3. 💾 Installation
4. 🧠 Basic Commands
5. 🚀 Test Types & Examples

   * Smoke Test
   * Progressive Ramp-Up Test
   * Soak Test
   * Spike Test
6. 📊 Monitoring During Tests
7. 📈 Interpreting Key Metrics
8. 🧾 Sample Output
9. 🧩 Best Practices & Safety
10. 🪪 License

---

## 🔍 Overview

**ApacheBench (ab)** is a lightweight, command-line HTTP benchmarking tool that comes bundled with the `apache2-utils` package. It's perfect for measuring performance metrics such as Requests Per Second (RPS), response times, throughput, and error rates.

> 💡 **Tip:** ApacheBench is great for single-host testing and early-stage performance validation. For distributed or high-scale load tests, consider using `k6`, `wrk2`, or `Gatling`.

---

## ⚙️ Requirements

| Component            | Details                                          |
| -------------------- | ------------------------------------------------ |
| **OS**               | Kali Linux 2024.3                                |
| **Tool**             | apache2-utils (includes `ab`)                    |
| **Processor**        | Intel Core i7 or higher                          |
| **Memory**           | 16 GB RAM (recommended)                          |
| **Network**          | 100 Mbps Broadband                               |
| **Monitoring Tools** | `top`, `vmstat`, `iostat`, `ss`, and server logs |

---

## 💾 Installation

```bash
sudo apt update
sudo apt install apache2-utils -y
```

Verify installation:

```bash
ab -V
```

---

## 🧠 Basic Commands

Run a quick test with 100 requests and 10 concurrent users:

```bash
ab -n 100 -c 10 https://www.example.com/
```

Save the result to a text file for later analysis:

```bash
ab -n 10000 -c 1000 https://www.example.com/ > ab_report.txt
```

**Common Parameters:**

| Flag | Description                              |
| ---- | ---------------------------------------- |
| `-n` | Total number of requests                 |
| `-c` | Number of concurrent users               |
| `-t` | Duration of the test (in seconds)        |
| `-H` | Add custom headers (e.g., Authorization) |
| `-p` | Send POST data from file                 |

---

## 🚀 Test Types & Examples

### 🟢 1. Smoke Test — Connectivity & Baseline

```bash
ab -n 100 -c 5 https://www.example.com/
```

Purpose: Verify basic server connectivity and baseline performance.

---

### 🟡 2. Progressive Ramp-Up Test — Scaling Behavior

```bash
ab -n 5000 -c 50 https://www.example.com/
ab -n 5000 -c 100 https://www.example.com/
ab -n 5000 -c 200 https://www.example.com/
ab -n 10000 -c 1000 https://www.example.com/
```

Purpose: Identify the point where latency or error rate increases significantly.

---

### 🔵 3. Soak Test — Long-Term Stability

```bash
ab -c 200 -t 1800 https://www.example.com/
```

Run for 30–60 minutes to detect memory leaks or slow degradation.

---

### 🔴 4. Spike Test — Sudden Surge Handling

```bash
# Gradual baseline
a b -n 10000 -c 100 https://www.example.com/
# Sudden spike
ab -n 10000 -c 1000 https://www.example.com/
```

Simulates flash traffic or sudden spikes to test resilience.

---

## 📊 Monitoring During Tests

**On Kali (Client):**

```bash
top           # Monitor CPU & Memory
vmstat 1      # System stats
 iostat -x 1  # Disk I/O
ss -s         # Network connections
```

**On Server (Target):**

* Check `access.log` and `error.log`
* Monitor CPU, RAM, I/O, and network
* Track reverse proxy or DB metrics

---

## 📈 Interpreting Key Metrics

| Metric                        | Description                         |
| ----------------------------- | ----------------------------------- |
| **Requests per second (RPS)** | Throughput performance              |
| **Time per request (mean)**   | Average latency per request         |
| **Failed requests**           | Error or dropped connections        |
| **Transfer rate**             | Throughput in KB/sec                |
| **Connection times**          | Connect, Processing, Waiting, Total |

> ⚠️ **Watch out:** A rising number of failed requests at high concurrency indicates bottlenecks like database locks, thread limits, or CPU starvation.

---

## 🧾 Sample Output

```
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Benchmarking www.example.com (be patient)
Completed 100 requests
Completed 200 requests

Server Software:        nginx/1.18.0
Server Hostname:        www.example.com
Server Port:            443

Document Path:          /
Document Length:        612 bytes

Concurrency Level:      10
Time taken for tests:   2.345 seconds
Complete requests:      100
Failed requests:        0
Requests per second:    42.66 [#/sec] (mean)
Time per request:       234.5 [ms] (mean)
Transfer rate:          39.87 [Kbytes/sec]
```

---

## 🧩 Best Practices & Safety

✅ Always test on staging or pre-production
✅ Monitor both client & server metrics
✅ Stop the test immediately if resources max out
✅ Avoid testing third-party endpoints
✅ For heavier loads, distribute across multiple clients

---


## 🪪 License

**MIT License © 2025 xamiron**
Feel free to copy, modify, and share.

