<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Generic Firewall Integration

Configure any firewall supporting URL-based IP lists to use Vesper Secure's External Dynamic List for automated ACL updates.

</div>

---

## Overview

This guide covers the general approach for integrating Vesper Secure with any firewall that supports fetching IP addresses from an HTTP/HTTPS URL. While specific menu locations and terminology vary by vendor, the core concepts remain the same.

### Supported Firewall Types

This generic approach works with many firewall platforms including:

- **Check Point** (R80.20+)
- **pfSense** (2.4+)
- **OPNsense** (19.1+)
- **Sophos XG Firewall**
- **WatchGuard**
- **SonicWall**
- **Juniper SRX**
- **Zyxel**
- Other firewalls with URL-based IP list support

!!! tip "Looking for Specific Guides?"
    If you're using Palo Alto, Fortinet, or Cisco, we have dedicated guides:
    
    - [Palo Alto Networks Guide](palo-alto.md)
    - [Fortinet FortiGate Guide](fortinet.md)
    - [Cisco ASA/FTD Guide](cisco.md)

---

## Before You Begin

Ensure you have:

- [ ] EDL URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Firewall admin access
- [ ] Outbound HTTPS (port 443) allowed from firewall to `edl.vespersecure.com`
- [ ] Understanding of your firewall's policy/rule structure
- [ ] Knowledge of your VPN gateway configuration

---

## General Integration Steps

### Step 1: Understand Your Firewall's Capabilities

First, determine what your firewall calls this feature. Common names include:

- External Dynamic Lists
- URL Tables
- External Address Lists
- Dynamic Address Objects
- Threat Intelligence Feeds
- IP Reputation Lists
- External IP Sources
- URL-based Aliases

Check your firewall documentation for:
- Support for HTTP/HTTPS URL sources
- Authentication methods (Basic Auth required)
- Refresh/update interval options
- How to reference the list in policies/rules

---

## Step 2: Add Vesper EDL as URL Source

### Configuration Details

You'll need to provide these settings to your firewall:

```
Name/Alias: Vesper-Secure-Allowed-IPs (or similar)
URL: https://edl.vespersecure.com/lists/your-id
Authentication Method: Basic Authentication / HTTP Basic Auth
Username: org_xxxxx
Password: ••••••••
Update Interval: 5-60 minutes (15 minutes recommended)
Format: Plain text / IP List / One IP per line
```

### Common Configuration Locations

**Check Point:**
- SmartConsole → Manage & Settings → Blades → Threat Prevention
- Create new Threat Intelligence feed
- Use "Generic Format" with URL

**pfSense:**
- Firewall → Aliases → IP → URLs
- Add URL to "URL(s)" field
- Set update frequency

**OPNsense:**
- Firewall → Aliases → Add
- Type: URL Table (IPs)
- Set URL and refresh frequency

**Sophos XG:**
- Hosts and Services → IP Host → Add
- Select "List of IP Host" → "Import from URL"

---

## Step 3: Create Address Object/Alias

After adding the URL source, create an address object or alias that references it:

1. Create a new address object/alias
2. Select type: "Dynamic" or "URL-based" or "External"
3. Reference the URL source you created
4. Name it descriptively (e.g., `Vesper-Allowed-IPs`)

Some firewalls combine steps 2 and 3 into a single configuration.

---

## Step 4: Create Firewall Rule/Policy

Create a rule or policy that uses the address object:

### Example Rule Structure

```
Name: Allow Vesper Verified Users
Source Zone: External/WAN/Untrust
Source Address: Vesper-Allowed-IPs (the object you created)
Destination Zone: VPN/Internal
Destination Address: VPN-Gateway-IP (or Any)
Service/Port: HTTPS (443), IPSEC (500, 4500), SSL VPN ports
Action: Allow/Accept/Permit
Logging: Enable
```

### Important Considerations

!!! warning "Rule Order is Critical"
    Place this allow rule **ABOVE** any deny/block rules that would otherwise block the traffic.

!!! info "Enable Logging"
    Enable logging on both allow and deny rules for troubleshooting and audit purposes.

---

## Step 5: Test and Validate

### Test URL Connectivity

Most firewalls provide a way to test external connections. Test that your firewall can reach the EDL:

**Manual Test from Another System:**
```bash
curl -u "org_xxxxx:your-password" https://edl.vespersecure.com/lists/your-id
```

This should return:
- List of IP addresses (if users have registered)
- Empty response (if no users registered yet)
- Authentication error if credentials wrong

### Verify Update Status

Check your firewall's interface for:
- Last update time
- Update status (success/failure)
- Number of IPs currently in the list
- Next scheduled update time

### Test with User Registration

1. Have a test user register through Vesper portal
2. Wait for firewall's update interval
3. Force manual update if supported
4. Verify user's IP appears in the list
5. Test VPN connection from user's location

---

## Common Configurations by Platform

### Check Point

```
1. SmartConsole → Objects → Network Objects → New → Network Group
2. Name: Vesper-Allowed-IPs
3. Add Dynamic Object → Updatable Object
4. Configure URL and authentication
5. Install policy
6. Use in access rules
```

### pfSense

```
1. Firewall → Aliases → IP
2. Click "+" to add new alias
3. Name: Vesper_Allowed_IPs
4. Type: URL (IPs)
5. URL: https://edl.vespersecure.com/lists/your-id
6. Update Frequency: 1 Day (adjust as needed)
7. Note: Add username:password in URL or use custom script
8. Use alias in firewall rules
```

!!! warning "pfSense Authentication"
    pfSense may require embedding credentials in URL: `https://username:password@edl.vespersecure.com/lists/your-id`
    
    Alternatively, use a shell script with curl for better security.

### OPNsense

```
1. Firewall → Aliases
2. Click "+" to add alias
3. Name: Vesper_Allowed_IPs
4. Type: URL Table (IPs)
5. URL: https://edl.vespersecure.com/lists/your-id
6. Authentication: Configure in URL or use plugin
7. Refresh Frequency: 1 day (adjust as needed)
8. Use alias in firewall rules
```

### Sophos XG

```
1. Hosts and Services → IP Host → Add
2. Type: IP List
3. Select "Import from URL"
4. URL: https://edl.vespersecure.com/lists/your-id
5. Configure authentication (may need custom script)
6. Set refresh schedule
7. Use in firewall rules
```

---

## Troubleshooting

### URL Fetch Fails

**Symptoms:**
- Firewall shows update failed
- Error accessing URL
- Empty list when IPs should exist

**Solutions:**

1. **Test DNS Resolution:**
   - Verify firewall can resolve `edl.vespersecure.com`
   - Check DNS settings

2. **Test Connectivity:**
   - Verify outbound HTTPS (port 443) allowed
   - Check firewall can reach internet
   - Test from firewall CLI if available

3. **Verify Authentication:**
   - Double-check username and password
   - Test manually with curl
   - Ensure no special characters causing issues

4. **Check Certificate Validation:**
   - Verify firewall trusts SSL certificates
   - Check if SSL inspection interfering
   - Ensure certificate store is updated

5. **Review Logs:**
   - Check system logs for error messages
   - Look for HTTP error codes
   - Check for timeout errors

### Authentication Issues

**Symptoms:**
- 401 Unauthorized errors
- Authentication failed in logs

**Solutions:**

1. **Verify Credentials:**
   - Check username starts with `org_`
   - Verify password is correct
   - Ensure no spaces or hidden characters

2. **Test Manually:**
   ```bash
   curl -v -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id
   ```

3. **Check Authentication Method:**
   - Ensure using "Basic Authentication"
   - Verify firewall supports HTTP Basic Auth
   - Check if credentials need to be URL-encoded

### Traffic Not Allowed

**Symptoms:**
- User registered but still blocked
- Policy not matching traffic

**Solutions:**

1. **Verify Rule Order:**
   - Ensure allow rule is ABOVE deny rules
   - Check rule hit counts
   - Verify rule is enabled

2. **Check IP is in List:**
   - View current IPs in address object
   - Confirm user's IP matches
   - Force update if needed

3. **Review Rule Configuration:**
   - Verify source address matches
   - Check zones/interfaces correct
   - Confirm ports/services match

4. **Check Logs:**
   - Review denied traffic logs
   - Identify which rule is blocking
   - Verify logging is enabled

---

## Firewall-Specific Considerations

### Update Intervals

Different firewalls have different capabilities:

- **Some firewalls**: 5-minute intervals supported
- **Others**: Minimum 1 hour or daily
- **Recommendation**: Use shortest interval your firewall supports

If your firewall only supports daily updates:
- Consider shorter temporary access durations
- Plan for delayed access grants
- Maintain backup static IP list for urgent access

### Authentication Methods

If your firewall doesn't support Basic Auth directly:

1. **Embed credentials in URL** (less secure):
   ```
   https://username:password@edl.vespersecure.com/lists/your-id
   ```

2. **Use custom script**:
   - Create script to fetch with curl
   - Save to local file
   - Point firewall at local file
   - Schedule script to run periodically

3. **Use intermediate proxy**:
   - Set up nginx or similar to handle auth
   - Firewall fetches from local proxy

### Format Considerations

Vesper EDL returns plain text, one IP per line:
```
192.0.2.1
198.51.100.5
203.0.113.42
```

Some firewalls expect specific formats. If needed, create a script to:
- Fetch the EDL
- Reformat for your firewall
- Save locally for firewall to consume

---

## Best Practices

!!! success "Recommendations"
    - Use shortest update interval your firewall supports
    - Enable logging on allow and deny rules
    - Test with pilot users before full rollout
    - Document your configuration
    - Keep backup static IP list for critical admins

!!! info "Monitoring"
    - Check update status regularly
    - Monitor for authentication failures
    - Review allowed traffic logs
    - Track denied access attempts

!!! tip "Security"
    - Protect stored credentials
    - Use strong EDL password
    - Review rule order regularly
    - Audit access logs

!!! warning "Important"
    - Always place allow rules above deny rules
    - Test thoroughly before production
    - Have rollback plan
    - Document firewall-specific quirks

---

## Need More Help?

### Vendor-Specific Assistance

1. **Check your firewall's documentation** for:
   - URL-based IP lists
   - External threat feeds
   - Dynamic address objects

2. **Search for**:
   - "[Firewall Model] URL IP list"
   - "[Firewall Model] external IP feed"
   - "[Firewall Model] threat intelligence feed"

3. **Contact vendor support** for:
   - Confirmation of feature support
   - Specific configuration steps
   - Best practices for your platform

### Contact Vesper Support

If you need assistance integrating with your specific firewall:

- Email: support@vespersecure.com
- Include: Firewall make/model, version, configuration attempted
- We may be able to provide specific guidance

---

## Next Steps

After firewall integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
