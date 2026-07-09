# Intel Pin Cache Simulator

## Overview
This project is a dynamic cache simulator built using the Intel Pin binary instrumentation framework. It intercepts memory reads and writes from a target application at runtime to simulate cache behavior, allowing you to evaluate memory access patterns and calculate cache performance metrics like hit and miss rates.

## Key Features
* **Dynamic Instrumentation:** Uses Intel Pin to analyze memory accesses on-the-fly without needing the target application's source code.
* **Pseudo-LRU (Tree-PLRU) Replacement:** Implements an efficient tree-based Pseudo-Least Recently Used (PLRU) algorithm to handle cache eviction.
* **Windowed Data Logging:** Automatically generates a `miss.csv` file that records cache misses per window of 1,000 accesses, which is highly useful for visualizing application phase behavior over time.
* **Write-Back Tracking:** Maintains dirty bits for cache lines and calculates write-back operations.

## Default Cache Configuration
The simulator is currently hardcoded with the following cache parameters in the `main` function:
* **Total Cache Size:** 16,384 bytes (16 KB)
* **Block (Line) Size:** 64 bytes
* **Associativity:** 4-way Set Associative
* **Replacement Policy:** Tree-PLRU
* **Write Policy:** Write-Back / Write-Allocate (simulated via dirty bits)

*(Note: You can easily modify these parameters by changing the arguments passed to `initCache()` inside `main()`.)*

## How the Pseudo-LRU (PLRU) Works
Instead of maintaining a strict time-based LRU queue (which is hardware-intensive), this simulator uses a binary tree implementation.
* For an *N*-way set, an *N−1* bit binary tree is maintained.
* **On a cache hit:** The tree paths are updated to point away from the accessed line, protecting it from eviction.
* **On a cache miss:** The tree is traversed to find the path pointing to the pseudo-least recently used block. That block is evicted, replaced, and the tree pointers are flipped.

## Output Details

### 1. Standard Output (Console)
Upon completion of the target program, the simulator flushes the cache and prints a summary to the console:
* Total Memory Accesses
* Cache Hits
* Cache Misses
* Hit Rate (%)
* Miss Rate (%)

### 2. CSV Output (`miss.csv`)
A CSV file is generated in the working directory to track cache behavior over time. It contains the following columns:
* `window`: The sequential ID of the tracking window.
* `accesses`: The number of accesses in this window (defaults to 1000).
* `misses`: The number of cache misses that occurred during this specific window.

## Prerequisites
* **C++ Compiler:** `g++` or equivalent.
* **Intel Pin Toolkit:** You must have the Intel Pin framework downloaded and configured on your system.

## Build and Execution (Linux/Unix)

### 1. Build the Pin Tool
Assuming you have placed this code in a file named `cache_sim.cpp` inside your Pin tool directory (e.g., `source/tools/ManualExamples`), compile it using the standard Pin makefile:

```bash
make obj-intel64/cache_sim.so TARGET=intel64
