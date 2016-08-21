---
layout: post
title:  "วิธีเข้า Server Parallel"
date:   2016-08-21 16:34:00 +0700
categories: tool
---

### Compile
- C: `mpicc -o a a.c`
- CPP: `mpic++ -o a a.c`

### Execute
`mpiexec -n 5 a`

#TH
## Windows

1. [Download PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
![putty](http://i.giphy.com/l0HlUpSQn024AUgy4.gif)

2. ติดตั้ง PuTTy แล้วเปิดขึ้นมา (Install then open putty prgrame)
![open putty](http://i.giphy.com/l2Sq3fq4twupfGXte.gif)

3. กด "Yes" และใส่ account, password ที่ให้ไว้ใน Facebook Group (Click "Yes" then type account and password which I've post on facebook group.)
![putty access](http://i.giphy.com/l0HlHoylfdg04Yakw.gif)

4. สร้างโฟลเดอร์ของตัวเอง (Make your folder with your student ID)
![mkdir](http://i.giphy.com/l2SpVyIqZk5K9I2hG.gif)

5. ให้เข้าไปที่โฟลเดอร์ของตัวเอง (Go to your directory)
`cd 560705032xx`

6. [Download WinSCP](https://winscp.net/eng/download.php)
![WinSCP Download](http://i.giphy.com/3o6ZsTeCxeC2GcVOJq.gif)

7. ติดตั้งและเปิดโปรแกรม WinSCP (Install then open WinSCP)
![WinSCP Access](http://i.giphy.com/l0HlyuF9YtgxOysh2.gif)

8. โยนไฟล์เข้าไปในโฟลเดอร์ของตัวเอง แล้ว ใช้ PuTTy ในการเข้าไปรันคำสั่ง (Put your file via SCP then use PuTTy to compile and execute your program)
![Transfer](http://i.giphy.com/l0MYNUnqkbzA4r2bC.gif)
![01](http://i.giphy.com/l0MYDs6qRLeYcgzjq.gif)
![02](http://i.giphy.com/l0MYFHFk4trh8sPyU.gif)

### Notes:
สำหรับไฟล์ `.cpp` ให้ใช้คำสั่ง `mpic++` แทน `mpicc` นะครับ
For `.cpp`, use `mpic++` instead of `mpicc`

## Linux
1. ไปที่ Directory ที่เก็บไฟล์ไว้ แล้วทำการ `scp`  ไฟล์ขึ้นไป (Go to directory which keep the src code in your machine, then use `scp` command to transfer file to the server.)
![mac01](http://i.giphy.com/3o7TKTnpWWudsZnU1a.gif)

2. `ssh` เข้าไปที่ `Host` ครับ (Use `ssh` command to access to the server.)
![mac02](http://i.giphy.com/26ufoughbfjmwDz4Q.gif)

3. คอมไพล์และรันเลยครับ (Compile and execute your code)
![02](http://i.giphy.com/l0MYFHFk4trh8sPyU.gif)
