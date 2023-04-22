## Challenge Name: Penguinator
HackPack CTF
Category: web
Points: 449
Solves: 16

Challenge Description:
Penguinator, LLC is launching their ground-breaking eponymous web platform for military-grade image encryption next week and they need some beta-testers to kick the tires and make sure their MVP offering is solid.\n\nUnfortunately, a disgruntled Penguinator employee leaked the contents of an administrator's authentication token/cookie on a Dark Web hacking forum just last week:\n\n/SsLocjiwUwqJW7uuAaD2ufL2ok0RaOZTXokZ77E1rjtIqkQpTKGuvkE0s+8vC3qlpRciGEo4PnE0BuYMOGYtA==\n\nThe CSO is concerned and wants you to see if there's any way an outsider could exploit this token to gain administrative access to Penguinator's new web platform!

### Approach

1)  The website hints to the fact that the Penguinator uses AES-256 which is a
    Block cipher, which always encrypts the same contents the same way given the same key.
2)  Users will log in with any credentials with Burp Suite intercepting traffic.
    Send the request to the repeater and users will see that the auth cookie changes
    after each time the request is sent. Only a certain portion is changing indicating
    that the cookie's value depends on the time.
3)  Since this is a block cypher, users can find out where the portion of the
    cookie correlates to the time.
4)  Replacing the auth cookie with the administrators cookie will reveal that it is expired.
5)  The portion of the cookie that correlates with the username can be determined by
    inputting "admin" into the username box. This will show
    "Hey, admins aren't allowed to log in during a CTF!" indicating that the
    username length needs to be five characters to replicate determine the
    delineation between the username and time.
6)  By typing "aaaaa" into the username box and sending the request to the repeater,
    users get a new auth cookie.  For example,
    "QHzh16DiuCgqDG0cGGA9lgRqwkkKgGgCx21jm96DSSZhJPCvpXUhF+u5935i1Jx++smyMZ2+LEB0RK/gLV6Eig==".
7)  Next, users will make another request to get a new but similar cookie. For example,
    "QHzh16DiuCgqDG0cGGA9lgRqwkkKgGgCx21jm96DSSYMKd7LpUeAYZJVKfldSt+oiFwspflZV35Xzc4oV7wQUg==".
8)  Users will make one more request, but this time changing the username to "aaaab".
    The cookie is "sEgTveFnfEca6CahpbnZAQRqwkkKgGgCx21jm96DSSYFUfGk6Aj7Zlpwlyf4187Fjvay3PBimejDQlzW4/3g5w=="
    in this case.
9)  It can be seen that after 22 characters, the cookies match.
    This indicates a part of the time string that is unchanged, most likely the hour.
10) Users will place 22 characters of the admin cookie in their login request: "/SsLocjiwUwqJW7uuAaD2u"
    combined with the rest of their user generated cookie:
    "n/3Mgl8C9iVue0Fg0Wn9p63dcHbymwY9qo1fwMBAVaQDmSApXeAt+1lgPrdrTeFQ==" to make
    the final cookie: "/SsLocjiwUwqJW7uuAaD2un/3Mgl8C9iVue0Fg0Wn9p63dcHbymwY9qo1fwMBAVaQDmSApXeAt+1lgPrdrTeFQ==".
11) This will show the user "checkAuth: bad auth bundle (invalid character...".
    This indicates that
12) Since the cookie did not work because a block decoded incorrectly, users will
    remove one character from the admin cookie and add one to the user's cookie.
    For example: "/SsLocjiwUwqJW7uuAaD2sn/3Mgl8C9iVue0Fg0Wn9p63dcHbymwY9qo1fwMBAVaQDmSApXeAt+1lgPrdrTeFQ=="
13) This will display the flag as "flag{c@n_y0u_s33_p3ngu1ns_thr0ugh_my_cryp70?}"
