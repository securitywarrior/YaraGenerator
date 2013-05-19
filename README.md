## Information
This is a simple tool to try to allow for quick, simple, and effective yara rule creation to isolate malware families. This is an experiment and thus far I've had pretty good success with it. It is a work in progress and I welcome forks and feedback!

To utilize this you must find a few files from a malware family you wish to profile, (the more the better, three to four samples seems to be effective). Place the samples in their own directory, and run the tool. Thats it! Yara Magic! Please note however that this tool will only be as precise as you are in choosing what you are looking for...

The theory behind the tool is as follows:


   As opposed to intensive analytical examination of a cadre of malware to determine similarities, by extracting all present strings and ensuring only to signature for those present in all desired samples and requiring ENOUGH of them to be present to equal a match, similar results can be achieved.

   In many ways this is less flexible than the existing methodology, but in some ways more so, as it relies less on anomalous indicators which can easily be changed. That said it needs a lot of work and tuning, because the risk is run of capturing strings only present in your sample set, but not the family at large. Lowering the critical hit from 100% of strings may approach a usable compromise there.

   I've integrated PEfile (http://code.google.com/p/pefile/) so when exes are part of the cadre of samples, their imports and functions will be removed from the lists of strings, also created a blacklist so you can exclude strings such as (!This program... etc) from inclusion in rules..

   I've lowered the string count to 20 from 30 to reflect these changes, of course the final number may be lower due to number of common strings, and random selection. 


## Version and Updates
0.4 - Added PEfile (http://code.google.com/p/pefile/) to extract and remove imports and functions from yara rules, added blacklist.txt to remove unwanted strings

0.3 - Added support for Tags, Unicode Wide Strings (Automatically Adds "wide" tag)

0.2 - Updated CLI and error handeling, removed hidden files, and ignored subdirectories

0.1 - Released, supports regular string extraction

## Author & License

YaraGenerator is copyrighted by Chris Clark 2013. Contact me at Chris@xenosys.org

YaraGenerator is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.
YaraGenerator is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with YaraGenerator. If not, see http://www.gnu.org/licenses/.

## Example

Usage is as follows with an example of a basic search +  hitting all of
the switches below:
<pre>

usage: yaraGenerator.py [-h] -r RULENAME [-a AUTHOR] [-d DESCRIPTION] [-t TAGS] InputDirectory

YaraGenerator

positional arguments:
  InputDirectory        Path To Files To Create Yara Rule From

optional arguments:
  -h, --help            show this help message and exit
  -r RULENAME         Enter A Rule/Alert Name (No Spaces + Must Start with Letter)
  -a AUTHOR           Enter Author Name
  -d DESCRIPTION      Provide a useful description of the Yara Rule
  -t TAGS             Apply Tags to Yara Rule For Easy Reference (AlphaNumeric)
</pre>

Example Usage To Build Two Rules from 3-4 Samples:

<pre>
python yaraGenerator.py ../pipedream/ -r Win_Trojan_PipeDream -a "Chris Clark" -d "PipeDream RAT" -t "MiddleEast APT"

[+] Generating Yara Rule Win_Trojan_PipeDream from files located in: ../pipedream/

[+] Yara Rule Generated: Win_Trojan_PipeDream.yar

  [+] Files Examined: ['50b136889962d0cbdb4f7bd460d7cd29', '79dce17498e1997264346b162b09bde8', '92ee1fb5df21d8cfafa2b02b6a25bd3b', 'a669c0da6309a930af16381b18ba2f9d']
  [+] Author Credited: Chris Clark
  [+] Rule Description: PipeDream RAT
  [+] Rule Tags: MiddleEast APT

[+] YaraGenerator (C) 2013 Chris@xenosec.org https://github.com/Xen0ph0n/YaraGenerator
</pre>

Another Example for a Specific Family of APT1 Malware:

<pre>
python yaraGenerator.py ../greencat/ -r Win_Trojan_APT1_GreenCat -a "Chris Clark" -d "APT Trojan Comment Panda" -t "APT"

[+] Generating Yara Rule Win_Trojan_APT1_GreenCat from files located in: ../greencat/

[+] Yara Rule Generated: Win_Trojan_APT1_GreenCat.yar

  [+] Files Examined: ['871cc547feb9dbec0285321068e392b8', '6570163cd34454b3d1476c134d44b9d9', '57e79f7df13c0cb01910d0c688fcd296']
  [+] Author Credited: Chris Clark
  [+] Rule Description: APT Trojan Comment Panda
  [+] Rule Tags: APT

[+] YaraGenerator (C) 2013 Chris@xenosec.org https://github.com/Xen0ph0n/YaraGenerator
</pre>
Resulting Yara Rules:
<pre>
rule Win_Trojan_APT1_GreenCat : APT
{
meta:
  author = "Chris Clark"
  date = "2013-05-18"
  description = "APT Trojan Comment Panda"
  hash0 = "871cc547feb9dbec0285321068e392b8"
  hash1 = "6570163cd34454b3d1476c134d44b9d9"
  hash2 = "57e79f7df13c0cb01910d0c688fcd296"
  yaragenerator = "https://github.com/Xen0ph0n/YaraGenerator"
strings:
  $string0 = "ProductName" wide
  $string1 = "t4j SV3"
  $string2 = "Remote"
  $string3 = "Service doesn't start"
  $string4 = "Service stopped"
  $string5 = "OpenP failed with %d"
  $string6 = "Bad Request (Invalid Hostname)"
  $string7 = "Invalid"
  $string9 = "pidrun"
  $string10 = "Comments" wide
  $string11 = "Totally %d volumes found."
  $string12 = "QVVVPVV"
  $string13 = "ServiceName>"
  $string14 = "Analog Devices, Inc." wide
  $string15 = " and the PID is %d"
  $string16 = "Create failed with %d"
condition:
  all of them
}

</pre>
<pre>
rule Win_Trojan_PipeDream : MiddleEast APT
{
meta:
  author = "Chris Clark"
  date = "2013-05-18"
  description = "PipeDream RAT"
  hash0 = "50b136889962d0cbdb4f7bd460d7cd29"
  hash1 = "79dce17498e1997264346b162b09bde8"
  hash2 = "92ee1fb5df21d8cfafa2b02b6a25bd3b"
  hash3 = "a669c0da6309a930af16381b18ba2f9d"
  yaragenerator = "https://github.com/Xen0ph0n/YaraGenerator"
strings:
  $string0 = "GetWindowThreadProcessId"
  $string1 = "LateIndexGet"
  $string2 = "lpVolumeNameBuffer"
  $string3 = "Assembly"
  $string4 = "Thread"
  $string5 = "7JU]dkr" wide
  $string6 = "Encoding"
  $string7 = "Process"
  $string8 = "WrapNonExceptionThrows"
  $string9 = "DirectoryInfo"
  $string10 = "Activator"
  $string11 = "[endof]" wide
  $string12 = "cbName"
  $string13 = "MaxLength"
  $string14 = "GetHashCode"
  $string15 = "System.Drawing"
condition:
  all of them
}



</pre>

## Results


PipeDream:
<pre>
100% Hits on Samples:

$ yara -g Win_Trojan_PipeDream.yar ../pipedream/
Win_Trojan_PipeDream [APT,MiddleEast] ../pipedream//VTDL50B136889962D0CBDB4F7BD460D7CD29.danger
Win_Trojan_PipeDream [APT,MiddleEast] ../pipedream//VTDL79DCE17498E1997264346B162B09BDE8.danger
Win_Trojan_PipeDream [APT,MiddleEast] ../pipedream//VTDL92EE1FB5DF21D8CFAFA2B02B6A25BD3B.danger
Win_Trojan_PipeDream [APT,MiddleEast] ../pipedream//VTDLA669C0DA6309A930AF16381B18BA2F9D.danger

100% True Negatives on ~5,000 Malware Samples
$ yara -r Trojan_Win_PipeDream.yar ../../MalwareSamples/

100% True Negatives on ~10,000 Clean Files
$ yara -r Trojan_Win_PipeDream.yar ../../CleanFiles/

100% Success Hunting On Virus Total
</pre>

GreenCat Rule:

<pre>
100% Hits on Test Samples:

$ yara -rg Trojan_Win_GreenCat.yar greencat/
Trojan_Win_GreenCat [APT] ../greencat//8bf5a9e8d5bc1f44133c3f118fe8ca1701d9665a72b3893f509367905feb0a00
Trojan_Win_GreenCat [APT] ../greencat//c196cac319e5c55e8169b6ed6930a10359b3db322abe8f00ed8cb83cf0888d3b
Trojan_Win_GreenCat [APT] ../greencat//c23039cf2f859e659e59ec362277321fbcdac680e6d9bc93fc03c8971333c25e

100% True Positives On Other Samples In the APT1 Cadre which were detected as Green Cat By Other Yara Rules:

$ yara -r Trojan_Win_GreenCat.yar .Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//1877a5d2f9c415109a8ac323f43be1dc10c546a72ab7207a96c6e6e71a132956
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//20ed6218575155517f19d4ce46a9addbf49dcadb8f5d7bd93efdccfe1925c7d0
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//4144820d9b31c4d3c54025a4368b32f727077c3ec253753360349a783846747f
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//4487b345f63d20c6b91eec8ee86c307911b1f2c3e29f337aa96a4a238bf2e87c
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//8bf5a9e8d5bc1f44133c3f118fe8ca1701d9665a72b3893f509367905feb0a00
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//c196cac319e5c55e8169b6ed6930a10359b3db322abe8f00ed8cb83cf0888d3b
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//c23039cf2f859e659e59ec362277321fbcdac680e6d9bc93fc03c8971333c25e
Win_Trojan_APT1_GreenCat [APT] ../../MalwareSamples/APT1Malware//f76dd93b10fc173eaf901ff1fb00ff8a9e1f31e3bd86e00ff773b244b54292c5

100% True Negatives on clean files:

$ yara -r Trojan_Win_GreenCat.yar ../../CleanFiles/

</pre>



