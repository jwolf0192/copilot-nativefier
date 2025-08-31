## Packaging Microsoft Copilot as a Native Linux App

This writeup documents how to package [https://copilot.microsoft.com](https://copilot.microsoft.com) as a standalone desktop app using Nativefier. It includes launcher integration and persistent fixes for temp directory and permission issues. Built and validated in a customized Xubuntu environment.

---

### 1. Install Dependencies

```bash
sudo apt update
sudo apt install nodejs npm
npm install -g nativefier
```

> **Note:** Nativefier requires Node.js v14.14+.  
> Run `node -v` to confirm.  
> If you see `fs.rm is not a function`, upgrade Node.js.

---

### 2. Fix Temp Directory Errors

Electron (used by Nativefier) relies on temp directories. In some Linux setups, this causes build failures like:

```
EACCES: permission denied, open '/tmp/electron-packager'
Error: fs.rm is not a function
```

**Solution:** Redirect temp paths.

```bash
mkdir -p ~/nativefier-temp
TMPDIR=~/nativefier-temp TEMP=~/nativefier-temp TMP=~/nativefier-temp \
nativefier --name="Copilot" --out=~/Apps "https://copilot.microsoft.com"
```

---

### 3. Launch and Test the App

```bash
~/Apps/Copilot-linux-x64/Copilot
```

> If the app fails silently, run it from terminal to catch Electron errors.

---

### 4. Create a Launcher

```bash
nano ~/.local/share/applications/copilot.desktop
```

Paste the following:

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=Copilot
Comment=Microsoft Copilot Web App
Exec=/home/jwolf/Apps/Copilot-linux-x64/Copilot
Icon=/home/jwolf/Apps/Copilot-linux-x64/resources/app/icon.png
Terminal=false
Categories=Utility;X-WebApp;
Keywords=AI;Assistant;Copilot;Chat;
```

Make it executable and refresh XFCE:

```bash
chmod +x ~/.local/share/applications/copilot.desktop
update-desktop-database ~/.local/share/applications/
xfce4-panel -r
```

Validate:

```bash
desktop-file-validate ~/.local/share/applications/copilot.desktop
```

> No output = success.

---

### 5. Lessons Learned

- Nativefier assumes default temp paths—override them explicitly.
- Electron errors are often silent—run from terminal to debug.
- Node.js version matters—`fs.rm` requires v14.14+.
- `.desktop` launchers need absolute paths and executable permissions.
- XFCE caches launchers—use `xfce4-panel -r` to refresh.

---

### 6. Optional Enhancements

**Autostart on login:**

```bash
ln -s ~/.local/share/applications/copilot.desktop ~/.config/autostart/copilot.desktop
```

**Global CLI launcher:**

```bash
sudo ln -s ~/Apps/Copilot-linux-x64/Copilot /usr/local/bin/copilot
```

**Move to lab folder:**

```bash
mv ~/Copilot-linux-x64 ~/Apps/Copilot
```

Update `.desktop` paths accordingly.

---

### ✅ Final Checklist

- [x] App launches from terminal  
- [x] `.desktop` file appears in menu  
- [x] Icon displays correctly  
- [x] No permission errors on temp paths  
- [x] Launcher passes `desktop-file-validate`

---

Want me to help you format this into a full GitHub README with headings, badges, or a table of contents? I can also help you write a short project summary or tagline for the repo.
