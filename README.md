# Analysis of `DEVICE ID CHANGER.SH`

## 📌 Overview
The file named **`DEVICE ID CHANGER.SH`** is a **Shell Script** intended for Android systems.  
It requires **root privileges** and is designed to **decode and execute hidden payloads**.  

- **File Type:** Shell Script (`.sh`)  
- **Execution:** Requires `su` (root)  
- **Hashes:**  
  - **SHA-256:** `496a2c51cafc6d468f26c819b8a732923ecd86e89aacff22432e032cc27c5928`  
  - **MD5:** `da6f65e1478d9cbf4f0d3baf4980ee20`  

---

## ⚙️ How It Works

### 1) Wrapper Command
The first line executes:
```sh
su -c echo "<Base64 Block>" | base64 -d | sh
```
- `su -c` → runs as **root**.  
- `echo "<Base64>" | base64 -d` → decodes a **large Base64 block**.  
- The output is piped to `sh` → executed immediately.  

➡️ This means the script is just a **loader**, not the real logic.

---

### 2) Stage 1 – Obfuscated Loader
The decoded script:
- Contains **randomized variable and function names** (e.g., `TdZPbMJHVCeYux...`).  
- Uses multiple layers of:
  - `base64 -d`  
  - `sed "s/$4/$3/g"` (alphabet substitution before decoding)  
  - `eval` (to run decoded code)  
- Builds a final payload inside a variable, then executes:
```sh
eval "$ZisbuLXXJiCwrLNgOBFeMGMnxkausxbFq"
```

➡️ This technique is called **multi-layer obfuscation**, hiding the true purpose of the script.

---

### 3) Possible Final Payload
- Based on the file name: **“DEVICE ID CHANGER”**, it is likely intended to modify:  
  - **Android ID**  
  - **Build properties (build.prop)**  
  - Possibly other device identifiers  
- Requires **root** to function.  
- **No evidence** of direct FRP bypass functionality (FRP = Factory Reset Protection).  

➡️ Changing **Device ID** does **not** bypass FRP, since FRP is tied to Google account verification.

---

## 🚩 Red Flags
- Runs hidden code with `su -c` and `eval`.  
- Multi-layer obfuscation to conceal intent.  
- Random variable names to avoid detection.  
- No comments, no documentation, no transparency.  

➡️ These are common traits in **malware, spoofing tools, or blackhat utilities**.

---

## 🔒 Security Notes
- **Do not execute** this script on real devices.  
- Always analyze such scripts in **isolated environments (VM / Emulator)**.  
- In many regions, **modifying IMEI or device IDs is illegal**.  

---

## 🧪 Safe Static Analysis
To study without executing:
```sh
# Extract Stage 1 (without running it)
echo "<Base64 block>" | base64 -d > stage1.sh

# Replace eval with print to see payload
# Example:
# eval $(echo <B64> | base64 -d)
# becomes:
printf "%s\n" "$(echo <B64> | base64 -d)" >> stage2.txt
```
Repeat until the final script is revealed.

---

## ✅ Conclusion
- This file is **not a direct FRP bypass tool**.  
- It is an **obfuscated loader** designed to run hidden shell code as root.  
- Its likely function: **Device ID manipulation**, not FRP removal.  
- Due to its obfuscation, it should be treated as **potentially malicious**.  
