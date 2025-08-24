# Description
```
 Forensics

Difficulty: Beginner
Author: Ha1fdan

I have intercepted encrypted HTTPS traffic from the secret brunsviger baking forum, but I need help decrypting it.

```

# Attachments
keys.log
traffic.pcap

# Solution
Open traffic.pcap with wireshark. \
Load keys.log file in wireshark. (Edit - Preferences - Protocols - TLS - (Pre)-Master-Secret log filename). \
Sort the rows with protocols. \
Read info attached to packets sent using HTTP. It contains JSON.\
One of the JSON has the flag in base64. Decode it and submit.
