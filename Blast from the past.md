# Blast from the past

## Problem:

The judge for these pictures is a real fan of antiques. Can you age this photo to the specifications?
Set the timestamps on this picture to 1970:01:01 00:00:00.001+00:00 with as much precision as possible for each timestamp. In this example, +00:00 is a timezone adjustment. Any timezone is acceptable as long as the time is equivalent. As an example, this timestamp is acceptable as well: 1969:12:31 19:00:00.001-05:00. For timestamps without a timezone adjustment, put them in GMT time (+00:00). The checker program provides the timestamp needed for each.
Use this [picture](https://artifacts.picoctf.net/c_mimas/74/original.jpg).
Submit your modified picture here:
nc -w 2 mimas.picoctf.net 55022 < original_modified.jpg
Check your modified picture here:
nc mimas.picoctf.net 64706
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

And BINGOO!!! We got out flag. Pretty simple challenge but yeah, thinking of the command could take some time.

## Flag:

picoCTF{ax8mC0RU6ve_NX85l4ax8mCl_52a5e2de}
