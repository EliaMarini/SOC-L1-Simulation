# SOC L1 Simulation

<h1>Scenario and activity description</h1>
<br />
<p>
Alerts triage and investigation as a SOC L1 analyst using TryHackMe SOC simulator platform. 
</p>

<br />


<h1>Tools</h1>
<br />

<ul>
  <li>Microsoft Defender Advanced Hunting</li>
  <li>TryDetectThis a tool that simulates open source inteliggence regardi IPs, URLs, domains and also files</li>
</ul>

<br />

<h1>Alert</h1>
<br />

<p>The activity begins by analyzing an alert regarding a phishing e-mail</p>
<br />

<img width="1507" height="577" alt="Phishing alert" src="https://github.com/user-attachments/assets/833ba3e2-4298-4621-87ac-7e5c5ddf78e6" />

<p>
  Info gathered:
  <ul>
    <li>Time: 10/20/2026 09:48:37.811</li>  
    <li>Sender: leonard@fashionindustrytrends.xyz</li>
    <li>Recipient: yani.zubair@tryhatme.com</li>
    <li>Content: "Unlock the ultimate strategy to skyrocket your hat empire No experience needed Just click and watch the profits roll in"</li>
  </ul>
</p>

<br />
<br />

<h1>Triaging and investigation</h1>
<br />
<br />

<p>First i checked the sender e-mail and only the e-mail that triggered the alert seems to have been sent, also nobody replied to it.
By just looking at the e-mail content it shows signs of possible phishing, trying to make the user click on the attached link.

Note: in the simulation no link is provided so i can't investigate it.
</p>
<br />
<img width="1407" height="695" alt="Phishing 1" src="https://github.com/user-attachments/assets/401b2274-5844-4421-9efc-36934bec28b6" />

<br />

<p>Then i started checking activity from the host machine "win-3449" which is the one used by "yani.zubair@tryhatme.com", i also used a time filter to search for events right after the e-mail was received.</p>

<br />
<img width="1411" height="690" alt="Phishing 3" src="https://github.com/user-attachments/assets/b4224ca2-b043-4426-9d11-9e6adf240bab" />
<br />

<p>
Note: in the table used for this exercies the TimeGenerated field has the same value for all the events since this is a simulation and all logs have been generated at the same time, so it can't be used to filter time. And since timestamp_s uses a string value i had to use the todatetime() function.</p>

<p>
As we can see from the query output there is a lot of activity by process "spoolsv.exe" that is usually a legitimate windows service used for printers

There is also a DNS query made by OUTLOOK.EXE for ip  34.244.197.202, which is a legitimate private ip assigned to domain mail.tryhatme.finance

At this point i decided to get back to the alert queue and I noticed that there is another possible phishing e-mail using the same domain as the first one. So i checked the events using only the domain "@fashionindustrytrends.xyz".
</p>
<br />
<img width="1477" height="638" alt="Phshing 4" src="https://github.com/user-attachments/assets/7f06a106-6ce5-4fc0-84d3-3aa5d05ce155" />
<br />

<p>Three e-mails from the same domain were sent to 2 other users beside from "Yani Zubair". So i did the same check on their machine to see events right after the e-mail was received.</p>
<br />
<img width="1453" height="580" alt="Phishing 5" src="https://github.com/user-attachments/assets/1045531a-e4b4-4345-a79b-cda808a4d88d" />
<br />
<img width="1429" height="603" alt="Phishing 6" src="https://github.com/user-attachments/assets/4581f5a3-f9b2-4c48-b45f-346d55572660" />
<br />

<p>
  On this two machines there are less events and all seeems legitimate.
</p>

<br />

<h1>Investigation report</h1>

<br />

<p>By reviewing the alert queue there are 5 alerts that are part of this investigation:
  <ul>
<li>3 phishing e-mail alerts triggered all by the same domain "@fashionindustrytrends.xyz"</li>
<li>2 process alerts triggered on "win-3453" and "win-3449"</li>
  </ul>
The affected entities in total are :
<ul>
  <li>win-3449 (Yani Zubair)</li>
  <li>win-3453 (Kyra Flores)</li>
  <li>win-3458 (Armaan Terry)</li>
</ul>
The domain from where the e-mails where sent is legitimate, the content shows some signs of phishing and a deeper investigation on the links inside the e-mails should be done. 

The process alerts  have been triggered by:
<ul>
  <li>TrustedInstaller.exe (linked to Windows Modules installer service)</li>
  <li>rdpclip.exe (is the native Windows process responsible for managing the clipboard  between a local machine and a remote computer during a Remote Desktop session and also it was run from    "C:\Windows\System32\rdpclip.exe" which is the legitimate path from where it should be executed)</li>
</ul>

Regarding the other processes that didn't trigger an alert and that we saw in the events right after the e-mails were received, they all are legitimate processes as well, beside from the last one that maybe needs an escalation and a deeper investigation:
-AtBroker.exe
-spoolsv.ex
-sethc.exe
-OUTLOOK.EXE that made some queries towards a private legitimate ip 
mail.tryhatme.finance, 34.244.197.202
-iexplore.exe with command line "C:\Program Files\Internet Explorer\iexplore.exe" -startmanager -Embedding

In conclusion the e-mails might be actual phishing but it seems that none of the users that received them clicked on the links inside, i would apply a remediation action by blocking that domain. I still wouldn't recommend an escalation to L2 yet just because more investigation should be done about the link inside the e-mails and that last process, then based on the results i might escalate it.

</p>


