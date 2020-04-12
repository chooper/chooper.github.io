---
title: "Quick Burp tip: Using Burp without changing your OS proxy settings"
---
Here's a quick trick for you all.

The laptop I do my assesments from is a MacBook running OSX and for web browsing I mostly use Chrome. I also use [Burp](https://portswigger.net/burp) as one of my primary tools for security assessments.

If you're unfamiliar with Burp, it's a set of tools that allow you to monitor and manipulate all of your HTTP(S) traffic while you're hunting for vulnerabilities. If you haven't used it yet, check it out! The free ("Community") version is great too. The way Burp works is that it acts as a proxy server that sits between your browser and the sites you are assessing.

But one of the annoying things about the proxy settings on OSX is that Chrome doesn't allow you to set proxy servers in your browser settings. Instead, you have to change the proxy settings for the entire OS.

I find that annoying!

Well, I figured out how to run a separate Chrome process with its own proxy settings. And then I wrote a shell script that makes it easy. To use this script, put it in one of the directories in your `$PATH`; In my case, I added `$HOME/bin` to `$PATH` and that's where I put it the script. On my laptop, I named it `burp-curl` and that's how I run it from the command line.

Without further ado, here's the script.

```bash
#!/bin/bash
# Runs a separate Chrome process with its own proxy settings. Set
# `$PROXY_IP_PORT` to the IP and port that you have Burp configured
# to use.

readonly USER_DATA_DIR="$HOME/.burp-chrome.user-data"
readonly PROXY_IP_PORT="127.0.0.1:8080"

mkdir -p "$USER_DATA_DIR"

exec /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
    --ignore-certificate-errors \
    --proxy-server="$PROXY_IP_PORT" \
    --user-data-dir="$USER_DATA_DIR" &
```

I hope this saves you some trouble!

Do you have any tricks like this that you use? Let me know [@subfnSecurity](https://twitter.com/subfnSecurity) on twitter!