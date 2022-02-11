---
description: https://tryhackme.com/room/iosforensics
---

# iOS Forensics

## What is Digital Forensics and How is it Used Today?

Digital forensics is the mere digitization of the traditional use and applications of forensic investigation, for example, within police departments after a crime has been committed.

Artefacts such as blood, fingerprints or hair fibers are used in criminal investigations to paint a picture of the events that took place and who was involved. Digital forensics is the same collection of artefacts, however, these artefacts being events on a digital device.

Removing evidence or covering your tracks is a fallacy to some extent - especially with digital devices. Someone may be able to hide exactly what they were doing, however, the act of hiding this will, in turn, leave the trace of _something_ behind hidden.

## Problems Facing Digital Forensics Analysts

### Time Consumption & Resources

Digital forensics is, without doing it justice, an incredibly time-consuming process. Despite the toolkits and suites available, you find yourself having to analyze data bit-by-bit to find that one smoking gun. Now extend that to a 1TB drive - not so fun huh.

With the very nature of forensic images being exact bit-for-bit copies of an acquired device or system, you need to have the facilities to be able to store this data before it can be processed. Take a file server full of data with 10 terabytes of data. You need 10 terabytes to store that on as well, slapping on another 10 terabytes required for the backups of the image that you make; suddenly you have at least 20 terabytes sitting around.

### Understanding the Person

As a forensic analyst, you have to piece various parts of information together, into a formal and well-documented timeline of events for presentation. For example, if you were to try to find a bit of text in a document on your computer, you'd know where to go. Now take away the desktop/GUI and terminal then ask your friend to try and find it through the means similar to the screenshot in Task 2. Pretty hard huh?

### Encryption

As it stands, an effective and secure implementation of encryption poses as one of the biggest hurdles to forensic analysts. The problem mostly? People and/or devices themselves leave the decryption keys within the same platform. Such as in the case of the iPhone you are going to analyse. After all, it's that catch-22 of a complex password is a strong password until you need to write it down.

### Steganography

We all know (and love) steganography here; Hiding data within data. And in some cases, is more secure then cryptography; seeing as cryptography makes the contents unreadable, steganography masquerades the entire existence of this data altogether.

### Cost of Entry

Sure, you can pick up FTK Imager lite or Autopsy for free, but these tool suites - whilst being lifesavers, are only the tip of the iceberg in digital forensics.

For example, you're not going to be creating any file system images of iPhones in FTK Imager lite, and if so, you're going through it bit-for-bit if it is at all unencrypted.

Enter infamous companies such as Cellebrite. This company are arguably the forefront of data acquisition. Costing approximately $15,000 for the equipment and adapters, specialist kits such these aren't available to hobbyists - only to law enforcement, government agencies and specific Universities. Cellebrite was used to dump this iPhone.

Let alone the cost of purchasing sophisticated tools, there is a heavy expectation of certifications and even degrees; that's what filled most of my 3 years at University!

## iOS File Systems

Apple, in their notorious way of doing things, have created their own sets of file system formattings: AFS and HFS+

Starting with the oldest, HFS+ or Mac OS Extended is the legacy file system used by Apple all the way in 1998 and is still supported today. The issue being that HFS was not future proof - given the fact it cannot support file timestamps past February 6th, 2040 (Vigo., 2018).

Whilst HFS+ didn't support encryption at its entirety (a win in our books as forensic analysts) any device such as iMac or iPhone past iOS 10.3 will have had their file system converted from HFS+ to AFS automatically.

AFS or Apple File System (creative right...!) boasts many features, including full disk encryption, worrisome for analysts considering all devices past iOS 10.3 will have this system structure. But from a design point of view, AFS introduces smarter data management such as in the screenshot below, where a file requiring 3 blocks worth of space when copied, would require another 3 blocks again.

Instead of writing and storing the entire data again (taking up six blocks in our example), AFS simply creates another reference to the file (only taking up a total of four blocks in our example), similar to `inodes` in Linux.

## Modern iOS Security

Throughout the years of design, Apple's operating system for its iOS devices has ten-folded in measure of protecting their user's data. So much so, companies have made their reputation purely by being the ones who can unlock iPhones. The ability to do so for law enforcement / governmental authorities is a sprint race behind very, very closed doors.

For example, Elcomsoft recently announced being able to acquisition file system data from iPhones running iOS 13 and 13.3 without any jailbreaking. That in of itself is groundbreaking in mobile device forensics. And that's only what we've been told about!

But We're Average Joes Here. We can't be paying thousands for bits of kit and licensing. Nor do we have the space to carry every phone adapter from Nokia's to A or Micro-B cables.

You might be thinking, but surely with all of the security measures iPhones have these days such as Touch-ID and Face ID on top of the passcode, your data is safe, right?

Well, toolkits such as this UFED can use all of the acquisition methods that we discussed in task 8. However, what's worth noting is that the UFED is capable of forcing the iDevice to boot using UFED's custom boot loader, bypassing the entire iOS operating system - similar to rooting an android; resulting in an entire dump of the entire device. The issue with this? It contradicts the golden rule of digital forensics: Never turn it off.

People often install "panic switches" into devices, where a shutdown event could trigger an entire wiping of the device. Or in the case of iPhones, if the iPhone isn't properly isolated, it can be remotely wiped via the iCloud - a very true story.

iOS' "Restricted Mode"

Since 2018, Apple enforced a "Restricted Mode" on all iDevices running that version and above. This feature disables the input/output of data functionality from the lightning (charge) cable until the iPhone is unlocked with a passcode. Devices must be trusted before any data can be written - or so as by design.

## Data Acquisition & Trust Certificates

## Looking for Loot!

## Analyzing iOS Files
