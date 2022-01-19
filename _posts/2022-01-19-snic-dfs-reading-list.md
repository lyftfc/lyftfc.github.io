---
layout: post
title:  "Reading List: DFS with SmartNIC"
date:   2022-01-19 00:03:27 +0800
categories: research readings
---

## Distributed Filesystem

- LineFS: Efficient SmartNIC Offload of a Distributed File System with Pipeline Parallelism
[link](https://dl.acm.org/doi/abs/10.1145/3477132.3483565)

The paper from SOSP'21 is a recent work that is highly related to the topic of accelerating
distributed filesystem using a SmartNIC. The design uses a Mellanox/Nvidia Bluefield,
configured in its Separated Host mode, to offload part of the DFS/RDMA operations. The DFS is
client-local, storing data on servers' Persistent Memory (PMEM). One of the main highlight of
the work is the co-design of server/SmartNIC that maintains low latency.

- Assise: Performance and Availability via Client-local NVM in a Distributed File System
[link](https://www.usenix.org/system/files/osdi20-anderson.pdf)

The OSDI'20 paper proposed a design of DFS using client-local NVM. It also provides detailed
benchmark results that compares to other client-server model DFS, and across storage medias
of SSD and Intel Optane PMEM. The design explores how a DFS can benefit from a tightly
local-coupled low-latency memory. This paper is the comparison baseline of LineFS.

- Ceph: A Scalable, High-Performance Distributed File System
[link](https://www.usenix.org/legacy/event/osdi06/tech/full_papers/weil/weil.pdf)

An old and renowned paper from OSDI'06, has long been commercialised by Red Hat for their
stroage cluster, also known as BlueStore. It provides near-POSIX filesystem interface to the
client processes, while letting the clients to operate file IO directly on the Object Storage
Devices (OSDs). Metadata are maintained on a separate cluster, to whom the clients and OSDs
connect. This work is nowadays a mature baseline for most DFS research.

## Persistent Memory

- Crash Consistent Non-Volatile Memory Express
[link](https://dl.acm.org/doi/10.1145/3477132.3483592)

The work proposed a novel extention to the exist NVMe interface for crash consistency support.
It surveyed the cost of existing protocols to maintain storage consistency, and demonstrated
how that can be eased with a redesign of NVMe that has crash consistency coupled in. Althrough
not directly related to the DFS topic, it may provide some insight for our system design,
especially in the case of FPGA-based SmartNIC, where there are greater implementation freedom.

- PACTree: A High Performance Persistent Range Index Using PAC Guidelines
[link](https://dl.acm.org/doi/10.1145/3477132.3483589)

The work investigated the limitation of current Non-Volatile Memory (NVM), and proposed a
new range indexing data structure for efficient operation on NVM hardwares, given the guidelines
that they have found out, i.e. packed access and concurrency. This work could provide insights on
the design of the DFS' metadata indexing scheme.

## SmartNIC

- Xenic: SmartNIC-Accelerated Distributed Transactions
[link](https://dl.acm.org/doi/10.1145/3477132.3483555)

The work used an SoC-based SmartNIC in a way beyond RDMA-based distributed transaction systems,
proposed an accelerated system utilising the lower network latency provided by the SmartNIC. The
NIC operates in-path, offloading the transaction-processing logics.

- Hyperloop: group-based NIC-offloading to accelerate replicated transactions in multi-tenant storage systems
[link](https://dl.acm.org/doi/10.1145/3230543.3230572)

The SIGCOMM'18 paper demonstrated a work that offloaded chain replication that required by DFS
to RDMA NICs, thus removing the CPU from the critical path. It provides better tail latency
with negligible replica CPU usage.

