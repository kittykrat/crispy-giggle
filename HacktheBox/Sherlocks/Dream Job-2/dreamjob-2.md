# Dream Job-2 

<img width="100" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/djlogo.png" />

## Threat Intelligence - Very Easy

> ### Sherlock Scenario
> As a Threat Intelligence Analyst investigating Operation Dream Job, you have identified that the Lazarus Group utilized a variety of custom-built malware and tools to facilitate their operations. Your task is to analyze and gather intelligence on the malware utilized by this APT.


- We download the required `DreamJob-2.zip` containing two files:
  - `DANGER.txt`
  - `DANGER.zip`

- `Danger.txt` reads:

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj1.png" />

With that we unzip the malware `DANGER.zip` with the password `Dvn62WlNrt09`.

- After unzipping, we are greeted with 3 different files:

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj2.png" />

- `17.dotm`
- `BAE_HPC_SE.iso`
- `Salary_Lockheed_Martin_job_opportunities_confidential.doc`

***

## Tasks:

### Task 1:
#### According to MITRE ATT&CK, what previously known malware does DRATzarus share similarities with?

Upon visiting the MITRE ATT&CK website, we can see the entry for [**DRATzarus**](https://attack.mitre.org/software/S0694/). In the description it mentions that it shares similarities to [**Bankshot**](https://attack.mitre.org/software/S0239/).

>[!NOTE]
> #### DRATzarus
> [**DRATzarus**](https://attack.mitre.org/software/S0694/) is a remote access tool (RAT) that has been used by [**Lazarus Group**](https://attack.mitre.org/groups/G0032/) to target the defense and aerospace organizations globally since at least summer 2020. DRATzarus shares similarities with [**Bankshot**](https://attack.mitre.org/software/S0239/), which was used by [**Lazarus Group**](https://attack.mitre.org/groups/G0032/) in 2017 to target the Turkish financial sector.
> - ID: S0694
> - Type: MALWARE
> - Platforms: Windows
> - Version: 1.1
> - Created: 24 March 2022
> - Last Modified: 16 April 2025

- **Flag for task:**
```
Bankshot
```

***

### Task 2:
#### Which Windows API function does DRATzarus use to detect the presence of a debugger?

- We are unable to locate the API function until we unpack the `InternalViewer.exe` file as it's being heavily obfuscated. We run:

```
upx -d InternalViewer.exe -o InternalViewer_Unpacked.exe
```

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj3.png" />

- Once we do, the file is no longer obfuscated and we can use `strings` to locate the API used:

```
strings -a -t x InternalViewer_Unpacked.exe | grep IsDebuggerPresent
```

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj4.png" />

- **Flag for task:**
```
IsDebuggerPresent
```

***

### Task 3:
#### Torisma is another piece of malware used by the Lazarus Group. According to MITRE, it has encrypted its C2 communications using XOR and which other method?

Upon visiting the MITRE ATT&CK website, we can see the entry for [**Torisma**](https://attack.mitre.org/software/S0678/). In the description it mentions that it has encrypted its C2 communications using XOR and VEST-32. [Reference](https://www.mcafee.com/blogs/other-blogs/mcafee-labs/operation-north-star-behind-the-scenes/)

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj5.png" />

>[!NOTE]
> #### Torisma
> [**Torisma**](https://attack.mitre.org/software/S0678/) is a second stage implant designed for specialized monitoring that has been used by [**Lazarus Group**](https://attack.mitre.org/groups/G0032/). [**Torisma**](https://attack.mitre.org/software/S0678/) was discovered during an investigation into the 2020 Operation North Star campaign that targeted the defense sector. [_Reference_](https://www.mcafee.com/blogs/other-blogs/mcafee-labs/operation-north-star-behind-the-scenes/)
> - ID: S0678
> - Type: MALWARE
> - Platforms: Windows
> - Version: 1.2
> - Created: 01 February 2022
> - Last Modified: 10 April 2024


- **Flag for task:**
```
VEST-32
```

***

### Task 4:
#### Which packing method has been used to obfuscate Torisma?

Upon visiting the MITRE ATT&CK website, we can see the entry for [**Torisma**](https://attack.mitre.org/software/S0678/). In the description it mentions that IT has been packed with LZ4 compression. [_Reference_](https://www.mcafee.com/blogs/other-blogs/mcafee-labs/operation-north-star-behind-the-scenes/)

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj6.png" />

- **Flag for task:**
```
LZ4 compression
```

***

### Task 5:
#### Analyze the provided ISO file and identify the executable contained within it?

- We examine the ISO: Mount the `BAE_HPC_SE.iso` file to see its contents without executing anything.

```
# Create a mount point
sudo mkdir /mnt/iso
# Mount the ISO (read-only)
sudo mount -o loop BAE_HPC_SE.iso /mnt/iso
# List the contents
ls -la /mnt/iso
# If you find files, you can copy them to a working directory for further analysis
cp /mnt/iso/* ~/malware_analysis/
# Unmount when done
sudo umount /mnt/iso
```

Running these commands gives us two more additional files:

- `InternalViewer.exe`
- `BAE_HPC_SE.pdf`

- **Flag for task:**
```
InternalViewer.exe
```

***

### Task 6:
#### The executable found in the previous question was renamed. Can you identify its original name?

- We run the full `strings` command and begin searching for any file that ends with `.exe` until we run into a file that sticks out, leading us to `SumatraPDF.exe` as that is not a commonly known file.

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj7.png" />

```
SumatraPDF.exe
```

- **Flag for task:**
```
SumatraPDF.exe
```

***

### Task 7:
#### According to VirusTotal, when was the EXE from the previous question First Seen In The Wild?(UTC)

[VirusTotal Link](https://www.virustotal.com/gui/file/adce894e3ce69c9822da57196707c7a15acee11319ccc963b84d83c23c3ea802)

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj8.png" />

From here we click the `Details` tab, and scroll down to see the `First Seen in the Wild` Time:

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj9.png" />


- **Flag for task:**
```
2020-08-13 08:44:50
```

***

### Task 8:
#### What packer was used to pack the executable from Question 6? (Full name)

- From VirusTotal, we can stay on the details page and at the top we see:

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj10.png" />

```
 Packer: UPX (3.96) [NRV,brute] 
```

Upon googling for the full name we see that it's called: `Ultimate Packer for eXecutables`

- **Flag for task:**
```
Ultimate Packer for eXecutables
```

***

### Task 9:
#### What is the full URL found within the macro in the document Salary_Lockheed_Martin_job_opportunities_confidential.doc?

- While not always reliable, we run:

```
strings Salary_Lockheed_Martin_job_opportunities_confidential.doc
```

- From here, it prints out the entire command. We search our terminal with anything ending with `.com`.

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj11.png" />

- From this, we see the link within the document (de-fanged):

```
https[:]//markettrendingcenter[.]com/lk_job_oppor[.]docx
```

- **Flag for task:**
```
https[:]//markettrendingcenter[.]com/lk_job_oppor[.]docx
```

***

### Task 10:
#### Who is the author of the document Salary_Lockheed_Martin_job_opportunities_confidential.doc?

- Before anything, we can use a tool like exiftool to extract any metadata from the files without executing them. Using `exiftool`, we can run the command:

```
exiftool Salary_Lockheed_Martin_job_opportunities_confidential.doc
```
This gives us a good amount of information about the document, including the author: `Mickey`

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj12.png" />

- **Flag for task:**
```
Mickey
```

***

### Task 11:
#### Who last modified the above document?

- In the same command, we can see a few fields down the last person who modified the document: `Challenger`.

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/dj13.png" />

- **Flag for task:**
```
Challenger
```

***

### Task 12:
#### Analyze the "17.dotm" document. What is the directory where a suspicious folder was created? (Format: Give the path starting immediately after <USER>. Please pay attention to placeholder.)

- We needed `olevba` for future malware processing:

```
┌──(kali㉿kali)-[~/Desktop/DreamJob-2/DANGER]
└─$ olevba — decode 17.dotm > decoded_macro.txt
```
```
┌──(kali㉿kali)-[~/Desktop/DreamJob-2/DANGER]
└─$ grep -iE “environ|mkdir|createobject|filesystemobject|userprofile|appdata|roaming|templates|chr\(|& \”” decoded_macro.txt -n
```

- **Results"**
```
29:Function MkDir(szDir)
31: MkDir = CreateObject(“Scripting.FileSystemObject”).CreateFolder(szDir)
36: FileExist = CreateObject(“Scripting.FileSystemObject”).FileExists(szFile)
41: FolderExist = CreateObject(“Scripting.FileSystemObject”).FolderExists(szFolder)
50: Set BinaryStream = CreateObject(“ADODB.Stream”)
65: Set oXML = CreateObject(“Msxml2.DOMDocument.3.0”)
78: Set oXML = CreateObject(“Msxml2.DOMDocument.3.0”)
90: Set objStream = CreateObject(“ADODB.Stream”)
105: Set objStream = CreateObject(“ADODB.Stream”)
118: curDocNameFull = ActiveDocument.Path & “\” & ActiveDocument.Name
122: curDocName = curDocName & “ “
132: workDir = Environ(“UserProfile”) & “\AppData\Local\Microsoft\Notice”
134: MkDir (workDir)
139: dllPath = workDir & “\” & binName
143: workDir = workDir & “\” & binDir
145: MkDir (workDir)
147: dllPath = workDir & “\” & binName
160: orgDocPath = ActiveDocument.Path & “\” & ActiveDocument.Name
170: Set objDocApp = CreateObject(“Word.Application”)
200:|Suspicious|Environ |May read system environment variables |
208:|Suspicious|MkDir |May create a directory |
209:|Suspicious|CreateObject |May create an OLE object |
```

- This gives us our answer:

```
132: workDir = Environ(“UserProfile”) & “\AppData\Local\Microsoft\Notice”
```

- **Flag for task:**
```
\AppData\Local\Microsoft\Notice
```

***

### Task 13:
#### Which suspicious file was checked for existence in that directory?

- Using a command from decoded macro, we can find the answer:

```
┌──(kali㉿kali)-[~/Desktop/DreamJob-2/DANGER]
└─$ grep -nE “^\s*(Dim |Private |Public )?([a-zA-Z0–9_]+)\s*=|^\s*[a-zA-Z0–9_]+\s*=” decoded_macro.txt
```

- **Results"**
```
31: MkDir = CreateObject(“Scripting.FileSystemObject”).CreateFolder(szDir)
36: FileExist = CreateObject(“Scripting.FileSystemObject”).FileExists(szFile)
41: FolderExist = CreateObject(“Scripting.FileSystemObject”).FolderExists(szFolder)
57: Stream_BinaryToString = BinaryStream.ReadText
69: Base64DecodeToBinary = oNode.nodeTypedValue
82: Base64DecodeToString = Stream_BinaryToString(oNode.nodeTypedValue)
116: strDocTag = “ .doc”
118: curDocNameFull = ActiveDocument.Path & “\” & ActiveDocument.Name
119: curDocName = Left(curDocNameFull, InStrRev(curDocNameFull, “.”) — 1)
120: newDocNameFull = curDocName & strDocTag
122: curDocName = curDocName & “ “
123: newDocNameFull = curDocName & strDocTag
125: GetDocName = newDocNameFull
132: workDir = Environ(“UserProfile”) & “\AppData\Local\Microsoft\Notice”
136: binName = “wsuser.db”
137: binDir = “ws”
139: dllPath = workDir & “\” & binName
141: nIdx = 0
143: workDir = workDir & “\” & binDir
147: dllPath = workDir & “\” & binName
150: GetDllName = dllPath
158: dllPath = GetDllName()
159: docPath = GetDocName()
160: orgDocPath = ActiveDocument.Path & “\” & ActiveDocument.Name
167: a = sqlite3_stmt_all(orgDocPath, “S-6–81–3811–75432205–060098–6872”, “17”)
```

- **Flag for task:**
```
wsuser.db
```

***

<img width="400" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Sherlocks/Dream%20Job-2/Images/djclear.png" />

