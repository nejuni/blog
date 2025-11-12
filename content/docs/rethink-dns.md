--- 
title: "rethink-dns"
description: "readme"
date: 2025-10-29T14:10:52Z
lastmod: 2025-10-29T15:27:52Z
tags: ["Dns"]
draft: false
---

# Rethink DNS - Complete Android Guide (2025)

## Table of Contents
- [Overview](#overview)
- [Key Features](#key-features)
- [Installation & Setup](#installation--setup)
- [Essential Daily Commands & Operations](#essential-daily-commands--operations)
- [DNS Configuration](#dns-configuration)
- [Firewall Rules](#firewall-rules)
- [Advanced Features](#advanced-features)
- [Logs & Monitoring](#logs--monitoring)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)

---

## Overview

**Rethink DNS** is a free, open-source DNS resolver and firewall for Android that provides:
- DNS-based content blocking (ads, malware, trackers)
- Per-app firewall capabilities
- On-device DNS server
- Support for DNS-over-HTTPS (DoH) and DNS-over-TLS (DoT)
- Network monitoring and logging

**Official Resources:**
- GitHub: https://github.com/celzero/rethink-app
- Website: https://rethinkdns.com
- Documentation: https://docs.rethinkdns.com

---

## Key Features

### 1. **DNS Blocking**
- 190+ pre-configured blocklists
- Custom blocklist support
- Domain whitelisting/blacklisting

### 2. **Firewall**
- Per-app network control
- Block background data
- IP-based blocking
- Domain-based filtering

### 3. **Privacy**
- On-device processing
- No data collection
- Open-source and auditable

### 4. **Protocols Supported**
- DNS-over-HTTPS (DoH)
- DNS-over-TLS (DoT)
- DNSCrypt
- DNS Proxy
- WireGuard VPN integration

---

## Installation & Setup

### Initial Setup

1. **Install from:**
   - Google Play Store
   - F-Droid
   - GitHub Releases (APK)

2. **First Launch Configuration:**
   ```
   1. Open Rethink DNS
   2. Grant VPN permission (required for DNS interception)
   3. Choose DNS mode:
      - Rethink DNS (recommended for beginners)
      - Custom DNS
      - DNS Proxy
   ```

3. **Enable the Service:**
   - Tap the power button/toggle at the top
   - Allow VPN connection request
   - Verify connection in notification bar

---

## Essential Daily Commands & Operations

### 1. Start/Stop DNS & Firewall

**Start Protection:**
```
Main Screen → Power Button (Top) → Enable
```
- Green indicator = Active
- Gray indicator = Inactive

**Stop Protection:**
```
Main Screen → Power Button → Disable
```

**Quick Toggle:**
- Use notification quick tile
- Add to Quick Settings panel

---

### 2. Check Current Status

**View Active Connections:**
```
Home Screen → Network Activity Section
```
Shows:
- Active queries
- Blocked domains
- Apps making connections

**View Statistics:**
```
Home Screen → Scroll to Stats
```
Displays:
- Total queries
- Blocked queries
- Allowed queries
- Top blocked domains

---

### 3. Block/Unblock Specific Apps

**Block an App from Internet:**
```
Configure → Apps → Select App → Toggle OFF
```

**Block App's Background Data Only:**
```
Configure → Apps → Select App → 3-dot menu → Block when screen off
```

**Allow App Through Firewall:**
```
Configure → Apps → Select App → Toggle ON
```

**Example: Block Facebook's Background Data**
```
1. Configure → Apps
2. Search/Find "Facebook"
3. Tap Facebook → Options menu (⋮)
4. Select "Block when screen off"
5. Result: Facebook works when app is open, blocked when closed
```

---

### 4. DNS Configuration

**Switch DNS Provider:**
```
Configure → DNS → Select Provider
```

**Popular DNS Options:**
- **Rethink DNS** (Default with blocklists)
- **Cloudflare** (1.1.1.1) - Fast & privacy-focused
- **Quad9** - Security & malware blocking
- **Google DNS** (8.8.8.8) - Fast but less privacy
- **AdGuard DNS** - Ad blocking

**Configure Custom DNS:**
```
Configure → DNS → Add Custom DNS
Enter:
- Name: "My Custom DNS"
- URL: https://dns.example.com/dns-query (DoH)
  OR
- IP: 1.1.1.1 (Plain DNS)
- Port: 853 (for DoT)
```

**Example: Setup Cloudflare DoH**
```
1. Configure → DNS
2. Tap "+" (Add)
3. Name: Cloudflare DoH
4. URL: https://cloudflare-dns.com/dns-query
5. Protocol: DNS-over-HTTPS
6. Save
7. Select as active DNS
```

---

### 5. Configure Blocklists

**Enable Pre-configured Blocklists:**
```
Configure → DNS → Configure Blocklists
```

**Popular Blocklist Combinations:**

**Light Blocking (Recommended for Daily Use):**
```
✓ 1Hosts (Lite)
✓ AdGuard DNS Filter
✓ OISD Basic
```

**Medium Blocking:**
```
✓ 1Hosts (Pro)
✓ Hagezi Pro
✓ StevenBlack Unified
✓ OISD Full
```

**Aggressive Blocking:**
```
✓ Hagezi Ultimate
✓ 1Hosts (Xtra)
✓ Energized Ultimate
✓ All Ads & Trackers lists
```

**Apply Changes:**
```
Select desired lists → Tap "Apply" (bottom) → Wait for download
```

---

### 6. Whitelist/Blacklist Domains

**Whitelist a Domain (Allow Permanently):**
```
Configure → DNS → Domain Rules → Whitelist
Tap "+" → Enter domain → Save
```

**Example: Whitelist cdn.example.com**
```
1. Configure → DNS → Domain Rules
2. Tap "Whitelist" tab
3. Tap "+" button
4. Enter: cdn.example.com
5. Save
6. Domain will always be allowed, even if in blocklists
```

**Blacklist a Domain (Block Permanently):**
```
Configure → DNS → Domain Rules → Blacklist
Tap "+" → Enter domain → Save
```

**Example: Block tracker.analytics.com**
```
1. Configure → DNS → Domain Rules
2. Tap "Blacklist" tab
3. Tap "+"
4. Enter: tracker.analytics.com
5. Save
```

---

### 7. Monitor Network Activity

**View Real-time Queries:**
```
Home Screen → Network Activity (Live Feed)
```
Shows:
- Domain being accessed
- App making request
- Status (Allowed/Blocked)
- Timestamp

**Access Query Logs:**
```
Configure → Logs → Network Logs
```

**Filter Logs:**
```
Logs → Filter icon → Select:
- Blocked only
- Allowed only
- Specific app
- Time range
```

**Example: Find What's Draining Battery**
```
1. Configure → Logs
2. Filter by "Last 3 hours"
3. Look for apps with high query count
4. Check if they're making background requests
5. Consider blocking background access
```

---

### 8. Block Connections by IP

**Block Specific IP Address:**
```
Configure → Apps → Universal Rules → IP Rules
Add IP → Block
```

**Block IP Range:**
```
IP Rules → Add Range
Example: 192.168.1.0/24
```

---

### 9. Set Up DNS Proxy

**Enable Local DNS Proxy:**
```
Configure → DNS → DNS Proxy Mode
```

Benefits:
- Use Rethink with other VPNs
- Compatible with enterprise VPNs
- Lower battery usage

**Configuration:**
```
1. Configure → DNS
2. Toggle "DNS Proxy" mode
3. Note the local proxy address (usually 127.0.0.1)
4. Configure system/other apps to use this proxy
```

---

### 10. Per-App DNS Settings

**Set Different DNS for Specific App:**
```
Configure → Apps → Select App → DNS Settings
```

**Example: Use Cloudflare Only for Browser**
```
1. Configure → Apps → Chrome
2. Tap DNS option
3. Select "Cloudflare"
4. Save
5. Chrome now uses separate DNS from other apps
```

---

## Advanced Features

### WireGuard VPN Integration

**Add WireGuard Config:**
```
Configure → Proxy → WireGuard
Import .conf file OR Enter manually
```

**Use Rethink DNS + WireGuard Together:**
```
1. Add WireGuard configuration
2. Enable WireGuard
3. Rethink will route traffic through VPN
4. DNS filtering still active
```

---

### On-Device DNS Server

**Use Rethink as DNS Server for Other Devices:**
```
Configure → DNS → On-Device Server
1. Enable "DNS Server"
2. Note IP address (e.g., 192.168.1.5:5354)
3. Configure other devices to use this DNS
```

**Example: Use Phone as Network-wide DNS**
```
1. Ensure phone has static IP
2. Enable On-Device Server
3. On router, set DNS to phone's IP
4. All network traffic filtered through Rethink
```

---

### Automatic Backups

**Enable Auto-Backup:**
```
Settings → Backup & Restore → Auto-backup
Select frequency (Daily/Weekly)
```

**Manual Backup:**
```
Settings → Backup & Restore → Backup Now
```

**Restore Configuration:**
```
Settings → Backup & Restore → Restore → Select backup file
```

---

## Logs & Monitoring

### Understanding Log Entries

**Log Entry Components:**
```
[Timestamp] [App] [Domain] [Status] [Latency]
```

**Status Indicators:**
- 🟢 **Allowed** - Request permitted
- 🔴 **Blocked** - Request blocked by blocklist
- ⚫ **Filtered** - Blocked by custom rule
- 🟡 **Bypassed** - Whitelisted domain

---

### Export Logs

**Export Network Logs:**
```
Configure → Logs → 3-dot menu → Export
Choose format: CSV / JSON
```

**Use Cases:**
- Analyze network usage patterns
- Identify misbehaving apps
- Debug connectivity issues

---

## Troubleshooting

### Common Issues & Solutions

#### 1. **Internet Not Working After Enabling Rethink**

**Solution:**
```
1. Check DNS is configured correctly
2. Try switching DNS provider (Configure → DNS)
3. Clear DNS cache: Settings → Clear DNS cache
4. Disable all blocklists temporarily
5. Check if specific app needs whitelisting
```

#### 2. **App Not Working / Connection Issues**

**Solution:**
```
1. Check if app is blocked in firewall
   Configure → Apps → Find app → Enable
2. Check logs for blocked domains
   Logs → Filter by app → Look for blocks
3. Whitelist required domains
4. Disable blocklists one-by-one to identify issue
```

**Example: Banking App Not Working**
```
1. Configure → Logs
2. Filter by banking app
3. Look for blocked domains (red entries)
4. Whitelist those domains:
   Configure → DNS → Domain Rules → Whitelist
5. Add each blocked domain
6. Test app again
```

#### 3. **High Battery Drain**

**Solution:**
```
1. Enable Battery Optimization
   Settings → Battery Saver → Enable
2. Reduce log retention
   Settings → Logs → Reduce retention period
3. Use DNS Proxy mode instead of VPN mode
4. Disable real-time monitoring if not needed
```

#### 4. **Cannot Use with Another VPN**

**Solution:**
```
1. Switch to DNS Proxy mode
   Configure → DNS → DNS Proxy
2. Or integrate WireGuard directly in Rethink
   Configure → Proxy → WireGuard → Add config
```

#### 5. **Blocklists Not Updating**

**Solution:**
```
1. Configure → DNS → Blocklists
2. Pull down to refresh
3. Check internet connectivity
4. Clear cache: Settings → Clear cache
5. Re-download lists: Long-press list → Update
```

---

## Best Practices

### Daily Use Recommendations

#### 1. **Optimal Blocklist Setup**
```
For balanced blocking + performance:
✓ OISD Basic
✓ Hagezi Pro
✓ 1Hosts (Pro)

Update frequency: Weekly
```

#### 2. **App-Specific Rules**

**Block Social Media Background Access:**
```
Configure → Apps → Instagram/Facebook/Twitter
Block when screen off
Reduces data usage by 30-50%
```

**Allow Critical Apps:**
```
Whitelist:
- Banking apps
- Authentication apps (2FA)
- Work-related apps
- Video calling apps
```

#### 3. **Battery Optimization**

**Settings for Best Battery Life:**
```
1. Settings → Battery Saver → ON
2. Settings → Logs → Retention: 24 hours
3. Use DNS Proxy if possible
4. Disable real-time network monitor
5. Reduce blocklist count (use only essential lists)
```

#### 4. **Security-Focused Configuration**

```
DNS: Quad9 or NextDNS
Blocklists:
✓ Hagezi Pro
✓ URLHaus Malware
✓ Phishing Army
✓ Dan Pollock's Hosts

Firewall:
- Block unknown apps by default
- Whitelist only trusted apps
- Enable "Block when screen off" for social apps
```

#### 5. **Privacy-Focused Configuration**

```
DNS: Mullvad DNS or Quad9
Blocklists:
✓ All tracker lists
✓ Privacy blocklists
✓ Analytics blocklists

Settings:
- Disable logs or set 1-hour retention
- Use DNS-over-HTTPS
- Enable "Block third-party DNS"
```

---

## Quick Reference Commands

### Most Used Operations

| Action | Path | Quick Tip |
|--------|------|-----------|
| Enable/Disable | Home → Power button | Use Quick Settings tile |
| Block app | Configure → Apps → Toggle | Swipe app left for quick block |
| View logs | Configure → Logs | Filter by app for specific issues |
| Whitelist domain | Configure → DNS → Domain Rules | Long-press log entry to whitelist |
| Change DNS | Configure → DNS → Select | Test different providers for speed |
| Export logs | Logs → Menu → Export | Use for debugging |
| Backup config | Settings → Backup | Do before major changes |

---

## Useful Shortcuts

### Quick Actions from Logs

**From Network Log Entry:**
- **Long-press domain** → Whitelist/Blacklist
- **Tap app name** → Go to app settings
- **Tap domain** → Copy domain name

### Notification Controls

**Quick Actions in Notification:**
- Pause/Resume protection
- View active queries count
- Access app quickly

---

## Performance Tips

### Optimize for Speed

1. **Use Fewer Blocklists:**
   ```
   Fewer lists = Faster DNS resolution
   Recommended: 3-5 well-maintained lists
   ```

2. **Choose Fast DNS:**
   ```
   Test DNS providers:
   Settings → Network Test → DNS Speed Test
   ```

3. **Clear Cache Regularly:**
   ```
   Settings → Clear DNS Cache (weekly)
   ```

4. **Use DNS Proxy Mode:**
   ```
   Lower overhead than full VPN mode
   Better for battery and performance
   ```

---

## Automation & Integration

### Tasker Integration (Advanced)

**Auto-enable Strict Mode at Night:**
```
Tasker Task:
1. Send Intent: com.celzero.bravedns.FIREWALL_MODE
2. Extra: mode=strict
3. Time: 11 PM - 6 AM
```

**Auto-disable for Specific WiFi:**
```
When connected to trusted WiFi:
Send Intent to disable Rethink temporarily
```

---

## Update & Maintenance

### Keep Rethink Updated

**Check for Updates:**
```
Settings → About → Check for updates
```

**Update Blocklists:**
```
Configure → DNS → Blocklists → Pull to refresh
Automatic updates: Weekly (configurable)
```

**Clean Old Logs:**
```
Settings → Logs → Clear Old Logs
Keeps app lightweight
```

---

## Example Workflows

### Workflow 1: Setting Up for First Time

```
1. Install Rethink DNS
2. Launch → Grant VPN permission
3. Select "Rethink DNS" mode
4. Enable blocklists:
   - OISD Basic
   - Hagezi Pro
5. Enable protection (Power button)
6. Test browsing → Verify ads blocked
7. Configure → Apps → Review critical apps → Whitelist if needed
8. Done!
```

### Workflow 2: Troubleshooting App Issues

```
1. User reports: "Banking app not working"
2. Configure → Logs
3. Filter by banking app name
4. Identify blocked domains (red entries)
5. Configure → DNS → Domain Rules → Whitelist
6. Add each blocked domain
7. Clear app cache
8. Test banking app
9. If still issues, temporarily disable blocklists
10. Re-enable lists one-by-one to find culprit
```

### Workflow 3: Battery Optimization

```
1. Settings → Battery Saver → Enable
2. Configure → Apps → Social Media apps
3. For each: Enable "Block when screen off"
4. Settings → Logs → Retention: 6 hours
5. Monitor battery usage over 24 hours
6. Adjust blocklist count if still high drain
```

### Workflow 4: Privacy-First Setup

```
1. Configure → DNS → Select "Mullvad DNS"
2. Configure → DNS → Blocklists → Enable:
   - All tracker lists
   - Analytics lists
   - Hagezi Privacy
3. Settings → Logs → Retention: 1 hour (or disable)
4. Configure → Apps → Block all by default
5. Whitelist only essential apps
6. Settings → Connectivity → Block IPv6 (optional)
7. Enable "Block third-party DNS"
```

---

## Community & Support

### Getting Help

- **GitHub Issues**: Report bugs, request features
- **Telegram**: Join community for quick help
- **Documentation**: https://docs.rethinkdns.com
- **Reddit**: r/rethinkdns (community discussions)

### Contributing

- **Report Issues**: GitHub Issues tracker
- **Translations**: Help translate the app
- **Blocklists**: Suggest new lists
- **Code**: Submit pull requests

---

## Important Notes

⚠️ **VPN Limitation**: Android allows only one VPN at a time. Use DNS Proxy mode if you need another VPN.

⚠️ **Enterprise Networks**: May conflict with corporate VPN. Use DNS Proxy or disable Rethink on work network.

⚠️ **Battery**: VPN mode uses more battery than DNS Proxy mode. Optimize settings for daily use.

⚠️ **Privacy**: Logs stored locally on device. Set retention period based on your needs.

✅ **Open Source**: Fully auditable code, no telemetry, no data collection.

---

## Version Information

**Current Guide Version**: 2025
**Rethink DNS Latest**: v0.5.5+ (Check GitHub for current version)
**Last Updated**: Based on latest available documentation as of 2025

---

## License

This guide is created for educational purposes. Rethink DNS is licensed under Apache License 2.0.

**Official Resources:**
- GitHub: https://github.com/celzero/rethink-app
- Website: https://rethinkdns.com
- Docs: https://docs.rethinkdns.com

---

*End of Guide*
```

---

## How to Use This Guide - Explained

### For Beginners

**Start Here:**
1. Read the "Overview" and "Key Features" sections first
2. Follow the "Installation & Setup" guide step-by-step
3. Learn the "Essential Daily Commands" - focus on commands 1-5 first
4. Use "Workflow 1" as your first-time setup guide

**Example Scenario: Complete Beginner Setup**

```
Day 1 - Initial Setup:
- Install app from Play Store
- Follow "First Launch Configuration"
- Enable with default settings (Rethink DNS mode)
- Browse normally and observe blocked ads

Day 2 - Basic Customization:
- Learn Command #2: Check Current Status
- Review what's being blocked
- Practice Command #3: Block/Unblock specific apps
- Example: Block Instagram when screen off

Day 3 - DNS Exploration:
- Learn Command #4: DNS Configuration
- Try different DNS providers (Cloudflare, Quad9)
- Notice speed differences
- Stick with fastest one

Day 4 - Fine-tuning:
- Learn Command #6: Whitelist/Blacklist
- Fix any broken websites/apps
- Add necessary domains to whitelist

Day 5 - Monitoring:
- Learn Command #7: Monitor Network Activity
- Watch real-time queries
- Understand what apps are doing
```

### For Intermediate Users

**Focus On:**
- Advanced Features section
- Per-app DNS settings
- Custom blocklist configurations
- WireGuard VPN integration
- Log analysis and export

**Example Use Case: Privacy-Conscious User**

```
Goal: Maximum privacy with minimal tracking

Setup Process:
1. Choose privacy-focused DNS (Mullvad/Quad9)
2. Enable aggressive blocklists:
   - All tracker blocklists
   - Analytics lists
   - Hagezi Privacy Plus
3. Set up per-app rules:
   - Block social media background access
   - Allow only essential apps
4. Configure logging:
   - Set 1-hour retention
   - Or disable logs completely
5. Monitor for a week:
   - Check which domains need whitelisting
   - Fine-tune rules
   - Export logs for analysis
```

### For Advanced Users

**Explore:**
- On-device DNS server setup
- WireGuard integration for VPN + DNS
- Tasker automation
- Network-wide deployment
- Custom blocklist creation

**Example Use Case: Network Administrator**

```
Goal: Use Rethink as network-wide DNS filter

Implementation:
1. Set static IP on Android phone
2. Enable "On-Device DNS Server"
3. Configure router to use phone as DNS:
   Primary DNS: 192.168.1.5:5354
4. Enable aggressive blocklists
5. Monitor all network traffic from phone
6. Create custom rules for family needs
7. Set up automatic backups
8. Export logs daily for analysis
```

---

## Practical Examples in Detail

### Example 1: Stop YouTube Ads (Basic)

```
Problem: YouTube shows ads

Solution:
1. Open Rethink DNS
2. Configure → DNS → Blocklists
3. Enable:
   ✓ AdGuard DNS Filter
   ✓ Hagezi Pro
4. Apply changes
5. Close and reopen YouTube
6. Verify ads are reduced

Note: YouTube ads in the app are harder to block.
Browser-based YouTube works better.
```

### Example 2: Fix Online Banking App (Troubleshooting)

```
Problem: Banking app shows "Connection Error"

Step-by-step Fix:
1. Configure → Logs
2. Open banking app (let it fail)
3. Go back to logs
4. Filter by app name "MyBank"
5. Look for red (blocked) entries
6. Common blocked domains:
   - analytics.bankname.com
   - cdn.bankname.com
   - secure.auth.bankname.com
7. For each blocked domain:
   Configure → DNS → Domain Rules → Whitelist → Add
8. Clear banking app cache
9. Test again

Prevention:
Add banking apps to whitelist before issues occur
```

### Example 3: Reduce Mobile Data Usage (Optimization)

```
Goal: Cut data usage by 40%

Strategy:
1. Identify data-hungry apps:
   Configure → Logs → Sort by query count
   
2. For social media apps (Facebook, Instagram, Twitter):
   Configure → Apps → [App Name]
   Enable "Block when screen off"
   
3. For games with ads:
   Enable aggressive ad-blocking lists
   Or block game completely
   
4. For news apps:
   Block tracker domains
   Reduces background prefetching
   
5. Monitor for a week:
   Settings → Statistics
   Compare data usage before/after

Expected Results:
- 30-50% reduction in background data
- Fewer tracking requests
- Longer battery life (bonus!)
```

### Example 4: Child-Safe Internet (Parental Control)

```
Goal: Safe internet for children's device

Configuration:
1. DNS Setup:
   Configure → DNS → Select "CleanBrowsing Family"
   (Blocks adult content automatically)

2. Blocklists:
   ✓ Hagezi Pro
   ✓ AdGuard DNS Filter
   ✓ OISD Full
   ✓ Add "Adult content" lists

3. App Rules:
   Configure → Apps → Block by default
   Whitelist only:
   - Educational apps
   - Approved games
   - Kid-friendly streaming

4. Time-based Rules (requires Tasker):
   - Restrict social media to 1 hour/day
   - Block gaming apps during homework time
   - Allow only educational apps during study hours

5. Monitoring:
   - Check logs daily
   - Review accessed domains
   - Adjust rules as needed

6. Lock Settings:
   Settings → Security → Set PIN
   Prevents child from disabling Rethink
```

### Example 5: Travel/Public WiFi Security (Advanced)

```
Scenario: Using untrusted public WiFi at airport/hotel

Security Setup:
1. Before Connecting:
   Configure → DNS → Select "Quad9" (malware blocking)
   
2. Enable Strict Firewall:
   Configure → Apps → Block all by default
   Whitelist only:
   - Browser
   - Messaging apps
   - Essential travel apps

3. Add WireGuard VPN:
   Configure → Proxy → WireGuard
   Import your VPN config
   Enable VPN + Rethink together

4. Aggressive Blocklists:
   ✓ URLHaus Malware
   ✓ Phishing Army
   ✓ Hagezi Threat Intelligence

5. Monitoring:
   Keep logs visible
   Watch for suspicious domains
   Common threats:
   - Fake login pages
   - Malware delivery domains
   - Tracking pixels

6. After Travel:
   Restore normal configuration
   Or keep travel profile saved for next time
```

---

## Visual Guide: Understanding the Interface

### Home Screen Elements

```
┌─────────────────────────────┐
│  [Power Button] 🟢         │ ← Enable/Disable protection
├─────────────────────────────┤
│  DNS: Rethink DNS          │ ← Current DNS provider
│  Mode: VPN                  │ ← Operating mode
├─────────────────────────────┤
│  Network Activity (Live)    │
│  ├─ Chrome: google.com ✓   │ ← Real-time queries
│  ├─ Instagram: graph.fb ✗  │ ← (✓=allowed, ✗=blocked)
│  └─ WhatsApp: wa.me ✓     │
├─────────────────────────────┤
│  Statistics                 │
│  Queries: 1,234            │
│  Blocked: 456 (37%)        │
│  Allowed: 778 (63%)        │
└─────────────────────────────┘
    ▼ Scroll for more ▼
```

### Configure Menu Structure

```
Configure
├─ DNS
│  ├─ DNS Provider (Select/Add)
│  ├─ Blocklists (Enable/Disable)
│  ├─ Domain Rules (Whitelist/Blacklist)
│  └─ DNS Proxy Mode
├─ Apps
│  ├─ All Apps (Individual controls)
│  ├─ Universal Rules
│  └─ IP Rules
├─ Proxy
│  ├─ WireGuard
│  └─ SOCKS5
└─ Logs
   ├─ Network Logs
   ├─ DNS Logs
   └─ Filters & Export
```

---

## Keyboard Shortcuts (Android)

While Rethink doesn't have traditional keyboard shortcuts, you can use:

- **Quick Settings Tile**: Swipe down → Tap Rethink tile (toggle on/off)
- **Search**: In app, use search bar to quickly find apps or domains
- **Long-press**: Long-press log entries for quick whitelist/blacklist

---

## Conclusion

This guide covers everything you need to use Rethink DNS effectively in 2025. The app is powerful but intuitive once you understand the core concepts:

1. **DNS filtering** blocks unwanted content at the domain level
2. **Firewall rules** control which apps can access the internet
3. **Logs** help you understand and troubleshoot your network
4. **Customization** lets you fine-tune everything to your needs

**Next Steps:**
- Bookmark this guide for reference
- Start with "Workflow 1" if you're new
- Join the community for updates
- Contribute back by sharing your configurations

**Remember:**
- Start simple, add complexity gradually
- Monitor logs to understand what's happening
- Backup your configuration before major changes
- Update blocklists regularly (weekly recommended)

---

**Sources & Citations:**

1. Official Rethink DNS GitHub Repository: https://github.com/celzero/rethink-app
2. Rethink DNS Official Website: https://rethinkdns.com
3. Documentation: https://docs.rethinkdns.com
4. Community discussions and user reports from GitHub Issues
5. Latest release notes and changelogs (v0.5.5+)

*This guide is community-created and maintained. For official documentation, always refer to the links above.*