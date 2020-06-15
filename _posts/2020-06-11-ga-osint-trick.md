---
title: "Neat OSINT trick: Google account disclosure"
published: false
---

Recently I discovered a little OSINT trick that lets you discover if an email address has a Google account associated with it and, if so, the address of that account.

But before I go into that, let's go into a little bit of background.

For the unfamiliar OSINT, or Open Source Intelligence, is the use of data from publicly available sources. For instance, researching the LinkedIn profiles of a company you are targeting is using Open Source Intelligence. In this case, you would discover employee names, a look at the organizational structure of the target, and even what technologies a company uses. This is information that would be useful for password cracking (by being able to infer valid usernames), social engineering, and technical exploitation.

# So what's the trick?
Google allows each Google account to have one or more "alternate addresses" associated with it. This alternate email address can be used as the login, used to recover the account's password, and more.

With the technique I'm about to show you, you'll be able to look up if a non-Google email address is associated with a Google account and, if so, what it is.

This could be useful in a number of ways:

1. You'll learn if the target uses Google with the given email address; maybe they can be phished using this knowledge.
2. You could learn a new email address for the target; with this new email address, you can perform further OSINT with it. It may also be that the target was using the alternate email as an attempt to masquerade their identity. Perhaps the backing Google account is using a naming convention that will reveal the target's identity (z3r0c00l@protonmail.com -> daedmurphy@gmail.com)

# How to do it
I first discovered this trick in Google Analytics. I had just set up a new site and was trying to invite someone to view its reports. When I entered the email address that I knew, before even clicking submit, Google popped up a message saying that they've replaced it with the person's _actual_ Google address.

![The popup you get from Google when you add an alternate email](/assets/img/ga-osint-trick-popup.jpeg)

To do it this way:

  1. Log into Google Analytics
  2. Navigate to _Admin_ > _Account User Management_ > _Add user_
  3. At this point, enter an email address. If the email is a backup email of a Google account, the primary Google email (e.g. the Google login email) will be disclosed

This is backed by an API endpoint that looked pretty simple, too, so this could be automated somewhat. Additionally, both the API and the UI accept multiple email addresses as input. I pasted over 1,000 email addresses into the UI and it resolved all of them with a _single_ API request.

It turns out too that there are actually _lots_ of ways to do this! According to Google, this is a _feature_ and [one that they've documented](https://support.google.com/accounts/answer/176347). Still, I think it might be a lesser-known secret and that's why I'm writing about it here.

# Is this legal or ethical?
As referenced above, Google says [that this is a feature](https://support.google.com/accounts/answer/176347). Specifically, they say:

> With some Google products, people will be able to see both your primary email address and your alternate email address.

If you're still not sure, I reported this as a potential abuse vector and here's what Google had to say:

![Response from Google about account lookup as an abuse vector: This is a feature and we're not going to fix it](/assets/img/ga-osint-trick-wontfix.jpeg)

# Summary
I hope you find this OSINT technique helpful. I know that before I stumbled upon this, I didn't know that anyone could discover my true Google account if they knew any of my other email addresses associated with it.

What are your favorite lesser-known OSINT techiques? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!