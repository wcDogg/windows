## Windows Terminal Fonts

Fonts are an important part of workspace ergonomics. Good font face, size, and contrast choices can do a lot to reduce eye, neck, and back strain. They also lighten your brain's processing load, freeing it up to do better things. 

Today I tried using a font that wasn't recognized by any command window - Windows PowerShell, Command Prompt, or PowerShell. Google turned up numerous solutions saying to create a registry string or a `fontcofig` file - and even more comments saying this doesn't work. 

To my mind, there was a reason this font wasn't being made available. And editing my registry will never feel like a solution. Here's how I figured it out and how you can get the font you want into Windows terminals. 

## Terminal Defaults

Right-click the terminal's title bar and select Defaults. Select the Font tab.

Here you can see & select from the fonts available to Windows PowerShell, Windows Command Prompt, and PowerShell.

## Start with Noto Sans Mono

I don't know a thing about fonts, but it was clear that some monospace fonts are made available to Windows terminals while other's aren't. So my first task was to find a font and method that does work. 

I started with [Noto Sans Mono](https://fonts.google.com/noto/specimen/Noto+Sans+Mono) because its broad language support implies technically correct font files. I thought if any font will work, this one should - a lucky first guess. 

## Install Location

I know most things can be installed globally or locally, so I tried both. Fonts are installed at either `C:\WINDOWS\FONTS` (all users) or `C:\USERS\USER\..` (current user). 

* To install a font for all users, right-click the font file and select Install for All Users.
* To install a font for the current user, drag the font file to `C:\WINDOWS\FONTS` - counterintuitive, I know. 
* To see a font's install location, navigate to Start > Font Settings and click a preview to see details. 

## Voila!

At this point I have a working font - Noto Sans Mono is available to all of my Windows terminals. The install location doesn't matter beyond which users will have access to the font. 

## Variable Fonts

Noto Sans Mono is a static font. Next I wanted to know if the newer variable fonts work. I tested [Source Code Pro](https://fonts.google.com/specimen/Source+Code+Pro?query=source+code) and yes, it does work. All weights for the normal face are available. The italic face is not available. 

## Dorking

Now I've got 2 fonts that are automatically available to Windows terminals and 1 that isn't - why?

I broadly understand that fonts come in different file formats and contain data beyond the glyphs. And I always account for the possibility it's some weird thing with Microsoft. A few Google dorks later and I had a list of popular fonts with the same issue, plus a list of technical requirements a font must meet to be available in terminals.  

I don't understand all of these requirements. Here I'm just trying to gather some information that might help the foundry fix the issue. 

```
site:github.com "windows" "monospace*" terminal or powershell or command prompt or fontconfig
site:docs.microsoft.com "LOGFONT"
site:docs.microsoft.com "PANOSE"
site:docs.microsoft.com "OpenType"
site:docs.microsoft.com "TrueType"
```

The most relevant results: 

* GitHub: [microsoft\terminal: #295 Some installed fonts are not loading](https://github.com/microsoft/terminal/issues/295)
* Microsoft: [Recommendations for OpenType Fonts](https://docs.microsoft.com/en-us/typography/opentype/spec/recom)
* Microsoft: [PANOSE](https://docs.microsoft.com/en-us/openspecs/office_file_formats/ms-doc/6063ca18-7f12-41bf-9e6b-eaaecdc315a0)
* Microsoft: [LOGFONT (Windows CE 5.0)](https://docs.microsoft.com/en-us/previous-versions/windows/embedded/ms901140(v=msdn.10))
* Microsoft: [LOGFONTA Structure (Win32 Apps)](https://docs.microsoft.com/en-us/windows/win32/api/wingdi/ns-wingdi-logfonta)

## OTF vs TTF

My new font is OTF. Noto Sans Mono and Source Code Pro are TTF. Microsoft indicates both should work if properly formatted. I spent about 20 minutes looking for popular fonts in OTF and it was hard. I only found a few and none of them worked. 

The bottom line is I don't know if the OTFs didn't work because they're OTF or because they're not compliant. 

## Working with the Foundry

I contacted the foundry and heard back within hours. They were shocked to learn the font wasn't working and one requirement stood out. We did a series of tests using uniquely named versions of the font with different formats and metadata. Here are the requirements we landed on: 

1. Use a properly licensed OTF or TTF font. 
1. The font must be monospace - `width: 100(f)(s)`, `spacing: 100(i)(s)`
1. The font cannot be italic - `slant: 0(i)(s)`
1. The font cannot have a negative A or C space.
1. The font must be [FF_MODERN](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-wmf/9a632766-1f1c-4e2b-b1a4-f5b1a45f99ad).
1. The font must have the correct [PANOSE flags](https://docs.microsoft.com/en-us/openspecs/office_file_formats/ms-doc/6063ca18-7f12-41bf-9e6b-eaaecdc315a0) set.

It turns out PANOSE flags are all but obsolete. Newer versions of font creation software no longer set them by default. And foundries don't always know to set them manually. Perhaps the only place left on Earth these flags are used is in Windows terminals - Microsoft weirdness as suspected. 

Even with these guidelines, OTF did not work while TTF did. 

## Clear System Font Cache

While testing, the foundry recommended clearing the system font cache. Here are the steps for Windows 10. 

Uninstall font: 

1. Start > Font Settings App.
1. Locate and click on the desired font to see details.
1. Click the **Uninstall** button. 

Stop Font Cache Services: 

1. Start > Services App.
1. Locate and double-click `Windows Font Cache Services`. 
1. Set Startup Type to Disabled. 
1. Click the Stop button. Click OK. 
1. Repeat for `Windows Presentation Foundation Font Cache 3.0.0.0`

Delete cached files (administrator privileges): 

1. Close all programs. 
1. Press `Windows + e` to open the File Explorer App.
1. Select the View tab. In the Show/Hide ribbon, check the Hidden Items box. 
1. Navigate to `C:\Windows\ServiceProfiles\LocalService\AppData\Local`.
1. Delete all `FontCache*.dat` files - not all will. 

Restart computer. Restart Font Cache Services. 

## Takeaways

The skill I value most is an ability to navigate & solve problems in unfamiliar territory. 

I had to reject a lot of what's on the Internet. What got me going is my conviction that editing registries or dinking with `fontconfig` files just shouldn't be necessary and don't count as solutions. The thing that stood out for me is that not a single post about these "solutions" included an explanation of what it was actually fixing. That told me I needed to formulate my own sense of the problem if I were to find a real answer. 

I had to do some of the work. I knew from the start I'd be reporting this issue to someone. I have years of experience writing & responding to bug reports. I understand how to keep things concise & relevant, and how to generate the good will you sometimes need to get things done. 

I was lucky to work with a responsive foundry. Even if that weren't true, you should know that what you report & how you report it absolutely effects the attention you'll get. 

I had to be methodical. This kind of problem is a straight-up rabbit hole. I had to decide in advance how much I value this font in order to limit my efforts. Luckily, I also have years of experience setting up test matrices and scenarios. It's how I knew to find a working font and method first. The point is to spend a little more time researching & planning, and a lot less time doing. 

I had to know when to stop. I'm hyper-curious. I could have happily spent a few weeks learning about the technical aspects of fonts and trying to fix things myself. Creating my own font is on my bucket list, so there's some chance I'd use the knowledge in the future. But doing it would have been a complete misuse of my time - the foundry is the expert and I was pretty sure they'd want to fix it for all users. 

That turned out to be true and within a few days I received a working version of the font, which I love. 

