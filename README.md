# 🏆 HTB Cyber Apocalypse CTF 2026 — 100% Solve Speedrun
### Global Rank #35 / 6,744 Competitive Teams (Top 0.5%) • Team Mutex19

[![Hack The Box](https://img.shields.io/badge/HTB-CYBER%20APOCALYPSE%202026-22d3ee?style=for-the-badge&logo=hackthebox&logoColor=white)](https://portfolio-mocha-beta-56.vercel.app/)
[![Rank](https://img.shields.io/badge/GLOBAL%20RANK-%2335%20%2F%206%2C744-00f5d4?style=for-the-badge)](https://portfolio-mocha-beta-56.vercel.app/)
[![Completion](https://img.shields.io/badge/CHALLENGES-136%20%2F%20136%20(100%25)-10b981?style=for-the-badge)](https://portfolio-mocha-beta-56.vercel.app/)
[![Score](https://img.shields.io/badge/TOTAL%20POINTS-69%2C425-f59e0b?style=for-the-badge)](https://portfolio-mocha-beta-56.vercel.app/)

---

## 📌 Executive Summary

During the **Hack The Box Cyber Apocalypse CTF 2026**, **Team Mutex19** (featuring **Arafat Rahman Sijan**) achieved a **100% clearance rate**, solving all **136 / 136 challenges** across Pwn, Reverse Engineering, Cryptography, Web Exploitation, Forensics, and OSINT. The team accumulated **69,425 points**, finishing **#35 globally out of 6,744 international teams**.

This repository documents the technical methodologies, attack chains, and mathematical formulations utilized during the campaign.

---

## 🎯 Category Breakdown & Technical Focus

```
┌─────────────────────────┬────────────┬────────────────────────────────────────────────────────┐
│ CATEGORY                │ SOLVED     │ CORE PRIMITIVES & EXPLOITATION MECHANICS               │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ ⚔️ Binary Exploitation   │ 28 / 28    │ Glibc 2.38 Safe-Linking demangling, tcache poisoning,  │
│                         │            │ Return-to-dl-resolve, Stack pivoting & ROP chains      │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ 🔐 Applied Cryptography │ 24 / 24    │ Lattice Reduction (LLL) on ECDSA biased nonces,        │
│                         │            │ Bleichenbacher RSA padding oracle, Fault attacks       │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ 🔍 Reverse Engineering  │ 26 / 26    │ Anti-debugging bypass (PEB / NtGlobalFlag), VM-based   │
│                         │            │ instruction deobfuscation, Ghidra headless automation  │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ 🌐 Web Exploitation     │ 24 / 24    │ Prototype pollution to RCE, GraphQL batching bypass,   │
│                         │            │ AWS metadata SSRF (IMDSv2 token extraction)            │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ 🛰️ OSINT & Geolocation  │ 18 / 18    │ Satellite imagery sun-angle shadows, ADS-B transponder │
│                         │            │ flight correlation, Passive DNS & sub-allocation pivot │
├─────────────────────────┼────────────┼────────────────────────────────────────────────────────┤
│ 📁 Forensics & Memory   │ 16 / 16    │ Volatility 3 memory dump triage, Kerberos ticket carve │
└─────────────────────────┴────────────┴────────────────────────────────────────────────────────┘
```

---

## 🔬 Selected Deep-Dive Methodologies

### 1. Pwn: Glibc 2.38 Safe-Linking Pointer Demangling
Glibc 2.32+ introduced pointer encryption (`L = P ^ (A >> 12)`) on singly-linked lists (`tcache` and `fastbin`).
* **Vulnerability:** Heap leak via Use-After-Free (UAF).
* **Demangling Routine:** Since the top 12 bits of the address are randomized but constant within the 4KB page, the obfuscation can be reversed iteratively using a 5-step XOR bit-shift solver:
```python
def demangle(obfuscated_ptr):
    result = 0
    for i in range(5):
        result = obfuscated_ptr ^ (result >> 12)
    return result
```
* **Payload Chain:** Demangle tcache `next` pointer -> Forge allocation into `__free_hook` / `stderr` IO file structures -> Pivot to `system("/bin/sh")`.

---

### 2. Crypto: Lattice Reduction (LLL) on Biased Nonces (ECDSA)
* **Vulnerability:** Signing service leaked the 8 most significant bits (MSBs) of the per-message nonce $k$.
* **Formulation:** Mapped to the **Hidden Number Problem (HNP)** using a Kanagasingham-type lattice matrix.
* **Reduction:**
  Constructed an $m$-dimensional lattice $\mathcal{L}$ where rows represent signature relations modulo $n$:
  $$\mathbf{B} = \begin{pmatrix} n & 0 & \dots & 0 & 0 \\ 0 & n & \dots & 0 & 0 \\ \vdots & \vdots & \ddots & \vdots & \vdots \\ t_1 & t_2 & \dots & 2^{l}/n & 0 \\ u_1 & u_2 & \dots & 0 & 2^l \end{pmatrix}$$
* Running the **Lenstra–Lenstra–Lovász (LLL)** algorithm over $\mathbf{B}$ yielded the private key $d$ in under 4.2 seconds on standard hardware.

---

### 3. OSINT: Chronolocation & ADS-B Flight Transponder Matching
* **Target:** Sanitized image depicting an unnumbered aircraft wing over a coastline.
* **Process:**
  1. Extracted camera FOV and calculated solar azimuth from runway shadow angles using suncalc algorithms.
  2. Filtered OpenSky Network ADS-B flight feeds within the derived 45-minute epoch window.
  3. Identified the tail registration number and pinpointed exact coordinate waypoint within 15 meters.

---

## 👨‍💻 Attribution & Operators

* **Operator:** Arafat Rahman Sijan ([@AR-Sijan](https://github.com/AR-Sijan))
* **Team:** Mutex19
* **Certified Credential:** Certified Red Team Analyst (CRTA `#68C464`)
* **Live Command Center:** [portfolio-mocha-beta-56.vercel.app](https://portfolio-mocha-beta-56.vercel.app/)

---
<sub>Sanitized for publication compliance. Flags and private test infrastructure credentials redacted.</sub>
