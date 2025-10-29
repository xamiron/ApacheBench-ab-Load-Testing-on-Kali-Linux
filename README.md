# ApacheBench (ab) Load Testing on Kali Linux

A lightweight guide and ready-to-use **README.md** for performing load testing with ApacheBench (ab) on Kali Linux. This document includes installation steps, common test patterns (smoke, ramp-up, soak, spike), example commands, monitoring tips, and a sample output snippet to help you quickly run and interpret tests.

---

## Table of Contents

1. Overview
2. Requirements
3. Installation
4. Basic Commands
5. Test Types & Examples

   * Smoke Test
   * Progressive Ramp-Up Test
   * Soak Test
   * Spike Test
6. Monitoring During Tests
7. Interpreting Key Metrics
8. Sample Output
9. Best Practices & Safety
10. How to add this README to GitHub
11. License

---

## 1. Overview

ApacheBench (ab) is a simple, command-line HTTP load testing tool included in the `apache2-utils` package. It's ideal for quick smoke, stress, and throughput checks. Use it to measure requests per second (RPS), average/maximum response time, transfer rates, and failed requests.

> **Note:** `ab` is single-threaded per process and best suited for quick checks and small–medium scale tests. For distributed/large-scale testing, consider tools like `k6`, `wrk2`, or `Gatling`.

## 2. Requirements

* Kali Linux (tested with Kali Linux 2024.3)
* `apache2-utils` (contains `ab`)
* Network connectivity to the target server
* Optional monitoring tools: `top`, `vmstat`, `iostat`, `ss`, and server logs

## 3. Installation

```bash
sudo apt update
sudo apt install apache2-utils -y
```

Verify installation:

```bash
ab -V
```

## 4. Basic Commands

Send 100 requests with 10 concurrent users:

```bash
ab -n 100 -c 10 https://www.example.com/
```

Save output to a file for later analysis:

```bash
ab -n 10000 -c 1000 https://www.example.com/ > ab_report.txt
```

Common useful flags:

* `-n <requests>`: Total number of requests to perform.
* `-c <concurrency>`: Number of multiple requests to make at a time.
* `-t <seconds>`: Maximum number of seconds to spend for benchmarking.
* `-H 'Header: value'`: Add a custom header (useful for auth or content-type).
* `-p postfile -T content-type`: Send POST data from file.

## 5. Test Types & Examples

### Smoke Test (quick connectivity & baseline)

```bash
ab -n 100 -c 5 https://www.example.com/
```

### Progressive Ramp-Up Test

Gradually increase concurrency to find the stability threshold:

```bash
ab -n 5000 -c 50 https://www.example.com/
ab -n 5000 -c 100 https://www.example.com/
ab -n 5000 -c 200 https://www.example.com/
ab -n 10000 -c 1000 https://www.example.com/
```

Run each step after reviewing server health and logs.

### Soak Test (long-duration stability)

Run for 30–60 minutes at a moderately high concurrency to detect memory leaks or slow degradation. Example using `-t` (time) and `-c`:

```bash
ab -c 200 -t 1800 https://www.example.com/    # 1800 seconds = 30 minutes
```

### Spike Test (sudden surge)

Simulate sudden traffic spike from low to very high concurrency:

1. Run a baseline: `ab -n 10000 -c 100 https://www.example.com/`
2. Immediately run a spike: `ab -n 10000 -c 1000 https://www.example.com/`

> **Caution:** Spike tests can cause service disruption. Coordinate with stakeholders and run against non-production if possible.

## 6. Monitoring During Tests

On the load generator (Kali):

```bash
# CPU & memory
top
# CPU, I/O, interrupts
vmstat 1
# Disk I/O
iostat -x 1
# Network sockets, connections
ss -s
```

On the server under test, monitor:

* Web server access & error logs (e.g., `/var/log/apache2/access.log`)
* Application logs
* System metrics (CPU, memory, swap, disk, network)
* Reverse proxies / load balancers metrics

## 7. Interpreting Key Metrics

* **Requests per second (RPS):** How many requests the server handled per second.
* **Time per request (mean):** Average latency per request.
* **Failed requests:** Number of requests that didn’t return a valid response.
* **Transfer rate:** Throughput of the requests (KB/sec).
* **Connection Time breakdown:** Connect, Processing, Waiting, Total.

If failed requests increase with concurrency, identify bottlenecks such as worker limits, database locks, or network saturation.

## 8. Sample Output

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
Total transferred:      95800 bytes
HTML transferred:       61200 bytes
Requests per second:    42.66 [#/sec] (mean)
Time per request:       234.5 [ms] (mean)
Transfer rate:          39.87 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        5   10   2.3     10      15
Processing:    50  220  40.4    210     420
Waiting:       40  200  37.0    190     400
Total:         55  230  41.1    220     435

Percentage of the requests served within a certain time (ms)
  50%    220
  66%    270
  75%    290
  80%    310
  90%    370
  95%    400
  98%    420
  99%    430
 100%    435 (longest request)
```

## 9. Best Practices & Safety

* Always test against staging or pre-production when possible.
* Coordinate high-load tests with ops/DevOps and stakeholders.
* Monitor the server and be ready to stop the test (Ctrl+C) if critical systems degrade.
* Avoid running extreme spike tests against third-party or shared infrastructure.
* Consider distributed load generation if a single client cannot generate sufficient concurrency.
