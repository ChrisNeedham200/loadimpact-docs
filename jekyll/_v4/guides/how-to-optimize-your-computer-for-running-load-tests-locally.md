---
layout: classic-docs
title: How to Optimize your Computer for Running Load Tests Locally
description: How to increase the limit of running processes and decrease the time each socket is occupied by the OS
categories: [guides]
order: 5
hide: true
---

***


# Background

A number of users while running their test scripts locally will run into limits within their OS which would prevent them from making the necessary number of requests to complete the test. This limit usually manifests itself in a form of **Too Many Open Files** error in MacOS or TODO for Linux.

These limits, if unchanged, can be a severe bottleneck if you choose to run a somewhat bigger or complicated test locally on your machine.

In this article we will show you how to increase these limits on all main operating systems we're supporting.



## MacOS

**Note:** Modifications below have been tested for macOS Sierra 10.12 and above, so if you are running an older version than that, the process for changing these settings may be different.


In order to prepare your machine for running tests locally your will need to increase the limit of maximum open files and running processes.

first step is to check the limits that are currently imposed by your OS. Running two following commands in the terminal should get you those values:

`sysctl kern.maxfiles`

`sysctl kern.maxfilesperproc`

The commands above will show you the system limits on open files and running processes, you can also find the user limits with the following command:

`ulist -a`

Given that you have ran into the 'too many open files' error, the limits have obviously been reached and we should increase them. Before you can change these limits, you will need to disable System Integrity Protection that was introduced in OS X El Capitain to prevent certain system-owned files and directories from being modified by processes without the proper privileges.

To disable it you will need to restart your Mac and hold down `Command + R` while it boots. This will boot it into Recovery Mode.

There you should navigate to `Utilities` which are located in the menu bar at the top of the screen, then open `Terminal`. Once you have it open, enter the following command:

`csrutil disable`

Once you press enter and close the Terminal, you can reboot your Mac normally and log into your account.

Next step will be to configure your new file limits. Open terminal and paste the following command:

`sudo nano /Library/LaunchDaemons/limit.maxfiles.plist`

This will open a text editor inside your terminal window where you will be prompted to provide your user password and then paste the following:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
 <dict>
 <key>Label</key>
 <string>limit.maxfiles</string>
 <key>ProgramArguments</key>
 <array>
 <string>launchctl</string>
 <string>limit</string>
 <string>maxfiles</string>
 <string>64000</string>
 <string>524288</string>
 </array>
 <key>RunAtLoad</key>
 <true/>
 <key>ServiceIPC</key>
 <false/>
 </dict>
</plist>
```

Pressing `Control + X` will save the changes and exit the editor. By pasting and saving this we have introduced two different limitations to your maxfiles limit. The first one (64000) is a soft limit, which if reached, will prompt your Mac to prepare to stop allowing new file opens but still let them open. If second one is reached (524288), a hard limit, you will again start seeing your old friend, the 'too many files open' error message.

We will use the same procedure to increase the processes limit next.

While in Terminal create a similar file with this command:

`sudo nano /Library/LaunchDaemons/limit.maxproc.plist`

Again, after prompted for your password, you can paste the following and save and close with `Control + X`


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple/DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
 <plist version="1.0">
 <dict>
 <key>Label</key>
 <string>limit.maxproc</string>
 <key>ProgramArguments</key>
 <array>
 <string>launchctl</string>
 <string>limit</string>
 <string>maxproc</string>
 <string>2048</string>
 <string>4096</string>
 </array>
 <key>RunAtLoad</key>
 <true />
 <key>ServiceIPC</key>
 <false />
 </dict>
 </plist>
 ```

All that is left after this is to reboot your Mac back to the Recovery Mode, open the Terminal, turn the SIP back on with `csrutil enable` and check if the limits were changed with commands we used at the beginning.

In most cases these limits should be enough to run most of your simple tests locally for some time, but you can modify the files above to any values you will need in your testing.

 **Note:** Those limitations are put in place to protect your operating system from files and applications that are poorly written and might leak memory like in huge quantities. We would suggest not going too overboard with the values, or you might find your system slowing down to a crawl if or when it runs out of RAM.

### Configuring TIME_WAIT timeout

One additional thing we can do in order to 'pump' more requests from your Mac is to configure the TIME_WAIT timeout setting for releasing the TCP sockets. This is a feature in TCP that ensures connections are closed cleanly. It requires one end of the connection to stay listening for a while after the socket has been closed.

By default MacOS has 16K ports available with a TIME_WAIT value of 15 seconds. You can check your currently set value with the following command:

`sysctl net.inet.tcp.msl`

Decreasing this value would enable your machine to reuse the sockets more rapidly and make more requests per second overall. The following command will decrease the TIME_WAIT setting to 100 milliseconds:

`sudo sysctl -w net.inet.tcp.msl=100`


**Note:** Please keep in mind that decreasing the TIME_WAIT setting this way won't change the setting globally in your OS and the default value will be reset to 15000 once you restart your Mac.





TODO


## Linux


## Windows
