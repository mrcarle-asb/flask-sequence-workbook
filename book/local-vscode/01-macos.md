---
name: macOS Setup
index: 1
---

# Setting up Flask locally — macOS

You need to install three things. The Git step is the awkward one on Mac — read it carefully.

---

## 1. Install Python

Go to **python.org/downloads** and download the latest Python 3 macOS installer (the `.pkg` file).

Run it and follow the prompts. Default options are fine.

To check it worked, open **Terminal** (search for it in Spotlight with Cmd+Space). Type:

```
python3 --version
```

You should see a version number like `Python 3.13.x`.

---

## 2. Install Git

Mac does not come with Git pre-installed. The way to get it is through Apple's developer tools.

Open Terminal and type:

```
xcode-select --install
```

A dialog box will pop up asking if you want to install the Command Line Developer Tools. Click **Install** (not "Get Xcode" — you do not want the full Xcode app). It will download and install. This takes a few minutes on a good connection.

When it finishes, check it worked:

```
git --version
```

You should see a version number.

:::alert{info}
This download is roughly 1–2 GB. If you are on a slow connection, start it and come back.
:::

---

## 3. Install VS Code

Go to **code.visualstudio.com** and download the macOS version. Open the downloaded `.zip`, drag **Visual Studio Code** into your Applications folder.

Open VS Code. Click the **Extensions** icon on the left sidebar (four squares). Search for **Python** and install the extension by Microsoft.

---

## Connect to GitHub

In VS Code, click the **Source Control** icon on the left sidebar (the branch icon). Click **Clone Repository**, then **Clone from GitHub**.

A browser window will open asking you to sign into GitHub. Use your school Google account. Come back to VS Code when it's done.

A dropdown shows your repos. Select the one you want to work on. When VS Code asks where to save it, Documents or Desktop is fine. Click **Open** when prompted.

---

## Install Flask and run your app

In VS Code, open **Terminal → New Terminal**.

Type this and press Enter:

```
pip3 install -r requirements.txt
```

When it finishes, type:

```
flask run --debug
```

You should see output ending in something like `Running on http://127.0.0.1:5000`. Open Chrome and go to **localhost:5000**.

The `--debug` flag means Flask watches your files. Every time you save `app.py`, the server restarts automatically — you can see it in the terminal. You do not need to stop and restart it yourself while you work.

### If you get a "port already in use" error

Newer versions of macOS use port 5000 for AirPlay. If Flask complains, run it on a different port:

```
flask run --debug --port 5001
```

Then go to **localhost:5001** instead.

If you close the terminal or restart VS Code, run `flask run --debug` again. Your code is always saved separately.

---

## Saving your work

The **Source Control** panel works the same way it did in Codespaces:

1. Write a short message describing your change
2. Click **Commit**
3. Click **Sync Changes**

Your work is saved to GitHub.
