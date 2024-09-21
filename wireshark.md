# Wireshark assignment

Written by Kendra Winhall

## DAYTIME

1. Identify the parts of the TCP 3-way handshake by listing the frame summaries of the relevant frames.
    * First, the nc client sends a `SYN` packet to the daytime server, as shown by Frame 1:

    `1  192.168.64.3  132.163.97.3  TCP  58266 → 13 [SYN] Seq=0 Win=32120 Len=0 MSS=1460 SACK_PERM TSval=981588626 TSecr=0 WS=128`

    * Second, the daytime server sends a `SYN-ACK` packet to the nc client, as shown by Frame 2:

    `2  132.163.97.3  192.168.64.3  TCP  13 → 58266 [SYN, ACK] Seq=0 Ack=1 Win=65535 Len=0 MSS=1386 WS=64 SACK_PERM`

    * Third, the nc client sends an `ACK` packet to the daytime server, as shown by Frame 3:

    `3  192.168.64.3  132.163.97.3  TCP  58266 → 13 [ACK] Seq=1 Ack=1 Win=32128 Len=0`

2. What port number does the client (i.e. nc on your Kali computer) use for this interaction?
    * As shown by the summary of Frame 1, which says `58266 → 13` (source port is `58266`, destination port is `13`), the nc client uses port `58266`.

3. Why does the client need a port?
    * Every client needs a different port number so that the server can send the appropriate information to each unique client. If a particular client didn't have a port allocated to it, then the server wouldn't know where to send data so that it reaches that particular client.

4. What frame contains the actual date and time? (Show the frame summary as in question 1 above.)
    * Frame 4 contains the date and time, as evidenced by its protocol and information sections:

    `4  132.163.97.3  192.168.64.3  DAYTIME  DAYTIME Response`

5. What do [SYN] and [ACK] mean?
    * `[SYN]` is a *synchronize* packet, which is a request from a device to establish a TCP connection with another device (in other words, to *synchronize* communication between devices). 
    * `[ACK]` is an *acknowledge* packet, which is confirmation that a device received a message from another device (in other words, to *acknowledge* the message).

6. Which entity (the nc client or the daytime server) initiated the closing of the TCP connection? How can you tell?
    * See Frame 5, which is the first time that the FIN packet is sent:

    `5  132.163.97.3  192.168.64.3  TCP  13 → 58266 [FIN, ACK] Seq=52 Ack=1 Win=66560 Len=0`

    The source IP is `132.163.97.3` (which is the IP of the daytime server) and the source port is `13` (which is the port number that corresponds to the Daytime Protocol). This means that the daytime server initiated the closing of the connection.

## HTTP

1. How many TCP connections were opened? How can you tell?
    * 5 TCP connections were opened, as evidenced by the following screenshot, which shows 5 instances of `SYN` packets followed by `SYN-ACK` packets.
  <img width="1440" alt="tcp-connections" src="https://github.com/user-attachments/assets/f20b7c4b-5393-42a1-bffc-42b34402034c">


2. Can you tell where my homepage (index.html) was requested? (If not, why not? If so, include frame summaries and/or other info that supports your answer.)
    * Yes. See Frame 26, which shows the `HTTP GET` request for `index.html`:

    `26  192.168.64.3  172.233.221.124  HTTP  GET /index.html HTTP/1.1` 

3. Can you tell where my photograph (jeff-square-colorado.jpg) was requested? (If not, why not? If so, include frame summaries and/or other info that supports your answer.)
    * Yes. See Frame 30, which shows the `HTTP GET` request for `jeff-square-colorado.jpg`:

    `30  192.168.64.3  172.233.221.124  HTTP  GET /jeff-square-colorado.jpg HTTP/1.1`

## QUESTIONS

* In the HTTP part of this assignment, just going to Jeff's website resulted in over 600 packets being sent. We've learned about how to spot the opening/closing of TCP connections on Wireshark, but how do we interpret all the packets in between? What important processes are happening that we haven't learned about?

* We've skimmed the surface of how to use Wireshark in this assignment. What useful features does Wireshark have that we haven't learned about or used yet?

* I know how to interpret a few parts of the "info" section for most frames (like `Seq` and `Ack`). But I don't understand most of it (like `MSS` or `SACK_PERM`). Can we learn more about what information is being transmitted in these packets?