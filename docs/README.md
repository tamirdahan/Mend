# Mend CLI – Setup & Usage Guide

## Overview
This document provides instructions for installing, configuring, and using the Mend CLI to perform SAST, SCA, IaC, and container scans.  
It also includes API usage examples, environment setup, and troubleshooting tips.

---

## 🧰 Installation

### 1. Download Mend CLI
Choose the version for your operating system:

#### **Linux**
```bash
sudo curl https://downloads.mend.io/cli/linux_amd64/mend -o /usr/local/bin/mend
sudo chmod +x /usr/local/bin/mend
```

#### **Windows**
```powershell
mkdir C:\mend\cli
curl https://downloads.mend.io/cli/windows_amd64/mend.exe -o C:\mend\cli\mend.exe
```

#### **MacOS (Apple Silicon)**
```bash
sudo curl https://downloads.mend.io/cli/darwin_arm64/mend -o /usr/local/bin/mend
sudo chmod +x /usr/local/bin/mend
```

#### **MacOS (Intel)**
```bash
sudo curl https://downloads.mend.io/cli/darwin_amd64/mend -o /usr/local/bin/mend
sudo chmod +x /usr/local/bin/mend
```

---

### 2. Add Mend CLI to PATH
#### **Windows**
```powershell
set PATH=%PATH%;C:\path\to\mend-cli
```
Or set permanently via *Environment Variables*.

#### **Linux / MacOS**
```bash
export PATH=$PATH:/path/to/mend-cli
```

Check installation:
```bash
mend version
```

---

### 3. Authenticate
```bash
mend auth login
```
- Select Mend environment (`https://saas-il.mend.io/` for Ness)
- Choose modules (SCA, SAST, CN)
- Provide **Email / API Token**
- Verify authentication:
  ```bash
  mend auth info
  ```

---

## ⚙️ Environment Variables

### **Windows**
```powershell
set MEND_URL=https://saas-il.mend.io/
set MEND_EMAIL=mendscanner@ness.com
set MEND_USER_KEY=<your_user_key>
set MEND_ORGANIZATION=8d77e662-b26a-41bd-840e-b48c4a2a7ed6
```

To persist permanently:
```powershell
setx MEND_URL https://saas-il.mend.io/
setx MEND_EMAIL mendscanner@ness.com
setx MEND_USER_KEY <your_user_key>
setx MEND_ORGANIZATION 8d77e662-b26a-41bd-840e-b48c4a2a7ed6
```

### **Linux / MacOS**
```bash
export MEND_URL=https://saas-il.mend.io/
export MEND_EMAIL=mendscanner@ness.com
export MEND_USER_KEY=<your_user_key>
export MEND_ORGANIZATION=8d77e662-b26a-41bd-840e-b48c4a2a7ed6
```

---

## 🧩 SCA & SAST Scanning

### **SCA Scan Examples**
```bash
mend sca -d . -s "CLI//HelloWorld" -u
mend dep -u -d <scan_folder>
```

### **SAST Scan Examples**
```bash
mend code -d <path_to_code> -s "App//Proj" --offline
mend code --submitsaved <Scan_ID>
```

### **Incremental Scan**
```bash
mend code --dir <path_to_project> --upload-baseline
mend code --dir <path_to_project> --inc
```

---

## 🐳 Container Scanning

### **Local Scan**
```bash
mend image alpine:3.21.2 --export-results ./result.json --local
```

### **Remote Scan**
```bash
mend image alpine:3.14
```

### **Export Results**
```bash
mend image node:23 --show license --local --export-results ./scan.json
```

---

## 🧱 IaC & Python Projects

### **IaC Scan**
See: [Mend IaC Configuration](https://docs.mend.io/bundle/integrations/page/download_the_mend_cli.html)

### **Python Scan**
```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
mend dep -u
deactivate
```

---

## 🧪 Testing & Debugging

### **Test Connectivity**
```bash
mend connectivity --mend-url https://saas-il.mend.io/
```

### **Logging Levels**
```bash
export MEND_LOG_LEVEL="DEBUG"  # Linux
setx MEND_LOG_LEVEL "DEBUG"    # Windows
--log-level debug
LOG_LEVEL=DEBUG
EXTERNAL_LOG_IN_CONSOLE=true
WS_UA_LOG_IN_CONSOLE=true
 

```

Log levels:
- `INFO` – basic behavior  
- `DEBUG` – verbose output  
- `WARNING` – non-critical issues  
- `ERROR` – critical errors  

---

## 🧠 Useful Flags

| Flag | Description |
|------|--------------|
| `-d, --dir` | Target directory for scan |
| `-s, --scope` | Define scan scope (ORG//APP//PROJ) |
| `-u, --update` | Update project inventory |
| `-r, --reachability` | Compute reachability for supported CVEs |
| `--export-results` | Save results to file (JSON/TXT) |
| `--local` | Save results locally only |
| `--fail-policy` | Fail on policy violations |

---

## 🔗 Mend API Calls

### **Login**
**POST** `https://api-saas-il.mend.io/api/v3.0/login`
```json
{
  "email": "mendscanner@ness.com",
  "userKey": "<user_key>"
}
```

**Response:**
- `refreshToken`
- `userUuid`
- `userName`
- `jwtTTL`

### **Access Token Request**
**POST**  
`https://api-saas-il.mend.io/api/v3.0/login/accessToken?orgUuid=8d77e662-b26a-41bd-840e-b48c4a2a7ed6`

Body:
```json
{
  "wss-refresh-token": "<refreshToken>"
}
```

---

## 🧯 Troubleshooting

- **PKIX Path Building Failed:**  
  [Guide](https://docs.mend.io/wsk/pkix-path-building-failed-sun-security-provider-ce)

- **Connectivity Issues:**  
  Ensure access to:
  - https://saas-il.mend.io/
  - https://downloads.mend.io/
  - Port 443

- **Proxy Considerations:**  
  If a proxy exists, ensure correct configuration before scanning.

---

## 📜 References

- [Mend CLI Documentation](https://docs.mend.io/bundle/mend_sast/page/running_a_scan.html)  
- [Mend API Docs](https://docs.mend.io/platform/latest/getting-started-with-mend-api-3-0)  
- [Mend Examples (Azure DevOps)](https://github.com/mend-toolkit/mend-examples/tree/main/CI-CD/AzureDevOps)
