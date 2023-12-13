# Sniffing Spooning Lab

### Task 1.1 - A

We started by setting up the sniffer.py script inside the attacker container so that we can capture packets. We used ifconfig command to get the interface name for the script.

Then, we ran the script so we are now capturing packets. In one of the other containers we did `ping google.com` and we are capturing packets:

![Alt text](/images/lb12i1.png)

When we run the script wihtout sudo permissions we are not allowed:

![Alt text](/images/lb12i2.png)

### Task 1.1 - B

In this task we are asked to filter packets:

- Capture only the ICMP packets

This is what was already in the provided sniffer.py code so that was what we did in the last task using this code:

`sniff(iface='br-0709a040ff43', filter='icmp', prn=print_pkt)`

- Capture only TCP packets from a specific IP adress.

We changed our sniffer.py script to do this:

`sniff(iface='br-0709a040ff43', filter='tcp and src host 10.9.0.5 and dst port 23', prn=print_pkt)`

![Alt text](/images/lb12i3.png)

- Capture  packets  comes  from  or  to  go  to  a  particular  subnet.   You  can  pick  any  subnet,  such  as128.230.0.0/16;

We changed our sniffer.py script to do this:

`sniff(iface='br-0709a040ff43', filter='net 128.230.0.0/16', prn=print_pkt)`

Then we pinged the subnet and we got the following:

![Alt text](/images/lb12i4.png)

### Task 1.2

To create a spoof packet we used the following code (with an arbitrary source IP address `100.100.100.100`):

```py
from scapy.all import *

a = IP()
a.src = '100.100.100.100'
a.dst = '10.9.0.5'

b = ICMP()
b.id = 0x23
b.seq = 0x1
p = a/b

send(p)
p.show()
```

We can confirm that it received and accepted the packet:

![Alt text](/images/lb12i5.png)

 
### Task 1.3

### Task 1.4
