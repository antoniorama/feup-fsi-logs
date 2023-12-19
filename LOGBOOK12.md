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

In this tasked we are asked to traceroute the path between our VM and a destination (we chose google public dns aka 8.8.8.8). For this we coded the following python script:

```py
from scapy.all import *

source = IP()
source.dst = '8.8.8.8'

notArrived = True
ttl = 1

while notArrived:
    source.ttl = ttl
    response = sr1(source/ICMP(), verbose=0, timeout=5)
   
    if response == None:
        print(f"Time Out: {ttl} packet")
    elif response.type == 0:
        print(f"Echo Reply: {ttl} {response.src}")
        notArrived = False
    else:
        print(f"Continuing: {ttl} {response.src}")
    
    ttl = ttl + 1
```

verbose - to not have output 

timeout - to not get stuck

And the output was the following:

![Alt text](/images/lb12i6.png)

As we can see, it took 13 hops to reach the destination.

### Task 1.4

In this task we are asked to Sniff and-then Spoof a packet. The goal is that when the user container machine pings X, the VM machine sends a response packet, so whether X is alive or not, the user container machine will receive a response.

For this we coded the following python script that we used in the VM:

```py 
from scapy.all import *

def spoof_reply(p):
    if (p[2].type == 8): #echo request
        psrc = p[1].src
        pdst = p[1].dst
        seq = p[2].seq
        pid = p[2].id
        load = p[3].load
        
        reply = IP(src=pdst, dst=psrc)/ICMP(type=0, seq=seq, id=pid)/load
        send(reply)
    

p = sniff(iface=['br-0709a040ff43', 'lo'], filter="icmp", prn=spoof_reply)
```

This code checks if there is a ICMP packet request, and builds a reply packet that is the same as the request packet but with src and dest swapped.

We are asked to ping 3 machines from the user container machine:

- ping 1.2.3.4

In the image it is shown a first ping without the script being running in the VM, so we can confirm that, as expected, there is no response from the host. 

The second ping was made with the running script in the VM so, as expected, we got a reply.

![Alt text](/images/lb12i7.png)

- ping 10.9.0.99

This IP is a non-existing host on the LAN so when we try to ping it from the user container machine, no packet actually passes the sniffing filter so there are no responses sent even though the script is running on the VM. This is because the user container machine doesn't know how to reach 10.9.0.99 as there are no information about that in the ARP table.

- ping 8.8.8.8

In the image it is shown a first ping without the script being running in the VM, so we can confirm that, as expected, there is only one response per packet sent.

The second ping was made with the running script in the VM, as the server actually exists, we got a duplicate response on each packet sent.

![Alt text](/images/lb12i8.png)
