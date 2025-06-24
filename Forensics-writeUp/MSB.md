# MSB

## Problem:

This image passes LSB statistical analysis, but we can't help but think there must be something to the visual artifacts present in this image...
Download the image [here](https://artifacts.picoctf.net/c/307/Ninja-and-Prince-Genji-Ukiyoe-Utagawa-Kunisada.flag.png)

## Basic Idea of the Problem:

The challenge basically gives you a png file and then simply expects you to find the flag. 

## Solution:

At first, let's check the type of file

![image](https://github.com/user-attachments/assets/3a23f6fd-e2d2-43f7-b480-d41149f661bf)

Since the challenge is called MSB, I put the image through this [online steg solver](https://stegonline.georgeom.net/upload), upload the file and chose Extract Files/Data

![image](https://github.com/user-attachments/assets/2f0e6f62-cfde-42c4-94ed-3ce6b9359b90)

![image](https://github.com/user-attachments/assets/1b65e9b2-5000-4101-a253-984020268769)

![image](https://github.com/user-attachments/assets/ea8de3ce-2cc7-44b0-a60c-0c094488682a)

Click the `go` button

![image](https://github.com/user-attachments/assets/5d3a222e-728e-410f-ac76-2fa18ee48acd)

Then, download the extracted data

![image](https://github.com/user-attachments/assets/4d178fa8-453e-4507-91d6-52f1486f9a65)

This extracted file is an ASCII text

![image](https://github.com/user-attachments/assets/a65c1fca-5d27-49a3-8fa2-915aff434e7a)

so that we can find the flag by `cat` and `grep` command

![image](https://github.com/user-attachments/assets/65171d14-fc56-40a4-b7f5-8f5fe3358cc1)

And we got out flag!!!

## Flag:

picoCTF{15_y0ur_que57_qu1x071c_0r_h3r01c_b5e03bc5}

