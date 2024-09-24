# Home Lab: Attack and Defense
Abstract. The goal of this lab is to simulate a real cyber attack and create endpoint rules to detect and respond to the attack. Utilizing Eric Capuano’s online guide, I will be using two virtual machines to simulate controlled attacks at my victim machine. The attack machine will utilize Sliver, a C2 post exploitation framework, to attack a Windows endpoint machine. The victim machine will be utilizing LimaCharlie as an EDR solution.

Eric Capuano’s guide: https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro?sd=pf

# Part I: Setup

To set up this lab, I will be setting up an attack machine on an Ubuntu server and an endpoint machine will be set up on Windows 11. First, I will be disabling Microsoft Defender so that it doesn’t interfere with any attacks I will be sending to this machine. Next, I will be installing Sliver on the Ubuntu machine as my primary attack tool. Finally, I will be setting up LimaCharlie on the Windows machine as an EDR solution to observe attacks and develop detection rules around the attacks. I will also be installing sysmon for auditing and logging, to generate telemetry inside LimaCharlie. LimaCharlie will have a sensor linked to the windows and will be importing sysmon logs.
![Screenshot 2024-09-15 175453](https://github.com/user-attachments/assets/d9b1363c-4f82-4551-8059-a80c73284a84
