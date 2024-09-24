# Home Lab: Attack and Defense
Abstract. The goal of this lab is to simulate a real cyber attack and create endpoint rules to detect and respond to the attack. Utilizing Eric Capuano’s online guide, I will be using two virtual machines to simulate controlled attacks at my victim machine. The attack machine will utilize Sliver, a C2 post exploitation framework, to attack a Windows endpoint machine. The victim machine will be utilizing LimaCharlie as an EDR solution.

Eric Capuano’s guide: https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro?sd=pf

# Part I: Setup

To set up this lab, I will be setting up an attack machine on an Ubuntu server and an endpoint machine will be set up on Windows 11. First, I will be disabling Microsoft Defender so that it doesn’t interfere with any attacks I will be sending to this machine. Next, I will be installing Sliver on the Ubuntu machine as my primary attack tool. Finally, I will be setting up LimaCharlie on the Windows machine as an EDR solution to observe attacks and develop detection rules around the attacks. I will also be installing sysmon for auditing and logging, to generate telemetry inside LimaCharlie. LimaCharlie will have a sensor linked to the windows and will be importing sysmon logs.

![Screenshot 2024-09-15 175453](https://github.com/user-attachments/assets/d9b1363c-4f82-4551-8059-a80c73284a84)
_Figure 1.1: Disabling Real-time protection_

![Screenshot 2024-09-15 185009](https://github.com/user-attachments/assets/4ab97b02-a94e-4579-9f8a-f3b05edb25c9)
_Figure 1.2: While in Safe Mode, disabling some services via the Registry_

![Screenshot 2024-09-15 191706](https://github.com/user-attachments/assets/c1cd5f02-8b63-4e02-8ce5-03c97a07afdc)
_Figure 1.3: Installing sysmon_

![Screenshot 2024-09-15 200057](https://github.com/user-attachments/assets/5eb8a014-39ef-475a-b3d9-2a34611c7dbd)
_Figure 1.4: Installing LimaCharlie EDR_

![Screenshot 2024-09-15 203414](https://github.com/user-attachments/assets/9e2c8c08-6240-4e33-9a6e-305ab4af533a)
_Figure 1.5: Configured LimaCharlie to send sysmon logs alongside its own EDR telemetry_

![Screenshot 2024-09-15 203510](https://github.com/user-attachments/assets/0904640c-5e7a-4365-a22d-8ffd21184f59)
_Figure 1.6: Setting up attack machine Ubuntu_

![Screenshot 2024-09-16 102331](https://github.com/user-attachments/assets/099bc407-4f05-4b7d-8517-8a46c8a34398)
_Figure 1.7: Installing Sliver_

# Part II: Attacks and Defense

In this step, I will be generating the C2 payload on Sliver and implanting the malware onto the Windows victim machine. After the malware is executed onto the Windows machine, I can create a command and control session.

![Screenshot 2024-09-16 102409](https://github.com/user-attachments/assets/0e16f046-2a3c-406d-84c0-6afb4ffc4142)
_Figure 2.1: Generated the first C2 session payload_

![Screenshot 2024-09-16 104512](https://github.com/user-attachments/assets/c70d91d9-c555-49f6-b67c-c660580bae73)
_Figure 2.2: Downloaded C2 payload from the Linux VM to the Windows VM_

![Screenshot 2024-09-16 144242](https://github.com/user-attachments/assets/a85d81af-40cb-418e-9cdd-8c5cf3ded67b)
_Figure 2.3: Enabled Sliver HTTP server to catch the callback_

![Screenshot 2024-09-16 144232](https://github.com/user-attachments/assets/cadbf9fd-34f8-4092-bc63-d67ea95fa845)
_Figure 2.4: Executed the C2 payload from its download location using the administrative Powershell prompt_

![Screenshot 2024-09-16 144242 (1)](https://github.com/user-attachments/assets/9f0b0e8a-1714-4f12-9f63-5c60be45d3b2)
_Figure 2.5: Session created in the Sliver server_

![Screenshot 2024-09-16 144302](https://github.com/user-attachments/assets/56860b71-7a1b-490e-8e67-81d17622ddf7)
_Figure 2.6: Verifying session in Sliver_

Now that I have a live session between the two machines, I can begin peeking around with the attack machine checking privileges and getting host information.

![Screenshot 2024-09-16 144726](https://github.com/user-attachments/assets/9e1d81ad-6d97-4d54-ae47-1706ab67468c)

![Screenshot 2024-09-16 144739](https://github.com/user-attachments/assets/5bf7f171-8330-49fd-80ef-07f64e3ed682)
_Figure 2.7: Info on what user my implant is running as_

![Screenshot 2024-09-16 144837](https://github.com/user-attachments/assets/9c8178a6-d794-443f-bd0b-2b6d43338746)
_Figure 2.8: Checking privileges such “SeDebugPrivilege” and “SeImpersonatePrivilege” that make attack activity easier_

![Screenshot 2024-09-16 145142](https://github.com/user-attachments/assets/3f06050d-6210-4802-aed6-1c358748c930)
_Figure 2.9: Examining network connections occurring on the remote system, Sliver process highlighted in green_

![Screenshot 2024-09-16 145350](https://github.com/user-attachments/assets/cd4857d5-8268-4fb2-8ecb-b843cbb074a4)
_Figure 2.10: “ps -T” command identifying running processes on the remote system, implant highlighted in green_

Now looking inside LiamCharlie SIEM and seeing telemetry from the attacker to the host machine.

![Screenshot 2024-09-16 150051](https://github.com/user-attachments/assets/6e0c7cf6-f5e2-4cab-af9d-dcd2639788ce)
_Figure 3.1: Processes_

![Screenshot 2024-09-16 150621](https://github.com/user-attachments/assets/b87543f1-c6d9-4062-8116-9bcb304edafc)
_Figure 3.2: Network list_

![Screenshot 2024-09-16 150745](https://github.com/user-attachments/assets/077c3eec-3680-488f-8d5b-961946470761)
![Screenshot 2024-09-16 150806](https://github.com/user-attachments/assets/cc5d2d40-aecc-4c5a-8231-977c6c54ceae)
_Figure 3.3: Identifying the destination IP this process is communication with_

Now I’ll use LimaCharlie to scan the hash of the payload through VirusTotal; it will be clean because I generated the payload myself.

![Screenshot 2024-09-16 150937](https://github.com/user-attachments/assets/d584d93c-eddd-4e6a-a00e-72ecfb38be0a)
![Screenshot 2024-09-16 150924](https://github.com/user-attachments/assets/8a830fd2-8e11-4eff-8b09-119a4c960a76)
![Screenshot 2024-09-16 150949](https://github.com/user-attachments/assets/ce3acec6-acf6-4c7a-986e-f2cbf7c2566e)
_Figure 3.4: Inspecting the hash of the suspicious executable by scanning it in VirusTotal_

![Screenshot 2024-09-16 151856](https://github.com/user-attachments/assets/92bbbd89-dfa8-432b-b315-7926ef9e8327)
_Figure 3.5: Viewing real-time EDR telemetry_

![Screenshot 2024-09-16 185154 (1)](https://github.com/user-attachments/assets/193e802f-e5a1-4ab1-bc42-1bc8f9a11dbe)
_Figure 3.6: Filtering timeline with known IOCs, this “SENSITIVE_PR0CESS_ACCESS” event is from when I enumerated my privileges in an earlier step_

Now on the attack machine, I’ll be stealing the credentials on the system by dumping the LSASS memory. In LimaCharlie, I’ll be checking the sensors, observing the telemetry, and writing rules to detect and respond to this attack.

![Screenshot 2024-09-16 185154](https://github.com/user-attachments/assets/c866c407-96be-497e-a791-2b9385c4337a)
_Figure 4.1: lsass.exe event_

![Screenshot 2024-09-16 185951](https://github.com/user-attachments/assets/17f7889d-d87b-49df-9018-e8f91052bcae)
_Figure 4.2: Building a detection and response rule from this event_

![Screenshot 2024-09-16 190500](https://github.com/user-attachments/assets/f43cf4ad-3d93-447e-b870-9684f16424d9)
![Screenshot 2024-09-16 191055](https://github.com/user-attachments/assets/b29616bf-c54a-42f7-a384-3b2e6266c2f4)
_Figure 4.3: Created new rule and tested rule_

Now that I have created a detection and response rule. I will now write a rule using LimaCharlie that will detect and block attacks coming from the Sliver server. On the Ubuntu attack machine, I will simulate parts of a ransomware attack by attempting to delete the volume shadow copies. In LimaCharlie, I’ll view the telemetry and then write a rule that will block the attack entirely. After I create the rule in the SIEM, the attack machine will not be able to try the same attack again.

![Screenshot 2024-09-16 192814](https://github.com/user-attachments/assets/a1fd3e7c-1675-4a57-a3d1-3e816198b3f4)
_Figure 5.1: shell into the victim machine_

![Screenshot 2024-09-16 192907](https://github.com/user-attachments/assets/03cf2d89-815f-41b7-afdb-76649c29e5f8)
_Figure 5.2: run command to delete Volume Shadow Copies and verify I still have an active system shell_

![Screenshot 2024-09-16 193024](https://github.com/user-attachments/assets/84aa1188-ccc6-43ee-9785-00c2c1024b2a)
_Figure 5.3: LiamCharlie detected the events_

![Screenshot 2024-09-16 193231](https://github.com/user-attachments/assets/5f6498c7-088d-4d79-b6d1-855af90ef48c)
_Figure 5.4: Raw event that generated this detection_

![Screenshot 2024-09-16 193648](https://github.com/user-attachments/assets/06d02b53-3d4d-4b71-829a-6b11b83be5eb)
_Figure 5.5: Crafted a response action that will take place when this activity is observed_

![Screenshot 2024-09-16 193834](https://github.com/user-attachments/assets/f8cc19ed-6409-4dc7-9b13-b49061a86fb8)
_Figure 5.6: Running the command to delete volume shadows, testing if the D&R rule terminated the parent process, and terminated the dead system_

