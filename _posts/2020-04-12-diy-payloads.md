---
title: "Do It Yourself: Make your own payloads"
---
In a recent post, I told everyone that instead of copying and pasting payloads they find on the web that they should learn how to [make their own bypasses](https://subfn.net/2020/04/08/learn-bypasses-not-payloads/) and create their own payloads. But, as someone [pointed out to me on twitter](https://twitter.com/Laboudi_Nadji/status/1248368041798389761), I didn't go into *how* you can learn to make your own payloads. I don't know of any canonical reference for this so I'll just give you some of my own thoughts along with what's worked for me.

# Learn the basics of the vulnerability
Learn what the vulnerability is and what's happening when it's being exploited. You need to have a solid foundation in this because this is the base of your payload. You can't write effective SQL injection payloads without understanding what string injection is or how it works. `a' or 'a'='a` is a great demonstration of the vulnerability but it won't work everywhere even if a SQLi is present.

# Learn what you're attacking
Learn the underlying technologies of the stack you are trying to hack. I'm not saying you need to go out and become a Java or Ruby expert or whatever but get familiar with the languages and some of the common libraries and frameworks in use. If you find a REST-like API that appears to be written in C#, you probably want to at least know about Json.NET (hint: google `rce json deserialization`).

This goes for database systems, too. Knowing the difference [between MySQL and PostgrelSQL's syntax](https://wiki.postgresql.org/wiki/Things_to_find_out_about_when_moving_from_MySQL_to_PostgreSQL) could help you find ways to bypass the application's filtering. For instance, MySQL's `LIKE` operator is case-insensitive and supports double quotes (`"`) for strings while PostgreSQL is case-sensitive and uses double quotes for identifiers like for table and column names. Knowing this could be the difference between achieving a POC for a vulnerability and not.

# Get familiar with defense mechanisms
Get familiar with defense mechanisms like a language's idiomatic way of handling input. Learn some common Web Application Firewalls (WAFs) in common use such as `modsecurity`. Maybe the application's WAF is using [OWASP's Core Rule Set](https://github.com/SpiderLabs/owasp-modsecurity-crs) or maybe it's a custom config. Try some things out to figure out the parameters of the rule set.

Another useful thing to do is determine if the application presents a difference in behavior when the WAF handles disallowed input and when the application handles disallowed input. On one bug I worked on, when I tried to use input that the WAF was configured to reject I would get a server-side redirection (`HTTP 302`) but when the application rejected my input I would be served a client-side (Javascript) redirection. Knowing this difference allowed me to work around both sets of rules to develop my POC.

# Learn different ways to say the same thing
Using what you've learned from the steps above, when you discover a vulnerability but you need to bypass some manner of filtering or sanitization, try to come up with different ways to say or achieve the same thing.

An example of this might be using Javascript's template literals (quoted with backticks) and strings (quoted with single or double quotes). Lots of programmers know to filter single and double quotes; fewer know that they also need to filter backticks.

Another example would be finding other ways to prove and exploit your discovered vulnerability. One bug I worked on would reject any input that contained the word `alert`. While it isn't the *exact* same thing, `prompt` was unfiltered and enough of a POC to collect my bounty.

One final example is learning how different applications and their databases treat whitespace. You might be able to bypass filtering of the word `javascript:` by inserting a newline into the input (e.g. `javasc%0Aript`). You can also do the opposite where you can insert whitespace into a string by using another combination of whitespace. If you're working on a SQLi and the application is rejecting or sanitizing input that uses spaces, SQL treats comments as whitespace and so `SELECT VERSION()` can also be written as `SELECT/**/VERSION()`.

# Deconstruct others' bypasses
Finally, another way you can learn how to develop your own bypasses is to read others' bypasses. Check out [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) for a ton of examples on different bypasses. Don't just copy/paste them, read them and try to understand how they work.

For example, why is it that `request.__class__` and `request["__class__"]` mean the same thing when exploiting [Server Side Template Injection (SSTI) in Jinja2](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/a19fd013fb5037edf5ba2fae5f2e88013344a232/Server%20Side%20Template%20Injection/README.md#filter-bypass)?

Also, read published write-ups and watch videos of talks from conferences. You can learn alot from them.

# Summary
There isn't a well-known "how to" for developing your own payloads to bypass application's filtering or sanitization of your input. You will still learn this though by understanding the basics of the vulnerabilities you are exploiting, learning common libraries and frameworks of the stacks you are attacking and defense mechanisms, learning how to say or achieve the same thing when encountering application or WAF filtering, and, finally, reading published bypasses and discovering how they work.

Do you have any examples of custom bypasses that have worked for you? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!