---
published: true
---
![1pickle](/images/1Pickle.png "1pickle")

<https://tryhackme.com/room/picklerick>\



### Initial Enumeration


## nmap

\
My first step is always to find **as much information** as I possible
can about the victim machine.
\
To start, I made sure to pay attention to **what I was looking for**.\
      → 3 flags, or 'ingredients'.\
\
After hitting deploy I had my Active Machine (victim machine)\

![active](/images/active.png "active")

\
First off I started with tool **nmap**\
→ **Why did I start with nmap?** NMAP is the most common tool used to
scan and probe services on the internet. Learn more in this free THM
room: <https://tryhackme.com/room/ccpentesting>\
\
I used **nmap -vv -A -p- \[IP of the victim box above\]**.\
→ **-vv:** Increases verbosity level\
→ **-A:** activates service detection, operating system detection, a
traceroute and common script scanning.\
→ **-p-**: scan **all** ports; not just the common ones. In-case the
victim machine reassigned ports to evade scanners.\
\
**NOTE**: When I use nmap I always use -vv, -A, and -p- at a
**minimum** if time permits.\
→ If I\'m in a time crunch I just use -vv -A; if it does not produce
anything interesting I add -p- (scanning all ports does take a
significant amount of time)\
\
![active](/images/2screen.png "active")
\
• This part was the most interesting to me is in boxed in red:\
\
![active](/images/3screen.png "active")
\
**What it tells me?** Port 80 is open with an apache **web-server**.
Remember the Description of Pickle Rick? It mentioned exploring a web
application.\
\
First, I popped the **victim machine URL** in my **Host Machine
web-browser**. The webserver displays a message from Rick:\
\
![active](/images/screen4.png "active")
\
**Boom!** Since I was in a web-browser I went ahead and checked the
page source (I am trying to find out the most information I can at this
stage). The **Page Source** can be found with a **CTRL +U** on the
keyboard (in Mozilla Firefox).\
\
→ In the Page Source I find a note. It is no doubt from Rick:\
\
![active](/images/screen5.png "active")
\
Now I know the **Username**. I took note.\
\
Next thought:\
\
**What will web servers certainly have?** [files and directories.
]{style="background-color:#a52a2a;"}\
\
this is where **gobuster** comes in...

## gobuster

\
**Gobuster** is a very useful Web Enumeration tool. The idea behind
**gobuster** is that it tries to find valid directories from a word-list
of possible directories. I suggest [CC: Pen
Testing](https://tryhackme.com/room/ccpentesting) if you\'ve never used
gobuster, it is a free tryhackme room.\
\
the command I used was **gobuster dir -u \[victim box IP\] -w
/usr/share/dirb/wordlists/common.txt**\
**break down**:\
→ **dir**:- specifies I am brute-forcing to find directories\
→ **-u** **http://10.10.255.233**:- specifies the URL I am trying to
bruteforce (replace with your victim machine).\
→ **-w /usr/share/dirb/wordlists/common.txt**:- sets what wordlist I am
going to use to bruteforce. Kali has this out-of-the-box.\
\
![active](/images/4screen.png "active")\
\
**now** I could see what directories were present on Rick\'s web
server.\
\
/robots.txt is a text file present on most web-servers, and usually
provides information on the site (site-map, what contents can be crawled
by search engines, etc). It\'s a **great** place to start.\
\
→ Next, I returned to my web browser on my attack machine and added
**/robots.txt** to the end of my victim machine IP.\
→ http://10.10.255.233/robots.txt\
→ and Boom!\
![active](/images/5screen.png "active")\
\
I\'ll bet I\'ve found Rick\'s password. I took note.\
\
It is not on the directories list from the gobuster scan, but since it
is a **web server**, and we possibly have a **username and password**,
It is generally a good idea to check if **/login.php** exists (other
examples are /admin.php, etc.)\
\
And there we go:\
\
![active](/images/6screen.png "active")\
\
next I entered in the username and password I had noted.\
\
It worked! **I\'m in**.\
\
![active](/images/7screen.png "active")\
\
Honestly at this stage I had never seen a website portal with a box to
enter **commands**. Seemed pretty weird.\
\
**Why would an admin have a command panel on a web server?**\
\
I entered command **ls -al**\
**Breakdown**:\
→ **ls**:- lists directories and files\
→ **-a**:- shows all files hidden or not hidden\
→ **-l**:- shows more information as well as file permissions in an
organized way\
\
![active](/images/8screen.png "active")\
\
two files immediately popped out. **Sup3rS3cretPickl3Ingred.txt** and
**clue.txt**\
\
I tried to enter the command '**cat** **clue.txt**' which would output
the contents of a text file. Below was the output! Ahh!\
\
![active](/images/9screen.png "active")\
\
**Note**: Upon reflection after, maybe that is why Rick would put a
command panel in a web-browser; to restrict the use of certain
**commands**.\
\
Next I concluded (after guidance and struggle) to research into the
other commands that would allow me to **output** the contents of a
**.txt file**. First result on google:\
\
![active](/images/10screen.png "active")\
\
![active](/images/11screen.png "active")\
\
Let\'s try. The less command is best for longer files, **it** **still
outputs a .txt**. Which is what I need.\
\
![active](/images/12screen.png "active")\
\
Now I\'ve found a command that **works**\...and a **clue**! Next I
tried with Sup3rS3cretPickl3Ingred.txt\
\
![active](/images/13screen.png "active")\
\
**Looks like a SUPER SECRET INGREDIENT! TWO TO GO!**
\
**I wasn\'t sure where to go from here so I went back to the gobuster
scan (which I threw in my notes to refer back later). I knew there are
other folders on this web server. Remember /assets ?**\

![active](/images/14screen.png "active")\
\
/assets did not end up being anything of use. As we can see below.**\
\
![active](/images/15screen.png "active")\
\
Stuck, I re-assessed what I knew at this point. I have access to
**Rick\'s web server**. clue.txt suggested I **look around the file
system** for the other flags.\
→ I\'ll bet Rick probably has a **home folder**\
→ I went back to the command prompt and entered ls -la /home/rick\
\
![active](/images/16screen.png "active")\
\
![active](/images/17screen.png "active")\
\
Now how do I **access** the file **second ingredients**?\
\
→ **Remember** I can\'t 'cat' it out\
\
I tried less again due to my success earlier. This time less
/home/root/\"second ingredients\".\
→ note that there is a **blank space** between second & ingredients. So
I input as a string.\
\
![active](/images/18screen.png "active")\

![active](/images/19screen.png "active")\
\
**THERE WE GO! FLAG 2!**\
\
next it looks like the only option remaining is the root folder. Maybe
I need higher permissions?\
\
next I tried sudo -l (sudo for root user and -l to list what I need to
gain root power)\
\
![active](/images/20screen.png "active")\
\
sudo ls -al /root/\
\
![active](/images/21screen.png "active")\
\
sudo less /root/3rd.txt into the command panel\
\
![active](/images/22screen.png "active")\
\
**LOOKS LIKE THAT IS THE LAST INGREDIENT! WOHOO!**

### Lessons Learned:

\
The best lesson I learned is when you get stuck, it\'s ok to step
back, start over, and recon recon recon.\
\
Also there were times that I was asking myself a question, that I
could just as easily google. As you saw with 'Less', a simple google
search pointed me directly to the main solution.\
\
Doing write-ups for CTF\'s helps create a professional portfolio, also
helps me really learn and own the solutions, and helps other people
learn the CTF.\
\
If you are reading through this and have any comments/ suggestions on
how I can improve the next write-up. I\'ll try every write up to explain
not only how I found the flags but my process and struggles. Please let
me know. Via this form:\
\
