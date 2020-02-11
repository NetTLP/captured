

- PCIe bus of theNetTLP adpater is 1b00
- BAR4 start address is 0xa0000000
- TX IRQ address is 0xfee1a000, data is 0x00004022
- RX IRQ address is 0xfee03000, data is 0x00004022
- TX desc base is 0x2f001000
- RX desc base is 0x2f002000

### TX Part
```
# 1. Host updates the TX queue tail pointer. 0xa000010 is pointer for the TX descriptor 
23:51:48.210015 IP adapter.12291 > libtlp.12291: NetTLP: MWr, len 1, requester 00:00, tag 0x03, last 0x0, first 0xf, Addr 0xa0000010

# 2. Device reads the TX descriptor
23:51:48.210055 IP libtlp.12291 > adapter.12291: NetTLP: MRd, len 4, requester 1b:00, tag 0x03, last 0xf, first 0xf, Addr 0x2f001000
23:51:48.210069 IP adapter.12291 > libtlp.12291: NetTLP: CplD, len 4, completer 00:00, success, bc 16, requester 1b:00, tag 0x03, lowaddr 0x00

# 3. Device reads packet data. len 25 is 100byte, and last byte enable is 0x3.  Thus, ICMP Echo packet is 98-byte (including Ethernet header)
23:51:48.210083 IP libtlp.12291 > adapter.12291: NetTLP: MRd, len 25, requester 1b:00, tag 0x03, last 0x3, first 0xf, Addr 0x3bb26800
23:51:48.210095 IP adapter.12291 > libtlp.12291: NetTLP: CplD, len 25, completer 00:00, success, bc 98, requester 1b:00, tag 0x03, lowaddr 0x00

# 4. Device generates interrupt that indicates TX done. 0xfee1a000 is MSI-X addr of TX interrupt
23:51:48.210122 IP libtlp.12291 > adapter.12291: NetTLP: MWr, len 1, requester 1b:00, tag 0x03, last 0x0, first 0xf, Addr 0xfee1a000
```


### RX part

On the RX side, the driver inform the device of the address of packet
buffer before receiving packets. Thus, the first TLP for receiving a
packet (ICMP Echo Reply in this case) is a DMA Write packet from the
device to the host (driver). After host consumes the received packet,
the host tells device the address of the next packet buffer.

```
# 1. Device starts DMA wirte the packet (received ECMP Reply)
23:51:48.210134 IP libtlp.12290 > adapter.12290: NetTLP: MWr, len 25, requester 1b:00, tag 0x02, last 0x3, first 0xf, Addr 0x2f003000

# 2. Device writes back RX descriptor to host
23:51:48.210139 IP libtlp.12290 > adapter.12290: NetTLP: MWr, len 4, requester 1b:00, tag 0x02, last 0xf, first 0xf, Addr 0x2f002000

# 3. Device generates interrupt that indicates RX done. 0xfee10000 is MSI-X addr of RX interrupt
23:51:48.210141 IP libtlp.12290 > adapter.12290: NetTLP: MWr, len 1, requester 1b:00, tag 0x02, last 0x0, first 0xf, Addr 0xfee03000

# 4. Host updates RX queue Tail Pointer -> next RX descriptor address.
23:51:48.212602 IP adapter.12288 > libtlp.12288: NetTLP: MWr, len 1, requester 00:00, tag 0x00, last 0x0, first 0xf, Addr 0xa0000014

# 5. Device DMA reads the new RX descriptor.
23:51:48.212620 IP libtlp.12288 > adapter.12288: NetTLP: MRd, len 4, requester 1b:00, tag 0x00, last 0xf, first 0xf, Addr 0x2f002000
23:51:48.212633 IP adapter.12288 > libtlp.12288: NetTLP: CplD, len 4, completer 00:00, success, bc 16, requester 1b:00, tag 0x00, lowaddr 0x00
```
