### Aadi Akyianu and Lazuli Kleinhans

## ======== DAYTIME ========



1. Identify the parts of the TCP 3-way handshake by listing the frame summaries of the relevant frames.
    * 1	0.000000000	192.168.204.129	132.163.96.4	TCP	74	43746 → 13 [SYN] Seq=0 Win=32120 Len=0 MSS=1460 SACK\_PERM TSval=2211560475 TSecr=0 WS=1024
    * 2	0.120718826	132.163.96.4	192.168.204.129	TCP	60	13 → 43746 [SYN, ACK] Seq=0 Ack=1 Win=64240 Len=0 MSS=1460
    * 3	0.120788864	192.168.204.129	132.163.96.4	TCP	54	43746 → 13 [ACK] Seq=1 Ack=1 Win=32120 Len=0

2. What port number does the client (i.e. nc on your Kali computer) use for this interaction? 
    * Port 52678 

3. Why does the client need a port?
    * Because there are several other processes and servers running on the client so it needs a port to distinguish this interaction from any other processes that may be running.

4. What frame contains the actual date and time? (Show the frame summary as in question 1 above.)
    * 4	0.164640818	132.163.96.4	192.168.204.129	DAYTIME	105	DAYTIME Response

5. What do [SYN] and [ACK] mean?
    * SYN means Synchronize which is a request to synchronize the sequence numbers between the client and the server
    * ACK means Acknowledge which is a response to let the server know that a packet has been received.

6. Which entity (the nc client or the daytime server) initiated the closing of the TCP connection? How can you tell?
    * In my case the nc client seems to have initiated the closing. The fist instance of a FIN request came from my source address.
    * However it was a [FIN, ACK] request so I'm not too sure.
    * 5	0.132397655	192.168.204.129	132.163.96.4	TCP	54	52678 → 13 [FIN, ACK] Seq=1 Ack=53 Win=32068 Len=0


## ======== HTTP ========


1. How many TCP connections were opened? How can you tell?
    * There were 2 TCP connections set up.
    * You can tell because the TCP handshake is done twice. Once from the client to the server and another time from the server to the client.

2. Can you tell where my homepage (index.html) was requested? (If not, why not? If so, include frame summaries and/or other info that supports your answer.)
    * You can tell where it is requested because there is a [GET] request for /index.html
    * 4	0.024768930	192.168.204.129	172.233.221.124	HTTP	409	GET /index.html HTTP/1.1 

3. Can you tell where my photograph (jeff-square-colorado.jpg) was requested? (If not, why not? If so, include frame summaries and/or other info that supports your answer.)
    * It was requested here:
    8	0.580481051	192.168.204.129	172.233.221.124	HTTP	382	GET /jeff-square-colorado.jpg HTTP/1.1 
    * You can tell because of the GET request for /jeff-square-colorado.jpg

## ======== QUESTIONS ========

1. What does a SYN or an ACK or a FIN packet actually look like?

2. How does the server break up a packet when it's too big?
