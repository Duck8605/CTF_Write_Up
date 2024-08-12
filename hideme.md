# hideme

## Problem:

Every file gets a flag.
The SOC analyst saw one image been sent back and forth between two people. They decided to investigate and found out that there was more than what meets the eye [here](https://artifacts.picoctf.net/c/259/flag.png).

## Basic Idea of the Problem:

The challenge basically gives you a png file and then simply expects you to find the flag. 

## Solution:

At first, let's check the type of file

![image](https://github.com/user-attachments/assets/c8f27ab2-080b-4da0-ad82-a071153dea68)

Then I try to read some information about this file by `exiftool`

![image](https://github.com/user-attachments/assets/983b02f4-580e-422f-91e9-64c445b4b267)

There is a warning `[minor] Trailer data after PNG IEND chunk`

I use `strings` and there was no flag, however I did find references to a "secret" and "secret/flag.png", but nothing in the form of picoCTF{flag}.

![image](https://github.com/user-attachments/assets/42ed0a7b-b45b-46fe-a426-df7445a93f3b)

So I use `binwalk` to identify a zip archive appended to the original flag image file

![image](https://github.com/user-attachments/assets/59d9b2b7-0465-448c-b856-8bace4e94290)

After that, unzip the file

![image](https://github.com/user-attachments/assets/0698a6ac-391a-4861-afef-9b2340d01c07)

![image](https://github.com/user-attachments/assets/91d1479f-e62b-4d79-800b-c89c9a41d258)

![image](https://github.com/user-attachments/assets/b23d63b0-e69f-40af-9595-4b0b86723a70)

And we got out flag!!!

## Flag:

picoCTF{Hiddinng_An_imag3_within_@n_ima9e_cda72af0}

