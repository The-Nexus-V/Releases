# 🛠️ Nexus-V Troubleshooting Guide

**Solve Any Issue Quickly and Get Back to Acing Interviews**

> **Common Problems, Quick Solutions**
> 
> This guide covers every possible issue you might encounter with Nexus-V and provides step-by-step solutions.

---

## 🚨 Security Warnings & External App Detection

### 🍎 macOS Security Issues

#### "Nexus-V cannot be opened because the developer cannot be verified"

**Why this happens:**
- macOS Gatekeeper protects against unsigned apps
- Nexus-V is signed but may trigger on first download
- Safari quarantine attributes can cause issues

**Solution 1: System Preferences (Easiest)**
1. Try to open Nexus-V (double-click)
2. When blocked, go to **System Preferences** → **Security & Privacy**
3. Click **"Open Anyway"** next to Nexus-V message
4. Enter your administrator password
5. App will open normally

**Solution 2: Right-Click Override**
1. **Right-click** on Nexus-V.app
2. Select **"Open"** from context menu
3. Click **"Open"** in warning dialog
4. App will open and be trusted permanently

**Solution 3: Terminal Command**
```bash
# Remove quarantine attribute (most effective)
sudo xattr -cr /Applications/Nexus-V.app

# Alternative - specific quarantine removal
sudo xattr -d com.apple.quarantine /Applications/Nexus-V.app

# If still blocked, temporarily allow
sudo spctl --add /Applications/Nexus-V.app
```

**Solution 4: Gatekeeper Temporary Disable**
```bash
# Disable Gatekeeper (use with caution)
sudo spctl --master-disable

# Open Nexus-V, then re-enable
sudo spctl --master-enable
```

#### "Nexus-V is damaged and can't be opened"

**Causes:**
- Incomplete download
- Antivirus interference
- Corrupted DMG file

**Solutions:**
1. **Re-download** from official source
2. **Verify download** integrity:
   ```bash
   # Check file size and signature
   ls -la Nexus-V*.dmg
   codesign -v /Applications/Nexus-V.app
   ```
3. **Clear all attributes**:
   ```bash
   sudo xattr -cr ~/Downloads/Nexus-V*.dmg
   sudo xattr -cr /Applications/Nexus-V.app
   ```

#### Screen Recording Permission Denied

**Symptoms:**
- Instant capture not working
- Black screenshots
- Permission error messages

**Solution:**
1. **Open System Preferences** → **Security & Privacy** → **Privacy**
2. **Select "Screen Recording"** from left sidebar
3. **Unlock settings** (click lock icon, enter password)
4. **Check the box** next to Nexus-V
5. **Restart Nexus-V** for changes to take effect

**If Nexus-V not in list:**
```bash
# Add manually via command line
sudo sqlite3 /Library/Application\ Support/com.apple.TCC/TCC.db "INSERT or REPLACE INTO access VALUES('kTCCServiceScreenCapture','nexus.v.id',0,1,1,NULL,NULL,NULL,'UNUSED',NULL,0,1541440109);"
```

#### Accessibility Permission Required

**For global shortcuts and automation:**
1. **System Preferences** → **Security & Privacy** → **Privacy**
2. **Select "Accessibility"** from left sidebar
3. **Click lock** and enter password
4. **Click "+"** button
5. **Navigate to Applications** → select Nexus-V
6. **Ensure checkbox is checked**

---

### 🪟 Windows Security Issues

#### "Windows protected your PC" (SmartScreen)

**Why this happens:**
- Windows SmartScreen blocks unrecognized apps
- New publisher/signature not yet established
- Normal for new software releases

**Solution:**
1. **Click "More info"** in the SmartScreen dialog
2. **Click "Run anyway"** button that appears
3. App will start normally and be trusted

**Alternative - SmartScreen Settings:**
1. **Windows Settings** → **Update & Security** → **Windows Security**
2. **App & browser control** → **Reputation-based protection settings**
3. **Temporarily turn off** "Check apps and files"
4. **Install Nexus-V**, then **turn protection back on**

#### Antivirus False Positive

**Common with:** Windows Defender, Avast, AVG, Norton, McAfee

**Why this happens:**
- Screen capture capabilities trigger detection
- Electron apps often flagged
- Global shortcut registration seems suspicious

**Solutions:**

**Windows Defender:**
1. **Windows Security** → **Virus & threat protection**
2. **Manage settings** under "Virus & threat protection settings"
3. **Add exclusions** → **Exclude folder**
4. **Add Nexus-V installation folder**

**Third-party Antivirus:**
1. **Open your antivirus software**
2. **Find "Exceptions" or "Whitelist" settings**
3. **Add Nexus-V.exe** to trusted applications
4. **Add installation folder** to excluded directories

**PowerShell Unblock:**
```powershell
# Run PowerShell as Administrator
Unblock-File -Path "C:\Users\YourName\Downloads\Nexus-V Setup 1.0.0.exe"

# After installation
Unblock-File -Path "C:\Program Files\Nexus-V\Nexus-V.exe"
```

#### "This app can't run on your PC"

**Causes:**
- 32-bit system (Nexus-V requires 64-bit)
- Outdated Windows version
- Missing dependencies

**Solutions:**
1. **Check system architecture:**
   ```cmd
   systeminfo | findstr /c:"System Type"
   ```
2. **Update Windows** to latest version
3. **Install Visual C++ Redistributables:**
   - Download from Microsoft official site
   - Install both x64 and x86 versions

#### Digital Signature Issues

**Error:** "The digital signature of this file cannot be verified"

**Solutions:**
1. **Check file integrity** - re-download if corrupted
2. **Update Windows** to latest version
3. **Manually trust certificate:**
   ```cmd
   # Run as Administrator
   certlm.msc
   # Import Nexus-V certificate to Trusted Publishers
   ```

---

### 🐧 Linux Issues

#### Permission Denied Errors

**AppImage won't execute:**
```bash
# Make executable
chmod +x Nexus-V-1.0.0.AppImage

# If still fails, check attributes
ls -la Nexus-V-1.0.0.AppImage

# Remove extended attributes if present
sudo setfattr -x user.* Nexus-V-1.0.0.AppImage
```

**Global shortcuts not working:**
```bash
# Check if running with sufficient privileges
sudo ./Nexus-V-1.0.0.AppImage

# For permanent solution, add to sudoers
sudo visudo
# Add: yourusername ALL=(ALL) NOPASSWD: /path/to/Nexus-V
```

#### Missing Dependencies

**Ubuntu/Debian:**
```bash
# Install all required libraries
sudo apt update
sudo apt install -y \
  libgtk-3-0 \
  libnotify4 \
  libnss3 \
  libxss1 \
  libxtst6 \
  xdg-utils \
  libatspi2.0-0 \
  libdrm2 \
  libxcomposite1 \
  libxdamage1 \
  libxrandr2 \
  libgbm1 \
  libxkbcommon0 \
  libasound2
```

**Fedora/CentOS:**
```bash
# Install dependencies
sudo dnf install -y \
  gtk3 \
  libnotify \
  nss \
  libXScrnSaver \
  libXtst \
  xdg-utils \
  at-spi2-atk \
  libdrm \
  libXcomposite \
  libXdamage \
  libXrandr \
  mesa-libgbm \
  libxkbcommon \
  alsa-lib
```

#### AppImage FUSE Issues

**Error:** "AppImages require FUSE to run"

**Solutions:**
```bash
# Install FUSE
sudo apt install fuse  # Ubuntu/Debian
sudo dnf install fuse  # Fedora

# Extract and run manually if FUSE unavailable
./Nexus-V-1.0.0.AppImage --appimage-extract
./squashfs-root/AppRun
```

---

## 🔧 Application Issues

### Global Shortcuts Not Working

#### Conflict Detection
**Windows:**
```powershell
# Check for shortcut conflicts
Get-WmiObject -Class Win32_Process | Where-Object {$_.ProcessName -like "*hotkey*"}
```

**macOS:**
```bash
# Check system shortcuts
defaults read com.apple.symbolichotkeys.plist
```

**Common Conflicts:**
- **Ctrl+C**: Clipboard managers, IDEs
- **Ctrl+S**: Browser save, applications
- **Ctrl+Enter**: Chat applications, IDEs

**Solutions:**
1. **Change conflicting shortcuts** in other applications
2. **Customize Nexus-V shortcuts** in Settings
3. **Use alternative key combinations**

#### Elevation Required

**Windows - Run as Administrator:**
1. **Right-click** Nexus-V icon
2. **Select "Run as administrator"**
3. **Click "Yes"** in UAC prompt

**Linux - Elevated Permissions:**
```bash
# Run with sudo for global shortcuts
sudo ./Nexus-V-1.0.0.AppImage

# Or add to sudoers for permanent solution
echo "$(whoami) ALL=(ALL) NOPASSWD: $(which nexus-v)" | sudo tee -a /etc/sudoers
```

### Screen Capture Issues

#### Black/Empty Screenshots

**Causes:**
- Missing screen recording permissions
- GPU hardware acceleration conflicts
- Multi-monitor setup issues

**Solutions:**

**macOS:**
1. **Grant screen recording permission** (see above)
2. **Disable hardware acceleration:**
   - Open Nexus-V settings
   - Advanced → Disable GPU acceleration
3. **Test with single monitor**

**Windows:**
```bash
# Disable hardware acceleration in registry
reg add "HKCU\Software\Nexus-V" /v "disable-gpu" /t REG_DWORD /d 1

# Or run with flag
Nexus-V.exe --disable-gpu
```

**Linux:**
```bash
# Run with software rendering
./Nexus-V-1.0.0.AppImage --disable-gpu --use-gl=swiftshader
```

#### Multi-Monitor Issues

**Symptoms:**
- Wrong monitor captured
- Partial screen capture
- Resolution scaling problems

**Solutions:**
1. **Primary monitor only:**
   - Settings → Screen Capture → Primary Monitor Only
2. **Specify monitor:**
   - Settings → Screen Capture → Select Monitor
3. **DPI scaling fix:**
   - Windows: Right-click app → Properties → Compatibility → Override DPI
   - macOS: System Preferences → Displays → Scale

### AI Provider Issues

#### API Key Problems

**Invalid API Key Error:**
1. **Verify key format:**
   - OpenAI: `sk-...` (51 characters)
   - Google: `AI...` (39 characters)
   - Anthropic: `sk-ant-...`
2. **Check key validity** on provider website
3. **Regenerate key** if expired
4. **Check billing/quota** status

#### Connection Timeouts

**Network Issues:**
```bash
# Test connectivity
ping openai.com
ping generativelanguage.googleapis.com
ping api.anthropic.com

# Check DNS resolution
nslookup openai.com
```

**Firewall/Proxy Issues:**
1. **Add Nexus-V to firewall exceptions**
2. **Configure proxy settings** in Nexus-V
3. **Check corporate network restrictions**

#### Rate Limiting

**Error:** "Too many requests" or 429 errors

**Solutions:**
1. **Check API quota** on provider dashboard
2. **Upgrade plan** if needed
3. **Switch to different provider** temporarily
4. **Reduce request frequency** in settings

### Performance Issues

#### High Memory Usage

**Causes:**
- Multiple AI models loaded
- Large screenshot queue
- Memory leaks

**Solutions:**
1. **Clear screenshot queue** regularly
2. **Restart application** daily
3. **Reduce concurrent processing**
4. **Close other memory-intensive apps**

#### Slow Response Times

**Optimization:**
1. **Choose faster AI models:**
   - OpenAI: GPT-3.5-turbo over GPT-4
   - Google: Gemini-Pro over Gemini-Ultra
2. **Reduce response length** settings
3. **Use wired internet** connection
4. **Close bandwidth-heavy applications**

---

## 🆘 Emergency Solutions

### Complete Reset

**If nothing works, nuclear option:**

**Windows:**
```cmd
# Stop Nexus-V process
taskkill /f /im Nexus-V.exe

# Clear all settings
rmdir /s "%APPDATA%\Nexus-V"

# Reinstall application
```

**macOS:**
```bash
# Force quit
sudo killall Nexus-V

# Clear all data
rm -rf ~/Library/Application\ Support/Nexus-V/
rm -rf ~/Library/Preferences/nexus.v.id.plist

# Remove and reinstall
sudo rm -rf /Applications/Nexus-V.app
```

**Linux:**
```bash
# Kill process
sudo killall nexus-v

# Clear config
rm -rf ~/.config/Nexus-V/
rm -rf ~/.local/share/Nexus-V/

# Fresh AppImage
rm Nexus-V-*.AppImage
# Re-download from official source
```

### Safe Mode

**Start with minimal features:**

**Command Line Flags:**
```bash
# Windows
Nexus-V.exe --safe-mode --disable-gpu --no-sandbox

# macOS
/Applications/Nexus-V.app/Contents/MacOS/Nexus-V --safe-mode

# Linux
./Nexus-V-1.0.0.AppImage --safe-mode --disable-features=gpu
```

**Safe Mode Features:**
- ✅ Basic UI only
- ✅ No global shortcuts
- ✅ Software rendering
- ✅ Minimal permissions
- ❌ Screen capture disabled
- ❌ AI features disabled

---

## 📞 Getting Additional Help

### Before Contacting Support

**Information to Gather:**
1. **Operating System** (exact version)
2. **Nexus-V version** (Help → About)
3. **Error messages** (exact text)
4. **Steps to reproduce**
5. **Screenshots** of errors (if safe)
6. **Log files** (if available)

### Log File Locations

**Windows:**
```
%APPDATA%\Nexus-V\logs\main.log
%APPDATA%\Nexus-V\logs\renderer.log
```

**macOS:**
```
~/Library/Logs/Nexus-V/main.log
~/Library/Logs/Nexus-V/renderer.log
```

**Linux:**
```
~/.config/Nexus-V/logs/main.log
~/.config/Nexus-V/logs/renderer.log
```

### Support Channels

**Email Support:** app.nexus.v@gmail.com

**Include in Support Request:**
- Operating system and version
- Nexus-V version
- Detailed problem description
- Steps to reproduce
- Error messages
- Log files (if requested)

**Response Times:**
- **Critical Issues**: 24-48 hours
- **General Support**: 2-5 business days
- **Feature Requests**: Weekly review

### Community Resources

**GitHub Issues:** Report bugs and track fixes
**GitHub Discussions:** Community help and tips
**Documentation:** Complete guides and tutorials

---

## ✅ Prevention Tips

### Regular Maintenance

**Weekly:**
1. **Restart Nexus-V** to clear memory
2. **Check for updates**
3. **Clear screenshot queue**
4. **Verify AI provider quota**

**Monthly:**
1. **Update operating system**
2. **Review security settings**
3. **Clean temporary files**
4. **Backup settings** (if customized)

### Best Practices

**Security:**
1. **Keep antivirus updated** with Nexus-V exceptions
2. **Regular OS security updates**
3. **Use official download sources only**
4. **Verify file signatures** before installation

**Performance:**
1. **Close unnecessary applications** during use
2. **Stable internet connection** for AI features
3. **Adequate RAM** (8GB+ recommended)
4. **Regular system maintenance**

---

## 🎯 Quick Reference

### Most Common Issues (90% of problems)

| Issue | Quick Fix |
|-------|-----------|
| **macOS "Can't open"** | Right-click → Open |
| **Windows SmartScreen** | Click "More info" → "Run anyway" |
| **Shortcuts not working** | Run as Administrator/sudo |
| **Black screenshots** | Grant screen recording permission |
| **AI not responding** | Check API key and internet |
| **High memory usage** | Restart application |

### Emergency Shortcuts

| Situation | Solution |
|-----------|----------|
| **App frozen** | Ctrl+Alt+Del (Win) / Cmd+Opt+Esc (Mac) |
| **Can't see app** | Ctrl/Cmd+B (toggle visibility) |
| **Need to quit fast** | Ctrl/Cmd+Q |
| **Reset position** | Ctrl/Cmd+R |

---

**Remember:** Most issues are related to permissions or security settings. Always try the simplest solutions first!

🔧 **You've got this!** Most problems have quick fixes, and the support team is here to help with anything complex.

---

*The-Nexus-V and team Support Team - We're here to ensure your success* 🚀
