# homework 3 questions
## part 1 
1. 
```
mininet> nodes
available nodes are: 
c0 h1 h2 h3 h4 h5 h6 h7 h8 s1 s2 s3 s4 s5 s6 s7
mininet> net
h1 h1-eth0:s3-eth2
h2 h2-eth0:s3-eth3
h3 h3-eth0:s4-eth2
h4 h4-eth0:s4-eth3
h5 h5-eth0:s6-eth2
h6 h6-eth0:s6-eth3
h7 h7-eth0:s7-eth2
h8 h8-eth0:s7-eth3
s1 lo:  s1-eth1:s2-eth1 s1-eth2:s5-eth1
s2 lo:  s2-eth1:s1-eth1 s2-eth2:s3-eth1 s2-eth3:s4-eth1
s3 lo:  s3-eth1:s2-eth2 s3-eth2:h1-eth0 s3-eth3:h2-eth0
s4 lo:  s4-eth1:s2-eth3 s4-eth2:h3-eth0 s4-eth3:h4-eth0
s5 lo:  s5-eth1:s1-eth2 s5-eth2:s6-eth1 s5-eth3:s7-eth1
s6 lo:  s6-eth1:s5-eth2 s6-eth2:h5-eth0 s6-eth3:h6-eth0
s7 lo:  s7-eth1:s5-eth3 s7-eth2:h7-eth0 s7-eth3:h8-eth0
c0
```

2. 
```
mininet> h7 ifconfig
h7-eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.0.7  netmask 255.0.0.0  broadcast 10.255.255.255
        inet6 fe80::40db:47ff:fe97:e981  prefixlen 64  scopeid 0x20<link>
        ether 42:db:47:97:e9:81  txqueuelen 1000  (Ethernet)
        RX packets 56  bytes 4232 (4.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10  bytes 796 (796.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

## part 2
1. ascii drawing
```
	packet comes in
	
        |
        V
	,__________________,
	|                  |
	| _handle_PacketIn |
	|__________________|
	
	    |
	    V
	,__________________,
	|                  |
	|   act_like_hub   |   (or act_like_switch, once we change it)
	|__________________|
	
	    |
	    V
	,__________________,
	|                  |
	|  resend_packet   |
	|__________________|
		
	    |
	    V

     forward message to port
```
2. using command $ h1 ping -c100 hx
```
	h1 ping h2: 
		--- 10.0.0.2 ping statistics ---
		100 packets transmitted, 100 received, 0% packet loss, time 99199ms
		rtt min/avg/max/mdev = 3.947/6.342/50.046/4.768 ms

	h1 ping h8: 
		--- 10.0.0.8 ping statistics ---
		100 packets transmitted, 100 received, 0% packet loss, time 99235ms
		rtt min/avg/max/mdev = 23.456/39.802/110.400/18.765 ms

a. on average, h1 to h2 took 6.342 ms, whereas h1 to h8 took 39.802 ms.

b. the min/max for h1 to h2 was 3.947/50.046 ms, and for h1 to h8 it was 23.456/110.400 ms. so overall, the minimum was 3.947 and the maximum was 110.400 ms.

c. the difference between these two is the amount of switches they need to go through. from h1 to h2, it is only one switch (s3). but from h1 to h8, it needs to pass through 5 switches (s3, s2, s1, s5, s7). since each switch has to forward it, it takes more time.
```   
3. using command $ iperf h1 hx
```
	h1 to h2
		*** Iperf: testing TCP bandwidth between h1 and h2 
		*** Results: ['2.17 Mbits/sec', '2.72 Mbits/sec']
		
	h1 to h8
		*** Iperf: testing TCP bandwidth between h1 and h8 
		*** Results: ['1.02 Mbits/sec', '1.54 Mbits/sec']


a. iperf is used to test the throughput of a network by sending data and measuring how much data can be sent in a fixed amount of time.

b. the throughput for h1 to h2 is 2.17 Mbits/sec, and from h2 to h1 is 2.72 Mbits/sec. the throughput for h1 to h8 is 1.02Mbits/sec, and from h8 to h1 is 1.54Mbits/sec. 

c. the throughput for h1 and h2 is higher, meaning more data can be sent between them in a set amount of time (Mbit/sec). it is for a similar reason as before; since h1 to h8 needs to travel through 5 switches it takes longer to get ACK packets and so it can't transmit data as fast. 
``` 
4. 
```
i tried putting `print(self.connection) in the function that handles packets. not sure if this really answers the question, but each switch seems to observe its own traffic (?). since in of_tutorial they are acting as hubs, a ping from h1 to h2 will flood everywhere. so when I did $ h1 ping -c1 h2, I got this response in the controller. you can see that all of the switches are receiving. I'm actually kind of confused why it is always in this order, since 5 and 7 are the furthest away. 
   
	    [00-00-00-00-00-03 5]
		[00-00-00-00-00-03 5]
		[00-00-00-00-00-02 7]
		[00-00-00-00-00-02 7]
		[00-00-00-00-00-01 2]
		[00-00-00-00-00-01 2]
		[00-00-00-00-00-04 3]
		[00-00-00-00-00-04 3]
		[00-00-00-00-00-05 6]
		[00-00-00-00-00-05 6]
		[00-00-00-00-00-07 1]
		[00-00-00-00-00-07 1]
		[00-00-00-00-00-06 4]
		[00-00-00-00-00-06 4]
```

## part 3
1. 
```
the code works by linking ports to source MACs (if not already in the dict). this way, it is learning as packets appear. it also checks if the packet dst is known (in the dict). if it is, then the switch behavior is to send the packet only through that port. if the dst mac is unknown, then it sends it everywhere (flooding). the more packets the controller recieves, the better it can have a mapping of mac to port, and thus forward more accurately.
   ```
2. using $ h1 ping -c100 hx
```

	h1 ping h2: 
		--- 10.0.0.2 ping statistics ---
		100 packets transmitted, 100 received, 0% packet loss, time 99197ms
		rtt min/avg/max/mdev = 1.088/2.533/6.996/1.366 ms

	h1 ping h8: 
		--- 10.0.0.8 ping statistics ---
		100 packets transmitted, 100 received, 0% packet loss, time 99199ms
		rtt min/avg/max/mdev = 4.422/7.260/14.296/2.182 ms


a. on average, h1 to h2 took 2.533 ms, whereas h1 to h8 took 7.260 ms.

b. the min/max for h1 to h2 was 1.088/6.996 ms, and for h1 to h8 it was 4.422/14.296 ms. so overall, the minimum was 1.088 and the maximum was 14.296 ms.

c. there is a significant difference, with these results being much faster.I would imagine this is because once the controller 'learns' which port to use, it doesn't have to waste time sending the packet to all of the other ports that are not the destination. also because it's a ping, the controller learns everything at the start. 
```   
3. using command $ iperf h1 hx
```
	h1 to h2
		*** Iperf: testing TCP bandwidth between h1 and h2 
		*** Results: ['29.1 Mbits/sec', '31.1 Mbits/sec']

	h1 to h8
		*** Iperf: testing TCP bandwidth between h1 and h8 
		*** Results: ['2.94 Mbits/sec', '3.33 Mbits/sec']

a. the throughput for h1 to h2 is 29.1 Mbits/sec, and from h2 to h1 is 31.1 Mbits/sec. the throughput for h1 to h8 is 2.94Mbits/sec, and from h8 to h1 is 3.3Mbits/sec. 

b. there is a very big difference, particularly between h1 and h2. they have a much higher throughput here, probably for the same reason as before. the switch controller does not have to waste time sending the packet everywhere, and instead can instantly funnel it to the dst. I am not sure why h1 to h2 is about 10 times faster whereas h1 to h8 is maybe twice as fast. it's possible that with the addition of more switches between h1 and h8, there is still that latency of just having to be forwarded 5 times instead of 1.
```
