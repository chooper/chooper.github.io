---
title: "Learn bypasses, not payloads"
---
The thing I love most about hacking is figuring out how things work. There's something magical about feeding input to a black box and figuring out what's happening inside based solely on its output.

That's why I love bypasses. I know how XSSes and SQL injections work. Not to down-play the level of knowledge and skill required here but once you understand string injection then you understand *many* types of vulnerabilities.

But bypasses get me excited. I don't _know_ how an application is going to handle my input yet. In my experience, while `"><script>alert(1)</script>` and `' or 'a'='a` are perfect string injections conceptually, they almost never work in the real world.

Why? Because the system is doing *something* with our input to stop us, we just don't know what.

Is the application escaping certain characters in our input, is it stripping them, or is it rejecting the requests outright?

Your job is to answer those questions! Is the string injection within an object definition in a script block, for example:

```html
<script>
    something = {
        "one thing": "a thing",
        "another thing": "<your input here>"
    };
</script>
```

If so, does the sanitiation or validation routine know that the greater-than bracket (`>`) closes the script block with our without a closing quotation mark? That would be a good thing to know!

Or is it a possible SQL injection vulnerability that seems like it *should* be exploitable but any time you use certain SQL keywords you get redirected?

# In the wild
Truthfully, *most* bugs I've encountered involved some kind of bypass technique. One bug I submitted recently was an XSS where I could break out of a script block but most HTML elements (including `script` tags) and most event handlers (e.g. `onLoad` or `onError`) would cause the request to fail immediately. There seemed to be two layers of validation and filtering: A web application firewall ("WAF") and the application itself. Eventually, I was able to exploit the XSS by using grave accents instead of quotes, finding a combination of an HTML element and event handler that would allow me to execute arbitrary JavaScript, and avoiding certain keywords. It was a janky proof-of-concept but it worked and I collected a bounty for it.

In a recent ([HackTheBox](hackthebox.eu/)) CTF I worked on there was an obvious command injection vulnerability. There was even a published exploit/proof-of-concept for it. But the forums were riddled with people "stuck" because the POC they downloaded from exploit-db wasn't working.

The point of the CTF was that there was a (mis)configured WAF - mod_security in this case - in front of the application. You had to figure out *what* the WAF was doing with your input and figure out a workaround. You couldn't find a payload to win this one, you had to step-by-step enumerate all of the probable inputs, analyze the outputs, and figure out what was gonna work.

# Tip
Remember to *enumerate*. When you find a potential injection site, try one thing at a time until you find something that seems to work.

Types of things you should ask yourself are: Are double-quotes being escaped; what about grave accents ("back ticks")? Is the word `SELECT` being escaped; what about `SeLeCt` or `SEL/**/ECT`? What happens with null bytes and newlines?

A lot of people will have come before you and found the low-hanging fruit. You know, the `"><script>alert(1)</script>` of the day. Because it was too easy or because an automated tool picked it up.

You will stand out and beat your competition if you can learn how to exploit the harder, trickier bugs. Good hunting!

What's your favorite bypass story? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!