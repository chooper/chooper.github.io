---
title: "Learn bypasses, not payloads"
---
The thing I love most about hacking is figuring out how things work. There's something magical about feeding input into a black box and figuring out what's happening inside based solely on its output.

That's why I love bypasses. I know how XSSes and SQL injections work. Not to down-play the level of knowledge and skill required to recognize and exploit these but once you understand string injection then you understand this *whole class* of vulnerabilities.

But bypasses make me excited. Yes, `"><script>alert(1)</script>` and `' or 'a'='a` are perfect string injections conceptually but they almost never work in the real world because because the application is going to do *something* to your input and you don't know what.

There are basically four ways a application can respond to our attempts at string injection.

1. **The application can do nothing**. The application processes our request leaving our input as-is. Easy mode :)
1. **The application can remove part of the input**. This is where the application strips off part of the input and continues processing the request. An example of this could be removing HTML tags or removing everything after the first whitespace.
1. **The application can escape special characters**. This is where the application still uses our input but attempts to make it safe for where it's being sent. For example, escaping single quotes for a string being used to make a SQL query.
1. **The application rejects our input outright**. This is where the application decides not to process our request. This might be the application returning an error (e.g. "these characters are not allowed!") or pretending as if it never _got_ the input, for instance.

The application can also do any combination of the above!

Our job is to work out how the application is doing each of those things so that we can work around it and still accomplish our goals.

# In the wild
Truthfully, *most* bugs I've encountered involved some kind of bypass technique. One bug I submitted recently was an XSS where I could break out of a script block but most HTML elements (including `script` tags) and most event handlers (e.g. `onLoad` or `onError`) would cause the request to fail immediately. There seemed to be two layers of validation and filtering: A web application firewall ("WAF") and the application itself. I went through the list of HTML elements one-by-one until I knew which ones weren't rejected by the application. Then I took each one and tried all of the event handlers that could be useful. Eventually I found a combination that worked! I spent three days on that XSS (yes, really) but I was *so* excited that I beat the system that it was worth it!

In a recent ([HackTheBox](hackthebox.eu/)) box I worked on (since retired) there was an obvious command injection vulnerability. There was even a published proof-of-concept for it. But the forums were riddled with people "stuck" because the POC they downloaded from exploit-db wasn't working.

The point of this challenge was that there was a (mis)configured WAF - mod_security in this case - in front of the application. You had to figure out *what* the WAF was doing with your input and figure out a workaround. You couldn't find a payload to win this one, you had to step-by-step enumerate all of the probable inputs, analyze the outputs, and figure out what was gonna work.

# Tip
Remember to *enumerate*. When you find a potential injection site, try one thing at a time until you find something that seems to work. For example, if `<img onError="...">` doesn't work, what about `<img>` by itself? This will let you determine whether the application is rejecting the image tag or whether it's the `onError` string it has an issue with.

A lot of people will have come before you and found the low-hanging fruit. You know, the `"><script>alert(1)</script>` of the day. Because it was too obvious or because an automated tool picked it up. You will stand out and beat your competition if you can learn how to exploit the harder, trickier bugs.

Finally, this applies to *all kinds* of vulnerabilities, from SQL injection to XSS to server-side template injections to command injection to file inclusion. I've just used a lot of XSS and SQL injection examples because it's what I have the most experience with but it absolutely applies to everything.

Good hunting!

What's your favorite bypass story? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!