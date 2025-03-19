<h1>DNS Records and Caching </h1>

<h2>Description</h2>
This project demonstrates the configuration and testing of DNS A-Records, CNAME records, and local DNS cache behavior within an Active Directory environment. The lab involves setting up DNS records on a domain controller (DC-1) and validating network connectivity from a client machine (CLIENT-1). Through practical exercises, we go over how to create and modify DNS records, manage DNS resolution, and troubleshoot DNS caching issues.
<br />

<h2>Techonologies Used</h2>

- <b>Microsoft Azure Virtual Machines</b>
- <b>Active Directory</b>
- <b>DNS Manager</b>
- <b>Remote Desktop</b>
- <b>Powershell</b>

<h2>Operating Systems</h2>

- <b>Windows 10</b>
- <b>Windows Server 2022</b>

<h2>Overview:</h2>

- <b>A-Record Exercise</b> 
- <b>Local DNS Cache Exercise</b>
- <b>CNAME Record Exercise</b>

<h2>Program Walk-Through:</h2>

<h2>Connect/Log in to virtual machines</h2>

Connect/log into DC-1 as your domain admin account (mydomain.com\jane_admin) <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/1%20Connect%20to%20dc1%20vm.PNG)
<br />

Connect/log into CLIENT-1 as an admin (mydomain\jane_admin) <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/2%20Connect%20to%20client1%20vm.PNG)
<br />

<h2>A-Record Exercise</h2>

From CLIENT-1 try to ping “mainframe” notice that it fails <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/3%20client1%20ping%20mainframe%20fail.PNG)
<br />

Nslookup “mainframe” notice that it fails (no DNS record)  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/4%20nslookup%20mainframe%20cant%20find.PNG)
<br />

<h3>Create a DNS A-record on DC-1 for “mainframe” and have it point to DC-1’s Private IP address</h3>

Open DNS Manager <br/>
Go to DC-1 -> Forward Lookup Zones -> mydomain.com <br/>
Notice the DNS settings for DC-1 and CLIENT-1 <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/5%20dc1%20dns%20manager.PNG)
<br />

Right click the whitespace  <br/>
Select New Host (A or AAAA) <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/5.2%20right%20click%20new%20host%20a.png)
<br />

Enter "mainframe" for the name  <br/>
Enter 10.0.0.4 for the IP address <br/>
Click the box for Create associated pointer (PTR) record  <br/>
Click Add Host  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/5.3%20new%20host%20mainframe.PNG)
<br />

We can see mainframe now points to 10.0.0.4  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/5.4%20mainframe%20points%20to%20ip10004.PNG)
<br />

We go back to CLIENT-1 and observe it successfully pings 10.0.0.4 <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/6%20client1%20ping%20mainframe%20success.PNG)
<br />

<h2>Local DNS Cache Exercise</h2>

Back in DC-1, right click on mainframe and go to Properties <br/>


![](https://github.com/rbrianshutt/dns/blob/main/DNS/7.1%20dc1%20right%20click%20properties.PNG)
<br />

We'll do an experiment and change the IP address to 8.8.8.8 <br/>
Click Apply and OK <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/7.2%20change%20ip%20address%20to%208888.PNG)
<br />

Back in CLIENT-1, ping mainframe  <br/>
Notice it is still pinging the old IP address of 10.0.0.4 and not 8.8.8.8  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/8.1%20client1%20ping%20mainframe%20still%20old%20ip%2010004.PNG)
<br />

Observe the local DNS cache after we run "ipconfig /displaydns"
It is still showing mainframe being associated with the old IP address 10.0.0.4   <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/9%20ipconfig%20displaydns.PNG)
<br />

We will flush the DNS cache<br/>
We need to log in to Powershell As Adminstrator and run "ipconfig /flushdns"

![](https://github.com/rbrianshutt/dns/blob/main/DNS/10%20as%20admin%20ipconfig%20flushdns.PNG)
<br />

When we run "ipconfig /displaydns" it shows an empty cache<br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/11%20run%20ipconfig%20displaydns%20observe%20cache%20empty.PNG)
<br />

When we ping mainframe again, it comes back as the updated IP address at 8.8.8.8  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/12%20ping%20mainframe%20new%20ip%20shows%20up.PNG)
<br />

<h2>CNAME Record Exercise</h2>

Back in DNS Manager on DC-1, right click in the whitespace and select New Alias (CNAME)  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/13.1%20dc1%20right%20click%20new%20alias%20cname.png)
<br />

Enter alias name "search"   <br/>
Enter "www.google.com" as the fully qualified domain name (FQDN)  <br/>
Click OK  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/13.2%20alias%20name%20search%20to%20wwwgooglecom.PNG)
<br />

Now the term "search" points to www.google.com <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/13.3%20search%20is%20alias%20cname.PNG)
<br />

Back on CLIENT-1, when we ping "search" it comes back with Google's IP address  <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/14%20client1%20ping%20search.PNG)
<br />

When we run "nslookup search", it points to IP addresses associated with Google <br/>

![](https://github.com/rbrianshutt/dns/blob/main/DNS/15%20nslookup%20search.PNG)
<br />

