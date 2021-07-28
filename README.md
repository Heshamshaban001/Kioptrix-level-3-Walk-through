# Kioptrix-level-3-Walk-through

scan the target using nmap
Nmap -sn 192.168.1.*

got the kioptrix vm ip at

Nmap -sC -sV 192.168.1.104

found some open ports ( 22-80)

now lets walkthrough each port and see what we can do (separately and combined):

surfing the web server 
---


![image](https://user-images.githubusercontent.com/52453415/127151497-d00d97cd-4f6e-41ee-92a6-459562ac40fc.png)

i've noticed some things 

1-the login page may have vulnarable to sqli 
 

2- this comment section may be vulnerable to xxs 

3- this name must mean something , so i'll assume that name is associated with username to ssh and i'll attempt password brute-forcing and let it work in background untill i finish surfing 

hydra   -e nsr -l $user -P /home/kali/10-million-password-list-top-100000.txt $host ssh -t 4


# trying basic sqli commands in password filed doesn't work 

but when i put i single quote to the url it reveals an error of eval function 

![image](https://user-images.githubusercontent.com/52453415/127153115-a2df707e-d0f2-42d4-888c-8d9ccbeb2310.png)


search the web for this error I've learned that error associated with php eval() function and this has an exploit 

searching meta sploit for the exploit 

![image](https://user-images.githubusercontent.com/52453415/127154117-182ac9d7-e281-4240-ba22-0a06a83c5a31.png)

same content management as our sever use sounds promising 

set the payload URI and RHOST  then Run 

![image](https://user-images.githubusercontent.com/52453415/127154542-a0f6cab9-9ad3-4f8d-b48d-7e9d38830b29.png)


exploit done but no session was created 

changing the payload to payload payload/php/reverse_php and try again ; it works 

whoami >> www-data

upgrading the shell

python -c 'import pty; pty.spawn("/bin/bash")'

cat /etc/passwd 

![image](https://user-images.githubusercontent.com/52453415/127160383-768e1237-9820-40c7-9621-f3b8395e3a3d.png)

cat /etc/ssh/ssh_host_rsa_key

not permitted 

no file transfer is allowed for this user and no permission  to create any files either , so i couldn't transfer exploits or create them there 

at the mean time i continue surfing the the machine 

i've found Companypolicy.txt inside lonferrete folder 

this file sys that we must use sudo before ht when dealing with software editing 

so what's this ht and i defiantly must be in soduers to use it 

so i couldn't know more with this user so i've to  wait the brute force hoping to reveal loneferrete password 

# now the brute force attack found the password  and it is " starwars " 

ssh lonferret@192.168.1.9

password : starwars 

now sudo ht 

it's ahexa editor with setuid falg and owned by root , perfect tool to edit our privliage 

so we are going to edit sudoers files in /etc/sudoers/

sudo ht /etc/sudoers/ 

Next, change mode to text by pressing F6, you’ll notice the following line:

loneferret ALL=NOPASSWD: !/usr/bin/su, /usr/local/bin/ht  

there are no edit in txt mode so i headed back to hex mode and replaced the ! with 0x20 

then try sudo su 


![image](https://user-images.githubusercontent.com/52453415/127290084-6bceaf4c-33b2-4073-8e34-607f3d8f1525.png)

it didn’t work. su is under /bin/su, not /usr/bin/su…

so we have to replace the !/usr/bin/su, with /bin/su 

replaces all hex values for these chars with 0x20 

![image](https://user-images.githubusercontent.com/52453415/127290438-c63ee6de-fa7b-477d-9660-772e2c8a4e64.png)

now f2 to save and f10 to quit 

sudo su 

bingo 

whoami 

root




