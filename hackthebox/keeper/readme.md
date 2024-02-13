<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/5ff96025-6771-4061-bc31-b9bf85d40558"></p>
<h2 align="center"><a href="https://app.hackthebox.com/machines/556"> KEEPER</a></h2>
<h3 align="center">
  <p align="center">

<h3 align="center">Overview</h3>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/24bb6bc9-2b5e-401e-a728-7ebd28d1dbb9"></p>
<p align="center">An easy-level straighforward machine with default credentials for a ticket system, and an exploit based on exploiting memory dumps as foothold, user access, and privilege escalation, respectively.</p>
<h3 align="center">TL/DR at the end (whole step-by-step resumed in a sentence)</h3>


<h2 align="center">Reconaissance/Scanning </h2>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/3385a9a0-9486-49c4-9eb4-7b93c94172c3"></p>
<p align="center">Always starting with nmap, but before the result of the nmap even shows up I usually always go check if trying acessing the IP through the browser returns something.<br>
In which this case, it did:<br></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/5ec9a7fb-2177-4170-9bfc-319e5c0d979f"></p>
<p align="center">The main domain (<code>keeper.htb</code>) auto-redirects you to the subdomain (<code><strong>tickets</strong>.keeper.htb</code>).<br> We need to add both of them to our <code>/etc/hosts</code> (so the DNS can resolve it).</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/ded74d5e-2cf2-4327-8299-25fc714bbc23"></p>
<p align="center">This is the page we get in return:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/583933fa-06c7-4948-9074-fd7d8af173cd"></p>
<p align="center">Just on plain sight we can actually spot some very useful info:</p>
<ul>
  <li >This subdomain is running on an program called "request tracker"</li>
  <li>The version of the program and what platform it is running on is right above the login form</li>
  <li>As well as the year on the left-bottom, which signs us this is a rather old version of the program</li>
</ul><br>
<p align="center">
By the way, this is the result of the nmap. Not much is learned aside from the fact that SSH is open.
</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/d55cde63-9a90-4b86-851e-6a8498dccf63"></p>
<p align="center">It's not stupid to leave another instance of <strong><i>nmap</i></strong> running scanning, this time scanning all ports <code>-p-</code>, — while exploring the platform and trying to find a foothold. And/or even a brute-forcer as <strong><i>gobuster</i></strong> or <strong><i>ffuf</i></strong> for directory and subdomain bruteforce.<br>
for this one though, it wasn't necessary. After my first google for any exploits regarding that version gave no return, I searched for the default credentials, which led me to this page.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/deec542f-57e8-4c29-b9d0-6d269e06f003"></p>
<h2 align="center">Foothold</h2>
<p align="center">After logging in, while looking through the menus on the plataform I strumbled upon the following:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/e91f03f2-6cf0-4d1a-9cc9-006c52b1045a"></p>
<p align="center">Looking into this e-mail/ticket, I highlighted the most important parts:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/fbe72d77-269b-4949-aaf8-6561fc9f3261"></p>
<p align="center">Anyway, going forward and looking through more menus I ended up finding the page for the users, and the webmaster(root) set themselves a reminder of the password for the user <strong>Lise</strong>.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/911c395f-c351-4137-8d04-6f32a9f96595"></p>
<h2 align="center">User and Privilege Escalation</h2>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/562bd193-4ee4-4e0e-aa47-971f43cb9025"></p>
<p align="center">After sucessfully logging in as "lnogaard" through <strong>ssh</strong> we find in her home directory a zip file that, upon extracting, contains the master password file as well as the memory dump<br> I <i><strong>netcat</strong>'ed</i> those back to my machine</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/0c031f8d-c468-4891-ac5d-09541c6f15f0"></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/e0a92122-a822-4cd8-a1e7-7099d74993f5"></p>
<p align="center">After searching for <i>"keepass dump exploit"</i> I was redirected to <a href="https://github.com/vdohney/keepass-password-dumper">this</a> repository, which is the original PoC for this CVE (CVE-2023-32784).<br>
  I ended up not using the original PoC because I didn't want to bother myself with .NET and I can't credit who wrote the python alternative because I couldn't find the repository and there's no credit in the source code. Any other alternative works though.<br> 
  You should use the original anyways. (but s/o to u bro lol)</p>
<h3 align="center">CVE-2023-32784</h3>
<p align="center">The vulnerability in question is "SecureTextBoxEx", a custom text box for passwords. The simplest way to explain is that during a password prompt every typed character gets stored in memory, and to explore this you need a dump of memory, <strong>any dump</strong> pretty much,
from swap files(<strong>pagefile.sys</strong>), hibernation files (<strong>hiberfil.sys</strong>) to a full RAM dump.<br>
Let's say your password is "password". At every character you type it gets stored in memory<br>
<p align="center"><strong>p => *a => **s => ***s => ****w => *****o => ******r => *******d => ********</strong></p>
<p align="center">in memory:<br></p>
<p align="center"><strong>p => p => pa => pas => pass => passw => passw => passwo => passwo => passwor => password</strong></p>
<p align="center">ippsec brokens up this exploit very well, <a href="https://youtu.be/0AafRQIaWmQ">here</a>.</p>
<p align="center">Enough of that, after running the PoC against the dump we get the following back:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/c34a3c95-0779-43d7-a954-1dd39c76aa53"></p>
<p align="center">After receiving this as output, I was confused at first, mostly because of the whitespaces and norse characters. It just didn't seem fit for a passsword. I throwed it on the first tab I had open, and it was Google translate, which ended up being very appropriate and yield me this result:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/0e7e6c25-9bfb-40c0-b5d5-0f139e27d0d9"></p>
<p align="center">Used <a href="https://app.keeweb.info/">this</a> website to open the master file and found a PuTTY ssh key inside.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/aa9e8e1d-4748-4e10-b0b6-42d3169def38"></p>
<p align="center">I can't use directly though, before that I need to convert into openssh format so I can properly use it. Fortunately, there is a way for that and it is with <strong><i>puttygen</i></strong>, for this case, especifically <strong><i>puttygen 0.75>+</i></strong>.<br>
  You can download it and compile directly from <a href="https://the.earth.li/~sgtatham/putty/latest/putty-0.80.tar.gz">here</a>.</p>
<p align="center">With that out of the way, we can finally execute this command:</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/00f3289a-14e0-41ec-853a-17a4d1ec72eb"></p>
<p align="center"><code>chmod 600 id_rsa.pem</code> so we have proper permissions, and finally SSH into root and get the flag.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/22bf811a-43c9-4fd0-9df7-2066285e14ee"></p>
<h2 align="center">TL/DR</h2>
<p align="center">Add the IP to your /etc/hosts, the login for the subdomain are <code>root:password</code>, on the navbar, go to <i>Admin</i> and in the dropbar to <i>Users</i> and <i>Select</i>, after that select <strong>lnogaard</strong>, get the <strong>password</strong> from the comments.<br>Log into the user, download the zip file in the /home directory and unzip it, run any PoC for <strong>CVE-2023-32784</strong> against the dump,
  search in the google whatever you got as result, use <strong>in lowcase</strong>on the master key.<br>Convert it to openssh format using a version of puttygen above 0.74 with <code>puttygen <ur-key>.ppk -O private-openssh -o id_rsa.pem</code>, give it the appropriate permissions with <code>chmod 600 id_rsa.pem</code> and ssh using it with the <code>-i</code> flag.</code></p>
<br><br>
<h3 align="center">Well, this has been aluche, thanks for reading. See ya.</h3>
<h2 align="center"><a href="https://evildocument.github.io">@evildocument</h2>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/4e555769-5b70-42d1-ba7f-de0a3596df40"></p>


















