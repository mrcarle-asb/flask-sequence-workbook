---
name: Windows Setup
index: 2
---

# Setting up Flask locally — Windows

You need to install three things. Do them in order.

---

## 1. Install Python

Go to **python.org/downloads** and download the latest Python 3 Windows installer.

Run it.

:::alert{warn}
**On the first screen, before you click anything else, check the box that says "Add python.exe to PATH."** It is near the bottom. If you miss this, uninstall Python and start this step again.
:::

Click Install Now and let it finish.

To check it worked, open the Start menu, search for **cmd**, and open Command Prompt. Type:

```
python --version
```

You should see a version number like `Python 3.13.x`. If you get an error, the PATH checkbox was missed.

---

## 2. Install Git

Go to **git-scm.com/download/win** and download the installer.

Run it. All default options are fine — keep clicking Next. This also installs Git Credential Manager, which handles your GitHub login so you never need to type passwords or set up SSH keys.

---

## 3. Install VS Code

Go to **code.visualstudio.com** and download the Windows installer. Run it. Default options are fine.

Open VS Code. Click the **Extensions** icon on the left sidebar (four squares). Search for **Python** and install the extension by Microsoft.

---

## Connect to GitHub

In VS Code, click the **Source Control** icon on the left sidebar (the branch icon). Click **Clone Repository**, then **Clone from GitHub**.

A browser window will open asking you to sign into GitHub. Use your school Google account. Come back to VS Code when it's done.

A dropdown shows your repos. Select the one you want to work on. When VS Code asks where to save it, Desktop is fine. Click **Open** when prompted.

---

## Install Flask and run your app

In VS Code, open **Terminal → New Terminal**.

Type this and press Enter:

```
pip install -r requirements.txt
```

When it finishes, type:

```
flask run --debug
```

You should see output ending in something like `Running on http://127.0.0.1:5000`. Open Chrome and go to **localhost:5000**.

The `--debug` flag means Flask watches your files. Every time you save `app.py`, the server restarts automatically — you can see it happen in the terminal. You do not need to stop and restart it yourself while you work.

If you close the terminal or restart VS Code, run `flask run --debug` again to start the server. Your code is always saved separately.

---

## Saving your work

The **Source Control** panel works the same way it did in Codespaces:

1. Write a short message describing your change
2. Click **Commit**
3. Click **Sync Changes**

Your work is saved to GitHub.
