This project demonstrates how to perform manual STIG compliance hardening on a RHEL 9 virtual machine using OpenSCAP and the `scap-security-guide`. I used VMware vSphere to deploy a clean VM, ran before-and-after scans, applied key remediations, and documented results.

---

## 📌 Project Goals

- ✅ Understand STIG security baseline for RHEL 9
- ✅ Practice manual remediation of common findings
- ✅ Generate compliance scan reports (before and after)
- ✅ Document work for portfolio and interviews

---

## 🧰 Tools Used

- RHEL 9 VM (via vSphere/ESXi)
- OpenSCAP (`openscap-scanner`)
- SCAP Security Guide (`scap-security-guide`)
- HTML scan reports
- GitHub for documentation

---

## 🖥️ System Setup

- **VM Name:** `stig-lab.localdomain`
- **CPU/RAM:** 2 vCPU, 4 GB RAM
- **Disk:** 20 GB
- **Network:** NAT
- **OS:** RHEL 9.x

---

## 🔍 STIG Scan – Before Hardening

Used the following command to generate the baseline compliance report:

```bash
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_stig \
  --results pre-scan-results.xml \
  --report pre-scan-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

📄 Report output: `reports/pre-scan-report.html`

---

## 🛠️ Manual Remediations (Selected 10)

```bash
# 1. Enforce password expiration
sudo chage --maxdays 90 devops

# 2. Disable SSH root login
sudo sed -i 's/^PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 3. Enable auditd
sudo systemctl enable --now auditd

# 4. Disable unused service
sudo systemctl disable --now avahi-daemon

# 5. Harden /etc/shadow permissions
sudo chmod 000 /etc/shadow

# 6. Require sudo re-authentication
sudo visudo
# Add:
Defaults timestamp_timeout=0

# 7. Disable Ctrl-Alt-Del reboot key
sudo dconf write /org/gnome/settings-daemon/plugins/media-keys/logout "''"

# 8. Disable core dumps
echo '* hard core 0' | sudo tee -a /etc/security/limits.conf

# 9. Enable SELinux in enforcing mode
sudo setenforce 1
sudo sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config

# 10. Set default umask
echo "umask 027" | sudo tee -a /etc/profile
```

📸 Screenshots available in the `screenshots/` folder

---

## 🔁 Post-Remediation Scan

Re-scanned after hardening:

```bash
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_stig \
  --results post-scan-results.xml \
  --report post-scan-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

📄 Report output: `reports/post-scan-report.html`

---

## 📸 Screenshots

See the `/screenshots` folder for:

- VM setup
- Pre-scan results
- Remediation commands
- Post-scan results

---

## ✅ Results Summary

| Stage            | Compliance Score |
|------------------|------------------|
| Pre-Hardening    | ~47%             |
| Post-Hardening   | ~78%             |

---

## 📘 Lessons Learned

- How to use OpenSCAP for STIG scanning
- How to interpret and act on STIG findings
- How to harden Linux systems manually
- Practice with common config files (`/etc/ssh/sshd_config`, `/etc/security/limits.conf`, `/etc/shadow`)
- Gained hands-on with SELinux, auditing, sudo policy, umask, and more

---

## 🧠 Talking Points (For Interview)

> "I used OpenSCAP and the STIG profile to audit a fresh RHEL 9 system, then manually remediated 10 high-impact findings. I created before-and-after compliance reports, practiced securing SSH, enforcing password policies, setting SELinux, and hardening default permissions. It was a great deep dive into Linux hardening and system compliance."

---

## 📂 Project Structure

```bash
stig-rhel-hardening/
├── README.md
├── reports/
│   ├── pre-scan-report.html
│   └── post-scan-report.html
├── screenshots/
│   ├── vm-setup.png
│   ├── pre-scan.png
│   ├── remediation-commands.png
│   └── post-scan.png
└── commands.sh
```

---

## 🔗 How to Use

1. Clone the repo
2. View scan reports in `reports/`
3. View screenshots in `screenshots/`
4. Run any hardening command from `commands.sh`

