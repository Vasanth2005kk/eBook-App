Got it ✅ I’ll clean up your draft and give you a **ready-to-use `calibre-web.md` documentation** in proper format with corrected commands.

Here’s the fixed version:

---

# Calibre-Web

## 1. Clone Repository

```bash
git clone https://github.com/janeczku/calibre-web.git
cd calibre-web
```

---

## 2. Create Virtual Environment

It’s essential to isolate your Calibre-Web installation to avoid dependency conflicts.

```bash
python3 -m venv calibre-web-env
```

### Activate the Virtual Environment

```bash
source calibre-web-env/bin/activate
```

---

## 3. Install Dependencies

```bash
pip install -r requirements.txt
```

(If you also want the latest PyPI release instead of source code, you can run:)

```bash
pip install calibreweb
```

---

## 4. Run Calibre-Web

```bash
python3 cps.py
```

By default, it runs on:
👉 [http://localhost:8083](http://localhost:8083)

---

## 5. OAuth Setup (Optional)

Enable login via Google or GitHub OAuth:

```bash
pip install calibreweb[oauth]
```

---

# Calibre-Web: Enable HTTPS Locally & Fix Google OAuth SSO Issues

This guide explains how to run **Calibre-Web with HTTPS locally** and fix the **Google OAuth login error** (`Login failed, No User Linked With OAuth Account` / `500 Internal Server Error: Scope has changed`).

---

## 1. Generate a Self-Signed SSL Certificate

Run these commands:

```bash
mkdir -p ~/ssl
cd ~/ssl

# Generate private key
openssl genrsa -out server.key 2048

# Generate self-signed certificate (valid 1 year, CN = localhost)
openssl req -new -x509 -key server.key -out server.crt -days 365 -subj "/CN=localhost"
````

You now have:

* `~/ssl/server.key`
* `~/ssl/server.crt`

(Optional) Combine into a `.pem`:

```bash
cat server.crt server.key > server.pem
```

---

## 2. Configure Calibre-Web to Use SSL

Edit `calibre-web/cps/server.py` and set the certificate paths:

```python
certfile_path = "/home/vasanth/ssl/server.crt"
keyfile_path  = "/home/vasanth/ssl/server.key"
```

Restart Calibre-Web:

```bash
cd calibre-web/
source venv/bin/activate
python cps.py
```

---

## 3. Fix Google OAuth Scope Error

Google changed OAuth scopes — it now requires `openid` in addition to `userinfo.email`.

Edit `calibre-web/cps/oauth_bb.py`:

```python
# Before
scope=["https://www.googleapis.com/auth/userinfo.email"]

# After
scope=["openid", "https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
```

---

## 4. Update Google OAuth Redirect URI

Go to **Google Cloud Console → APIs & Services → Credentials → Your OAuth 2.0 Client ID**
and add the following to **Authorized redirect URIs**:

```
https://localhost:8083/login/google/authorized
```

Save changes.

---

## 5. Test Setup

* Open Calibre-Web in browser:

  ```
  https://localhost:8083
  ```

* Browser may warn about **self-signed certificate** → Click **Advanced → Proceed**.

* Click **Google Login** button → It should now work.


## 6. Dependencies in Calibre-Web

For extra features (e.g., metadata editing, eBook conversion, etc.), check the official wiki:
🔗 [Dependencies in Calibre-Web (Linux & Windows)](https://github.com/janeczku/calibre-web/wiki/Dependencies-in-Calibre-Web-Linux-and-Windows)

---
