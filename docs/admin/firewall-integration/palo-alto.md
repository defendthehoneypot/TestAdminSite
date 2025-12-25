<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Palo Alto Networks Integration

Configure Palo Alto Networks (PAN-OS) firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

Palo Alto Networks firewalls have native support for External Dynamic Lists (EDL), making integration with Vesper Secure straightforward. This guide covers PAN-OS 8.0 and later versions.

### What You'll Configure

1. External Dynamic List object pointing to Vesper Dynamic IP List  
2. Security policy to allow registered IPs  
3. Optional deny policy for unregistered IPs  
4. Testing and validation  

### Time Required

**15-20 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Palo Alto firewall admin access
- [ ] Outbound HTTPS (port 443) allowed from firewall to `edl.vespersecure.com`
- [ ] Knowledge of your VPN gateway IP and zones

---

## Step 1: Add External Dynamic List

### Navigate to External Dynamic List Configuration

1. Log in to Palo Alto web interface  
2. Go to **Objects** → **External Dynamic Lists**  
3. Click **Add**  

### Configure the External Dynamic List

Configure the following settings:

```
Name: Vesper-Secure-Allowed-IPs
Type: IP List
Source URL: https://edl.vespersecure.com/lists/your-id
Check for updates: Hourly (or every 5 minutes)
Username: org_xxxxx
Password: ••••••••
```

**Field Details:**

- **Name**: Use a descriptive name (e.g., `Vesper-Secure-Allowed-IPs`)
- **Type**: Must be "IP List"
- **Source URL**: Your unique Dynamic IP List URL from Vesper admin panel
- **Check for updates**: 
  - Every 5 minutes for fastest access grants
  - Hourly for reduced firewall load
  - 15 minutes recommended for balanced approach
- **Username**: Your organization ID (starts with `org_`)
- **Password**: Dynamic IP List password from Vesper admin panel

### Save Configuration

4. Click **OK** to save the External Dynamic List object  

!!! warning "Don't Forget to Commit"
    Changes won't take effect until you commit. We'll commit after testing connectivity.

---

## Step 2: Test External Dynamic List Connectivity

Before committing changes, test that the firewall can reach the External Dynamic List:

1. In the External Dynamic Lists page, select your EDL object  
2. Click **Test Source**  
3. Wait for the test to complete  

**Expected Results:**

- ✅ **Success**: Message shows "Success" or displays list of IPs
- ❌ **Failed**: See [Troubleshooting](#troubleshooting) below

!!! tip "Empty List is OK"
    If no users have registered yet, the list may be empty. A successful connection to an empty list is still a success.

---

## Step 3: Create Security Policy

### Navigate to Policies

1. Go to **Policies** → **Security**  
2. Click **Add** to create a new policy  
3. Place the new policy **above** any general deny rules  

### Configure Allow Policy

Create a security policy with these settings:

```
Name: Allow-Vesper-Verified-VPN-Access
Source Zone: untrust (or your external zone)
Source Address: Vesper-Secure-Allowed-IPs
Destination Zone: vpn-gateway (or your VPN zone)
Destination Address: your-vpn-gateway-ip
Application: ssl, ipsec-esp, ipsec-ah
Service: application-default
Action: Allow
Log at Session End: Yes
```

**Field Details:**

- **Name**: Descriptive name for the policy
- **Source Zone**: The zone where external traffic arrives (typically "untrust")
- **Source Address**: Select the External Dynamic List object you created
- **Destination Zone**: Zone containing your VPN gateway
- **Destination Address**: Your VPN gateway IP or object
- **Application**: Include VPN protocols (ssl, ipsec-esp, ipsec-ah, or as needed)
- **Service**: Use "application-default" to follow application definitions
- **Action**: Allow
- **Log at Session End**: Enable for auditing

### Policy Placement

!!! warning "Critical: Policy Order Matters"
    This allow policy **must** be placed **above** any deny rules that would block the traffic. Review your policy order carefully.

---

## Step 4: Create Block Policy (Recommended)

Add an explicit deny rule below the allow rule to block unverified IPs:

```
Name: Block-Unverified-VPN-Access
Source Zone: untrust
Source Address: any
Destination Zone: vpn-gateway
Destination Address: your-vpn-gateway-ip
Application: ssl, ipsec-esp, ipsec-ah
Service: application-default
Action: Deny
Log at Session Start: Yes
```

**Why This Helps:**

- Explicitly logs denied access attempts
- Makes it clear what traffic is being blocked
- Helps with troubleshooting and security monitoring

---

## Step 5: Commit Changes

1. Click **Commit** in the top-right corner  
2. Review the changes to be committed  
3. Add a comment (e.g., "Added Vesper Secure integration")  
4. Click **Commit** to apply changes  

Wait for the commit to complete successfully.

---

## Validation and Testing

### Verify External Dynamic List is Updating

1. Go to **Objects** → **External Dynamic Lists**  
2. Select your External Dynamic List object  
3. Check the "Last Update" timestamp  
4. Verify status shows success  

### Test with a User Registration

1. Have a test user register their IP through Vesper portal  
2. Wait for External Dynamic List refresh interval (5 minutes to 1 hour depending on your setting)  
3. Force a manual refresh if desired:  
   - Select the External Dynamic List object
   - Click **Test Source** or wait for scheduled update
4. Verify the test user's IP appears in the list  
5. Test VPN connection from the test user's location  

### Verify in Traffic Logs

1. Go to **Monitor** → **Traffic**  
2. Look for connections from registered IPs  
3. Verify they're being allowed by your Vesper policy  
4. Check that unregistered IPs are being denied  

---

## Troubleshooting

### External Dynamic List Test Fails

**Symptoms:**
- "Test Source" shows error or timeout
- Status shows "Failed"

**Solutions:**

1. **Check Management Interface Connectivity:**  
   ```
   > ping source <mgmt-interface> host edl.vespersecure.com
   ```

2. **Verify DNS Resolution:**  
   ```
   > show system info | match "DNS Server"
   > test dns-resolution edl.vespersecure.com
   ```

3. **Test HTTPS Connectivity:**  
   - Ensure outbound HTTPS (port 443) is allowed from management interface
   - Check if SSL inspection is interfering

4. **Verify Credentials:**  
   - Double-check username and password
   - Ensure no extra spaces
   - Test manually: `curl -u "user:pass" https://edl.vespersecure.com/lists/your-id`

5. **Check Certificate Profile:**  
   - Go to **Device** → **Certificate Management** → **Certificate Profile**
   - Ensure default profile allows connection to edl.vespersecure.com

### Traffic Not Being Allowed

**Symptoms:**
- User registered but still can't connect
- Traffic logs show denies

**Solutions:**

1. **Verify Policy Order:**  
   - Go to **Policies** → **Security**
   - Ensure Vesper allow policy is **above** deny rules
   - Rearrange if necessary and commit

2. **Check List Contents:**  
   - Test source on External Dynamic List to see current IPs
   - Verify user's IP is in the list
   - Have user confirm their current IP matches

3. **Force Refresh:**  
   - Select External Dynamic List object
   - Click **Test Source** to force update

4. **Review Traffic Logs:**  
   - Go to **Monitor** → **Traffic**
   - Find the blocked session
   - Click on it to see which policy is blocking
   - Adjust policies as needed

### Authentication Errors

**Symptoms:**
- Shows 401 Unauthorized
- Credentials rejected

**Solutions:**

1. Verify credentials in Vesper admin panel  
2. Check for typos or extra spaces when entering  
3. Re-generate credentials if needed  
4. Test with curl: `curl -v -u "user:pass" URL`  

---

## Advanced Configuration

### Using Certificate Pinning

For enhanced security, you can pin the server certificate:

1. Download the certificate from edl.vespersecure.com  
2. Import to **Device** → **Certificate Management** → **Certificates**  
3. Create a Certificate Profile referencing this certificate  
4. Reference the profile in your External Dynamic List configuration  

### High Availability Configuration

If using HA pair:

1. Configure External Dynamic List on the active firewall  
2. Commit changes  
3. HA sync will replicate configuration to passive firewall  
4. Both firewalls will independently fetch the list  

### Multiple VPN Gateways

If you have multiple VPN gateways:

1. Use the same External Dynamic List object in multiple security policies  
2. Create separate policies for each VPN gateway  
3. Maintain consistent policy ordering  

---

## Best Practices

!!! success "Recommended Settings"
    - **External Dynamic List Refresh**: 15 minutes for balanced approach
    - **Logging**: Enable logging on both allow and deny policies
    - **Policy Order**: Review quarterly to ensure correct ordering
    - **Testing**: Test with pilot users before full rollout

!!! info "Monitoring"
    - Set up alerts for External Dynamic List update failures
    - Monitor traffic logs for unexpected denies
    - Review allowed connections regularly
    - Keep backup static IP list for critical admins

!!! tip "Optimization"
    - Use policy-based forwarding if needed
    - Consider security profiles on the allow policy
    - Document your configuration for future reference
    - Schedule regular External Dynamic List connectivity tests

---

## Next Steps

After Palo Alto integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### Palo Alto Documentation

- [External Dynamic Lists Configuration Guide](https://docs.paloaltonetworks.com)
- [Security Policy Best Practices](https://docs.paloaltonetworks.com)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
