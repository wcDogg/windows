# Windows 10 + 11 Pro Telemetry

Where and how to control OS data sent to Microsoft. Note individual apps have their own telemetry that's configured separately from these steps. 

* Windows 10 Pro, Enterprise, Education 
* Windows 11 Pro, Enterprise, Education 

## The Big Idea

When it comes to disabling Windows telemetry, there are 2 broad choices:

* Via the Settings UI for blanket control over common options.
* Via Group Policies and registry settings for even more explicit and granular control. 

**Start > Settings**

Because I've never used Group Polices, I started with the Settings UI and suggest you do too. One reason is to know what's there - which is more than I expected. Another reason is to quickly set the most relevant things while continuing to learn. There's one more reason...

**Start > Edit Group Policies**

Most of the policies to look at are at: `Computer Configuration > Administrative Templates > Windows Components`. I had hoped I could run through these & feel confident in the result. The truth is, this is going to take a while. As I go, I'm also looking at the Settings UI which indicates when an option is policy controlled. 

And there's weirdness. In Group Policies, it seems like I have Contra and microphones disabled, but  Settings > Time & Language > Microphone isn't disabled and doesn't display the 'controlled by policy' message. The Get Started button launches a troubleshooter with 2 options - I can't hear Contra, or Contra can't hear me. 

* Microsoft: [Manage connections from Windows 10 and Windows 11 operating system components to Microsoft services](https://docs.microsoft.com/en-us/windows/privacy/manage-connections-from-windows-operating-system-components-to-microsoft-services)
* Microsoft: [Configure Windows diagnostic data in your organization](https://docs.microsoft.com/en-us/windows/privacy/configure-windows-diagnostic-data-in-your-organization)


## No Telemetry

Before turning it all off, clarify your privacy concerns and read the articles above. Here's [why I allow some telemetry](in-defense-of-telmetry.md). 

To prevent Windows OS from sending any data to Microsoft:

* Configure Diagnostic Data at the **Security** level.
* Turn off Microsoft Defender Antivirus diagnostic data and MSRT reporting. 
* Use Group Policies to [turn off these options](https://docs.microsoft.com/en-us/windows/privacy/manage-connections-from-windows-operating-system-components-to-microsoft-services#settings-for-windows-10-and-windows-11-enterprise-edition).

## UI Configuration

If you don't want to use Group Policies, navigate to Start > Settings and explore everything. There's too many to list here and some have options that you might not think of.

## Group Policies: Security Settings

I cam across several posts related to the polices at `Computer Configuration > Windows Settings > Security Settings`. Most of these are above my pay grade. It's a good thing each one I checked was already configured as suggested in the posts. 

## Group Policies: Windows Components  

A list of the **Windows Component** policies I find interesting. Note Windows 10 Pro uses the word **'telemetry'**. Windows 11 Pro uses terms like **'diagnostic data'**. 

### Diagnostic Data

Windows Components > Data Collection & Preview Builds

To control the diagnostic data sent to Microsoft, enable telemetry and select a level. 

1. Double-click **Allow Diagnostic Data** policy. 
2. Select the **Enable** radio button.
3. Use the **Options** dropdown to select no, required, or optional data. I'm allowing required data because I want to use Windows Update.
4. Click **OK**. 

Once this policy is set, look at the other policies in this collection. Some only apply in enterprise scenarios. Users on a single machine should review:  

* Allow device name to be sent in Windows Diagnostic Data
* Configure telemetry opt-in change notifications
* Configure telemetry opt-in setting user interface
* Do not show feedback notifications
* Windows 11: Limit Dump Collection

### App Privacy 

Control app access to voice activation, location, phone, microphone, camera and more. Review all of these carefully. 

### AutoPlay Policies

I can believe AutoPlay and AutoRun are ever allowed, let alone the default. Turn these off!

### Windows Consumer Experiences

Windows Components > Cloud Content (not complete)

* Turn off cloud-optimized content
* Turn off cloud consumer account state content

### Microsoft Store

Disable all Microsoft Store apps and turn off the Store itself. 

### Microsoft Edge

Windows 11 Pro doesn't let you uninstall Edge and I found some of the policies scary. Specifically:

* Allow Microsoft Edge to pre-launch at Windows startup...
* Allow Microsoft Edge to start and load...at Windows startup...



## Services

```
Win + R
services.msc

Double-click: 
Connected User Experiences and Telemetry
Device Management Wireless Application Protocol (WAP) Push Message Routing Service

General tab: 
Startup Type = Didabled
Stop button
```

## Disable Telemetry for Common Apps

### Windows Command Prompt

```
# CMD as admin
sc config DiagTrack start= disabled
sc config dmwappushservice start= disabled
```

### Windows PowerShell + PowerShell 7

[Microsoft: PowerShell: About Telemetry](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_telemetry?view=powershell-7.2)

```
# Windows PowerShell as admin
$env:POWERSHELL_TELEMETRY_OPTOUT to true, yes, or 1
```
