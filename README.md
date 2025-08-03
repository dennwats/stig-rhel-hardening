# RHEL 9 STIG Partial Hardening Lab (Manual Remediation)

This project demonstrates how to perform **partial STIG compliance hardening** on a RHEL 9 virtual machine using **OpenSCAP** and the `scap-security-guide`. I deployed a clean VM, ran before-and-after scans, applied selected **manual fixes**, and documented results and screenshots.

> ⚠️ Note: This is a **partial demonstration**. I intentionally applied a handful of **manual remediations** to showcase practical skills, not to achieve 100% compliance.

---

## 📌 Project Goals

- ✅ Understand STIG baseline compliance for RHEL 9
- ✅ Practice **manual remediation** of common findings
- ✅ Generate and compare before/after scan reports
- ✅ Showcase work as a portfolio project

---

## 🧰 Tools Used

- RHEL 9 VM (on vSphere/ESXi)
- OpenSCAP (`openscap-scanner`)
- SCAP Security Guide (`scap-security-guide`)
- HTML scan reports
- GitHub for documentation

---

## 🖥️ VM Setup

- **VM Name:** `stig-lab.localdomain`
- **vCPU/RAM:** 2 vCPU, 4 GB RAM
- **Disk:** 20 GB
- **OS:** RHEL 9.x
- **Network:** NAT
(screenshots/photo1.png)
---

## 🔍 Pre-Hardening STIG Scan

Generated a baseline scan report using OpenSCAP:

```bash
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_stig \
  --results pre-scan-results.xml \
  --report pre-scan-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

📄 Output saved to `reports/pre-scan-report.html`

---

## 🛠️ Manual Remediations (Performed)

Below are the specific remediations I manually completed to demonstrate hands-on proficiency with common STIG findings:

### 1. Installed Required Security Tools

Installed entropy and crypto tools:

```bash
sudo dnf install -y gnutls-utils nss-tools rng-tools
```
<img width="1037" height="1104" alt="Photo 4" src="https://github.com/user-attachments/assets/7fbe0584-423f-49fc-9726-473497c3cd78" />


**Purpose:** Improves system entropy generation, DNSSEC validation, and cryptographic capabilities.

> Dependencies auto-installed: `gnutls-dane`, `jitterentropy`, `unbound-libs`

---

### 2. Removed Unnecessary Packages

```bash
sudo dnf remove -y iputils tuned
```
<img width="1263" height="1159" alt="Photo 4-1" src="https://github.com/user-attachments/assets/8fe96d02-b305-41f3-b7b2-a8e020cbcc74" />


**Purpose:** Reduces the attack surface by removing unused packages like `tuned` and `tuned-profiles`.

---

### 3. Reduced `sudo` Timeout to 0

File modified: `/etc/sudoers`

```bash
Defaults timestamp_timeout=0
```
<img width="712" height="135" alt="Photo 4-2" src="https://github.com/user-attachments/assets/506faf3b-3f6e-429d-8f99-1252832b804d" />


**Purpose:** Forces re-authentication on every sudo use to limit privilege escalation windows.

---

### 4. Disabled Media Key Logout Shortcut

```bash
dbus-launch dconf write /org/gnome/settings-daemon/plugins/media-keys/logout '""'
```
<img width="888" height="83" alt="Photo 4-3" src="https://github.com/user-attachments/assets/f1eaf3a6-f5ea-45b5-baf5-878d4c177b9f" />


**Purpose:** Disables hardware logout keys that could bypass auditing or screen lock enforcement.

---

📸 Screenshots for each change are included in the `screenshots/` folder.

---

## 🔁 Post-Hardening Scan

Re-ran OpenSCAP scan after manual fixes:

```bash
sudo oscap xccdf eval \
  --profile xccdf_org.ssgproject.content_profile_stig \
  --results post-scan-results.xml \
  --report post-scan-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rhel9-ds.xml
```

📄 Output saved to `reports/post-scan-report.html`

---

## ✅ Results Summary

| Stage            | Compliance Score |
|------------------|------------------|
| Pre-Hardening    | ~46.59%          |
| Post-Hardening   | ~55.4%          |

---

## 📸 Screenshots

Included in `screenshots/`:

- VM deployment
- Pre-scan result
- Each manual fix
- Post-scan result

---

## 📘 Lessons Learned

- How to run OpenSCAP scans against STIG profiles
- Identifying and interpreting STIG findings
- Applying common Linux hardening techniques manually
- Gaining familiarity with tools like `dconf`, `sudoers`, and `dnf`

---

## 🧠 Talking Point (for Interviews)

> "I used OpenSCAP with the STIG profile to assess a fresh RHEL 9 system, then manually fixed selected high-priority items. I generated before-and-after compliance reports and used this as a hands-on hardening lab. This demonstrated my understanding of real-world remediation practices, not just automation."

---

## 📂 Folder Structure

```bash
stig-rhel-hardening/
├── README.md
├── reports/
│   ├── pre-scan-report.html
│   └── post-scan-report.html
├── screenshots/
│   ├── pre-scan.png
│   ├── fix-sudo.png
│   ├── fix-dconf.png
│   └── post-scan.png
└── commands.sh
```

---

## 🧪 How to Reproduce

1. Clone this repo
2. Use OpenSCAP to scan `ssg-rhel9-ds.xml`
3. Apply selected fixes manually
4. View screenshots and reports

---

## 🏁 What's Next?

- Extend this project with Ansible-based automation
- Apply full STIG profile using `--remediate`
- Build a hardened base image for future use
