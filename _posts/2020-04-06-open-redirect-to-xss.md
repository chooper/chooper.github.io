---
title: "How some open redirect vulnerabilities are XSSes in disguise"
---
One class of vulnerability that doesn't seem to get much attention is the [open redirect](https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html). In this post, I am going to give a little bit of background on open redirect vulnerabilities and describe how to parlay one into an XSS.

So what is an open redirect? An open redirect vulnerability exists when a site redirects or forwards a request using user-supplied input without performing validation.

It kind of makes sense that open redirects aren't a very attractive attack vector. After all, getting one just means that you can send a relatively legitimate-looking link to someone, hope that they click through, and hope that they fall for the phish, right?

Well, no, not exactly! It turns out that if we're dealing with a client-side redirect then we have one more trick up our sleeve. By client-side we just mean that it is JavaScript initiating the redirect instead of a server sending along a `Location` header with a `3XX` status code.

While server-side open redirects are relatively boring, client-side redirects allow for the trivial execution of javascript!

Imagine a web page that includes the following JavaScript:

```javascript
current_url = new URL(window.location.href);
next_url = current_url.searchParams.get("next");
window.location.href = decodeURIComponent(next_url);
```

Whe a user visits this page with, for example, `redirect.html?next=https://www.google.com` they will be redirected to Google so long as JavaScript is enabled in their browser.

So how do we get from here to an XSS? Let's talk about *bookmarklets* and `javascript:` URLs.

The idea of [bookmarklets](https://en.wikipedia.org/wiki/Bookmarklet) is that you can bookmark Javascript code to be run the same way you bookmark any other type of URL. These bookmarklets work by using `javascript:` URLs. For instance, if you wanted to pop up an alert box you could bookmark the URL `javascript:alert("hello");` and that code would be ran whenever you opened that bookmark.

What makes `javascript:` URLs *super* valuable for an attacker though is that they have access to the DOM of the currently-loaded page.

Putting this together: If we come across a client-side redirect based on user-supplied input then we can redirect to a `javascript:` URL allowing us to execute arbitrary code. This code will *have access to the DOM* of the redirecting page including the value of any form elements and cookies!

If you want to experiment with this concept a little bit yourself, I've thrown up a gist of [an example page vulnerable to a client-side open redirects](https://gist.github.com/chooper/e7633ac9d8f49477099f3adb54a61cb5).

# In the wild
I recently came across and reported one of these for a bug bounty.

The open redirect was in the target's login modal. The login flow was:

1. The user enters their username and password and clicks "login"
1. Instead of being sent to a new page during login, the credentials are submitted to an API endpoint for authentication using a [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest) ("AJAX")
1. If authentication was successful, the value of the `nextUrl` query parameter would be assigned as the new location (`window.location.href`) triggering a redirect (or XSS in our case)

There wasn't any form of validation or sanitization of the `nextUrl` parameter making this one especially trivial to exploit.

Finally, because this open redirect / XSS was present on the login page and, as we learned, the JavaScript we write has access to the DOM, this XSS could steal valid usernames and passwords!

# Bonus bug bounty tip
If you find one of these, *report it as an XSS*. Obviously explain that this vulnerability exists but your initial technical severity should be based on the fact that we have client-side code execution.

If you need more convincing, here is [@hakluke, a Bugcrowd employee, telling me to report these are XSSes](https://twitter.com/hakluke/status/1242951288305750016) too!

Not only will this help your target prioritize their fix amongst all of the other work they have to do but it might fetch you a bigger bounty as well! For example, per [BugCrowd's Vulnerability Rating Taxonomy](https://bugcrowd.com/vulnerability-rating-taxonomy), the difference between an open redirect and an XSS is the difference between a P4 and a P3.


What are your favorite underestimated vulnerabilities? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!
