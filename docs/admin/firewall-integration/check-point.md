<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Check Point Integration

Configure Check Point firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

Check Point firewalls (R80.20+) support Updatable Objects (formerly External Objects) that can pull IP addresses from external HTTP/HTTPS URLs. This guide covers Check Point R80.20 and later versions.

### What You'll Configure

1. Updatable Object pointing to Vesper Dynamic IP List  
2. Network group containing the updatable object  
3. Access rule to allow registered IPs  
4. Testing and validation  

### Time Required

**15-20 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Check Point SmartConsole access
- [ ] Check Point Security Management Server (SMS) R80.20 or later
- [ ] Outbound HTTPS (port 443) allowed from SMS to `edl.vespersecure.com`
- [ ] Knowledge of your VPN gateway configuration

---

## Step 1: Create Updatable Object

### Navigate to Updatable Objects

1. Open Check Point SmartConsole  
2. Go to **Manage & Settings** (gear icon)  
3. Navigate to **Blades** → **Threat Prevention**  
4. Click on **Updatable Objects**  
5. Click **New** → **Updatable Object**  

!!! info "Check Point Versions"
    In older versions (R80.20-R80.30), this may be called "External Objects" instead of "Updatable Objects".

### Configure the Updatable Object

Configure the following settings:

```
Name: Vesper_Secure_Allowed_IPs
Resource: External - IP Address List
URI: https://edl.vespersecure.com/lists/your-id
Update Interval: 900 seconds (15 minutes)
Authentication: HTTP Basic Authentication
Username: org_xxxxx
Password: ••••••••
```

**Field Details:**

- **Name**: Use a descriptive name without spaces (e.g., `Vesper_Secure_Allowed_IPs`)
- **Resource**: Select "External - IP Address List"
- **URI**: Your unique Dynamic IP List URL from Vesper admin panel
- **Update Interval**: 
  - 300 seconds (5 minutes) for fastest access grants
  - 900 seconds (15 minutes) recommended for balanced approach
  - 3600 seconds (1 hour) for reduced load
- **Authentication**: Select "HTTP Basic Authentication"
- **Username**: Your organization ID (starts with `org_`)
- **Password**: Dynamic IP List password from Vesper admin panel

### Test Connectivity

1. Click **Test Connection** before saving  
2. Verify connection succeeds and returns valid data  
3. Click **OK** to save the updatable object  

!!! success "Connection Successful"
    If the test shows "Success" or displays IP addresses, proceed to the next step.

---

## Step 2: Create Network Group

### Navigate to Network Groups

1. In SmartConsole, go to the **Objects** pane (left side)  
2. Right-click on **Network Objects** → **New** → **Group** → **Simple Group**  

### Configure the Group

Create a network group with these settings:

```
Name: Vesper-Allowed-IPs-Group
Members: Add the Vesper_Secure_Allowed_IPs updatable object
```

**Steps:**

1. Name the group: `Vesper-Allowed-IPs-Group`  
2. Click **Add** under Members  
3. Search for and select `Vesper_Secure_Allowed_IPs`  
4. Click **OK** to save the group  

This group will be used in access rules to reference the dynamic IP list.

---

## Step 3: Create Access Rule

### Navigate to Access Control Policy

1. In SmartConsole, select your Security Policy (usually under **Security Policies**)  
2. Open the **Access Control** policy layer  
3. Click **Add Rule** → **Top** to add a new rule at the top  

!!! warning "Rule Placement is Critical"
    Place this rule **above** any implicit or explicit deny rules to ensure registered IPs are allowed.

### Configure Allow Rule

Create an access rule with these settings:

```
Name: Allow Vesper Verified Users
Source: Vesper-Allowed-IPs-Group
Destination: VPN-Gateway (or appropriate object)
Services & Applications: HTTPS, IKE, IPSEC (or as needed)
Action: Accept
Track: Log
```

**Field Details:**

- **Name**: Click the rule name field and enter a descriptive name
- **Source**: Drag the `Vesper-Allowed-IPs-Group` to the Source column
- **Destination**: Add your VPN gateway object or network
- **Services & Applications**: Add relevant services:
  - HTTPS (443) for SSL VPN
  - IKE, IPSEC for IPsec VPN
  - Custom services as needed
- **Action**: Ensure it's set to **Accept**
- **Track**: Select **Log** to enable logging

### Add Comment

Add a comment to the rule for documentation:

```
Allows VPN access for users registered through Vesper Secure portal. 
IPs are automatically updated from Dynamic IP List every 15 minutes.
```

---

## Step 4: Create Block Rule (Recommended)

Below the allow rule, add an explicit deny rule for unverified access:

```
Name: Block Unverified VPN Access
Source: Any
Destination: VPN-Gateway
Services & Applications: HTTPS, IKE, IPSEC
Action: Drop
Track: Log
```

**Why This Helps:**

- Explicitly logs denied access attempts
- Provides clear visibility into blocked traffic
- Helps with troubleshooting and security monitoring

---

## Step 5: Install Policy

### Validate and Install

1. Click **Install Policy** (toolbar icon)  
2. Select target gateways  
3. Review changes in the preview  
4. Add an installation comment (e.g., "Added Vesper Secure Dynamic IP List integration")  
5. Click **Install** to push the policy to gateways  

Wait for the policy installation to complete successfully.

!!! tip "Verify Installation"
    Check the installation log to ensure policy was installed without errors on all target gateways.

---

## Validation and Testing

### Verify Updatable Object Status

1. Go to **Manage & Settings** → **Blades** → **Threat Prevention** → **Updatable Objects**  
2. Select your `Vesper_Secure_Allowed_IPs` object  
3. Check the "Last Update" timestamp  
4. Verify status shows success  
5. Click **View** to see current IP addresses in the list  

### Check Update Log

1. Go to **Logs & Monitor** → **System Events**  
2. Filter for "Updatable Objects" or search for your object name  
3. Verify successful update events  
4. Check for any error messages  

### Test with User Registration

1. Have a test user register their IP through Vesper portal  
2. Wait for Dynamic IP List refresh interval (5-15 minutes depending on your setting)  
3. Force manual update if desired:  
   - Select the updatable object
   - Click **Update Now**
4. Verify the test user's IP appears in the object  
5. Test VPN connection from the test user's location (should succeed)  

### Verify in Logs

1. Go to **Logs & Monitor** → **Logs**  
2. Filter for your access rule name  
3. Verify connections from registered IPs are being logged as "Accept"  
4. Check that unregistered IPs are being denied  

---

## Troubleshooting

### Updatable Object Update Fails

**Symptoms:**
- Object shows "Failed" status
- Error in System Events logs
- No IPs being loaded

**Solutions:**

1. **Check Management Server Connectivity:**  
   - Verify SMS can reach `edl.vespersecure.com` on port 443
   - Test from SMS command line:
     ```bash
     curl_cli -k https://edl.vespersecure.com
     ```

2. **Verify DNS Resolution:**  
   - Ensure SMS can resolve `edl.vespersecure.com`
   - Check DNS configuration on SMS

3. **Test Authentication:**  
   - Verify credentials are correct
   - Test manually:
     ```bash
     curl_cli -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id
     ```

4. **Check Certificate Validation:**  
   - Verify SMS trusts the SSL certificate
   - Check if SSL inspection is interfering
   - Disable certificate validation temporarily for testing (not recommended for production)

5. **Review System Events:**  
   - Go to **Logs & Monitor** → **System Events**
   - Search for updatable object name
   - Review error details

### Traffic Not Being Allowed

**Symptoms:**
- User registered but still can't connect
- Logs show traffic being denied

**Solutions:**

1. **Verify Rule Order:**  
   - Open Access Control policy
   - Ensure Vesper allow rule is **above** any deny rules
   - Check rule numbers/positions
   - Reinstall policy if order was changed

2. **Check Object Contents:**  
   - View the updatable object
   - Verify user's IP is in the list
   - Have user confirm their current IP
   - Force update if needed

3. **Verify Rule Configuration:**  
   - Check source object is correct
   - Verify destination matches VPN gateway
   - Ensure services/applications are correct
   - Confirm action is "Accept"

4. **Review Logs:**  
   - Go to **Logs & Monitor** → **Logs**
   - Find the denied connection
   - Check which rule is blocking
   - Review rule order and modify as needed

### Authentication Errors

**Symptoms:**
- 401 Unauthorized in logs
- Updatable object shows authentication failure

**Solutions:**

1. Verify credentials in Vesper admin panel  
2. Check for typos or extra spaces in SmartConsole  
3. Re-enter credentials in updatable object  
4. Test with command line:  
   ```bash
   curl_cli -v -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id
   ```
5. Re-generate credentials in Vesper if needed  

---

## Advanced Configuration

### High Availability Configuration

If using Check Point HA cluster:

1. Configure updatable object on the active management server  
2. Policy and object configuration will sync to standby  
3. Both SMS instances will independently fetch the Dynamic IP List  
4. Gateways will use policy from active SMS  

### Multiple VPN Gateways

If you have multiple VPN gateways:

1. Use the same network group in multiple rules  
2. Create separate rules for each gateway if needed  
3. Maintain consistent rule ordering across policies  
4. Install policy to all relevant gateways  

### Custom Update Intervals

For different use cases:

- **High Security**: 300 seconds (5 minutes) - fastest response
- **Balanced**: 900 seconds (15 minutes) - recommended default
- **Low Load**: 3600 seconds (1 hour) - reduces SMS load

### Backup Access

Create a separate network group with static IPs for critical administrators:

1. Create static IP host objects for admin IPs  
2. Create a new network group with these static IPs  
3. Add this group as an additional source in your allow rule  
4. Provides backup access if Dynamic IP List is unavailable  

---

## Best Practices

!!! success "Recommended Settings"
    - **Update Interval**: 900 seconds (15 minutes) for balanced approach
    - **Logging**: Enable on both allow and deny rules
    - **Rule Placement**: Always above deny rules
    - **Testing**: Test with pilot users before full rollout

!!! info "Monitoring"
    - Set up alerts for updatable object update failures
    - Monitor logs for denied access attempts
    - Review allowed connections regularly
    - Keep backup static IP list for critical admins

!!! tip "Optimization"
    - Document your configuration
    - Add detailed comments to rules
    - Schedule regular connectivity tests
    - Review and update as needed

!!! warning "Important"
    - Protect SMS access - credentials are stored there
    - Use strong Dynamic IP List password
    - Review logs regularly for security issues
    - Test policy changes in non-production first

---

## Next Steps

After Check Point integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### Check Point Documentation

- [Updatable Objects Administration Guide](https://sc1.checkpoint.com)
- [SmartConsole User Guide](https://sc1.checkpoint.com)
- [Security Management Best Practices](https://sc1.checkpoint.com)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
