# MSSentinelSysmonParser
A simple parser for Sysmon logs through EID28 for Microsoft Sentinel

[Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) - the best system monitor for Windows! Even better than Windows auditing!

[Olaf Hartong's Sysmon Modular](https://github.com/olafhartong/sysmon-modular) - The best configuration generator for Sysmon ever shared with the world!

[Olaf Hartong's recent article on Sysmon EID 27 - file block executables](https://medium.com/@olafhartong/sysmon-14-0-fileblockexecutable-13d7ba3dff3e) - and the baseline for getting this written, the parser cleaned up, and pushed to GitHub.

Let's start with a description of the sysmon schema version. As shown below, the latest schema version as of 23-DEC-22 was 4.83. This will need to be updated in your sysmon config files if you wish to stay bleeding edge.

<kbd> <img src="img/sysmon-config-schema.jpg" > </kbd>

The following blocks include some additions to the version of sysmon modular generating as of 23-DEC-22. Modular was referenced in a link above, the few lines below allow the writing of sysmon EIDs 27 and 28 to the operational logs.

<kbd> <img src="img/sysmon-config-adds.jpg" > </kbd>

But wait...why am I talking about these things in the Sentinel Sysmon parser's GitHub repo? Hang tight, trust the process, we will get through this. 

As you can see, default download (c:\users\*\downloads) locations in userland get blocked with the sysmon EID 27 configuration shown above. This configuration is insufficient for proper usage for modern protective considerations but demonstrates the possibilities. 

<kbd> <img src="img/chrome-download.jpg" > </kbd>

This event then gets written to windows logs. Assuming you are integrated with Microsoft Analytics or Log Analytics agents and are capturing sysmon logs in your workspace, these logs will be queryable in just a few minutes. 

<kbd> <img src="img/eid27-block-chrome.jpg" > </kbd>

We can now also restrict file shredding in locations we configure with the config file directives. Scroll back up and check out the <-- EID 28 --> config block. All we restrict is c:\users\*\Downloads, obviously this is insufficient.

Shown next is an attempt to sdelete (shred) the firefox installer (pretend this is an adversary trying to cover their tracks ;). BLOCKED!!!!!!!

<kbd> <img src="img/cli-sdelete-firefox.jpg" > </kbd>

This was also written to the event log.

<kbd> <img src="img/eid28-shred-firefox.jpg" > </kbd>

Now for the actual goods, what we all came here for. If you want to make your Sysmon logs meaningful in most SIEMs, you need to parse them. There are a few available, and some appear to be well-maintained. The link below was last updated in October, and appears to cover all versions of sysmon.

[Microsoft Azure Parsers GitHub Repo](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/) - and has a Sysmon parser available.

So, here's another one crafted from other bits and pieces available.

[Click here](https://github.com/DefensiveOrigins/MSSentinelSysmonParser/blob/main/SysmonParser) for another sysmon parser.

As shown, you want to copy the contents of the parser.

<kbd> <img src="img/parser-copy.jpg" > </kbd>

Paste the entire blob into a Sentinel > Logs query window and run it. The query may take a moment. Once complete, click to Save As > Function. 

<kbd> <img src="img/parser-paste.jpg" > </kbd>

Name the function accordingly, and it matters, because you will be using this to query sysmon logs in all future queries. As shown, it was named SysmonParser. 

<kbd> <img src="img/parser-save.jpg" > </kbd>

Finally, run the function in a new query window by calling SysmonParser() and looking for those couple of event IDs - 27 and 28. 

<kbd> <img src="img/parser-usage.jpg" > </kbd>
