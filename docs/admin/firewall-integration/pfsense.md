<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# pfSense Integration

Configure pfSense firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

pfSense (2.4+) provides native URL Table Aliases that can fetch IP addresses from HTTP/HTTPS URLs. Additionally, the pfBlockerNG package offers enhanced Dynamic IP List-like functionality. This guide covers both approaches.

### What You'll Configure

1. URL Table Alias pointing to Vesper Dynamic IP List  
2. Firewall rule to allow registered IPs  
3. Optional pfBlockerNG integration for advanced features  
4. Testing and validation  

### Time Required

**15-20 minutes** for basic configuration, **25-30 minutes** with pfBlockerNG.

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] pfSense 2.4 or later
- [ ] pfSense web interface admin access
- [ ] Outbound HTTPS (port 443) allowed from pfSense to `edl.vespersecure.com`
- [ ] Knowledge of your VPN configuration

---

## Method 1: Native URL Table Aliases (Recommended)

This method uses pfSense's built-in URL Table functionality without additional packages.

### Step 1: Create URL Table Alias

#### Navigate to Aliases

1. Log in to pfSense web interface  
2. Go to **Firewall** → **Aliases** → **IP** tab  
3. Click **Add** (+ icon)  

#### Configure the Alias

Configure the following settings:

```
Name: Vesper_Allowed_IPs
Description: Vesper Secure verified user IPs
Type: URL Table (IPs)
Refresh Frequency: 1 day
URL: https://org_xxxxx:password@edl.vespersecure.com/lists/your-id
```

**Field Details:**

- **Name**: Use a descriptive name without spaces (e.g., `Vesper_Allowed_IPs`)
- **Description**: Add a clear description for documentation
- **Type**: Select **"URL Table (IPs)"** from dropdown
- **Refresh Frequency**: 
  - 1 day (default, updates daily)
  - Note: pfSense URL aliases don't support frequent updates natively
- **URL**: Your Dynamic IP List URL with embedded credentials
  - Format: `https://username:password@edl.vespersecure.com/lists/your-id`
  - Replace `username` with your org ID (e.g., `org_xxxxx`)
  - Replace `password` with your Dynamic IP List password
  - Replace `your-id` with your list ID

!!! warning "Credentials in URL"
    pfSense requires embedding credentials in the URL for authentication. While this is less secure than separate credential storage, the URL is stored in pfSense's encrypted configuration.

#### Save the Alias

1. Click **Save**  
2. Click **Apply Changes** at the top of the page  
3. pfSense will immediately attempt to fetch the URL  

---

### Step 2: Verify Alias Download

#### Check Alias Status

1. Go to **Diagnostics** → **Tables**  
2. Find your alias name (e.g., `Vesper_Allowed_IPs`)  
3. Verify it shows IP addresses in the list  
4. If empty and no users registered yet, this is normal  

!!! tip "Manual Update"
    To force an immediate update, click the **refresh icon** next to the alias in **Firewall** → **Aliases**.

---

### Step 3: Create Firewall Rule

#### Navigate to Firewall Rules

1. Go to **Firewall** → **Rules**  
2. Select the **WAN** interface (or interface where external traffic arrives)  
3. Click **Add** (↑ up arrow) to add rule at top  

!!! warning "Rule Order Matters"
    Add this rule at the **top** of the rule list to ensure it's evaluated before any deny rules.

#### Configure Allow Rule

Create a rule with these settings:

```
Action: Pass
Interface: WAN
Address Family: IPv4
Protocol: TCP/UDP (or Any)
Source: Single host or alias → Vesper_Allowed_IPs
Destination: WAN address (or VPN gateway IP)
Destination Port Range: HTTPS (443), or VPN ports as needed
Description: Allow Vesper Secure verified users to VPN
```

**Field Details:**

- **Action**: Pass
- **Interface**: WAN (or your external interface)
- **Address Family**: IPv4 (or IPv4+IPv6 if needed)
- **Protocol**: 
  - TCP/UDP for specific services
  - Any for all VPN protocols
- **Source**: 
  - Type: Single host or alias
  - Select: `Vesper_Allowed_IPs` from dropdown
- **Destination**: 
  - Type: WAN address (for VPN on WAN IP)
  - Or specify specific IP/alias
- **Destination Port Range**:
  - HTTPS (443) for SSL VPN
  - OpenVPN (1194)
  - IPsec (500, 4500)
  - Or custom ports as needed
- **Description**: Add clear description

#### Advanced Options (Recommended)

1. Click **Display Advanced** button  
2. Under **Extra Options**:  
   - Enable **Log**: Check to log packets matched by this rule
   - Set log description if desired

#### Save the Rule

1. Click **Save**  
2. Click **Apply Changes** at the top  
3. The rule is now active  

---

### Step 4: Create Block Rule (Optional)

For explicit logging of denied access:

1. Add another rule **below** the allow rule  
2. Configure:  
   ```
   Action: Block
   Interface: WAN
   Protocol: TCP/UDP
   Source: any
   Destination: WAN address (VPN gateway)
   Destination Port: VPN ports
   Description: Block unverified VPN access attempts
   Log: Enabled
   ```

---

## Method 2: pfBlockerNG (Advanced)

pfBlockerNG provides more frequent updates and advanced features.

### Step 1: Install pfBlockerNG

1. Go to **System** → **Package Manager** → **Available Packages**  
2. Search for **pfBlockerNG**  
3. Click **Install**  
4. Wait for installation to complete  

!!! info "pfBlockerNG vs pfBlockerNG-devel"
    Either version works. pfBlockerNG-devel has newer features but may be less stable.

---

### Step 2: Configure pfBlockerNG for Vesper

#### Navigate to pfBlockerNG

1. Go to **Firewall** → **pfBlockerNG** → **IP** → **IP Configuration**  
2. Click **Add** to create new IP list  

#### Configure IP List

```
List Name: Vesper_Secure_EDL
Description: Vesper Secure verified user IPs
List Action: Permit (Whitelist)
Format: Auto
Update Frequency: Every 4 hours (or as desired)
URL: https://org_xxxxx:password@edl.vespersecure.com/lists/your-id
```

**Field Details:**

- **List Name**: `Vesper_Secure_EDL`
- **Description**: Clear description
- **List Action**: **Permit** (or Alias Native for use in rules)
- **Format**: Auto (pfBlockerNG will detect format)
- **Update Frequency**: 
  - Every 4 hours for faster updates
  - Every 8 hours for balanced approach
  - Daily for minimal load
- **URL**: Dynamic IP List URL with embedded credentials (same format as Method 1)

#### Configure List Settings

Under **Header/Label**:
- Enable: Check
- Interface: Select interface to apply (e.g., WAN)

Click **Save**

---

### Step 3: Enable pfBlockerNG

1. Go to **Firewall** → **pfBlockerNG** → **General** tab  
2. Enable pfBlockerNG: Check  
3. Click **Save**  
4. Click **Update** button to force initial download  

---

### Step 4: Create Firewall Rule with pfBlockerNG

If using "Alias Native" mode:

1. Go to **Firewall** → **Rules** → **WAN**  
2. Create rule using the pfBlockerNG alias (similar to Method 1)  
3. Source: Select the alias created by pfBlockerNG  

pfBlockerNG will automatically create rules if using Permit/Deny actions.

---

## Validation and Testing

### Verify Alias Contents

**For Method 1:**

1. Go to **Diagnostics** → **Tables**  
2. Find `Vesper_Allowed_IPs`  
3. Click to view contents  
4. Verify IPs are listed  

**For Method 2 (pfBlockerNG):**

1. Go to **Firewall** → **pfBlockerNG** → **Reports**  
2. Select your list  
3. View downloaded IPs  

### Test URL Fetch Manually

From pfSense CLI (SSH or console):

```bash
curl -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id
```

Or with embedded credentials:

```bash
curl "https://org_xxxxx:password@edl.vespersecure.com/lists/your-id"
```

Should return list of IPs or empty response.

### Test with User Registration

1. Have a test user register their IP through Vesper portal  
2. Wait for pfSense alias update (up to 24 hours for Method 1, faster for Method 2)  
3. Force manual update:  
   - **Method 1**: Click refresh icon next to alias
   - **Method 2**: Click Update in pfBlockerNG
4. Verify the test user's IP appears in the alias/list  
5. Test VPN connection from test user's location (should succeed)  

### Check Firewall Logs

1. Go to **Status** → **System Logs** → **Firewall** tab  
2. Look for entries matching your rule description  
3. Verify connections from registered IPs are being passed  
4. Check that unregistered IPs are being blocked  

---

## Troubleshooting

### Alias Download Fails

**Symptoms:**
- Alias is empty
- Error in system logs

**Solutions:**

1. **Verify Connectivity:**  
   ```bash
   ping edl.vespersecure.com
   curl -I https://edl.vespersecure.com
   ```

2. **Check DNS Resolution:**  
   - Go to **Diagnostics** → **DNS Lookup**
   - Look up `edl.vespersecure.com`
   - Verify it resolves correctly

3. **Test URL with credentials:**  
   ```bash
   curl "https://org_xxxxx:password@edl.vespersecure.com/lists/your-id"
   ```

4. **Check System Logs:**  
   - Go to **Status** → **System Logs** → **System** tab
   - Look for errors related to alias updates

5. **Verify Credentials:**  
   - Ensure username and password are correct
   - Check for special characters that need URL encoding
   - Try re-entering credentials

### Special Characters in Password

If your password contains special characters, URL-encode them:

- `@` → `%40`
- `#` → `%23`
- `$` → `%24`
- `%` → `%25`
- `&` → `%26`

Example: If password is `Pass@123`, use `Pass%40123` in URL.

### Traffic Not Being Allowed

**Symptoms:**
- User registered but still can't connect
- Logs show blocks

**Solutions:**

1. **Verify Rule Order:**  
   - Go to **Firewall** → **Rules** → **WAN**
   - Ensure Vesper allow rule is **above** any deny rules
   - Drag to reorder if needed

2. **Check Alias Contents:**  
   - Go to **Diagnostics** → **Tables**
   - Verify user's IP is in the alias
   - Force alias refresh if needed

3. **Verify Rule Configuration:**  
   - Check source alias is correct
   - Verify destination and ports match VPN configuration
   - Ensure Action is "Pass"

4. **Review Logs:**  
   - Go to **Status** → **System Logs** → **Firewall**
   - Find blocked connection
   - Check which rule is blocking

### Alias Updates Too Slow

**Symptoms:**
- Updates only happen daily
- Need faster refresh

**Solutions:**

1. **Use pfBlockerNG** (Method 2):  
   - Supports updates every 4 hours or more frequently
   - Better for dynamic environments

2. **Create Custom Update Script** (Advanced):  
   ```bash
   #!/bin/sh
   /etc/rc.update_url_aliases
   ```
   - Add to cron for more frequent updates
   - Go to **System** → **Cron**
   - Add new cron entry to run script

3. **Manual Updates**:  
   - Refresh alias manually when needed
   - Educate users to request manual refresh for urgent access

---

## Advanced Configuration

### Automatic Alias Refresh Script

Create a cron job for more frequent updates:

1. Go to **System** → **Cron**  
2. Click **Add**  
3. Configure:  
   ```
   Minute: */30 (every 30 minutes)
   Hour: *
   Day: *
   Month: *
   Weekday: *
   Command: /etc/rc.update_url_aliases
   Description: Update Vesper Dynamic IP List alias
   ```
4. Click **Save**  

!!! warning "System Load"
    Frequent updates may increase system load. Monitor pfSense resource usage.

### High Availability Configuration

If using pfSense HA (CARP):

1. Configure alias and rules on primary node  
2. Configuration will sync to secondary via Config Sync  
3. Both nodes will independently fetch the Dynamic IP List  
4. Ensure both nodes have internet connectivity  

### IPv6 Support

To support IPv6 addresses:

1. Create a second alias with type "URL Table (IPs)"  
2. Use the same URL (if Dynamic IP List returns IPv6 addresses)  
3. Or create separate IPv6 Dynamic IP List if available  
4. Add to firewall rules alongside IPv4 alias  

### Multiple VPN Servers

If you have multiple VPN servers:

1. Use the same alias in multiple rules  
2. Create separate rules for each VPN destination  
3. Maintain consistent rule ordering  

---

## Best Practices

!!! success "Recommended Settings"
    - **Update Frequency**: As frequent as your environment allows
    - **pfBlockerNG**: Use for environments needing updates more than daily
    - **Logging**: Enable on firewall rules for auditing
    - **Rule Order**: Always place allow rules above deny rules

!!! info "Monitoring"
    - Check **Status** → **System Logs** regularly
    - Monitor alias update status
    - Review allowed and denied connections
    - Keep backup static IP list for critical admins

!!! tip "Optimization"
    - Document your configuration
    - Test with pilot users first
    - Schedule manual refreshes during maintenance windows
    - Consider pfBlockerNG for production environments

!!! warning "Important"
    - Protect configuration backups - they contain credentials
    - Use strong Dynamic IP List password
    - Regularly review firewall logs
    - Test changes in non-production first

---

## Next Steps

After pfSense integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### pfSense Documentation

- [pfSense Aliases Documentation](https://docs.netgate.com/pfsense)
- [pfBlockerNG Documentation](https://docs.netgate.com/pfsense)
- [pfSense Firewall Rules Guide](https://docs.netgate.com/pfsense)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
