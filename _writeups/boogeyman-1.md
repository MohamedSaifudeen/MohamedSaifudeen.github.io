---
title: "Boogeyman 1"
room: "Boogeyman 1"
platform: "TryHackMe"
category: "Phishing Analysis"
difficulty: "Medium"
date: 2026-01-01
tags: [phishing, email-analysis, windows-event-logs, network-analysis]
status: "complete"
---

This rooms walks you through a structured way to solve the **Boogeyman 1** Challenge 

## Task 1 Introduction
So 1st the room requires 3 set of experience 
1. In Phishing and Email Analysis
2. In windows event logs 
3. In Network Traffic and Packet Analysis 

we get provided with 

1. A Phishing email and a attachment added to it
2. Powershell logs from julianne's workstation in json format to analyze it in the linux environment 
3. And a packet capture from the same workstation 

Note: The powershell.json file contains JSON-formatted PowerShell logs extracted from its original evtx file via the [evtx2json(opens in new tab)](https://github.com/Silv3rHorn/evtx2json) tool.

Tools

﻿The provided VM contains the following tools at your disposal:

- Thunderbird - a free and open-source cross-platform email client.
- [LNKParse3(opens in new tab)](https://github.com/Matmaus/LnkParse3) - a python package for forensics of a binary file with LNK extension.
- Wireshark - GUI-based packet analyser.
- Tshark - CLI-based Wireshark. 
- jq - a lightweight and flexible command-line JSON processor.

That's all Mentioned in Task 1 lets get into investigation 

---

## Task 2 Email analysis

So having a look into the Task 2 Julianne a finance employee at Quick Logistics LLC, received a follow-up email regarding an unpaid invoice from their business partner, B Packaging Inc. Unbeknownst to her, the attached document was malicious and compromised her workstation.

The email is clearly an Phishing email and it has already executed some process from the attachment as per the security team mentioned so we just jump straight into questiones with no further delay 

1. What is the email address used to send the phishing email?

	To find this lets just open the email with thunderbird and have a look at the mail and its headers. 
	![Email headers showing From address](images/t2-img1-mail-headers.png)
	we can see the From address in the above mail so that's the address used to send the phishing email 
	ANS: agriffin@bpakcaging.xyz

2. What is the email address of the victim?
	Same for the victim Email we can see it clearly so that's the answers
	ANS: julianne.westcott@hotmail.com

3. What is the name of the third-party mail relay service used by the attacker based on the DKIM-Signature and List-Unsubscribe headers?
	To get the information on 3rd party email relay used my the attacker we now have to go to the email headers by clicking More >> view source we can proceed 

	There we can see the DKIM signatures right above the from email

	In that we can see it has 2 domains so which one is the answer, so the way i concluded it is we already got the senders domain by their email so the 1st one is not the 3rd party rely and the 2nd one is a well known email service provider which is `elasticemail.com` then its the answer.
	![DKIM-Signature header showing relay domain](images/t2-img2-dkim-signature.png)

4. What is the name of the file inside the encrypted attachment?
	To find answer for this we have to download the attachment
	1st Download the `invoice.zip` file and Unzip it 
	![Unzipped invoice attachment contents](images/t2-img3-invoice-unzip.png)
	by unzipping it we can clearly see the fila name so that's the answer for this 
	don't forget to get the password from the email to fully unzip it 

5. What is the password of the encrypted attachment?
	You know the password which you got from the mail if not get the pass from the mail and unzip the mail.
	![Password found in phishing email](images/t2-img4-password.png)

6. Based on the result of the lnkparse tool, what is the encoded payload found in the Command Line Arguments field?
	Here we are going to use the `lnkparse` tool 
	
	`lnkparse Invoice_20230103.lnk`
	
	By executing the above command you'll get a ton of information What's inside the file
	and when you scroll down you can see the Base64 encoded payload right after the -enc 
	parameter.

	![Base64 encoded payload in LNK Command Line Arguments](images/t2-img5-enc-payload.png)

---

## Task 3 Endpoint Security

Based on the initial findings, we discovered how the malicious attachment compromised Julianne's workstation:

- A PowerShell command was executed.
- Decoding the payload reveals the starting point of endpoint activities.

Investigation Guide  

With the following discoveries, we should now proceed with analysing the PowerShell logs to uncover the potential impact of the attack:

- Using the previous findings, we can start our analysis by searching the execution of the initial payload in the PowerShell logs.
- Since the given data is JSON, we can parse it in CLI using the `jq` command.
- Note that some logs are redundant and do not contain any critical information; hence can be ignored.

JQ Cheatsheet 

﻿**jq** is a lightweight and flexible command-line JSON processor**.** This tool can be used in conjunction with other text-processing commands. 

You may use the following table as a guide in parsing the logs in this task.

Note: You must be familiar with the existing fields in a single log.

|                                                                    |                                                                           |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| Parse all JSON into beautified output                              | `cat powershell.json \| jq`                                               |
| Print all values from a specific field without printing the field  | `cat powershell.json \| jq '.Field1'`                                     |
| Print all values from a specific field                             | `cat powershell.json \| jq '{Field1}'`                                    |
| Print values from multiple fields                                  | `cat powershell.json \| jq '{Field1, Field2}'`                            |
| Sort logs based on their Timestamp                                 | `cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[]'`            |
| Sort logs based on their Timestamp and print multiple field values | `cat powershell.json \| jq -s -c 'sort_by(.Timestamp) \| .[] \| {Field}'` |

You may continue learning this tool via its [documentation(opens in new tab)](https://stedolan.github.io/jq/manual/).

1. What are the domains used by the attacker for file hosting and C2? Provide the domains in alphabetical order. (e.g. a.domain.com,b.domain.com)

	To get the domains used by the attacker
	 
		`cat powershell.json | jq |grep bpakcaaging.xyz` 
	
	This command prints the events occurred with `bpakcaging.xyz`
	![PowerShell log grep showing attacker domains](images/t3-img1-domains.png)
	we can see 2 domains are used  
	`cdn.bpakcaging.xyz,files.bpakcaging.xyz`

2. What is the name of the enumeration tool downloaded by the attacker?

	There might be other best practices to find the installation of tool but out of curiosity i tried this command
		`cat powershell.json | jq |grep http`
	![PowerShell log showing seatbelt tool download](images/t3-img2-tool-used.png)
	
	I simply changed the domain name to `http` to have a look at the http traffic and turns out it has the answer. 
		`seatbelt`
	It's an open-source Windows enumeration tool

3. What is the file accessed by the attacker using the downloaded **sq3.exe** binary? Provide the full file path with escaped backslashes.
	
	The below command prints the event related with the sq3.exe binary 
	
		`cat powershell.json | jq |grep sq3.exe`
	
	Before this file access the attacker had changed into multiple directories those are 
		`C:\\Users\\j.westcott\\`
	
	So the full answer becomes this 
		`C:\\Users\\j.westcott\\AppData\\Local\\Packages\\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\\LocalState\\plum.sqlite
	![sq3.exe accessing Sticky Notes database file](images/t3-img3-sq3-file-access.png)



4. What is the software that uses the file in Q3? 
	And you can See the name of the software is mentioned 
		Microsoft Sticky Notes
	![Software identified using the accessed file](images/t3-img4-software-name.png)

5. What is the name of the exfiltrated file?
	The same command can help to find that
	`cat powershell.json | jq | grep file`
	![Name of exfiltrated file in logs](images/t3-img5-exfil-filename.png)

6. What type of file uses the .kdbx file extension?
	
	You need to do external research.
	![Research result on .kdbx file type](images/t3-img6-kdbx-filetype.png)

6. What is the encoding used during the exfiltration attempt of the sensitive file?
	
	We already came past this encoding of sensitive file which is by this command
	`cat powershell.json | jq | grep bpakcaging.xyz`
		hex
	![Hex encoding used during exfiltration](images/t3-img7-encoding-hex.png)
	
7. What is the tool used for exfiltration?
	In the same image we can see what tool was used for exfiltration
		nslookup
	![nslookup identified as exfiltration tool](images/t3-img8-nslookup-tool.png)

---

## Task 4 Network Traffic Analysis

Based on the PowerShell logs investigation, we have seen the full impact of the attack:

- The threat actor was able to read and exfiltrate two potentially sensitive files.
- The domains and ports used for the network activity were discovered, including the tool used by the threat actor for exfiltration.

Investigation Guide  

Finally, we can complete the investigation by understanding the network traffic caused by the attack:

- Utilize the domains and ports discovered from the previous task.
- All commands executed by the attacker and all command outputs were logged and stored in the packet capture.
- Follow the streams of the notable commands discovered from PowerShell logs.
- Based on the PowerShell logs, we can retrieve the contents of the exfiltrated data by understanding how it was encoded and extracted.


1. What software is used by the attacker to host its presumed file/payload server?
	
	If you go back to the Task 3 Question 5 we can see the Ip used to exfiltrate the .kdbx file 
	we will use those in the wireshark filter to find the software used
		`ip.addr==167.71.211.113 && http contains "bpakcaging.xyz"`
	
	![Wireshark filter showing payload server software](images/t4-img1-server-software.png)
1. What HTTP method is used by the C2 for the output of the commands executed by the attacker?
	Filtered `http contains "cdn.bpakcaging.xyz"` first to see the full C2 conversation shape GETs (beacon/task pull) mixed with POSTs. 
	![HTTP GET/POST traffic shape to C2](images/t4-img2-c2-get-post.png)
	
	Narrowed to `http.request.method==POST` to confirm every command-output submission uses POST, all `application/x-www-form-urlencoded`![Filtered POST requests to C2](images/t4-img3-post-method.png)
	
2. What is the protocol used during the exfiltration activity?
	Of course its DNS tunneling so
		DNS
3. What is the password of the exfiltrated file?
	
	Followed the HTTP stream immediately after the command-dispatch stream (749 → 750) on the same C2 host. The POST body was a long run of space-separated numbers decoded as decimal (not hex, unlike the DNS exfil channel) via CyberChef's "From Decimal" operation, revealing the Sticky Notes content in plaintext.  
	 That inconsistency is a small but real fingerprint of the toolkit/scripts in play.
	![HTTP stream after command-dispatch](images/t4-img4-http-stream-1.png)
	![Decoded decimal POST body](images/t4-img5-http-stream-2.png)
	![Sticky Notes plaintext content revealed](images/t4-img6-http-stream-3.png)
	This confirms the full C2 loop end-to-end — GET pulls a task, the task runs locally, POST sends the output back and shows the attacker used two different encodings across two different channels (decimal over HTTP for command output, hex over DNS for the bulk file exfil).

4. What is the credit card number stored inside the exfiltrated file?
	
	Need to reconstruct`protected_data.kdbx` from the DNS exfil traffic, then open it in KeePassXC using the password recovered from the Sticky Notes POST body.
	
	I found this tshark command browsing through internet this only prints the encoded strings removing the cutting the entire domain 
		`tshark -r capture.pcapng -Y dns -T fields -e dns.qry.name | grep bpakcaging.xyz | cut -f1 -d '.' | grep -v -e "files" -e "cdn"| uniq | tr -d '\\n'
	![tshark command extracting DNS exfil strings](images/t4-img7-tshark-dns-extract.png)
	I didn't get the result the way i want so i needed assist from AI ![AI-assisted extraction of hex strings](images/t4-img8-ai-assist.png)
		
	Save the Output file for the reconstruction![Saved output file for reconstruction](images/t4-img9-output-saved.png)
	![Reconstructing protected_data.kdbx](images/t4-img10-file-reconstruction-1.png)
	
	![KeePassXC opening reconstructed file](images/t4-img11-file-reconstruction-2.png)
	
	4024007128269551
	![Credit card number found in KeePass vault](images/t4-img12-credit-card.jpeg)