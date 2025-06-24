# Trickster

## Problem:

I found a web app that can help process images: PNG images only!Try it [here](http://atlas.picoctf.net:57185/)!

## Basic Idea of the Problem:

So, a very interesting but kind of obvious problem. You got a website where you are asked to upload png files. It doesn’t accept any but the PNG files. And after it uploads the png. It does nothing. Well basically, it was pretty obvious that it was a file upload vulnerability challenge.

## Solution:

So, first thing first, we needed to know what kind of check were done on the file we upload, so that it believes that well its a PNG file. So I did what every desperate Web Exploit challenge solver do, and hoped there was a robots.txt, and i was lucky because there was.

![image](https://github.com/user-attachments/assets/08fc33ba-20ef-4a0a-9fbc-84492e5d5d75)


so i tried to see the instructions.txt

![image](https://github.com/user-attachments/assets/b5dad966-bf88-409d-8085-1239a658840c)


Okay so we got the conditions we were looking for:

1. Our file should have the extension .png
2. It should have PNG file’s magic bytes.

Also when i tried to access the uploads folder, we didn’t have the permission. Maybe we need to access that.

Okay so now i first created an empty file called empty.png and then opened the proxy with Burp Suite and intercepted the request of uploading my empty.png on it.

In the request, i updated the name of the file to ‘payload.png.php’ and then wrote some HTML and PHP in it.

![image](https://github.com/user-attachments/assets/52d30b18-2fc1-45df-a022-8bc64c73797c)

Something like this. Basically what this will do will create a form for me, and will execute shell commands on the server.

Now lets access our payload by typing it in the URL and lets add some command in the URL as well for it to execute.

![image](https://github.com/user-attachments/assets/828b0607-88a1-4f41-8f99-a6ad7f9049aa)


We can see the files. Lets read the weird named txt. file.

![image](https://github.com/user-attachments/assets/f3259629-fc63-4565-b086-b6ffa1f29e17)


## Flag:

`picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_d3ac625b}`
