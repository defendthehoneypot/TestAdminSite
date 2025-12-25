<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Generic Firewall Integration

Configure any firewall supporting URL-based IP lists to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

This guide covers the general approach for integrating Vesper Secure with any firewall that supports fetching IP addresses from an HTTP/HTTPS URL. While specific menu locations and terminology vary by vendor, the core concepts remain the same.

### Supported Firewall Types

This generic approach works with many firewall platforms that have URL-based IP list functionality. If you're using one of the following firewalls with dedicated guides, please use those instead:

- **[Palo Alto Networks](palo-alto.md)** - Use dedicated guide
- **[Fortinet FortiGate](fortinet.md)** - Use dedicated guide
- **[Cisco ASA/FTD](cisco.md)** - Use dedicated guide
- **[Check Point](check-point.md)** - Use dedicated guide
- **[pfSense](pfsense.md)** - Use dedicated guide
- **[OPNsense](opnsense.md)** - Use dedicated guide
- **[Juniper SRX](juniper-srx.md)** - Use dedicated guide

### Firewalls NOT Supported

The following firewalls do **NOT** have the required Dynamic IP List functionality and **cannot** be easily integrated with Vesper Secure:

!!! danger "Unsupported Firewalls"
    **Sophos XG Firewall**
    
    - Limited to vendor threat intelligence feeds only
    - Does not support custom URL-based IP lists
    - Cannot fetch and parse plain text IP lists from external URLs
    
    **WatchGuard**
    
    - No native URL-based dynamic IP list support
    - Limited to static IP configurations
    - Requires manual IP list management
    
    **SonicWall**
    
    - Does not support custom external dynamic lists
    - Limited to vendor-provided security services only
    - No HTTP/HTTPS custom IP list ingestion capability
    
    **Zyxel**
    
    - No comparable Dynamic IP List functionality
    - Lacks URL-based IP list support
    - Static configuration only

### Alternatives for Unsupported Firewalls

If you're using an unsupported firewall, consider these alternatives:

1. **Manual Updates**: Periodically export IPs from Vesper admin panel and manually update firewall rules  
2. **API Scripts**: If your firewall has an API, create a script to fetch the Dynamic IP List and update rules programmatically  
3. **Intermediate Server**: Set up a server to fetch the Dynamic IP List and push updates via your firewall's management interface  
4. **Platform Upgrade**: Consider upgrading to a firewall platform with native Dynamic IP List support  

!!! tip "Looking for Specific Guides?"
    If you're using Palo Alto, Fortinet, Cisco, Check Point, pfSense, OPNsense, or Juniper SRX, we have dedicated guides:
    
    - [Palo Alto Networks Guide](palo-alto.md)
    - [Fortinet FortiGate Guide](fortinet.md)
    - [Cisco ASA/FTD Guide](cisco.md)
    - [Check Point Guide](check-point.md)
    - [pfSense Guide](pfsense.md)
    - [OPNsense Guide](opnsense.md)
    - [Juniper SRX Guide](juniper-srx.md)

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Firewall admin access
- [ ] Outbound HTTPS (port 443) allowed from firewall to `list.vespersecure.com`
- [ ] Understanding of your firewall's policy/rule structure
- [ ] Knowledge of your VPN gateway configuration

---

## General Integration Steps

### Step 1: Understand Your Firewall's Capabilities

First, determine what your firewall calls this feature. Common names include:

- Dynamic IP Lists
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

## Step 2: Add Vesper Dynamic IP List as URL Source

### Configuration Details

You'll need to provide these settings to your firewall:

```
Name/Alias: Vesper-Secure-Allowed-IPs (or similar)
URL: https://list.vespersecure.com/organizationid
Authentication Method: Basic Authentication / HTTP Basic Auth
Username: organizationid
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

Most firewalls provide a way to test external connections. Test that your firewall can reach the Dynamic IP List:

**Manual Test from Another System:**
```bash
curl -u "organizationid:your-password" https://list.vespersecure.com/organizationid
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

### For Other Firewalls with URL Support

If your firewall supports URL-based IP lists but isn't covered in the dedicated guides, follow this general pattern:

**General Configuration Steps:**

```
1. Locate the feature in your firewall's interface  
   - Look for: "External Lists", "URL Tables", "Dynamic Objects"
   - Usually under: Network Objects, Addresses, or Security Settings

2. Create new URL-based object:  
   - Name: Vesper_Allowed_IPs
   - Type: URL Table / External List / IP List
   - URL: https://organizationid:password@list.vespersecure.com/organizationid
   - Update Frequency: As frequent as supported (5-60 minutes)

3. Create firewall rule:  
   - Source: Vesper_Allowed_IPs object
   - Destination: VPN gateway
   - Action: Allow
   - Logging: Enabled
```

!!! warning "Authentication Requirements"
    Most firewalls require HTTP Basic Authentication. You may need to:
    - Embed credentials in URL: `https://username:password@host/path`
    - Configure separate username/password fields
    - Use a script for advanced authentication

---

## Troubleshooting

### URL Fetch Fails

**Symptoms:**
- Firewall shows update failed
- Error accessing URL
- Empty list when IPs should exist

**Solutions:**

1. **Test DNS Resolution:**  
   - Verify firewall can resolve `list.vespersecure.com`
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
   - Check username is correct
   - Verify password is correct
   - Ensure no spaces or hidden characters

2. **Test Manually:**  
   ```bash
   curl -v -u "organizationid:password" https://list.vespersecure.com/organizationid
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
   https://username:password@list.vespersecure.com/organizationid
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

Vesper Dynamic IP List returns plain text, one IP per line:
```
192.0.2.1  
198.51.100.5  
203.0.113.42  
```

Some firewalls expect specific formats. If needed, create a script to:
- Fetch the Dynamic IP List
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
    - Use strong Dynamic IP List password
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
