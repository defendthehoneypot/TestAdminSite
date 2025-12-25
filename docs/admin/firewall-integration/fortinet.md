<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Fortinet FortiGate Integration

Configure Fortinet FortiGate firewalls to use Vesper Secure's External Dynamic List for automated ACL updates.

</div>

---

## Overview

Fortinet FortiGate firewalls support External Connectors (also called Fabric Connectors) that can pull IP addresses from external sources. This guide covers FortiOS 6.0 and later versions.

### What You'll Configure

1. External Connector to pull IPs from Vesper EDL
2. Dynamic address object based on the connector
3. Firewall policy to allow registered IPs
4. Testing and validation

### Time Required

**15-20 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] EDL URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] FortiGate firewall admin access
- [ ] Outbound HTTPS (port 443) allowed from firewall to `edl.vespersecure.com`
- [ ] Knowledge of your VPN gateway interface and IP

---

## Step 1: Create External Resource

### Navigate to External Connectors

1. Log in to FortiGate web interface
2. Go to **Security Fabric** → **External Connectors**
3. Click **Create New** → **IP Address**

!!! info "FortiOS Versions"
    In some FortiOS versions, this may be under **Security Fabric** → **Fabric Connectors** or **External Resources**.

### Configure External Connector

Configure the following settings:

```
Name: Vesper_Secure_EDL
Type: IP Address
Update Method: HTTP(S)
URL: https://edl.vespersecure.com/lists/your-id
Authentication: Basic
Username: org_xxxxx
Password: ••••••••
Refresh Rate: 5 minutes
```

**Field Details:**

- **Name**: Use a descriptive name without spaces (e.g., `Vesper_Secure_EDL`)
- **Type**: Must be "IP Address"
- **Update Method**: Select "HTTP(S)"
- **URL**: Your unique EDL URL from Vesper admin panel
- **Authentication**: Select "Basic"
- **Username**: Your organization ID (starts with `org_`)
- **Password**: EDL password from Vesper admin panel
- **Refresh Rate**: 
  - 5 minutes for fastest access grants
  - 1 hour for reduced firewall load
  - 15 minutes recommended for balanced approach

### Test Connectivity

1. Click **Test Connectivity** before saving (if available)
2. Verify connection succeeds
3. Click **OK** to save the external connector

---

## Step 2: Create Address Object

### Navigate to Addresses

1. Go to **Policy & Objects** → **Addresses**
2. Click **Create New** → **Address**

### Configure Dynamic Address

Create an address object with these settings:

```
Name: Vesper-Allowed-IPs
Type: Dynamic
Sub-type: Fabric Connector Address
Connector: Vesper_Secure_EDL
Interface: any
```

**Field Details:**

- **Name**: Descriptive name for the address object
- **Type**: Select "Dynamic" (not Static)
- **Sub-type**: Select "Fabric Connector Address"
- **Connector**: Select the connector you created (`Vesper_Secure_EDL`)
- **Interface**: Leave as "any" or select specific interface if needed

### Save Address Object

Click **OK** to save the address object.

---

## Step 3: Verify External Connector

Before creating policies, verify the connector is working:

1. Go to **Security Fabric** → **External Connectors**
2. Find your `Vesper_Secure_EDL` connector
3. Check the status shows green/connected
4. Click on the connector name to view details
5. Verify it shows IP addresses (if any users have registered)

!!! tip "Empty List is OK"
    If no users have registered yet, the connector may show 0 addresses. This is normal - it will populate as users register.

---

## Step 4: Create Firewall Policy

### Navigate to Policies

1. Go to **Policy & Objects** → **Firewall Policy**
2. Click **Create New**
3. Position the policy **above** any general deny rules

### Configure Allow Policy

Create a firewall policy with these settings:

```
Name: Allow-Vesper-Verified-Users
Incoming Interface: wan1 (or your external interface)
Outgoing Interface: vpn (or your VPN interface)
Source: Vesper-Allowed-IPs
Destination: VPN-Gateway-IP (or all)
Service: HTTPS, IPSEC
Schedule: always
Action: Accept
NAT: (configure as needed)
Log Allowed Traffic: All Sessions
```

**Field Details:**

- **Name**: Descriptive name for the policy
- **Incoming Interface**: Interface where external traffic arrives (e.g., wan1, port1)
- **Outgoing Interface**: Interface connected to VPN gateway
- **Source**: Select the address object you created (`Vesper-Allowed-IPs`)
- **Destination**: Your VPN gateway IP or address object (or use "all")
- **Service**: Include VPN protocols:
  - HTTPS (for SSL VPN)
  - IPSEC (for IPSec VPN)
  - Or create custom service for specific ports
- **Schedule**: "always"
- **Action**: Accept
- **NAT**: Configure source NAT if required by your environment
- **Log Allowed Traffic**: Enable "All Sessions" for auditing

### Enable Security Profiles (Optional)

Under **Security Profiles**, optionally enable:
- Antivirus
- Web Filtering  
- Application Control
- IPS

!!! warning "Policy Order Matters"
    This allow policy **must** be placed **above** any deny rules that would block the traffic. Use the up/down arrows to reorder policies.

### Save Policy

Click **OK** to save the firewall policy.

---

## Step 5: Create Block Policy (Optional)

Add an explicit deny rule below the allow rule to block unverified IPs:

```
Name: Block-Unverified-VPN-Access
Incoming Interface: wan1
Outgoing Interface: vpn
Source: all
Destination: VPN-Gateway-IP
Service: HTTPS, IPSEC
Action: Deny
Log Blocked Traffic: All Sessions
```

This helps with logging and troubleshooting by explicitly capturing denied attempts.

---

## Validation and Testing

### Verify External Connector Status

1. Go to **Security Fabric** → **External Connectors**
2. Check your connector shows "Connected" or green status
3. Click on it to view the current IP addresses
4. Verify "Last Updated" timestamp is recent

### Test Connector Manually

You can force a manual update:

1. Go to **Security Fabric** → **External Connectors**
2. Right-click on your connector
3. Select **Refresh** or **Update Now** (if available)
4. Wait a few seconds and verify update timestamp

### Verify Address Object

1. Go to **Policy & Objects** → **Addresses**
2. Find your `Vesper-Allowed-IPs` address object
3. Hover over or click to view current IP addresses
4. Verify IPs are being populated from the connector

### Test with User Registration

1. Have a test user register their IP through Vesper portal
2. Wait for refresh interval (5 minutes to 1 hour depending on your setting)
3. Force a manual refresh of the external connector
4. Verify the test user's IP appears in the address object
5. Test VPN connection from the test user's location

### Review Firewall Logs

1. Go to **Log & Report** → **Forward Traffic**
2. Look for connections from registered IPs
3. Verify they're being allowed by your Vesper policy
4. Check that unregistered IPs are being denied (if you created a deny rule)

---

## Troubleshooting

### External Connector Shows "Failed" or "Disconnected"

**Symptoms:**
- Connector status shows red or failed
- No IPs being populated
- Error in connector details

**Solutions:**

1. **Check Outbound Connectivity:**
   - Go to **Dashboard** → **CLI Console**
   - Execute: `execute ping edl.vespersecure.com`
   - Verify connectivity

2. **Verify DNS Resolution:**
   ```
   execute dns lookup edl.vespersecure.com
   ```

3. **Check Interface Selection:**
   - Verify the correct management or WAN interface can reach the internet
   - Go to **System** → **Settings** and check source IP for updates

4. **Test with Diagnostics:**
   ```
   diagnose test application fcnacd 4
   ```
   This shows external connector debug information.

5. **Verify Credentials:**
   - Double-check username and password in connector configuration
   - Ensure no extra spaces
   - Test manually: `curl -u "user:pass" https://edl.vespersecure.com/lists/your-id`

6. **Check Routing:**
   - Ensure firewall has a route to reach edl.vespersecure.com
   - Verify default gateway is configured

### Address Object is Empty

**Symptoms:**
- Connector shows connected but address object has no IPs
- Policy not matching traffic

**Solutions:**

1. **Verify Users Have Registered:**
   - Check Vesper admin panel
   - Confirm active registrations exist

2. **Force Connector Refresh:**
   - Right-click connector and refresh
   - Wait for update to complete

3. **Check Connector Configuration:**
   - Verify URL is correct
   - Ensure connector type is "IP Address"

4. **Review Logs:**
   - Go to **Log & Report** → **System Events**
   - Filter for external connector events
   - Look for errors

### Traffic Not Being Allowed

**Symptoms:**
- User registered but still can't connect
- Logs show denies

**Solutions:**

1. **Verify Policy Order:**
   - Go to **Policy & Objects** → **Firewall Policy**
   - Ensure Vesper allow policy is **above** deny rules
   - Drag to reorder if necessary

2. **Check Policy Configuration:**
   - Verify source address is set to `Vesper-Allowed-IPs`
   - Confirm interfaces are correct
   - Ensure services include necessary VPN protocols

3. **Verify IP is in Address Object:**
   - Go to **Policy & Objects** → **Addresses**
   - Check `Vesper-Allowed-IPs` contains the user's IP
   - Have user confirm their current IP

4. **Review Policy Logs:**
   - Go to **Log & Report** → **Forward Traffic**
   - Find the blocked session
   - Check which policy is blocking
   - Verify hit count on Vesper allow policy

### Authentication Errors

**Symptoms:**
- Connector shows authentication failed
- 401 Unauthorized in logs

**Solutions:**

1. Edit the external connector
2. Re-enter username and password carefully
3. Ensure no extra spaces
4. Test credentials with curl: `curl -v -u "user:pass" URL`
5. Re-generate credentials in Vesper admin panel if needed

---

## Advanced Configuration

### Using VDOMs

If using Virtual Domains (VDOMs):

1. Create the external connector in each VDOM that needs access
2. Each VDOM maintains its own connector instance
3. Configure policies within each VDOM

### High Availability Configuration

For HA cluster:

1. Configure external connector on the primary unit
2. Configuration syncs to secondary automatically
3. Both units independently fetch the EDL
4. Verify connectivity from both units

### Multiple VPN Interfaces

If you have multiple VPN gateways:

1. Use the same address object in multiple policies
2. Create separate policies for each VPN interface
3. Maintain consistent policy ordering

---

## Best Practices

!!! success "Recommended Settings"
    - **Refresh Rate**: 15 minutes for balanced approach
    - **Logging**: Enable logging on both allow and deny policies
    - **Policy Order**: Review quarterly to ensure correct ordering
    - **Testing**: Test with pilot users before full rollout

!!! info "Monitoring"
    - Monitor external connector status daily
    - Set up alerts for connector failures
    - Review forward traffic logs regularly
    - Keep backup static IP list for critical admins

!!! tip "Optimization"
    - Use security profiles on allow policy for additional protection
    - Consider creating address groups if managing multiple EDLs
    - Document your configuration for future reference
    - Schedule regular connectivity tests

---

## Next Steps

After FortiGate integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### Fortinet Documentation

- [External Connectors Configuration Guide](https://docs.fortinet.com)
- [Dynamic Address Objects](https://docs.fortinet.com)
- [Firewall Policy Best Practices](https://docs.fortinet.com)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
