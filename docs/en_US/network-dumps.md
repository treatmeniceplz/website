# Network Dumps
One of the best ways to support the project is to help in gathering network dumps of games. These network dumps can be used by developers to help understand how the servers operate in a live environment. This kind of data greatly simplifies development of the servers, as it removes a large amount of guesswork and reverse engineering.

In order to make this easier, we have developed a suite of internal tools to capture and read network traffic, as well as make use of several "off the shelf" tools. This document will go into detail about how to capture these packets yourself, and how you can submit them to us for use in research and development. This document will be fairly long, please read it completely before submitting any dumps.

## Table of Contents
1. [Submissions](#submissions)
2. [Game Packets](#game-packets)
	- [Wii U (HokakuCafe)](#wii-u-hokakucafe)
	- [3DS (HokakuCTR)](#3ds-hokakuctr)
	- [All (WireShark)](#all-wireshark)
3. [HTTP Packets](#http-packets)
	- [Fiddler](#fiddler-windows-free)
	- [Charles](#charles-cross-platform-50)
	- [Mitmproxy](#mitmproxy-cross-platform-free)

<div class="tip red">
	<h2>Security Warning</h2>
	<strong>Network dumps will oftentimes have sensitive information, including emails, passwords, usernames, IP addresses, etc. Because of this, ensure you only provide network dumps to trusted individuals. When submitting network dumps to Pretendo Network developers your dumps are uploaded and stored in a private channel. This channel can only be accessed by a small number of people, not even all developers have access.</strong>
</div>

# Submissions
There are multiple ways to submit network dumps. You may either contact a developer directly, or upload your network dumps to any channel in the Discord server. If uploaded to Discord, the Bandwidth bot will automatically detect the dumps and reupload them to our private channel, deleting the original message for security purposes.

When submitting we ask that you do the following:

1. Only submit dumps for one game at a time, to keep the files organized. Sending multiple dumps of multiple games makes it difficult to know which dumps go to which game,
2. Perform only small actions if possible. Each session should be focussed on as few aspects of the game as possible to minimize "noise" (unrelated packets) which makes it difficult to decipher what is actually going on. For example, rather than uploading a session of playing random courses on Super Mario Maker it would be more helpful if the session was focussed on one part of the game, such as 100 Mario Challenge, starring a course, etc. That way it's clear exactly what the packets are doing in game.
3. When submitting, please provide a detailed description of what was done in the session. For example "Loaded course world and played a random course, starred the course and played a recommended course". This will add further context as to what the packets are doing in the dumps.

# Game Packets
Game packet dumps are the packets sent during a session to a game server. These packets are typically encrypted, so additional data will need to be submitted in order for us to decrypt them. There are several ways to dump these packets and the data needed to decrypt them.

### Wii U (HokakuCafe)
The easiest way to dump game server packets from a Wii U is to use [HokakuCafe](https://github.com/PretendoNetwork/HokakuCafe). This is an Aroma setup module which patches IOSU to write all network frames to a `.pcap` file on the SD card.

To use HokakuCafe download the [latest release](https://github.com/PretendoNetwork/HokakuCafe/releases/latest) setup module (`30_hokaku_cafe.rpx`). Place this file on the SD card at `wiiu/environments/[ENVIRONMENT]/modules/setup` and insert it back into the Wii U. Start your Wii U once, turn it off, and open the newly created `HokakuCafe/config.ini` file on the SD card. Set `Mode` to `ALL`. At the time of writing, HokakuCafe has a bug where it will not dump all packets unless `Mode` is set to `ALL`. Insert the SD card back into the Wii U and all network traffic flowing through your console should be written to a PCAP.

The PCAP files are written to the `HokakuCafe` folder on the SD card, named as the date and time the session was started. HokakuCafe will also dump a BIN file, named `nexServiceToken-AAAAAAAAAA-BBBBBBBB.bin`. This file contains the data needed to decrypt the session packets, and MUST also be submitted. Otherwise the PCAP packets cannot be decrypted, and are of no use.

### 3DS (HokakuCTR)
The easiest way to dump game server packets from a 3DS is to use [HokakuCTR](https://github.com/PretendoNetwork/HokakuCTR). Like HokakuCafe this homebrew attempts to dump game traffic directly from the console. However unlike HokakuCafe, it does not dump all system traffic and may not work in all games. If HokakuCTR does not work in your game, see the last solution.

To use HokakuCTR, ensure you are updated to the latest Luma release. The latest Luma release now supports 3GX plugins. Download the [latest release](https://github.com/PretendoNetwork/HokakuCTR/releases/latest). Place this file on the SD card at `luma/plugins` and rename it `default.3dsx`.

When launching a game you will see 1 of 2 notifications on screen. You will either see `Not Ready` or `Ready`. If you see `Not Ready`, your game is not compatible. If you see `Ready`, your game is compatible. If you see nothing, either your game is not compatible or the plugin is not installed. If your game is compatible, it will start dumping game traffic to a PCAP file once the game has connected to the server. You will know the game has connected to the server when you see either `Detected NEX buffer type: V0` or `Detected NEX buffer type: V1` on screen. Until one of these notifications is shown, the PCAP will have no data.

The PCAP is written to the SD card located at `HokakuCTR/[GAME NAME]`. HokakuCTR dumps are already decrypted, no additional data is required to decrypt them. They may be submitted as-is.

### All (WireShark)
If neither solution above works, the only option you have is to capture the traffic manually off the console. This is easiest done with WireShark and a hosted Wi-Fi access point. Create a hosted wifi access point on your PC which has WireShark installed and select that access point's interface in WireShark. Connect the console to this hosted access point, and you should begin to see all the network traffic from your console in WireShark. In order to both create a hosted access point and keep your PC online, you must have 2 ways of connecting to the internet on the PC. This is easiest done by using a USB Wi-Fi adapter to host the access point, and using ethernet to remain online. Not all USB Wi-Fi adapters support hosting access points. Ensure the one you use does. [This adapter](https://www.amazon.com/dp/B07C9TYDR4) is relatively cheap and supports hosting access points.

Just like with HokakuCafe, traffic dumped with WireShark will be encrypted. In order to decrypt the traffic, your NEX account username and password must also be submitted. Your NEX account has no relation to any other account, such as your NNID, and cannot be used to gain any information about you. It can, however, allow someone to log into game servers as you. Ensure you only provide these details to trusted individuals.

- 3DS - To dump your NEX username and password from a 3DS download and run [this homebrew application](https://9net.org/~stary/get_3ds_pid_password.3dsx). This will create a file on your SD card named `nex-keys.txt` with your NEX username and password. Copy and paste the contents of this file into the message of your submission.
- Wii U - To dump your NEX username and password from a Wii U connect to your Wii U with an FTP client. Navigate to `/storage_mlc/usr/save/system/act` and download all the folders inside this folder. Check the `account.dat` file in each folder and look for your NNID username in the `AccountId` field. Once found, in the same `account.dat` file locate both the `PrincipalId` and `NfsPassword` fields. Copy their values and paste them into the message of your submission.

# HTTP Packets
Some games use HTTP requests for some features. Additionally, non-game titles will often use HTTP requests for their services. The above methods will not always capture HTTP requests in a way that is usable, if at all. SpotPass data is also downloaded via HTTP requests. For this, an HTTP proxy server is required. There are several options for HTTP proxy servers, which all depend on your operating system, skill level, and, for one, your budget

TODO - We have a NoSSL patch for the Wii U, but no builds are uploaded anywhere. Where should we upload this? Discord won't work anymore because of the expiring download link update. 3DS NoSSL patch by Billy https://github.com/SciresM/3DS-SSL-Patch/pull/2

### Fiddler (Windows, free)
TODO - Fiddler docs

### Charles (Cross platform, $50)
TODO - Charles docs

### Mitmproxy (Cross platform, free)
TODO - Mitmproxy docs