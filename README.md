# subshell.py

```
  _________    ___.     _________.__           .__  .__   
 /   _____/__ _\_ |__  /   _____/|  |__   ____ |  | |  |  
 \_____  \|  |  \ __ \ \_____  \ |  |  \_/ __ \|  | |  |  
 /        \  |  / \_\ \/        \|   Y  \  ___/|  |_|  |__
/_______  /____/|___  /_______  /|___|  /\___  >____/____/
        \/          \/        \/      \/     \/           
SubShell - Webshell Console Framework - Joe Vest

Usage: 
    subshell.py  --url=<url>
    subshell.py  --url=<url> [--useragent=<useragent] [--logdir=<logdir>]  
    subshell.py (-h | --help) More Help and Default Settings

Options:
    -h --help                This Screen
    --url=<url>              URL source of webshell (http://localhost:80)
    --useragent=<useragent>  User-Agent String to use [default: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/6.0)]
    --logdir=<logdir>        Directory path to logs [default: ./logs]
    --debug                  Enable debugging


```

SubShell is a python command shell used to control a webshell through HTTP Post requests to a remote webshell.  SubShell acts as the interface to the remote webshells.  

The design this project uses the principle of hiding in plain sight.  The goal is minimize attention.  Like numerous malicious tools, they are easy to find once you look.  This project is not designed to be steahly, but to minimize triggering defense such as IDS, Firewall, AV etc.

How does it do this?

  - POST request to minmize data in web logs
  - Encoded traffic to avoid clear text detection
  - Console interface forces a slow down when issuing commands.  Use of a webshell should be deliberate and focused.  Numerous connections may attract attention.
  - Customization of user-agent to help blend in.

## Current Features

  - Designed for use on Windows Based systems
  - Supports shells in asp, aspx, php, jsp
  - Logging of all actions
  - Base64 encoded data send and recevied in POST requests
  - Invalid request generates a legitimate 404 message.
  - Tracks current directory by implementing common commands (cd, pwd, ls, dir)
  - Built in helper commands
  - Simple replay attack prevention.  Uses a token that is only valid +/- 12 hours of current time.
  - 'Authentication' using replay attack prevention

## Python Dependencies
 
  - import os
  - import base64
  - import cmd
  - import readline
  - import requests
  - import sys
  - import docopt
  - import datetime
  - import calendar
  - import re
  - import shlex
  - import signal
  - import threading
  - import time
  - from socket import timeout as TimeoutException
  - from UserString import MutableString

## SubShell Console Reference 

Interaction with a remote 'shell' using subshell is similar to a non-interactive shell.  Non interactive commands can be submited and the results displayed.  

If an interactive command is submitted, the command will not return.  Command will display a timeout error.  

| Command   | Description      | Example
|-----------|------------------|--------
|cd         | change directory | cd c:\temp
|command    | Optional command used to issue remote command.  If no other built in command matches, then this command is assumed. | command tasklist
|dir        | directory command | dir c:\temp
|download   | download remote file.  Files stored in ./downloads.  The original file structure is created. | download c:\temp\myfile.txt
|upload     | upload file to remote server. | upload myfile.txt c:\windows\temp\myfile.txt
|exit       | exit command shell | exit
|history    | show command  history | history
|config     | Show current config | config
|status     | Show status for Uploads and Downloads | status
|ls         | alias for dir | ls c:\temp
|pwd        | show current directory | pwd
|shell      | submit command to local shell | shell ifconfig 
|timeout    | display or set the command timeout setting in seconds | timeout 120
|python     | drop to interactive python shell | python

## API Used to Communicate to web shells

All command submitted to the shell are POST request with a minimu of 2 parameters (sessionid,'command')

POST Parameters

|Parameter  | Description |
|-----------|-------------|
|sessionid  |Used to 'authenticate' requests and minimized replay attacks.  Based on current time.  Any request +/- 12hrs will not be allowed.  If the time is off a HTTP 408 will be sent with the HTTP header expires:'timevalue'.  SubShell can use this value to adjust its authentication it sends.|            
|apikey     |used to submit OS commands|
|apikeyd    |Download file from remote host|
|apikeyu    |Upload file to remote host |
|feed       |stores Base64 encoded file.  Used for upload |

## Features Support by Shell Type

|Feature           | jsp  | aspx | asp  | php  |
|------------------|------|------|------|------|
|Replay Protection |  x   |   x  |   x  |      |
|Issue command     |  x   |   x  |   x  |  x   |
|Upload            |  x   |      |      |  x   |
|Download          |  x   |   x  |   x  |  x   |
|404 on GET        |  x   |   x  |   x  |  x   |

  
## Initial Requirements
  - Single command and control server
  - Web shell payloads (3 versions)
    - .jsp, .asp, .aspx
  - Requests/Responses must be obfuscated or encrypted
    - SubShell is currently using base64
  - Web shell features
    - File browsing – (track current directory)
    - File Upload/Download
    - Command execution
    - HTTP POST Requests used for C2 traffic
    - Non-POST Requests render a valid 404 response



