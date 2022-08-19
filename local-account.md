# Windows 11 Pro Local Account

How to install Windows 11 Pro using a local account instead of a Microsoft account. No hacks, just hoops.  

## New Installs

At some point in the setup process, you'll be asked how you want to set up the device. From here, follow these options:

1. How would you like to set up this device -> Set up for personal use
1. Let's add your Microsoft account -> Sign in options -> Offline account
1. What is a Microsoft account -> Skip for now
1. Who's going to use this device -> Computer username
1. Password -> Computer password
1. Confirm password -> Computer password
1. Now add security questions -> Add 3 questions and answers

## Existing Installs

You can switch from a Microsoft account to a local account at any time. 

1. Start > Settings > Accounts
1. On the desired account's **Your Info** page, use the **Sign In with a Local Account** link. Jump through a few hoops. 

## Protect Your Password

Windows generates and stores user account passwords in hashes. 

* Windows generates both a LAN Manager hash (LM hash) and a Windows NT hash (NT hash) of passwords. 
* Hashes are stored the local Security Accounts Manager (SAM) database or Active Directory.
* The LM hash is weak and prone to hacking.

You can prevent Windows from storing a LM hash of your passwords by enabling a Group Policy. For me, on Windows 11 Pro, the policy was enabled by default.

1. Start > Edit Group Policies 
2. Computer Configuration > Windows Settings > Security Settings > Local Policies > Security Options
3. Enable this policy: **Network security: Do not store LAN Manager hash value on next password change**
