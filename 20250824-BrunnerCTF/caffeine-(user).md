# Description
```
 Boot2root

Difficulty: Beginner
Author: Quack

This new coffee shop recently opened and you've been waiting too long for your daily caffeine injection! Can you find a way to see a little more than just your order status?

    Boot2root challenges are about privilege escalation, normally from a low-privilege user to administrator, or "root". The initial access is often from a web application and typically involve getting a reverse shell or some other remote code execution (RCE) on a server. From there, the point is to get root access on the system. These challenges are split into two parts: user and root. They can only be solved in order.

The user flag is in a file called user.txt.

Note: The series continues in Caffeine (Root).
```

# Attachments
<img width="1884" height="1565" alt="image" src="https://github.com/user-attachments/assets/f43fe706-ef64-4bb2-b78f-30fc587078a0" />


# Solution
Identify that this is some sort of injection exercise. Then, enter common injection commands to text fields to see if you see something. \
It turns out that bash commands work. For example: \
```shell
; ls
```
displays files in some directory. \
Then you just navigate through and find user.txt\
