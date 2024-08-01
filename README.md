# Azure-Network-Traffic-Observation-Lab <br> 
  <p>This lab guides you through observing various network traffic types in an Azure environment using Wireshark.
</p><br> 
<h2>1. Introduction</h2> 
This lab helps you understand how to observe different network traffic flows within a virtual network created in Azure. You'll utilize tools like Remote Desktop and Wireshark to analyze network communication.
</p><br>
<h2>2. Prerequisites</h2>
<dl>
	<dt>•	An Azure subscription with access to create resources. </dt>
	<dt>•	A Remote Desktop client installed on your local machine.(Windows 11, included)</dt> 
  <dd>	o	Remote Desktop Client for Mac available at the app store. </dd>
</dl>
</p><br>

<h2>3. Lab Steps</h2>
<dl><h3>Part 1: Create our Resources</h3><br> 	
	<dt> 1.	Create a Resource Group: Start by creating a resource group in Azure to organize your resources for this lab.</dt>
  <dt> 2.	Create a Windows 10 Virtual Machine (VM):</dt>
  <dd>		o	Deploy a Windows 10 VM within the resource group you just created.</dd>
  <dd>		o	During VM creation, select the option to create a new Virtual Network (VNet) and Subnet.</dd>
  <dt> 3.	Create a Linux (Ubuntu) VM:</dt>
  <dd>		o	Deploy a Linux (Ubuntu) VM within the same resource group.</dd>
  <dd>		o	While creating the Ubuntu VM, ensure you select the previously created VNet.</dd>
  <dt>4.	Observe Your Virtual Network: Utilize Network Watcher within Azure to view the details of your newly created virtual network.</dt>
</dl>
<br> 
<dl><h3>Part 2: Observe Network Traffic</h3>
<h4>General Approach:</h4>
<dL> For each traffic type (ICMP, SSH, DHCP, DNS, RDP), we'll:
	<dt> 1.	Briefly explain the purpose of observing this traffic.</dt>
	<dt>2.	List the steps to generate and observe the traffic.</dt>
	<dt>3.	Include a screenshot from Wireshark showcasing the specific traffic type.</dt>
</dl>
<br> 
<dl><h3>Part 2.1: Observe ICMP Traffic</h3>
<dt>•	Purpose: We'll use ICMP (ping) to test basic connectivity between VMs.</dt	>
<dt>•	Steps:</dt>
<dd>1.	Connect to your Windows 10 VM using Remote Desktop.</dd>
<dd>2.	Install Wireshark on the Windows 10 VM.</dd>
<dd>3.	Open Wireshark and apply a filter for ICMP traffic only <mark>(filter: icmp).</mark></dd>
<dd>4.	Retrieve the private IP address of your Ubuntu VM (use ipconfig command on the Ubuntu VM).</dd>
<dd>5.	Ping the Ubuntu VM's private IP address from the Windows 10 VM command prompt (e.g., ping 10.0.0.5).</dd>
<dd>6.	Observe the ping requests and replies within Wireshark  </dd>

![image](https://github.com/user-attachments/assets/c85c19b0-2ee8-44f6-b9d2-15b889ac08d8)

<dd>7.	From the Windows 10 VM, ping a public website (e.g., ping www.google.com). Observe the traffic in Wireshark.</dd>
<dd>8.	Initiate a continuous ping to the Ubuntu VM using ping -t 10.0.0.5 (Windows) or ping 10.0.0.5 -c 4 (Linux).</dd>
<dd>9.	Open the Network Security Group (NSG) associated with your Ubuntu VM2 (vm2-nsg) and disable inbound ICMP traffic.</dd>
<ul>
	<li>On the search back in Azure type “Network Security Groups”, and follow this click path :  vm2-nsg > inbound Security Rules > + add </li>
	<li>Now Create the new rule using this information</li>
	<ul>
		<li>Source  : 	Any</li>
		<li>Destination : 	Any </li>
		<li>Service: 	Custom </li>
		<li>Protocol :	ICMP</li>
		<li>Action: 	Deny</li>
		<li>Priority:	200 (before/less than the SSH priority) </li>
		<li>Name:		DENY_ICMP_FROM_ANYWHERE</li>
</ul></ul><br>
<dd>10.	Observe the changes in ICMP traffic on Wireshark and the ping results in the command prompt.</dd>
<dd>11.	Re-enable inbound ICMP traffic for the Ubuntu VM's NSG. Observe the ping activity resume in both Wireshark and the command prompt.</dd>
<ul>	
	<li>You can go back to the step 9  bullet , do not add a rule. Click on the existing rule [name: DENY_ICMP_FROM_ANYWHERE ] and change the action to “deny” or you can delete the rule to get the same effect.</li>
</ul>
<dd>12.	Stop the continuous ping on the Windows 10 VM (Ctrl+C).</dd>


![image](https://github.com/user-attachments/assets/48cd8a13-533f-41a3-b799-3cdf4a2d3c95) ![image](https://github.com/user-attachments/assets/a39a5c19-9d9d-4309-9c5c-49f77e2f1618)

</dl>  
<br>
<dl><h3>Part 2.2: Observe SSH Traffic</h3>
<dt>•	Purpose: We'll observe the traffic generated when establishing an SSH connection.</dt>
<dt>•	Steps:
<ol>
<li>Switch back to Wireshark and apply a filter for SSH traffic only <marK>(filter: tcp.port == 22).</mark></li>
<li>From the Windows 10 VM, initiate an SSH connection to your Ubuntu VM using its private IP address and credentials (e.g., ssh username@10.0.0.5).</li>
<li>Type commands within the SSH session and observe the corresponding SSH traffic displayed in Wireshark (Login & password required). </li>
	
![image](https://github.com/user-attachments/assets/f5230e17-e02b-4c66-b095-467ebe4c2e0c)

<mark>note: password will not show while typing. </mark>
 
<li>Exit the SSH connection by typing exit and pressing Enter.</li>
</ol></dl>

![image](https://github.com/user-attachments/assets/1b892aea-430b-4214-a2ab-e8c8eb2608bb)
<mark>Screenshot 2: SSH Traffic</mark>
<br>
<dl><h3>Part 2.3: Observe DHCP Traffic</h3>
<dt>•Purpose: We'll observe the communication between a VM and DHCP server for obtaining an IP address.</dt>
<dt>•Steps:</dt>
<br>
<ol>
	<li>In Wireshark, apply a filter for DHCP traffic only <mark>(filter: udp.port == 67).</mark></li>
	<li>On the Windows 10 VM, use the command `ipconfig /renew</li>
</ol></dl>	

![image](https://github.com/user-attachments/assets/4a8c8b8d-1303-40cc-a5f8-1fc5e27d87fb)


<dl><h3>Part 2.4: Observe DNS Traffic</h3>
	<dt>•	Purpose: We'll observe the DNS resolution process for translating domain names to IP addresses.</dt>	
	<dt>•	Steps:</dt>	
<br> <ol>
	<li>In Wireshark, switch the filter to capture only DNS traffic <mark>(filter: udp.port == 53).</mark></li>
	<li>On the Windows 10 VM, open a command prompt and use nslookup to query the IP addresses for google.com and disney.com.</li>
	<li>Observe the DNS requests and replies within Wireshark (Screenshot 4: DNS Resolution).</li>
</ol>
Screenshots 4.1-4.4: DNS Resolution 
<p>Google.com 

![image](https://github.com/user-attachments/assets/233f1a71-dfca-43a0-8b39-a5f73fd3e47f)

<br> 
Google.com  

![image](https://github.com/user-attachments/assets/c2d12897-ce39-477a-9338-867189f53de0)</p>

<p>Disney.com

![image](https://github.com/user-attachments/assets/f97efeec-a589-4738-b08c-a1f2bfc9a370)

<br> Disney.com

![image](https://github.com/user-attachments/assets/00785bcd-c887-4694-926c-d04d29cf63fd) </p>



<dl><h3>Part 2.5: Observe RDP Traffic</h3>
<dt>•	Purpose: We'll observe the traffic generated during a Remote Desktop connection.</dt>
<dt>•	Steps:</dt>
<ol>
<li>In Wireshark, apply a filter for RDP traffic only <mark>(filter: tcp.port == 3389).</mark></li>
<li>Initiate a Remote Desktop connection to your Windows 10 VM from your local machine.</li>
</ol>
</dl> 

 
<h3>Observation:</h3> 
You'll notice a continuous stream of traffic in Wireshark.
Explanation: Unlike protocols like ICMP or SSH that generate traffic for specific actions (pings, commands), RDP establishes a live connection for displaying the remote desktop. This continuous traffic flow maintains the real-time visual representation between the two machines.
<h3>Conclusion</h3>
This lab provided a hands-on experience observing various network traffic types within an Azure virtual network using Wireshark. You've learned to utilize filters to isolate specific traffic flows and gained insights into how different protocols communicate. Remember, proper cleanup of resources is crucial after completing your experiments.
