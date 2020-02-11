

## Captured TLPs

This repository contains TLPs captured on a NetTLP platform. All the
pcap files in this repository can be parsed and read by a modified
[tcpdump](https://github.com/nettlp/tcpdump) or Wireshark with [the
NetTLP plugin](https://github.com/nettlp/wireshark-nettlp). Please see
https://haeena.dev/nettlp/example_apps.html.

The captured data is described in a
[paper](https://www.usenix.org/conference/nsdi20/presentation/kuga).

Note that these pcap files (excluding simple-nic) are captured with
nanosecond-scale timestamping with NIC hardware assist. You can see
them with `tcpdump --nano`.


### Directories

####  root-complex/

This directory contains two pcap files: 512-byte Memory Read through a
root complex or a PCIe switch. The TLPs are captured by using two
NetTLP adapters. The experimental setup is described in the apper,
Section 6.1.1. 

#### nic-and-nvme/

This directory contains pcap files of TLPs sent from four commercail
peripherals: Intel X520 10Gbps NIC, Intel XL710 40Gbps NIC, Intel
P4500 NVMe, and Samsung PM1725a NVMe. The subdirectories contain some
variations of pcap files corresponding to different numbers of packets
to be sent and different numbers of blocks to be written or read
to/from the NVMe SSDs. It is described in the paper, Section 6.1.2.


#### simple-nic/

`simple-nic-ping.pcap` in this directory is captured TLPs when sending
an ICMP echo packet and receiving an ICMP reply packet through
[simple-nic](https://haeena.dev/nettlp/nonexistent_nic.html). It is
described in the paper, Section 6.2.



