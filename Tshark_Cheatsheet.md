# Tshark cheatsheet

## List network interfaces

```shell
tshark -D
```

## Some configuration options

Don't use root account to capture packets :
```shell
sudo dpkg-reconfigure wireshark-common
sudo usermod -a -G wireshark $USER
newgrp wireshark
```

Set promiscuous mode if you need to capture all packets
```shell
sudo ifconfig eth0 promisc
```

## Capturing packets

```shell
tshark -i <any | eth0 | id n°>
```

Capturing packets and save them to a file :
```shell
# Use Ctrl + c to stop the capture
tshark -i eth0 -w test.pcap
# Capture only 20 packets
tshark -i eth0 -c 20 -w test.pcap
# Create a ring buffer of 10 files of 1kB each (Overwritting loop)
tshark -i eth0 -w test.pcap -b filesize:1 -b files:10
```

## Read a pcap file

```shell
tshark -r test.pcap
# Show only the first two packets
tshark -r test.pcap -c 2
# Show packet details (All fields)
tshark -r test.pcap -c 1 -V
```

## Exporting capture file in other format
```shell
# Show all available formats
tshark -T x
# Export in psml format
tshark -r test.pcap -T psml
# Export in pdml format and save it to a file
tshark -r test.pcap -T pdml > test.pdml
```

## Packet filters

```shell
# Capture traffic only for TCP port 80
tshark -i eth0 -c 10 -f "tcp port 80" -w http.pcap
# Apply filter on display to show only GET request
tshark -r http.pcap -Y 'http.request.method == "GET"'
```

## Extract some fields from a capture

```shell
# Get SSID and MAC address in all beacon frame (sort and delete non unique lines)
tshark -r Wifi.pcap -Y 'wlan.fc.type_subtype == 0x0008' -Tfields -e wlan.ssid -e wlan.bssid | sort | uniq
# Get hostname and URI from all HTTP GET request and showw column header
tshark -r http.pcap -Y 'http.request.method == "GET"' -Tfields -e http.host -e http.request.uri -E header=y
```

## Decoding packets

```shell
# Show all decode as options
tshark -d .
# Decode all traffic on UDP port 4000 as Real-Time Transport Protocol
tshark -r VOIP.pcap -Y 'udp.port == 4000' -d udp.port==4000,rtp
```

## Automation with PyShark

https://github.com/KimiNewt/pyshark

Installation with pip :
```shell
# Require python 3
pip install pyshark
```