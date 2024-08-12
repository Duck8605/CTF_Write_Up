# FindAndOpen

## Problem:

Someone might have hidden the password in the trace file.
Find the key to unlock this [file](https://artifacts.picoctf.net/c/494/flag.zip). [This tracefile](https://artifacts.picoctf.net/c/494/dump.pcap) might be good to analyze.

## Basic Idea of the Problem:

The challenge basically gives you a zip file and a pcap file then simply expects you to find the flag. 

## Solution:

It appears we are suppose to find the password for the zip file somewhere within the pcap.

![image](https://github.com/user-attachments/assets/5c72cf9c-92ec-4266-9f41-71b12d48cbf7)

At first, I try `strings` and there is some text

![image](https://github.com/user-attachments/assets/d7621a8f-1ef1-4016-9784-810f26b19931)

![image](https://github.com/user-attachments/assets/a5cd638f-f551-4b9b-8bd4-882d1fe5815b)

The decoded hex for each of these packets seem to be messages of some kind. In total there are five unique strings:

```
1. Flying on Ethernet secret: Is this the flag
2. iBwaWNvQ1RGe1Could the flag have been splitted?
3. AABBHHPJGTFRLKVGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8=
4. PBwaWUvQ1RGesabababkjaASKBKSBACVVAVSDDSSSSDSKJBJS
5. PBwaWUvQ1RGe1Maybe try checking the other file
```

Sadly none of the five strings are the password to flag.zip, but there is something about string #3. That string looks to be encoded in base64, an we can tell by the ‘=’ which is used as the padding character for base64 encoded strings. From the wikipedia article for base64…

>… when the length of the unencoded input is not a multiple of three, the encoded output must have padding added so that its length is a multiple of four. The padding character is =, which indicates that no further bits are needed to fully encode the input.
>
Example:

![image](https://github.com/user-attachments/assets/e3e2d5ce-b445-4643-ad38-572f438d70b2)

Let’s try to decode string #3 that looks to be base64 encoded, I use [kt.gy](https://kt.gy/)

![image](https://github.com/user-attachments/assets/e3045e57-87da-4a46-92cc-11a3257640a1)

Oh wait a second the length of any base64 encoded string must be a multiple of 4, but our string has a length of 70 characters. We have to fix it somehow so it can be decoded correctly. Let’s start by making the string length a multiple of 4. We can do this by simply appending 2 characters…

![image](https://github.com/user-attachments/assets/07ea98d9-9797-479f-8ecc-48e1d5a0d04a)

Nice, but that isn’t the full flag. Where is the other part of our flag? Well remember the five strings we extracted? Take a look at string #2 and #5…

```
2. iBwaWNvQ1RGe1Could the flag have been splitted?
5. PBwaWUvQ1RGe1Maybe try checking the other file
```

So the flag is separated, and maybe the rest is in the other file (flag.zip)? Try unlocking the zip with the partial flag we got by decoding string #5.

![image](https://github.com/user-attachments/assets/9521f43d-d85b-43e6-a301-91303bc19761)

![image](https://github.com/user-attachments/assets/a274a843-2462-4b6c-9147-f93f58c9cfeb)

And we got out flag!!!

## Flag:

picoCTF{R34DING_LOKd_fil56_succ3ss_b98dda6a}

