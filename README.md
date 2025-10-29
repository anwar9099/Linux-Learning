# Linux-Learning

## Linux IPTables Lab Notes

This is a personal walkthrough and notes from the [KodeKloud Free Lab: Linux IPTables](https://kodekloud.com/free-labs/linux/iptables).  
The goal was to practice basic firewall setup and understand how to control network traffic between servers using `iptables`.

---

## 🧩 Lab Setup

### Environment
- **devapp01** – Web Server  
- **devdb01** – Database Server  
- **caleston-repo-01** – Software Repository  
- **Bob’s laptop** – Client machine  

---

## 🎯 Objectives

- Allow SSH access from Bob’s laptop to both the web and DB servers.  
- Allow Bob’s laptop to access the web app via HTTP (port 80).  
- Allow the web server (**devapp01**) to:  
  - Connect to the DB (**devdb01**) on port **5432** (PostgreSQL)  
  - Connect to the repo server (**caleston-repo-01**) on port **80**  
- Block all other outgoing HTTP/HTTPS traffic, except HTTPS to **google.com**.  

---

## ⚙️ Steps I Followed

### 1. SSH into the servers
```bash
ssh bob@devapp01
ssh bob@devdb01

Switched to root and installed iptables:

sudo -i
apt update
apt install iptables -y


Checked existing rules:

iptables -L 

2. Configure firewall on devapp01

Allow outgoing connections:

# PostgreSQL to devdb01
iptables -A OUTPUT -p tcp -d 172.16.238.11 --dport 5432 -j ACCEPT

# HTTP to caleston-repo-01
iptables -A OUTPUT -p tcp -d 172.16.238.15 --dport 80 -j ACCEPT


Block all other HTTP/HTTPS traffic:

iptables -A OUTPUT -p tcp --dport 80 -j DROP
iptables -A OUTPUT -p tcp --dport 443 -j DROP


Allow HTTPS to google.com:

# Find the IP of google.com
nslookup google.com

# Allow HTTPS traffic to that IP
iptables -I OUTPUT 1 -p tcp -d <GOOGLE_IP> --dport 443 -j ACCEPT

3. Verify rules
iptables -L OUTPUT -v -n --line-numbers


Expected order:

HTTPS to google.com → ACCEPT

PostgreSQL to devdb01 → ACCEPT

HTTP to caleston-repo-01 → ACCEPT

Other HTTP → DROP

Other HTTPS → DROP

4. Save rules (optional)
apt install iptables-persistent -y
netfilter-persistent save

🧠 What I Learned

How to view, add, and order iptables rules.

Why rule order matters (ACCEPT before DROP).

How to restrict outgoing traffic safely.

How to make rules persistent across reboots.

✅ Summary

SSH and HTTP access configured as required.

Web server limited to DB and repository servers.

HTTPS allowed only for Google.

All other web access blocked.

📚 References

KodeKloud Free Lab: Linux IPTables

iptables Manual Page

DigitalOcean Guide: IPTables on Ubuntu


---

This version is **fully copy-paste ready**, keeps all steps in order, uses proper bullet points and code blocks, and renders nicely on GitHub.  

If you want, I can also **add a tiny ASCII diagram** at the top showing Bob’s laptop → devapp01 → devdb01 + repo — it makes the README visually clearer.  

Do you want me to do that?
