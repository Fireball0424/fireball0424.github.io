---
title: TryHackMe - Introduction to Cyber Security 
date: 2026-04-24 17:00 +0800 
categories: [CyberSecurity]
tags: [Note, TryHackMe]
---

## Offensive Security Intro 
> **Offensive Security** is about thinking like an attacker to find weaknesses before real hackers do.
{: .prompt-tip}

## Dirb 

```bash 
dirb http://example.com # 
dirb http://example.com /path-to-wordlist.txt # Custom word-list
dirb http://example.com -x php,txt,bak        # Specific file extensions 
```

Dirb is a command-line web content scanner used to discover hidden directories and files on a web server. 
Basically, it takes a target URL and a dictionary of common directory and file names, then attempts to access each entry. 
By analyzing the HTTP response codes, it can determine whether a resource exists. 

Simple and effective tool for **peneteration testing**