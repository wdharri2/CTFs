## Challenge Name: Master Shackle
HackPack CTF
Category: web
Points: 490
Solves: 8

Challenge Location:
Mastershackle.cha.hackpack.club

### Approach

1) Upon landing on the home page, users are presented with two links. The first link,
"/flag", leads to a page with two input boxes. The second box only takes PEM-encoded
certificates, otherwise, the submission returns a stack trace. This indicates that a
specific signed certificate is needed to get the flag.

2) Checking the second page leads to "/crypto4kidz" which displays a description
of the Caesars Cipher and a handful of crypts. Decrypting the last crypt using
dcode.fr/caesar-cipher shows
"THEURLFORTHECAISATMASTERSHACKLEDOTCHADOTHACKPACKDOTCLUBSLASHMASTERCRYPT" meaning
users need to navigate to "/mastercrypt".

3) Upon navigating to "/mastercrypt", users land back on the home page. Using
Burp Suite's repeater, one can see on the redirect page an error message that says
"No cookie named token was passed, or the value was not correct!". This indicates
that users need to find a token to use as a cookie.

4) Looking at the source code of the home page, users can see javascript in the
head section. Within it is a commented-out endpoint "/cmd" and the endpoint for
the testimonials that display at the bottom of the home page. Users should take
note of the testimonials endpoint for later.

5) Users can navigate to "/cmd" to find a directory listing. Within it, a file
named "ca.token" can be found. Additionally, users find out that the testimonials
file most likely exists within the testimonials directory that is displayed.

6) Using the testimonial endpoint from step 4, "/testimonials?path=testimonials.json",
users should replace testimonials.json with a directory to crash the navigation.
For example, using the root directory so the link looks like "/testimonials?path=/".

7) This will show the code for the testimonials retrieval in the server. The code
indicates that if the path variable in the endpoint does not start with
"/var/www/html/resources/", or the directory starts with "/var/www/html/resources/ca/"
or "/var/www/html/resources/views/", the request will return with the testimonials
file. This limits the extent of directory traversal. Additionally, the path will
always be appended with "/testimonials" so users always have to navigate out of that directory.

8) Given the logic, users can avoid the protections against directory traversal
by setting the path to "../ca.token". The function will convert this string to
"/var/www/html/resources/testimonials/../ca.token" which reduces to
"/var/www/html/resources/ca.token". Using "/testimonials?path=../ca.token", users
can find the token printed on the web page.

9) After creating a new cookie called "token" and setting it to the previous string
then navigating to "/mastercrypt", users land on a welcome page.

10) Navigating to the login page, users are presented with username and password
prompts. Users can submit a username and password while using Burp Suit to intercept.
Changing the POST request to PUT will redirect users to an error page. The error
page will show that usersname and password are sent in a JSON tag, with the values
being surrounded by double quotes. This indicates that there is a possibility that
SQL injection starting with a double quote will allow code execution.

11) Users can use SQL injection by putting'" or 1=1-- -' in either box of the login
page and anything else in the other box. This will show all usersnames on the server.
Users can pick one. For example, "026e71a".

12) Users can put the username in the username box with '"-- -' so the entire string
is '026e71a"-- -' and an arbitrary password to log in. This works by commenting out
the check for a password and it maintains that there is a username and the username is correct.

13) Upon navigating to the "List all CAs" link, users will see that there is a
"MasterShackle Flag SubCA" CA that exists.

14) If users goes to the "View CA Details" link, they can only view "MasterShackle SSL SubCA"
in the dropdown list. By submitting the form, every username will be displayed
since the account used to log in is in the most general group.

15) Users can go back to the submit page with the dropdown and click submit
while Burp Suite is intercepting the submission. A value called "caid" will be found,
which represents the group users belongs to. By changing it to equal 3, only one
username will be seen in the list.

15) Users can use that username to log in using the same SQL injection method as
in step 11.

16) Once logged in, users can navigate to the "Issue a cert" link and change the
dropdown option to "MasterShackle Flag SubCA".

17) To generate a certificate signing request, users can use
"openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr"
on Linux and follow the instructions. They can then paste the contents of "server.csr"
into the text box on the Master Shackle page they were just on to obtain a new
certificate request to use later.

18) Users can then navigate back to the "/flag" endpoint and copy the signed
challenge at the top of the page. They should not close this page or they will
lose the signed challenge. Then navigate to the "Sign arbitrary content" link on
the welcome page and paste the signed challenge into the first box. The contents
of "server.key" should be pasted into the second box.

19) Users can then copy the output and paste it into the second box of the "/flag"
page. Paste the certificate request gained in step 17 into the first box.

20) By following these steps, users can obtain the flag: "flag{Upfmnhx1/KsMmrxfYGiLLTicLvMc2YTqV4ivOHWTIsKHqcUsJIuOTFJ2njd2ueOCgf7jrIVahuyU948z3lUM2A==}".
