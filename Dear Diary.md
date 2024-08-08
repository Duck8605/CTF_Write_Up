# Dear Diary

## Problem:

If you can find the flag on this disk image, we can close the case for good!
Download the disk image [here](https://artifacts.picoctf.net/c_titan/63/disk.flag.img.gz).

## Basic Idea of the Problem:

The challenge basically gives you a disk image file and then simply expects you to find the flag. We need to find the right file. 

## Solution:

At first, let's check the type of file

![image](https://github.com/user-attachments/assets/f09f6ef0-ce1b-4f30-b5d4-f87aa986ac95)

So I need to unzip and check it again

![image](https://github.com/user-attachments/assets/d2ab13b4-c64a-4101-8cf1-d1faa4b9d1ca)

The given file is disk.flag.img, so I use autopsy to analyze it.

![image](https://github.com/user-attachments/assets/3061f214-1ffc-44d5-9028-d94c6b4a0c2e)

![image](https://github.com/user-attachments/assets/b13030ea-85b5-44aa-8663-69d47abc4304)

Create a new case and name it whatever you want, then add the path to the img file downloaded.

![image](https://github.com/user-attachments/assets/cfc9118d-ccf3-40c7-b348-81aadf55cf72)

I use all the default settings and analyze the raw disk file.

![image](https://github.com/user-attachments/assets/3763a097-6ae2-49ac-abf7-740dc19aacf9)

I then used keyword search with ASCII option to search for raw data. My first search is “.txt” to find if there’s a flag file. 

![image](https://github.com/user-attachments/assets/4672d8e2-a1bc-4509-a7d6-044865d5db61)

Although there’s no file named flag directly, I saw parts of the flag distributed around innocuous-file.txt

![image](https://github.com/user-attachments/assets/781aae90-a228-4034-aded-d36e223bc852)

![image](https://github.com/user-attachments/assets/64e59f05-cb71-4f9a-af66-e2bd0768e0fb)

![image](https://github.com/user-attachments/assets/88f2e742-d00b-4879-8f03-90e50a230171)

![image](https://github.com/user-attachments/assets/66812319-ddbe-4ce3-84ca-9a6b007a0528)

![image](https://github.com/user-attachments/assets/7c07e08c-a8c4-4caf-a24d-c9b91a8e6690)

![image](https://github.com/user-attachments/assets/e1ffcb7f-31fd-4668-99f9-89b5df5ae391)

![image](https://github.com/user-attachments/assets/b8a87e46-08b3-4eb3-a599-e9b5f2b5ce0b)

![image](https://github.com/user-attachments/assets/f7639a12-8b7f-4456-8028-b2927ecdd6fa)

![image](https://github.com/user-attachments/assets/13b59367-09aa-4fa4-9df2-0898ae13b7c0)

![image](https://github.com/user-attachments/assets/62d5fc4d-32aa-4dce-9fb8-7c1c9aea8bdf)

Just like that, we can continue to view these files, assemble all the parts found, and then send them in smoothly.
And we got out flag.

## Flag:

picoCTF{1_533_n4m35_80d24b30}
