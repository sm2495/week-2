# Week 2 — Detection Engineering

## Overview

This repository contains my week 2 detection documentation. The task focused on creating basic Linux log-based detections using `/var/log/auth.log` and command-line tools such as `grep`.

The detections cover:

- Failed authentication attempts
- Successful login after failures
- New local user creation

---

## Detection 1: Failed Authentication Attempts

### Detection Logic

This detection searches the authentication log for failed authentication events.

### Detection Command

    sudo grep "authentication failure" /var/log/auth.log

### Screenshot Evidence

![Failed Authentication Attempts](pass_guessing.png)


This detection identifies repeated failed authentication attempts in the Linux authentication log. The screenshot shows multiple `pam_unix(su:auth): authentication failure` entries. These entries show failed attempts to authenticate using `su`.

This activity may indicate password guessing or brute-force behavior. If many failed attempts happen close together, it can suggest that someone is repeatedly trying different passwords to gain access.

### ATT&CK Mapping

**Technique Name:** Brute Force  
**ATT&CK ID:** T1110  
**ATT&CK Tactic:** Credential Access  

### False Positive Analysis

A legitimate user may forget their password and enter it incorrectly several times. This can trigger the detection even if the activity is not malicious.

---

## Detection 2: Successful Login After Failures

### Detection Logic

This detection searches for failed `su` authentication attempts and successful `su` session activity in the same log file.

### Detection Command

    sudo grep -E "pam_unix\(su:auth\): authentication failure|pam_unix\(su:session\): session opened" /var/log/auth.log

### Screenshot Evidence

![Successful Login After Failures](success_login.png)


This detection identifies a successful authentication session after previous failed authentication attempts. The screenshot shows failed `su` authentication attempts followed by a successful session opened for the root user.

This matters because a successful login or session after repeated failures may indicate that the correct password was eventually guessed or obtained. In a real investigation, this pattern could suggest possible credential compromise.

### ATT&CK Mapping

**Technique Name:** Valid Accounts  
**ATT&CK ID:** T1078  
**ATT&CK Tactic:** Initial Access  

### False Positive Analysis

A legitimate user may type the wrong password several times before successfully entering the correct password. This can create the same pattern even if no attack happened.

---

## Detection 3: New User Creation

### Detection Logic

This detection searches the authentication log for user creation activity.

### Detection Command

    sudo grep -E "useradd|adduser|new user" /var/log/auth.log

### Screenshot Evidence

![New User Creation](newuser.png)


This detection identifies local user account creation. The screenshot shows the `adduser` command being executed and a new user account named `newuser1` being created.

This activity matters because attackers may create new local accounts to maintain access to a compromised system. This is considered persistence because the attacker could use the new account to return to the system later.

### ATT&CK Mapping

**Technique Name:** Create Account: Local Account  
**ATT&CK ID:** T1136.001  
**ATT&CK Tactic:** Persistence  

### False Positive Analysis

A system administrator may create a new user account for a legitimate reason, such as adding a lab account, test account, or new employee account.

---

## Findings

The detection commands successfully identified three important activities in the Linux authentication logs.

Detection 1 showed repeated failed authentication attempts, which may indicate brute-force or password guessing behavior. Detection 2 showed a successful session after previous failed attempts, which may suggest possible credential compromise. Detection 3 showed new local user creation, which may indicate persistence if performed by an attacker.

These detections show how simple Linux commands can turn raw logs into useful security evidence for investigation. Even though the commands are basic, they demonstrate how SOC analysts can search logs for suspicious activity and map the evidence to MITRE ATT&CK techniques.
