<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# OPNsense Integration

Configure OPNsense firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

OPNsense (19.1+) provides native URL Table Aliases that can fetch IP addresses from HTTP/HTTPS URLs with automatic periodic updates. This guide covers OPNsense 19.1 and later versions.

### What You'll Configure

1. URL Table Alias pointing to Vesper Dynamic IP List  
2. Firewall rule to allow registered IPs  
3. Testing and validation  

### Time Required

**15-20 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] OPNsense 19.1 or later
- [ ] OPNsense web interface admin access
- [ ] Outbound HTTPS (port 443) allowed from OPNsense to `list.vespersecure.com`
- [ ] Knowledge of your VPN configuration

---

## Step 1: Create URL Table Alias

### Navigate to Aliases

1. Log in to OPNsense web interface  
2. Go to **Firewall** → **Aliases**  
3. Click the **+** (Add) button in the lower right  

### Configure the Alias

Configure the following settings:

```
Enabled: ✓ (checked)
Name: Vesper_Allowed_IPs
Type: URL Table (IPs)
Description: Vesper Secure verified user IPs
Content: https://organizationid:password@list.vespersecure.com/organizationid
Refresh Frequency: 1 day
```

**Field Details:**

- **Enabled**: Check the box to enable the alias
- **Name**: Use a descriptive name without spaces (e.g., `Vesper_Allowed_IPs`)
- **Type**: Select **"URL Table (IPs)"** from the dropdown
- **Description**: Add a clear description for documentation
- **Content**: Your Dynamic IP List URL with embedded credentials
  - Format: `https://username:password@list.vespersecure.com/organizationid`
  - Replace `username` with your organization ID
  - Replace `password` with your Dynamic IP List password
- **Refresh Frequency**: 
  - 1 day (default, updates daily at midnight)
  - 0.5 days (12 hours) for more frequent updates
  - 0.25 days (6 hours) for faster access grants

!!! warning "Credentials in URL"
    OPNsense requires embedding credentials in the URL for HTTP Basic Authentication. The configuration is stored securely in OPNsense's encrypted config.

!!! tip "Special Characters in Password"
    If your password contains special characters, URL-encode them:
    - `@` → `%40`
    - `#` → `%23`
    - `$` → `%24`
    - `%` → `%25`
    - `&` → `%26`

### Save the Alias

1. Click **Save** at the bottom  
2. The alias will be created and added to the list  

---

## Step 2: Download Alias Data

### Trigger Initial Download

After creating the alias, trigger the first download:

1. In the **Firewall** → **Aliases** page  
2. Find your alias in the list  
3. Look for the status indicator (may show pending)  
4. Click **Apply** at the top of the page to apply changes  

OPNsense will immediately attempt to download the URL content.

### Verify Download

1. Go to **Firewall** → **Diagnostics** → **Aliases**  
2. Find `Vesper_Allowed_IPs` in the list  
3. Click the **magnifying glass icon** to view contents  
4. Verify IP addresses appear (if any users have registered)  

!!! success "Empty List is OK"
    If no users have registered yet, the list may be empty. This is normal and doesn't indicate an error.

---

## Step 3: Create Firewall Rule

### Navigate to Firewall Rules

1. Go to **Firewall** → **Rules**  
2. Select the **WAN** tab (or interface where external traffic arrives)  
3. Click **Add** button (+ icon with up arrow) to add rule at top  

!!! warning "Rule Order is Critical"
    Add this rule at the **top** of the rule list to ensure it's evaluated before any deny rules.

### Configure Allow Rule

Create a rule with these settings:

```
Action: Pass
Interface: WAN
Direction: in
TCP/IP Version: IPv4
Protocol: TCP/UDP (or Any)
Source: Vesper_Allowed_IPs
Destination: WAN address (or VPN gateway)
Destination port range: HTTPS (443), or VPN ports as needed
Description: Allow Vesper Secure verified users to VPN
```

**Field Details:**

- **Action**: Pass
- **Interface**: WAN (or your external interface)
- **Direction**: in (incoming traffic)
- **TCP/IP Version**: IPv4 (or IPv4+IPv6 if needed)
- **Protocol**: 
  - TCP/UDP for specific services
  - Any for all VPN protocols (IPsec, OpenVPN, etc.)
- **Source**: 
  - Check "Invert" if needed: NO
  - Type: Select **"Vesper_Allowed_IPs"** from the dropdown
- **Destination**: 
  - Type: WAN address (for VPN hosted on WAN IP)
  - Or select specific host/alias
- **Destination port range**:
  - From: HTTPS (443) To: HTTPS (443) for SSL VPN
  - From: OpenVPN (1194) To: OpenVPN (1194) for OpenVPN
  - From: ISAKMP (500) To: ISAKMP (500) for IPsec
  - Or other custom ports as needed
- **Description**: "Allow Vesper Secure verified users to VPN"

### Advanced Options (Recommended)

1. Expand the **Advanced Options** section  
2. Set **Log**: Check to log packets matched by this rule  
3. Leave other settings at default unless you have specific requirements  

### Save the Rule

1. Click **Save** at the bottom  
2. You'll return to the rules list  

### Apply Changes

1. Click **Apply changes** button at the top of the page  
2. The rule is now active and will be evaluated for incoming traffic  

---

## Step 4: Create Block Rule (Optional)

For explicit logging of denied access attempts:

1. Click **Add** to add another rule (this time without the up arrow, so it goes to the bottom)  
2. Configure:  
   ```
   Action: Block
   Interface: WAN
   Direction: in
   TCP/IP Version: IPv4
   Protocol: TCP/UDP
   Source: any
   Destination: WAN address (or VPN gateway)
   Destination port: VPN ports
   Description: Block unverified VPN access attempts
   Log: ✓ (checked)
   ```
3. Click **Save**  
4. Click **Apply changes**  

**Why This Helps:**

- Explicitly logs denied access attempts
- Makes troubleshooting easier
- Provides security monitoring visibility

---

## Validation and Testing

### Verify Alias Contents

1. Go to **Firewall** → **Diagnostics** → **Aliases**  
2. Find `Vesper_Allowed_IPs` in the list  
3. Click the **magnifying glass icon** (view contents)  
4. Verify IP addresses are listed  

### Check Alias Update Status

1. In **Firewall** → **Aliases**  
2. Look for status indicators next to your alias  
3. Green checkmark indicates successful download  
4. Red X indicates download failure  

### Force Manual Update

To manually trigger an update:

1. Go to **Firewall** → **Aliases**  
2. Click the **refresh icon** (circular arrow) next to the alias  
3. Wait a moment for the download to complete  
4. Check the diagnostics to verify new contents  

### Test URL from OPNsense CLI

SSH into OPNsense or use console:

```bash
curl -u "organizationid:password" https://list.vespersecure.com/organizationid
```

Or with embedded credentials:

```bash
curl "https://organizationid:password@list.vespersecure.com/organizationid"
```

Should return list of IPs or empty response if no users registered.

### Test with User Registration

1. Have a test user register their IP through Vesper portal  
2. Wait for OPNsense alias refresh (up to 24 hours for default setting)  
3. Force manual refresh:  
   - Go to **Firewall** → **Aliases**
   - Click refresh icon next to `Vesper_Allowed_IPs`
4. Verify the test user's IP appears in the alias  
5. Test VPN connection from test user's location (should succeed)  

### Check Firewall Logs

1. Go to **Firewall** → **Log Files** → **Live View**  
2. Look for entries matching your rule description  
3. Verify connections from registered IPs show "Pass"  
4. Check that unregistered IPs are being blocked  

---

## Troubleshooting

### Alias Download Fails

**Symptoms:**
- Alias shows error status (red X)
- Contents are empty when they shouldn't be
- Logs show download errors

**Solutions:**

1. **Verify Connectivity:**  
   
   From OPNsense shell:
   ```bash
   ping list.vespersecure.com
   curl -I https://list.vespersecure.com
   ```

2. **Check DNS Resolution:**  
   - Go to **System** → **Diagnostics** → **DNS Lookup**
   - Look up `list.vespersecure.com`
   - Verify it resolves correctly

3. **Test URL with Credentials:**  
   ```bash
   curl -v "https://organizationid:password@list.vespersecure.com/organizationid"
   ```
   Should return 200 OK and IP list

4. **Check System Logs:**  
   - Go to **System** → **Log Files** → **General**
   - Look for errors related to alias updates
   - Check for authentication failures

5. **Verify Credentials:**  
   - Double-check username is correct
   - Ensure password is correct
   - Check for special characters needing URL encoding

### Special Characters in Password

If your password contains special characters, URL-encode them:

| Character | Encoded |
|-----------|---------|
| `@` | `%40` |
| `#` | `%23` |
| `$` | `%24` |
| `%` | `%25` |
| `&` | `%26` |
| `+` | `%2B` |
| `=` | `%3D` |
| `?` | `%3F` |

Example: Password `Pass@123#` becomes `Pass%40123%23`

### Traffic Not Being Allowed

**Symptoms:**
- User registered but VPN connection still fails
- Firewall logs show blocks instead of passes

**Solutions:**

1. **Verify Rule Order:**  
   - Go to **Firewall** → **Rules** → **WAN**
   - Ensure Vesper allow rule has lower sequence number (higher position)
   - Drag and drop to reorder if needed
   - Click **Apply changes** after reordering

2. **Check Alias Contents:**  
   - Go to **Firewall** → **Diagnostics** → **Aliases**
   - View contents of `Vesper_Allowed_IPs`
   - Verify user's IP is in the list
   - Force refresh if needed

3. **Verify User's IP:**  
   - Have user check their current IP
   - Ensure it matches what's registered
   - Dynamic IPs may have changed since registration

4. **Review Rule Configuration:**  
   - Check source alias is set to `Vesper_Allowed_IPs`
   - Verify destination and ports match your VPN
   - Ensure Action is "Pass"
   - Confirm rule is enabled (not grayed out)

5. **Check Logs:**  
   - Go to **Firewall** → **Log Files** → **Live View**
   - Find the blocked connection
   - Check which rule is blocking (rule number/description)
   - Adjust as needed

### Authentication Errors (401)

**Symptoms:**
- Alias shows authentication failure
- 401 errors in logs

**Solutions:**

1. **Verify Credentials:**  
   - Check username in Vesper admin panel
   - Confirm password is correct
   - Test manually with curl

2. **Check for Typos:**  
   - Re-enter the URL in the alias configuration
   - Ensure no extra spaces
   - Verify correct format

3. **Test from CLI:**  
   ```bash
   curl -v -u "organizationid:password" https://list.vespersecure.com/organizationid
   ```
   Should show 200 OK, not 401

4. **Re-generate Credentials:**  
   - If problems persist, generate new credentials in Vesper
   - Update the alias with new credentials

---

## Advanced Configuration

### More Frequent Updates

For faster than daily updates, use cron:

1. Go to **System** → **Settings** → **Cron**  
2. Click **+** to add new cron job  
3. Configure:  
   ```
   Enabled: ✓
   Minutes: 0
   Hours: */4 (every 4 hours)
   Days: *
   Months: *
   Weekdays: *
   Command: Update Alias
   Parameters: Vesper_Allowed_IPs
   Description: Update Vesper Dynamic IP List more frequently
   ```
4. Click **Save**  

This will update the alias every 4 hours instead of daily.

### High Availability Configuration

If using OPNsense HA (CARP):

1. Configure alias and rules on the primary node  
2. Configuration will sync to secondary via XMLRPC Config Sync  
3. Ensure config sync is enabled: **System** → **High Availability** → **Settings**  
4. Both nodes will independently fetch the Dynamic IP List  
5. Ensure both nodes have internet connectivity  

### IPv6 Support

To support IPv6 addresses:

1. Create a second alias with Type "URL Table (IPs)"  
2. Use the same URL (if Dynamic IP List returns IPv6)  
3. Or configure separate IPv6 Dynamic IP List if available  
4. Create firewall rules with TCP/IP Version: IPv6  
5. Use the IPv6 alias in those rules  

### Multiple VPN Servers

If you have multiple VPN servers:

1. Use the same alias in multiple firewall rules  
2. Create separate rules for each destination  
3. Maintain consistent rule ordering across rules  

### Backup Static Access

Maintain a backup method for critical admins:

1. Create a standard alias (not URL-based) with static IPs  
2. Create a network alias: **Firewall** → **Aliases**  
3. Add it as an additional source in your allow rule using "or" logic  
4. Or create a separate rule for static backup IPs  

---

## Best Practices

!!! success "Recommended Settings"
    - **Refresh Frequency**: 0.25-0.5 days for responsive updates
    - **Logging**: Enable on firewall rules for audit trail
    - **Rule Order**: Always place allow rules above deny rules
    - **Testing**: Test with pilot users before full deployment

!!! info "Monitoring"
    - Regularly check **Firewall** → **Log Files** → **Live View**
    - Monitor alias update status in **Firewall** → **Aliases**
    - Review system logs for errors
    - Keep backup static IP list for critical admins

!!! tip "Optimization"
    - Use cron for more frequent updates if needed
    - Document your configuration for future reference
    - Schedule manual refreshes during critical periods
    - Monitor OPNsense system resources

!!! warning "Important"
    - Protect configuration backups - they contain credentials
    - Use strong Dynamic IP List password
    - Regularly review firewall logs for security
    - Test configuration changes in non-production first

---

## Next Steps

After OPNsense integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### OPNsense Documentation

- [OPNsense Aliases Documentation](https://docs.opnsense.org)
- [OPNsense Firewall Rules Guide](https://docs.opnsense.org)
- [OPNsense CLI Reference](https://docs.opnsense.org)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
