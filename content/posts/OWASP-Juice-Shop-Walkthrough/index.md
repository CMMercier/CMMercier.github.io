---
title: "OWASP Juice Shop Walkthrough"
date: 2021-09-19
description: "I run through the steps I took to get my own personal Juice Shop up and running easily and for free and then I explain the steps I took to complete each task
on the scoreboard."
menu:
  sidebar:
    name: OWASP Juice Shop Walkthrough
    identifier: OWASP Juice Shop Walkthrough
    weight: 2
---

### Setup

For this I visited https://elements.heroku.com/buttons/bkimminich/juice-shop and looked through the many options to get my own personal instance
of Juice Shop setup. The first option, Deploy on Heroku for free, caught my eye. For this I simply had to:

1. Sign up to Heroku and log in.
2. Click the provided deploy button.

This really is the fastest and simplest way to get setup, and its free!

After I clicked deploy I had to give the app a name and wait for it to build. Really the longest part of this was waiting for it to build.
Now that the Juice Shop app is up and running its time to get hacking!

Note: Hacking progress is restored on server resets but deleting your cookies will destroy your progress!

### 1 * Challenges
##### Scoreboard
Category: Miscellaneous

First to find access to the scoreboard. I remembered it existed but not where and forgot that it was actually a part of the challenges so I took
to google and did a quick search for its location.

https://exampleapp.herokuapp.com/#/score-board

First challenge complete: Score Board (Find the carefully hidden 'Score Board' page.)

##### Bonus Payload

Category: XSS

This challenge is as simple as adding the provided payload to the search function.

`<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay"
src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show
_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>`

##### Bully Chatbot

Category: Miscellaneous

To access the Chatbot I created a new account on the site then opened up the menu and selected the support chat. I really just had to send any
message containing the words "coupon code" enough times for it to give up and hand over a code.

##### Confidential Document

Category:  Sensitive Data Exposure

The hints:

>Somewhere in the application you can find a file that contains sensitive information about some - potentially hostile - takeovers the Juice Shop top management has planned.

>    -Analyze and tamper with links in the application that deliver a file directly.

>    -The file you are looking for is not protected in any way. Once you found it you can also access it.

In the About Us section I found a link "Check out our boring terms of use if you are interested in such lame stuff." that opened up a download
window to get a legal.md document. I opened up burp suite and intercepted this request there and noticed the GET request was to a ftp server.

So I checked to see if something simple like https://exampleapp.herokuapp.com/ftp would work and it did!

![ftp page](/images/posts/owasp/ftp.png)

##### DOM XSS

Category: XSS

> Perform a DOM XSS attack with `<iframe src="javascript:alert(`xss`)">`.

This is another task where I was able to simply just copy the script provided and paste it into the search function to complete this challenge.

 `<iframe src="javascript:alert(`xss`)">`

##### Error Handling

Category:  Security Misconfiguration

> Provoke an error that is neither very gracefully nor consistently handled.

This one has so many ways to trigger it that I ended up completing it and didn't even notice.

##### Exposed Metrics

Category:  Sensitive Data Exposure

> Find the endpoint that serves usage data to be scraped by a popular monitoring system.

The hints pretty much give this one away. A quick read through and the name of this challenge made it obvious that the path I was looking for
was /metrics/.

> Guessing the path is probably just as quick as taking the RTFM route via https://prometheus.io/docs/introduction/first_steps

##### Missing Encoding

Category:  Improper Input Validation

> Retrieve the photo of Bjoern's cat in "melee combat-mode".

Luckily this was as easy as url encoding the #'s to %23 because that emoji really had me scratching my head for a bit there.

https://www.eso.org/~ndelmott/url_encode.html

![encoding](/images/posts/owasp/Encoding.png)

##### Outdated Allowlist

Category:  Unvalidated Redirects

For the reading: https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html

> Let us redirect you to one of our crypto currency addresses which are not promoted any longer.

For this one I inspected the page to open the debugger window so that I could have a look around the Javascript files to try to find the crypto
currency address. The first place I checked was main-es2018.js and I simply did a search for redirect which had 20 results to check through but
the answer was there and I simply had to paste this blockchain.info link to the app address.

https://exampleapp.herokuapp.com/redirect?to=https://blockchain.info/address/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm

![Javascript](/images/posts/owasp/outdatedallowlist.png)

#####  Privacy Policy

Category: Miscellaneous

> Read our privacy policy.

This simply required me to be logged in and go to Account > Privacy & Security > Privacy Policy.

##### Repetitive Registration

Category:  Improper Input Validation

> Follow the DRY principle while registering a user.

>

>    The DRY (Don't Repeat Yourself) Principle states:

>    Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.

This challenge required me to turn on burp and foxyproxy to catch a legit registration attempt and change the value of passwordRepeat then forward
the request.

![burp request](/images/posts/owasp/repeatregburp.png)

##### Zero Stars

Category:  Improper Input Validation

> Give a devastating zero-star feedback to the store.

Reading: https://cwe.mitre.org/data/definitions/20.html

I found the customer feedback option as the first option on the menu. This page allows us to give a feedback of 1 to 5. Getting around this is
as simple as intercepting the request in burp and changing the rating to 0.

![burp request](/images/posts/owasp/feedback.png)

### 2 * Challenges

##### Admin Section

Category:  Broken Access Control

> Access the administration section of the store.

The quick and easy first check was to try to guess the admin page location. I tried https://exampleapp.herokuapp.com/#/admin but no luck there so I
tried administration instead and got a 403 You are not allowed to access this page!. Also turns out you can find this path from the main.js as well
if guessing failed.

I have found the page but I still need to login as the administrator to complete this challege. Going to the login page I see that we need the
administrators email not username to login. So I go back and look through the shop looking for the complete email address somewhere or at least an
@email to just have to guess the name.

On the review for the product Apple Juice contains a review from the admin themselves!

`admin@juice-sh.op`

The category for this challenge is injection but there's another challenge to log in without using injection so its time to brute force the login
page instead and complete both challenges in one go. I first attempted this with hydra but soon discovered that hydra is does not work because this
is a client-side AngularJS route. So its time to try with burp! I captured the request and sent it over to intruder. I set the position to the
password and on payload options I loaded a smaller file from seclist called best1050.txt as I only have the free version of burp and it doesn't load
rockyou. Running this attack on the free version is a bit slow but it works and I get the password.

![burp request](/images/posts/owasp/adminburp.png)

Now to just login with the credentials admin@juice-sh.op:admin123 and head over to the previously found /#/administration page to complete this challenge.

##### Deprecated Interface

Category:  Security Misconfiguration

> Use a deprecated B2B interface that was not properly shut down.

For this I had to do some googling to understand what B2B integration was about and came across this explanation "It allows them to work and trade more effectively with their customers, suppliers and business partners by automating key business processes." from www.ibm.com. So I headed over to the complaint page on the menu and took a look at what file types were accepted which turned out to be .pdf and .zip files. Inspecting this and heading back to debugger to take a look at the main-es2018.js and doing a search for zip revealed that .xml was also accepted.

![main.js](/images/posts/owasp/filetypes.png)

I didn't have any random .xml files around so I used `touch test.xml` to create one and then selected browse on the site and chose all file types so that I could select this file and upload it.

##### Five-Star Feedback

Category:  Broken Access Control

> Get rid of all 5-star customer feedback.

Sounds like an admin task, so I headed back to the administration page to take a look and sure enough the customer feedback section was there with only one 5-star feedback. All I had to do was hit delete on this one and the challenge completed.

##### Login Admin

Category: Injection

> Log in with the administrator's user account.

See [Admin Section](#admin-section) challenge solve.

Another method is with injection like the category suggests. Log in with Email `' or 1=1--` and any Password which will authenticate the first entry in the Users table which usually happens to be the administrator.

#####  Login MC SafeSearch

Category:  Sensitive Data Exposure

> Log in with MC SafeSearch's original user credentials without applying SQL Injection or any other bypass.

The hints for this supply a link to a video by MC SafeSearch at https://www.youtube.com/watch?v=v59CX2DiX0Y called "Protect Ya' Passwordz". Watch this video to learn that MC used the name of his dog "Mr. Noodles" as a password but changed "some vowels into zeroes".

All I had to do was log in with email `mc.safesearch@juice-sh.op`, as we already know what email addresses look like for this site and could easily guess the name, and password `Mr. N00dles` to solve this challenge.

##### Meta Geo Stalking

Category:  Sensitive Data Exposure

> Determine the answer to John's security question by looking at an upload of him to the Photo Wall and use it to reset his password via the Forgot Password mechanism.

The title hints toward geo data so I headed over to the photo wall and downloaded the picture by jOhNny. I then ran exiftool to get the GPS coordinates.

![exiftool](/images/posts/owasp/exiftool.png)

And then did a google search of these coordinates to find a name that could be the security question.

![map](/images/posts/owasp/coords.png)

It took quite a few attempts but I eventually found it to be `Daniel Boone National Forest` and was able to login by going to "forgot your password?" and using john@juice-sh.op for the email, Daniel Boone National Forest for the security question, and resetting the password.

##### Password Strength

Category:  Broken Authentication

> Log in with the administrator's user credentials without previously changing them or applying SQL Injection.

See [Admin Section](#admin-section) challenge solve.

##### Security Policy

Category: Miscellaneous

> Behave like any "white-hat" should before getting into the action.

For this I attempted to run gobuster and dirbuster against the site to no success. So I loaded up OWASP's very own ZAP and was able to find a file at `https://exampleapp.herokuapp.com/.well-known/security.txt`. I went to that address and the challenge completed but out of curiosity I looked up what this file was about anyway and heres the link for reference: https://securitytxt.org/.

##### View Basket

Category:  Broken Access Control

> View another user's shopping basket.

I headed over to the main page and added an item to the basket then selected the basket. On the basket page I inspected the page and went to the session storage where I found the item total and bid. Bid must hold the user id value so I changed the 1 to 2 and reloaded the page to complete the challenge.

##### Visual Geo Stalking

Category:  Sensitive Data Exposure

> Determine the answer to Emma's security question by looking at an upload of her to the Photo Wall and use it to reset her password via the Forgot Password mechanism.

At first I attempted a reverse image search on google but saw quickly that that was not the way to go with this. Looking closer at the image itself I noticed the ITsec logo in a window on the left side.

Using this (case sensitive) as the security question I was able to reset her password and complete the challenge.

#####  Weird Crypto

Category:  Cryptographic Issues

> Inform the shop about an algorithm or library it should definitely not use the way it does.

When completing the Meta Geo Stalking challenge I got a password hash back in the response from the burp request. Using hash-identifier I found out that this has is only an unsalted MD5. Taking this hash to crackstation.net easily got me the password of `john@juice-sh.op` as `test1234`.

The challenge says to inform the shop about the algorithm so to complete this challenge I had to go to Customer Feedback on the menu and simply leave a comment that said MD5 and solve the captcha to submit.

##### Reflected XSS

Unavailable on the Heroku environment.

### 3 * Challenges

##### API-only XSS

Unavailable on the Heroku environment.

##### Admin Registration

Category:  Improper Input Validation

> Register as a user with administrator privileges.

For this challenge all I had to do was submit a POST request to the API like so:

`curl -d '{"email":"admin","password":"admin","role":"admin"}' -H 'Content-Type: application/json' https://exampleapp.herokuapp.com/api/users/`
```
{"status":"success","data":{"username":"","deluxeToken":"","lastLoginIp":"0.0.0.0","profileImage":"/assets/public/images/uploads/defaultAdmin.png","isActive":true,"id":21,"email":"admin","role":"admin","updatedAt":"2021-09-24T13:43:00.474Z","createdAt":"2021-09-24T13:43:00.474Z","deletedAt":null}}  
```

#####  Bjoern's Favorite Pet

Category:  Broken Authentication

> Reset the password of Bjoern's OWASP account via the Forgot Password mechanism with the original answer to his security question.

The hint for this suggests looking up Bjorn. I quickly found he has a youtube channel that looks interesting and the information I need is likely there given the that the playlist is called "OWASP Juice Shop", https://www.youtube.com/playlist?list=PLV9O4rIovHhO1y8_78GZfMbH6oznyx2g2. But there are 110 videos here! I started at the bottom of the list as this is likely to be an early recording and going through the titles to check for ones that stand out or seem like they might hold the key I eventually came across https://www.youtube.com/watch?v=Lu0-kDdtVf4 (81 on the list).

This is the introduction video to Juice Shop. Around 4 mins into the video he creates an account `bjoern@owasp.org` and chooses Name of your favorite pet? as his security question with the answer being `Zaya`.

With this information I headed to the login page and chose "Forgot your password?" and tested the information from the video. Sure enough this account still existed using this information.

##### CAPTCHA Bypass

Category:  Broken Anti Automation

> Submit 10 or more customer feedbacks within 10 seconds.

I already ran into the CAPTCHA section early on in the Customer Feedback section so I immediately head there. Next I inspect the page and go to the network tab to check for the CAPTCHA request and found https://example.herokuapp.com/rest/captcha/.

```
captchaId	2
captcha	"6*1+8"
answer	"14"
```
When I submit feedback I see a POST call to /api/Feedback with the information:

```
captcha	"14"
captchaId	2
comment	"test (***in@juice-sh.op)"
rating	1
UserId	1
```
So I open up Burp and capture a request to the /api/Feedback and send it to repeat to simply spam send and complete this challenge.

##### CSRF

Category:  Broken Access Control

> Change the name of a user by performing Cross-Site Request Forgery from another origin.

For this challenge I used Real-Time HTML Editor at http://htmledit.squarefree.com/ and ran the following code:

```
<form action="https://exampleapp.herokuapp.com/profile" method="POST">
  <input name="username" value="CSRF"/>
  <input type="submit"/>
</form>
<script>document.forms[0].submit();</script>
```

The challenge completed without the successful message I usually get but I could also verify its success by going to https://exampleapp.herokuapp.com/profile and seeing that the username and indeed been changed to CSRF.


##### Client-side XSS Protection

Unavailable on the Heroku environment.

##### Database Schema

Category: Injection

> Exfiltrate the entire DB schema definition via SQL Injection.

From an earlier challenge (DOM XSS) I knew that there was no input sanitization on the “Search” field, so I decided to start there. Running sqlmap -u https://exampleapp.herokuapp.com/rest/products/search?q= --dbs --level=3 --risk=3   quickly tells us this is an SQLite database we are dealing with.

```
sqlmap identified the following injection point(s) with a total of 1383 HTTP(s) requests:
---
Parameter: q (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: q=') AND 6959=6959 AND ('KGhU' LIKE 'KGhU
---
[19:46:31] [INFO] testing SQLite
[19:46:31] [INFO] confirming SQLite
[19:46:32] [INFO] actively fingerprinting SQLite
[19:46:32] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[19:46:32] [WARNING] on SQLite it is not possible to enumerate databases (use only '--tables')
```

Cool its vulnerable. I was able to rerun sqlmap with `--tables` and retrieve the database `SQLite_masterdb` as well as all the table but the challenge did not complete. Nonetheless I made a copy of the results and saved it some place ill remember in case its useful later on. Doing this the manual way really took some time as at first I thought we had to enumerate all 20 tables that sqlmap found but apparently it was just 9.

`https://exampleapp.herokuapp.com/rest/products/search?q=qwert')) UNION SELECT sql, '2', '3', '4', '5', '6', '7', '8', '9' FROM sqlite_master--`

##### Deluxe Fraud

Category: Improper Input Validation

> Obtain a Deluxe Membership without paying for it.

I headed to https://exampleapp.herokuapp.com/#/payment/deluxe and started up Burp to see if I could get an easy win intercepting a request but didn't find anything useful there. So I inspected the `pay using wallet` button and removed both codes that mentioned disabled (`mat-button-disabled` and `disabled="true"`). After that I clicked the now enabled button and `continue` then checked the network tab of devtools. There is a POST request sent, which only contains one parameter in the request payload, "paymentMode", which is set to "wallet". I right clicked the POST request itself on the network tab list and selected to edit and resent. I changed the `"wallet"` value to just `""` and send.

##### Forged Feedback

Category: Broken Access Control

> Post some feedback in another users name.

I completing this one accidentally while doing the CAPTCHA bypass challenge simply by being logged in as one of the users accounts that I had gained access to earlier on.

The proper way to do this is to inspect the page and find the hidden element under `feedback-form`.

`<input id="userId" class="ng-untouched ng-pristine ng-valid" hidden="">`

Remove the `hidden` and you can change the userId to complete this challenge.

##### Forged Review

Category: Broken Access Control

> Post a product review as another user or edit any user's existing review.

For this one I selected an item and created a review that simply said test. As I was not logged in the user for this review was logged as Anonymous. Inspecting the page and checking the Network tab I found the POST request that was sent. I right clicked this request and selected to edit and resent and changed the `request body` to `{"message":"test","author":"admin@juice-sh.op"}` and hit send.

This actually didn't work so I created an account and logged in then repeated the process and successfully completed it this time.

##### GDPR Data Erasure

Category: Broken Authentication

> Log in with Chris' erased user account.

For this challenge I tried using `\' or deletedAt IS NOT NULL--` as the username with any password knowing that this small application built specifically for testing was likely to have Chris' account as the only deleted account and sure enough this worked and completed the challenge.

As this was not the way they likely wanted me to complete this challenge I went back to the SQL injection method to find the proper email address for Chris. By using `qwert')) UNION SELECT id, email, password, '4', '5', '6', '7', '8', '9' FROM Users--` at `/rest/products/search` I successfully get the email `chris.pike@juice-sh.op`.

Full address: `https://exampleapp.herokuapp.com/rest/products/search?q=qwert%27))%20UNION%20SELECT%20id,%20email,%20password,%20%274%27,%20%275%27,%20%276%27,%20%277%27,%20%278%27,%20%279%27%20FROM%20Users--`

With that I just need to login with the email as `chris.pike@juice-sh.op'--` and any password.

##### Login Amy

Category: Sensitive Data Exposure

> Log in with Amy's original user credentials. (This could take 93.83 billion trillion trillion centuries to brute force, but luckily she did not read the "One Important Final Note")

Googling either hints: "This could take 93.83 billion trillion trillion centuries" or "One Important Final Note" comes up with a link related to password haystacks, https://www.grc.com/haystack.htm?id.

This site gives two password examples and asks which we think is the more difficult to crack. The answer being `D0g.....................`. As the more hints section tells us this Amy is meant to be the Futurama Amy whose husband is named Kif, simply attempting `amy@juice-sh.op` and replacing the strong password with her husbands name `K1f.....................` succeeds in logging in and completing this challenge.

##### Login Bender

Category: Injection

> Log in with Bender's user account.

As the category for this is injection I attempted to login as `bender@juice-sh.op'--` with any password and it worked. Or alternatively decode the MD5 passwords we found from the SQL Injection vulnerability found at `/rest/products/search`

##### Login Jim

Category: injection

> Log in with Jim's user account.

The same injection method used on Bender's account works on Jim. Simply change the login email to `jim@juice-sh.op'--` and again use anything for the password. Or alternatively decode the MD5 passwords we found from the SQL Injection vulnerability found at `/rest/products/search`

##### Manipulate Basket

Category: Broken Access Control

> Put an additional product into another user's shopping basket.

For this one I had to be logged in as any user and inspect the network traffic while adding an item to the cart.
I selected to edit and resend the POST request to `/api/BasketItems`. Attempting to edit the BasketId and send got me an unauthorized 401 response.

Adding another BasketId to also add the item to another users account however did succeed. `{"ProductId":24,"BasketId":"2","quantity":1,"BasketId":"3"}`

##### Payback Time

Category: Improper Input Validation

> Place an order that makes you rich.

To complete this one I simply had Burp Suite on while I accessed the basket and noticed when I changed the quantity Burp made a PUT request to /api/BasketItems/13 with `"quantity":2`. I simply changed this to a negative number on Burp before forwarding the request to get "rich".

##### Privacy Policy Inspection

Category:  Security through Obscurity

> Prove that you actually read our privacy policy.

Going to Account > Privacy and Security > Privacy Policy didn't immediately complete this task so I started having a look around and noticed some areas highlight. So I inspected one of these areas to find a `class="hot"`. There seems to be a code hidden here that starts with the site address, We may also, instruct you, to refuse all, reasonably necessary and responsibility.

`https://exampleapp.herokuapp.com/we/may/also/instruct/you/to/refuse/all/reasonably/necessary/responsibility`

Going to this greets us with a `404 Error: ENOENT: no such file or directory, stat '/app/frontend/dist/frontend/assets/private/thank-you.jpg'`

And going back to the scoreboard I see the challenge has indeed completed.

##### Product Tampering

Category: Broken Access Control

> Change the href of the link within the OWASP SSL Advanced Forensic Tool (O-Saft) product description into https://owasp.slack.com.

https://exampleapp.herokuapp.com/rest/products/search?q=o-saft we find the id is 9. I intercepted a request to `/api/Products/9` in Burp and changed the request from `GET` to `PUT`, adding `Content-Type: application/json`, and finally `{"description": "<a href=\"https://owasp.slack.com\" target=\"_blank\">More...</a>"}` to the body.

```
PUT /api/products/9 HTTP/1.1
Host: exampleapp.herokuapp.com
Cookie: language=en; welcomebanner_status=dismiss; cookieconsent_status=dismiss; continueCode=wkhOtpIEsZUyHRumhYTZFyfySqtBi4fvSPHoMuaDtqni46fokSnxH5VuRYc7vS9KUZesDVig3S6ohOvtvvIx2TMnCPEs9RumbfoK; token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdGF0dXMiOiJzdWNjZXNzIiwiZGF0YSI6eyJpZCI6MjEsInVzZXJuYW1lIjoiIiwiZW1haWwiOiJ0ZXN0QHRlc3QuY29tIiwicGFzc3dvcmQiOiIwNWE2NzFjNjZhZWZlYTEyNGNjMDhiNzZlYTZkMzBiYiIsInJvbGUiOiJjdXN0b21lciIsImRlbHV4ZVRva2VuIjoiIiwibGFzdExvZ2luSXAiOiJ1bmRlZmluZWQiLCJwcm9maWxlSW1hZ2UiOiIvYXNzZXRzL3B1YmxpYy9pbWFnZXMvdXBsb2Fkcy9kZWZhdWx0LnN2ZyIsInRvdHBTZWNyZXQiOiIiLCJpc0FjdGl2ZSI6dHJ1ZSwiY3JlYXRlZEF0IjoiMjAyMS0wOS0yOSAyMjo1ODoyNC4xODEgKzAwOjAwIiwidXBkYXRlZEF0IjoiMjAyMS0wOS0yOSAyMzowNDo1NC4yNDcgKzAwOjAwIiwiZGVsZXRlZEF0IjpudWxsfSwiaWF0IjoxNjMyOTYxNTcyLCJleHAiOjE2MzI5Nzk1NzJ9.UEhLYcuCkaSYF0JvRXS64GYNMIxQqE98Wi46VTSXResmOH8w5Bg0pxXmCW0mGmNJPTGKBptbTPG_v-t5GfO4E36TeqiLCbOol_MHN1ZnyPQoqSfPLALIA37iNw6BQZsSa7TyG8ixA6hFNjmcno44lq_qM9p_V2cbUdffHbfwt4A
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Upgrade-Insecure-Requests: 1
If-None-Match: W/"1f7-edRq59DDoY0qO1LQdUtOO3s+ErQ"
Cache-Control: max-age=0
Te: trailers
Connection: close
Content-Type: application/json
Content-Length: 84

{"description": "<a href=\"https://owasp.slack.com\" target=\"_blank\">More...</a>"}
```

##### Reset Jim's Password

Category: Broken Authentication

> Reset Jim's password via the Forgot Password mechanism with the original answer to his security question.

So the first step is obviously to go over to the login page and select `Forgot Password`. Inputting the email as `jim@juice-sh.op` tells us that his secret question is "Your eldest siblings middle name?". So I must find out who Jim is, looking around reviews I see one by Jim for the product `OWASP Juice Shop-CTF Velcro Patch` stating `"Looks so much better on my uniform than the boring Starfleet symbol."`

If you are like me and Starfleet isn't a quick give away a simple Google search tells me this is related to Star Trek and that this account belongs to `James T. Kirk`. https://memory-alpha.fandom.com/wiki/James_T._Kirk reveals his only sibling as `George Samuel Kirk`.

Use `Samuel` as the answer to the security question and change the password.

##### Upload Size

Category: Improper Input Validation

> Upload a file larger than 100 kB.

For this I headed over to Complaint where we already know there's an option to upload. This accepts only .pdf and .zip files so I zip a file that is below 100kB and capture a legit request via Burp.

I change the extension back to `.pdf` and also change `application/pdf` from zip to pdf as well and delete the contents. Then I right click and choose `paste from file` and select a pdf file that is over the 100kB limit but still below 200kB and send the request.

##### Upload Type

Category: Improper Input Validation

> Upload a file that has no .pdf or .zip extension.

Simply capture the request in Burp and remove the extension from the `filename=`.

##### XXE Data Access

Unavailable on the Heroku environment.

### 4 * Challenges

##### Access Log

Category: Sensitive Data Exposure

> Gain access to any access log file of the server.

When I was testing out a tool from Github called Sitadel(`https://github.com/shenril/Sitadel`) I came across the log files. Another easy way is to use the OWASP ZAP DirBuster plugin for this.

Go to `https://exampleapp.herokuapp.com/support/logs` and download the access log here to complete the challenge.

##### Allowlist Bypass

Category: Unvalidated Redirects

> Enforce a redirect to a page you are not supposed to redirect to.

On the menu list theres an option that redirects to the Juice Shop Github page `https://exampleapp.herokuapp.com/redirect?to=https://github.com/bkimminich/juice-shop`. To bypass the allowlist and the `406 Error: Unrecognized target URL for redirect` error I had to use the original redirect but craft it a bit differently so that it includes where I want to redirect to between the site we are on and the allowed redirect. Like so:

https://exampleapp.herokuapp.com/redirect?to=http://kimminich.de?pwned=https://github.com/bkimminich/juice-shop

##### CSP Bypass

Unavailable on the Heroku environment.

##### Christmas Special

Category: Injection

> Order the Christmas special offer of 2014.

I already know that this is susceptible to SQL Injection but if I didn't the category would have been a give away. So first I use Injection to get a list of all the products `https://exampleapp.herokuapp.com/rest/products/search?q=%27))--`. I found one Christmas related entry with an id of 10.

```
id	10
name	"Christmas Super-Surprise-Box (2014 Edition)"
description	"Contains a random selection of 10 bottles (each 500ml) of our tastiest juices and an extra fan shirt for an unbeatable price! (Seasonal special offer! Limited availability!)"
price	29.99
deluxePrice	29.99
image	"undefined.jpg"
createdAt	"2021-10-06 12:56:19.525 +00:00"
updatedAt	"2021-10-06 12:56:19.525 +00:00"
deletedAt	"2014-12-27 00:00:00.000 +00:00"
```

Next I added an item to the basket and inspected the basket page. Heading over to the `Network` tab to make a `POST` request to `/api/BasketItems` and added the following lines:

in the header: `Content-Type: application/json`
in the body: `{"BasketId": "<Your Basket ID>", "ProductId": 10, "quantity": 1}`

Reloading the page got a successful 200 code response and the Christmas product was visible in the basket and clicking `Checkout` completes this challenge.

##### Easter Egg

Category: Broken Access Control

> Find the hidden easter egg.

I've already been across the `/ftp` directory before in my search of log files and noticed a file there named `eastere.gg`. Attempting to download this I was met with an error, `403 Error: Only .md and .pdf files are allowed!`. So I had to bypass this by tricking it into thinking it was a .md file.

`https://exampleapp.herokuapp.com/ftp/eastere.gg%2500.md`

The attack used:
http://projects.webappsec.org/w/page/13246949/Null%20Byte%20Injection

##### Ephemeral Accountant

Category: Injection

> Log in with the (non-existing) accountant acc0unt4nt@juice-sh.op without ever registering that user.

Carefully read the hints as it is possible to fail this challenge and have to restart the Juice Shop. Luckily it also reminds us that the category is a give away as to how to do this challenge properly. I had to look up the solution to this challenge as it was a bit above my current knowledge.

`' UNION SELECT * FROM (SELECT 15 as 'id', '' as 'username', 'acc0unt4nt@juice-sh.op' as 'email', '12345' as 'password', 'accounting' as 'role', '123' as 'deluxeToken', '1.2.3.4' as 'lastLoginIp' , '/assets/public/images/uploads/default.svg' as 'profileImage', '' as 'totpSecret', 1 as 'isActive', '1999-08-16 14:14:41.644 +00:00' as 'createdAt', '1999-08-16 14:33:41.930 +00:00' as 'updatedAt', null as 'deletedAt')--`

Logging in with this syntax as the email and anything as the password gets me a temporary JWT Token and establishes the ephemeral user session.

##### Expired Coupon

Category: Improper Input Validation  

> Successfully redeem an expired campaign coupon code.

For this I went back to the debugger tab to have a look at `main-es2015.js` and did a search for `campaign`.

```
WMNSDY2019: {
validOn: 1551999600000,
discount: 75
},
```

This tells us when the coupon was valid if we convert it to a JavaScript date this tells us that its valid on `March 8th 2019`. I simply had to set my system to this date ```timedatectl set-time `2019-03-08` ``` and submit the code `WMNSDY2019` to complete this challenge.
