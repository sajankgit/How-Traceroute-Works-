# How-Traceroute-Works ?

Traceroute is a handy network diagnostic tool used by network administrators in day today basis to check network issues.

Traceroute gives insights to —

- The routers in the path between source and destination.
- Round Trip Time — That is the time taken to send and receive the packet

Let’s try to analyze tracerouteto 8.8.8.8 -

```
~ traceroute -n 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.0.1  6.768 ms  6.462 ms  6.223 ms
 2  183.83.192.1  5.842 ms  5.543 ms  5.288 ms
 3  183.82.14.5  5.078 ms  6.755 ms  6.468 ms
 4  183.82.14.57  20.789 ms  27.609 ms  27.931 ms
 5  72.14.194.18  17.821 ms  17.652 ms  17.465 ms
 6  66.249.94.170  19.378 ms  15.975 ms  23.017 ms
 7  209.85.241.21  16.633 ms  16.607 ms  17.428 ms
 8  8.8.8.8  17.144 ms  17.662 ms  17.228 ms
 ```
 
Here from the output we can see that we are sending 60 byte packet and the TTL value is 30 hops.

TTL or Time To Live is the number of hops a packet can travel before it is dropped off.

Now, first I am sending the packet from my local machine. The first router is my gateway — 192.168.0.1.

`1  192.168.0.1  6.768 ms  6.462 ms  6.223 ms`

You can see 3 Round Trip Time in the output. Round Trip Time or RTT is the time taken send and receive the packet. Actually, we send 3 UDP packets and then calculate the average RTT. That is why you are seeing 3 RTT values in the output.

Now let’s see what is happening in background —

![image](https://user-images.githubusercontent.com/37524392/181218254-db1b7944-fcfb-4a5a-9273-7031fe00ebc8.png)

Let’s assume that the default TTL is 30. The source will first set TTL as 1 and send the packet to first nearest router (R2). R2 will then set reduce TTL by 1 and now TTL became 0 and R2 sends `TTL exceeded` message.

Remember — **Source will increment the TTL value and routers will decrement the TTL value.**

Now source will make the TTL value to 2. When it reaches R2, TTL becomes 1 and packet is forwarded to R5 where TTL became 0. R5 will send TTL exceeded .

Again, source will make TTL value to 3 and this continues until it reaches the destination or packet is dropped till source increments TTL value to 30.

**But how will the source know it has reached the destination ?** The packet contains the following information — **source address, destination address and an invalid UDP port.**

So, When the packet reaches the destination it hits an invalid port and will send a ICMP **Destination/Port Unreachable** message intead of TTL exceeded.
