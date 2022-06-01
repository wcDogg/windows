# Windows 10 + 11 Product Key

**Do I own my Operating System?**

I'm old enough to remember installing my OS from floppy discs or a CD. On the disk envelope there was a sticker with the licence key. You had to hang on to that disc & key for when you royaly screwed up & needed to reinstall. I had 2 disc binders full of them. 

Discs are still a thing, but nou we can download an OS & purchase a key online. You still need to hang on to the key, but you can dowload as needed. 

Today I'm setting up my new computer. It came with Windows 11 Pro already installed and ready to activate. My invoice has a line item for the OS. That got me thinking ... Where's my license key? What happens if I need to reinstall? Can I deactivate it on this computer and use in on another? 

## Reinstall

One of my first search results was this [Microsoft Community post](https://answers.microsoft.com/en-us/windows/forum/all/new-dell-laptop-windows-product-key/ad817538-3c9a-48b2-9c16-b5e00e1e23d6) explaining that Windows 10 and above are digitally licensed. When you first activate the OS, your hardware generates a unique identifier for the PC and sends it to Microsoft Activation servers where it's stored. This grants the PC a digital entitlement to run the OS. 

When you reinstall the same OS edition - Home, Pro, Enterprise - on the same PC, Windows will self-activate once the PC is connected to the Internet. 

This is essenitally true, but there's something missing...

## Transfer Product Key

Depending on how your key was obtained, you may be able to transfer it to another compter. 

* Software installed by the PC manufacturer use OEM licenses and are not transferable. 
* Software and upgrades you purchase are transferable. 

Which leaves me where exactly? According to my invoice, I paid for Windows 11 Pro but the manufacturer installed it. And apparently, if your OS is eligible for transfer, you need a Microsoft account to deacivate it on the old device and activate it on the new. 

But wait! Windows 11 Pro lets you [use a local account](windows-11-pro-local-account.md), which is what I did. I don't have a Micorsoft account to peroform this proceedure. 

## Locate Product Key

This got me thinking about the digital licencing explanation above. Your hardware creates an ID for the PC and sends it to Microsoft. But what would they do with just a PC identifier when you could be activating any number of Microsoft products? 

Either the server is OS-specific and only needs the PC identifier. Or it's a general activation server and what's being sent maps the PC idnetifier to OS key. If that's true, the mapping is being done by my hardware. Would this mean the OS key is on my machine? 

Turns out it is. From an elevated terminal: 

```
wmic path SoftwareLicensingService get OA3xOriginalProductKey

OA3xOriginalProductKey
XXXXX-XXXXX-XXXXX-XXXXX-XXXXX
```

## Transferable?

But is it transferable? Part of me thinks I paid for it and I have the key, so I get to put it on whatever machine I want. There has to be some kind of automation for when there's only a local account. It probably cares about an aggregated number of activations per manufacturer for bulk licencing. 

Another part thinks the key knows it's an OEM license and either won't work or may only work on other machines by the same manufacturer. 

I have no easy way to test and no real reason to keep researching. I was probably one dork away from an official answer :P

