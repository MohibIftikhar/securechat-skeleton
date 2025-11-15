# Secure Client-Server Chat System (IS Assignment 2)

This project is a console-based secure chat system implementing a custom application-layer security protocol. It fulfills the requirements for **Information Security – Assignment 2 (Fall 2025)**.

The system is written in Python and uses standard cryptographic primitives:

- **AES-128 (ECB + PKCS#7)**
- **RSA-2048 (PKCS#1 v1.5, SHA-256)**
- **Diffie–Hellman Key Exchange**
- **SHA-256 hashing**
- **X.509 Certificates + Custom CA**

The goal is to achieve:

✅ Confidentiality  
✅ Integrity  
✅ Authenticity  
✅ Non-Repudiation  

⚠ **TLS/SSL is *not* used** — all cryptographic logic runs at the application layer.

---

# 🚀 1. Prerequisites & System Setup

This guide is intended for **Kali Linux / Debian-based systems**.

---

## 1.1 Install System Packages

```bash
sudo apt update
sudo apt -y upgrade
sudo apt install -y git mariadb-server python3-venv
```

---

## 1.2 Secure the Database

Run the secure setup:

```bash
sudo mariadb-secure-installation
```

Recommended:

- Set root password → YES  
- Remove anonymous users → YES  
- Disallow remote root login → YES  
- Remove test database → YES  
- Reload privilege tables → YES  

---

# 🔧 2. Project Configuration

## 2.1 Clone & Set Up Project

```bash
git clone https://github.com/MohibIftikhar/securechat-skeleton.git
cd securechat-skeleton
python3 -m venv venv
source venv/bin/activate
pip3 install cryptography mysql-connector-python
```

---

## 2.2 Configure Database Access

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
FLUSH PRIVILEGES;
```

Create DB:

```sql
CREATE DATABASE secure_chat;
USE secure_chat;

CREATE TABLE users (
    email VARCHAR(255) NOT NULL,
    username VARCHAR(255) NOT NULL UNIQUE,
    salt VARBINARY(16) NOT NULL,
    pwd_hash CHAR(64) NOT NULL,
    PRIMARY KEY (email)
);
```

---

## 2.3 Create config.py

```python
DB_CONFIG = {
    'host': 'localhost',
    'user': 'root',
    'password': 'YOUR_PASSWORD_HERE',
    'database': 'secure_chat'
}
```

Add to `.gitignore`.

---

# 🔑 3. Generate Keys & Certificates

```bash
python3 scripts/gen_ca.py
python3 scripts/gen_cert.py server localhost
python3 scripts/gen_cert.py client client.user
python3 scripts/gen_dh_params.py
```

---

# ⚡ 4. Execution

## Run Server

```bash
python3 server.py
```

## Run Client

```bash
python3 client.py
```

---

# 💬 Sample Usage

Registration, login, encrypted chat, logout, session receipts.

---

# 🧪 5. Offline Verification

```bash
python3 verify_transcript.py client_receipt.json client_transcript.log
```

Expected:

```
Hash OK
Signature VALID
Transcript authentic and untampered.
```

---

# ✔ End of README.md
