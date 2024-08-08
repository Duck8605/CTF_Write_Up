# PcapPoisoning

## Problem:

How about some hide and seek heh?
Download this [file](https://artifacts.picoctf.net/c/377/trace.pcap) and find the flag.

## Basic Idea of the Problem:

The challenge basically gives you a pcap file and then simply expects you to find the flag. 

## Solution:

At first, let's check the type of file

![image](https://github.com/user-attachments/assets/ef8d0f0d-622f-49bb-993e-3cc0908d93a5)

I open the pcap file in Wireshark

![image](https://github.com/user-attachments/assets/bfa93ab0-5083-4bba-8055-477640e295d2)

We could have found the flag using the Find Packet (Ctrl+F) tool, specifying a search for packet bytes and using the string "pico" to search.

![image](https://github.com/user-attachments/assets/f1fa6a2b-9a33-4a48-aca7-824d61e26f47)

And we got out flag.

Or we could also have found the flag by using the "grep" command in Linux terminal

![image](https://github.com/user-attachments/assets/ce250b01-cb57-4ead-a105-ac2eaaba3669)

## Flag:

picoCTF{P64P_4N4L7S1S_SU55355FUL_31010c46}
