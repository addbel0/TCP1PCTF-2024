---
title: " Bypassing the flow to obtain OTP | $500"
tags:
  - bugbounty
  - hackerone
  - application
  - security
---

Hi everyone 👋 this is my first writeup since I started bug bounty hunting in mid-2022. 
**I know it's kinda late for a writeup**, but I want to try give something back to the community. 

We can call the bug I found as **"Bypassing the flow to obtain OTP"** it's related to Improper Authentication.

To wrap up the introduction, sorry if it's not too clear for you because I need to redact any related info the target information except few parameters. And of course because this is my first writeup, any advice to improve my writing skills are very welcome. :smile:

# Overview

As we know OTP (*One-time Password*) mainly being used as another layer of authentication for a login, register, or any kind sensitive action that maybe by design needed that kind of protection. It’s like a second key that proves it’s really you, not someone else, trying to access your account.The OTP is typically sent via text message, email, or through an app and is valid for a short time, usually a few minutes.

Then how about PIN? what is the difference between PIN in terms of how is work?

| OTP | PIN |
| -------- | -------- |
| As the name ***One-time***, OTP is a temporary code used only once and for a short period  | Pin is a static & fixed number that users create themselves, and it stays the same until the user changes it |
| Because an OTP is unique and valid for a limited time, it’s much harder for hackers to steal and reuse. Each time you need to verify your identity, a new OTP is generated | A PIN is reusable until changed. If someone gets access to your PIN, they can use it multiple times unless it’s changed or disabled |

If OTP implemented correctly indeed it's secure (*unless there's a bug on the implementation :sweat:*)

# About the bug

Like I said above, what could go wrong if the OTP implementation is somehow broken or misconfigured? Well that is what I want to share about. The bug I found was affecting the **flow to obtain an OTP**.

Let's say the target as `redacted.com`. From the in-scope bug bounty program page there's a Android apps that serve as their platform services to customer. The customer can register an account to use the apps.

Like a normal registration form, there's few field need to filled by the user:

* Title (Mr, Ms, etc)
* First Name & Last Name
* Email Address
* Phone Number
* Password

Next after the Phone Number filed, there's a button to send OTP code to the phone number. 

## First request

Touching the button will hit to an API endpoint `https://redacted.com/api/XX/XX/XX/XX/XX/validate` with the following request body:

```
{
    "uid": xxx,
    "mobileNumber": 012345670,
    "detail"{
        "countryCode": xxx,
        "mobileNumber": 012345670,
        "email": xxx@xxx.xxx
    },
    "token_request"{
        "action": REGISTRATION,
        "countryCode": xxx,
        "target": 012345670,
        "type": SMS
    }
}
```

The request is needed as the data validation to access the next flow for completing account registration, which is the OTP verification from mobile phone number.

From this request, I noticed that there's a whitelist on the country code and mobile number. My real phone number for testing purpose is **not allowed** because different with the allowed country code

So the idea is what happend if I can bypass this by restriction? and how I can do that?

Let's take a quick look at the request body again

```
{
    "uid": xxx,
    "mobileNumber": 012345670,  #1
    "detail"{
        "countryCode": xxx,
        "mobileNumber": 012345670, #1
        "email": xxx@xxx.xxx
    },
    "token_request"{
        "action": REGISTRATION,
        "countryCode": xxx,
        "target": 012345670, #1
        "type": SMS #2
    }
}
```

1. The ==target== parameter having the same value as ==mobileNumber== 
2. Also the ==type== parameter is having **SMS** value

What happend if I change the ==target== parameter value to my **email address** and ==type== parameter value to **EMAIL**? does the backend will sending the OTP to my email address instead  mobile phone number? :thinking_face: 

After thinking about that, I tried to register an account again but this time I changed the ==target== parameter value to my **email address** and ==type== parameter value to **EMAIL** and forward the request. ***The request throwing back **200 OK** response here*** 👀

## Second request

Next after the first request there's another request made to an API endpoint `https://redacted.com/api/XX/XX/otpToken?parameter=x` like the folowing

```
{
    "action": REGISTRATION,
    "type": SMS,
    "target": 012345670,
    "countryCode": xxx,
    "lang": xxx
}
```
This request is responsible to send an OTP to the mobile phone number. Like the previous request, I changed the ==target== parameter value to my **email address** and ==type== parameter value to **EMAIL** then forward the request. 

**Surprisingly** the OTP code was sent to email and after that I can completed the registration process!

![image alt](https://media1.tenor.com/m/9CJaHEmyKPAAAAAC/chris-pratt-andy-dwyer.gif =50%x)

I immediately recorded the evidence PoC then submitted a report to their HackerOne program page.

# Impact

After the account created I noticed the mobile phone number being used at the registration are **bound** to only a single account. So if mobile phone number A is already used, another person can't reuse that mobile phone number.

So the impact here is clear, I can claim someone else's mobile number without getting physical access to their phone to get the OTP code and therefore the person whose mobile number has been claimed by the attacker cannot use it to register at the `redacted.com`!

# Timeline

-- *Submited as High (7.5) at August 13, 2022*  
-- *Triaged at August 20, 2022*  
-- *Severity Adjusted to Medium (6.1) at September 19, 2022*  
-- *Bounty paid $500 at September 21, 2022*  
-- *Bug fixed at October 25, 2022*  
-- *Report Resolved at October 26, 2022*  

![image](https://hackmd.io/_uploads/Skrr35pnR.png)

# Lesson learned

What I can learned from this?

- **Analyzing the application flow, don't rush**. Analyzing the application flow can be advantage to our hunting process.
- **Note down every request that seems useful**. Like in this case it's very helpful to make your own note with explanation what the request do, what parameter it takes, and what behavior happens with the apps if some parameter or parameter value changed.
- **Always be creative**. Don't be affraid to try to use every possible parameter value that can be found from the apps flow that **maybe** can make unexpected behavior.


That's it from me! Thanks for reading, and see you in the next writeup!

Cheers! :v: