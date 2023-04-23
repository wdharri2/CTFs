## Challenge Name: EZILA
Category: web
Points: 366
Solves: 17

Challenge Description:
BigCorp is going all in on the LLM chat-based AI craze and will soon be publicly releasing EZILA, their very own flag-protection chatbot.\n\nFor the moment, access is limited to beta testers who will use terminal shell access to invoke EZILA and make sure she will properly defend the flag.

nc cha.hackpack.club 41701

### Approach
After using Netcat to arrive at the server and ls command reveals 5 files
*CREDITS.txt  ezila.py  flag.txt  run-ezila  script.txt

Only CREDITS.txt has reading privileges
Using cat CREDITS.txt we get

*ELIZA-in-Python implementation forked from https://github.com/wadetb/eliza.

*If you like the challenge, go give the original repo a star!

*(If you don't like the challenge, blame us...)

Arriving at the repo shows an implementation of a chatbot using natural language processing

Running ./run-ezila allows you to interact with this chatbot

Our teams spent many hours trying to deteremine a vuleerability or clue from ezila's messages

Finally we entered the command python and received

Python always respects its environment with all its variables , no ?

So we looked up Python's enviroment variables to find one that allows us to utilize its shell enviroment, we found this site https://docs.python.org/3/using/cmdline.html

We found the -i command that can be passed to a script to enter interactive mode after the script is executed, unfortunately we do not have a script we have a binary

However the site was helpful enough to provide a link to PYTHONINSPECT which when set to a non-empty string is equivalent to -i

So after using PYTHONINSPECT=a ./run-ezila and quitting the conversation we were granted a python shell

Using import os we can start to make changes to the file and user permissions

We want to access the file with Ezilas permissions "1001" but using os.getuid() shows we have 1000

We tried os.setuid but that did not change the users id
We found os.resuid from here https://docs.python.org/3/library/os.html
This sets the current processes real effective and saved user ids

we confirm our user id is changed with os.getuid()
Then we simply use the cat command from python with os.system("cat flag.txt")

This gives us the flag flag{n3v3r_7ru57_a_ch@7b07_t0_cl3@n_th3_3nv1r0nm3n7}
