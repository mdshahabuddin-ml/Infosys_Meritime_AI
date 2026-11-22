# FreightQuote AI — Milestone 1

Secure sign-in and role-based access for **FreightQuote AI**, an AI-assisted freight quoting platform.
Milestone 1 delivers the authentication module: register, sign in, forgot password and a role-based workspace for each user type.

The app is built with **Streamlit** and runs in **Google Colab**. **ngrok** gives it a public URL.

---

## Contents

| File | Purpose |
|---|---|
| `Milestone1_FreightQuote.ipynb` | Main notebook. Merges the Maritime AI and FreightQuote AI notebooks and includes the start-up cells. |
| `FreightQuote_AI/app.py` | Authentication module (Streamlit). The notebook writes it to Drive. |
| `FreightQuote_AI/fq_ui.py` | Page styling and layout: login hero panel, auth header, workspace chips and dashboards |
| `FreightQuote_AI/run_colab.py` | Starts Streamlit on port **8503** and opens the ngrok tunnel |
| `FreightQuote_AI/freightquote.db` | SQLite database for users, login attempts and OTPs. It is created automatically. |
| `FreightQuote_AI/security_auth.py`, `dashboard_views.py`, `quote_service.py`, `quote_ui.py` | Supporting modules for security, dashboards and quoting |
| `FreightQuote_AI/ui_upgrade/backups/` | Time-stamped backups of `app.py`, made before each overwrite |

---

## Features

### Sign in
- Sign in with **email or username** and a password.
- **Workspace chips:** Admin, Operations Manager, Freight Broker, Auditor and Customer.
  - The chip you select must match the account's role in the database (Phase 1 RBAC).
- **Account lockout:** the 4th wrong password locks the account for **30 minutes**.

### Register
- Username: 3–50 characters (letters, digits, `_ . -`).
- Valid email address.
- Password: 10–72 characters.
- Three **security questions**: pet's name, first school and favorite city.

### Forgot password (two methods)
1. **Security questions.** No email setup is needed. 5 wrong tries lock this method for 15 minutes.
2. **Email OTP.** A 6-digit code is sent by Gmail SMTP.
   - The code is valid for **10 minutes**.
   - You get 5 tries.
   - You must wait 60 seconds before requesting a new code.

### Security
- Passwords, security answers and OTPs are stored only as **bcrypt hashes**.
- The OTP request gives the same reply whether or not the email exists, to stop account enumeration.

### Role dashboards
Dashboards include the Command Center, the AI Copilot (Admin and Customer views) and the other role views.

---

## Requirements

- A Google account that **owns (or has Editor access to)** the `FreightQuote_AI` folder in Google Drive.
- Google Colab, in the browser or through the Colab extension for VS Code.
- An ngrok account, for the auth token.
- Optional, for email OTP: a Gmail account with an **App Password**.

Python packages (installed by the notebook):

```
streamlit
pyngrok
bcrypt
requests
```

The notebook also installs everything in `requirements.txt`.

---

## Colab Secrets

Open **🔑 Secrets** in the Colab left sidebar and add:

| Name | Required | Value |
|---|---|---|
| `NGROK_AUTHTOKEN` | Yes | Your token from dashboard.ngrok.com |
| `SMTP_EMAIL` | For email OTP | The Gmail address that sends the OTP |
| `SMTP_APP_PASSWORD` | For email OTP | 16-character Gmail App Password |

Turn on **Notebook access** for each secret.

> Secrets are saved **per Google account**. If you change accounts, add them again.

---

## How to run

1. Open `Milestone1_FreightQuote.ipynb` in Colab. Sign in with the **account that owns the `FreightQuote_AI` Drive folder**.
2. Run the three cells under **▶ Start here**:
   1. **`[FQ-APP-BACKUP]`** mounts Google Drive and backs up the current `app.py` to `ui_upgrade/backups/`.
   2. **`%%writefile …/app.py`** writes the Milestone 1 authentication module.
   3. **`[FQ-START]`** installs the packages and runs `run_colab.py`.
3. Open the **ngrok URL** printed by the last cell. The FreightQuote AI sign-in page appears.
4. Click **Register** to create an account. Then choose your workspace chip and sign in.

To stop the app, interrupt the running cell. You can also run the cleanup cell at the end of Part A.

### Notebook layout

| Section | Content |
|---|---|
| Screenshots | Login, AI Copilot (Admin and Customer) and Command Center |
| ▶ Start here | Backup → write `app.py` → launch (port 8503). This is all you need for Milestone 1. |
| Part A | The original Maritime AI Milestone 1 app (dark theme, port 8501). It is optional and kept for reference. |
| Part B | The FreightQuote AI project history: patches, UI upgrade and role updates |

---

## Troubleshooting

| Problem | Cause / fix |
|---|---|
| `FreightQuote_AI not found` | Colab is signed in to a different Google account from the one that owns the folder. Use the owner account, or share the folder (Editor) with your account and choose **Add shortcut → My Drive**. |
| `ModuleNotFoundError: fq_ui` | `fq_ui.py` is missing from the project folder. Check it with `!ls /content/drive/MyDrive/FreightQuote_AI`. |
| ngrok error / no URL | `NGROK_AUTHTOKEN` is missing or wrong, or another tunnel is already open. Restart the runtime and run again. |
| OTP email not sent | Check `SMTP_EMAIL` and `SMTP_APP_PASSWORD`. Use an App Password, not your normal Gmail password. |
| "Selected workspace does not match" | The chip you picked is not this account's role. Pick the chip that matches your role. |
| Account locked | Too many wrong passwords. Wait 30 minutes, or reset the password. |

To check that Drive is mounted correctly:

```python
from google.colab import drive
drive.mount("/content/drive")
!ls /content/drive/MyDrive/FreightQuote_AI
```

You should see `app.py`, `fq_ui.py`, `run_colab.py` and `freightquote.db`.

---

## Tech stack

Python · Streamlit · SQLite · bcrypt · Gmail SMTP · pyngrok · Google Colab / Google Drive
