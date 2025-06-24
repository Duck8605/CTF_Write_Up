# Blast from the past

## Problem:

The judge for these pictures is a real fan of antiques. Can you age this photo to the specifications?
Set the timestamps on this picture to 1970:01:01 00:00:00.001+00:00 with as much precision as possible for each timestamp. In this example, +00:00 is a timezone adjustment. Any timezone is acceptable as long as the time is equivalent. As an example, this timestamp is acceptable as well: 1969:12:31 19:00:00.001-05:00. For timestamps without a timezone adjustment, put them in GMT time (+00:00). The checker program provides the timestamp needed for each.

Use this [picture](https://artifacts.picoctf.net/c_mimas/74/original.jpg).

Submit your modified picture here: nc -w 2 mimas.picoctf.net 55022 < original_modified.jpg

Check your modified picture here: nc mimas.picoctf.net 64706
## Basic Idea of the Problem:

The challenge give you a png file, you need to set the timestamps on the picture to 1970:01:01 00:00:00.001+00:00 with as much precision as possible for each timestamp.

## Solution:

Downloaded the the given picture, then I used exiftool and I have noticed there is some timestamps.

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past.png)

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past%201.png)

So need to set the timestamps on the picture to 1970:01:01 00:00:00.001+00:00.

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past%202.png)

Then, I try to submit and check my modified picture

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past%203.png)

But there are 4 more tags I need to modify SubSecCreateDate, SubSecDateTimeOriginal, SubSecModifyDate, TimeStamp

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past%204.png)

![image](https://github.com/Duck8605/ex-pic/blob/main/Blast%20from%20the%20past%206.png)

However, the tag TimeStamp isn't right because TimeStamp can't be edited by Exiftool.
After some search again I used strings original.jpg and I have found this

![image](https://github.com/user-attachments/assets/ffd16233-b510-42d5-8fcc-0a4e229ca641)

I have found it so now what is this number ? 1700513181420

It’s this date 2023:11:20 15:46:21.420–05:00 but in milisec unix timestamp

So it’s easy now we will convert 1970:01:01 00:00:00.001+00:00 to unix timestamp and replace it

So I used website called [Unix Time Stamp — Epoch Converter](https://www.unixtimestamp.com/)

I put the date I want then clicked convert and this was the output

![image](https://github.com/user-attachments/assets/46524ea8-c85a-4641-b581-d3b9accfefd2)


It’s 0000000000001 so now we want to replace the 0000000000001 with our old date so I used hexeditor
online [Hexedit](https://hexed.it/)

![image](https://github.com/user-attachments/assets/23d6d7fb-5b8c-430d-bff4-afa42259308e)

![image](https://github.com/user-attachments/assets/93649db6-9acc-4cea-9fdc-1aacf3f8f11d)

Then, let's check our picture one more time

![image](https://github.com/user-attachments/assets/7ebe8aab-045d-4cbd-9579-c54d4ad99385)

And BINGOO!!! We got out flag.

## Flag:

picoCTF{71m3_7r4v311ng_p1c7ur3_b5f7bcb5}
