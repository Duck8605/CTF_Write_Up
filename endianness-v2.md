# endianness-v2

## Problem:

Here's a file that was recovered from a 32-bits system that organized the bytes a weird way. We're not even sure what type of file it is.
Download it [here](https://artifacts.picoctf.net/c_titan/35/challengefile) and see what you can get out of it

## Basic Idea of the Problem:

The challenge basically gives you a data file and then simply expects you to find the flag. We need to find the right type for this file. 

## Solution:

At first, let's check the type of file

![image](https://github.com/user-attachments/assets/2de342bb-3c10-4d4a-a749-78dbf1eef4b4)

So nothing much just data. So now I opened it on the hex editor and had a look at its data. Well it was weird and for some time I didn't have any idea. But the problem is named ‘endianness-v2’ so it has something to do with endianness order right?

So on this thinking, I took the first few bytes of the file and tried to see there different endianness on this [site](https://www.scadacore.com/tools/programming-calculators/online-hex-converter/)

and this is what I got:

![image](https://github.com/user-attachments/assets/21227586-d39f-4b8f-9c4a-66570833a767)

Now when I saw the results, something felt familiar. Little Endian form represented some magic numbers I suppose. So I checked for the magic numbers of different file types.

![image](https://github.com/user-attachments/assets/22dbcfdb-d151-4ec7-a34a-310f47812993)

These were the magic numbers of JPEG file.

So to solve this challenge what i had to do was:
> Take every 4 bytes of the file, arrange them in little-endian form
>

Researching online about converting a file from little endian to big endian, I stumbled upon this [forum](https://unix.stackexchange.com/questions/239543/is-there-a-oneliner-that-converts-a-binary-file-from-little-endian-to-big-endian) that has a single command to convert the bytes.
```
hexdump -v -e '1/4 "%08x"' -e '"\n"' input_file | xxd -r -p > output_file
```
So that, I try to convert this data file to a jpg file

![image](https://github.com/user-attachments/assets/7391f9dd-0e60-4a37-a4ad-53a7eaf84898)

![image](https://github.com/user-attachments/assets/5b8d0430-ea0a-4814-be81-aeafb24db105)

And BINGOO!!! We got out flag.

## Flag:

picoCTF{cert!f1Ed_iNd!4n_s0rrY_3nDian_b039bc14}
