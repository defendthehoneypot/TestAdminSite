<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Juniper SRX Integration

Configure Juniper SRX firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

Juniper SRX Series firewalls (JunOS 15.1X49+) support Dynamic Address Objects that can fetch IP addresses from external HTTP/HTTPS URLs. This guide covers SRX configuration using both GUI (J-Web) and CLI methods.

### What You'll Configure

1. Dynamic address object pointing to Vesper Dynamic IP List  
2. Security policy to allow registered IPs  
3. Zone configuration and policy ordering  
4. Testing and validation  

### Time Required

**20-25 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] Dynamic IP List URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Juniper SRX with JunOS 15.1X49 or later
- [ ] CLI access (SSH) or J-Web access
- [ ] Outbound HTTPS (port 443) allowed from SRX to `list.vespersecure.com`
- [ ] Knowledge of your security zones and VPN configuration

!!! info "Configuration Method"
    This guide primarily uses CLI configuration as it's more consistent across SRX models. J-Web steps are noted where applicable.

---

## Step 1: Configure Dynamic Address Object

### Connect to SRX CLI

SSH to your SRX device and enter configuration mode:

```junos
ssh admin@srx-firewall
configure
```

### Create Dynamic Address Object

Create a dynamic address object for the Vesper Dynamic IP List:

```junos
set security dynamic-address address-name Vesper-Allowed-IPs
set security dynamic-address address-name Vesper-Allowed-IPs profile Vesper-DIPL-Profile
```

**Field Details:**

- **address-name**: `Vesper-Allowed-IPs` (the name you'll reference in policies)
- **profile**: `Vesper-DIPL-Profile` (profile we'll create next)

---

## Step 2: Configure Feed Profile

### Create the Feed Profile

Configure the profile that defines how to fetch the Dynamic IP List:

```junos
set services security-intelligence url Vesper-DIPL-Profile url https://list.vespersecure.com/organizationid
set services security-intelligence url Vesper-DIPL-Profile category Vesper-IPs
set services security-intelligence url Vesper-DIPL-Profile username organizationid
set services security-intelligence url Vesper-DIPL-Profile password your-password
set services security-intelligence url Vesper-DIPL-Profile update-interval 900
```

**Parameter Details:**

- **url**: Your unique Dynamic IP List URL from Vesper admin panel
- **category**: A label for this feed (e.g., `Vesper-IPs`)
- **username**: Your organization ID
- **password**: Dynamic IP List password from Vesper admin panel
- **update-interval**: Update interval in seconds
  - `300` = 5 minutes (most responsive)
  - `900` = 15 minutes (recommended)
  - `3600` = 1 hour (lower load)

!!! warning "Password Storage"
    The password will be encrypted in the configuration. Protect access to configuration files and commit scripts.

---

## Step 3: Configure Address Book Entry

### Create Address Book Entry

Add the dynamic address to your address book:

```junos
set security address-book global address Vesper-Allowed-IPs-Entry dynamic-address Vesper-Allowed-IPs
```

**Parameter Details:**

- **global**: Use global address book (or specify a zone-specific book)
- **address**: Name for the address book entry (e.g., `Vesper-Allowed-IPs-Entry`)
- **dynamic-address**: Reference to the dynamic address object created in Step 1

### Verify Configuration

Check your configuration:

```junos
show security dynamic-address
show security address-book global
```

---

## Step 4: Create Security Policy

### Identify Your Zones

Determine your zone configuration:

```junos
show security zones
```

Typically:
- **untrust** zone: External/internet-facing interface
- **trust** zone: Internal/VPN interface

### Create Security Policy

Create a policy to allow traffic from registered IPs:

```junos
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users match source-address Vesper-Allowed-IPs-Entry
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users match destination-address any
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users match application junos-ssl
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users match application junos-ike
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users match application junos-ipsec-vpn
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users then permit
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users then log session-init
set security policies from-zone untrust to-zone trust policy Allow-Vesper-Users then log session-close
```

**Parameter Details:**

- **from-zone**: `untrust` (or your external zone)
- **to-zone**: `trust` (or your VPN/internal zone)
- **policy**: `Allow-Vesper-Users` (policy name)
- **source-address**: `Vesper-Allowed-IPs-Entry` (the address book entry)
- **destination-address**: `any` (or specific VPN gateway address)
- **application**: VPN applications
  - `junos-ssl` for SSL VPN (port 443)
  - `junos-ike` for IPsec IKE (port 500)
  - `junos-ipsec-vpn` for IPsec ESP
  - Add custom applications as needed
- **then permit**: Allow the traffic
- **then log**: Enable logging for session start and end

### Set Policy Priority (Important)

Ensure this policy is evaluated before deny policies:

```junos
insert security policies from-zone untrust to-zone trust policy Allow-Vesper-Users before policy Deny-All
```

Replace `Deny-All` with your actual deny policy name.

!!! warning "Policy Order is Critical"
    Security policies are evaluated top-to-bottom. Place this allow policy above any deny policies.

---

## Step 5: Create Deny Policy (Recommended)

Create an explicit deny policy for visibility:

```junos
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN match source-address any
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN match destination-address any
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN match application junos-ssl
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN match application junos-ike
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN then deny
set security policies from-zone untrust to-zone trust policy Block-Unverified-VPN then log session-init
```

Ensure this policy is ordered after the allow policy:

```junos
insert security policies from-zone untrust to-zone trust policy Block-Unverified-VPN after policy Allow-Vesper-Users
```

---

## Step 6: Commit Configuration

### Review Configuration

Review your changes:

```junos
show | compare
```

This shows what will change when you commit.

### Commit Changes

Commit the configuration:

```junos
commit
```

Add a comment for documentation:

```junos
commit comment "Added Vesper Secure Dynamic IP List integration"
```

Wait for commit to complete successfully.

### Exit Configuration Mode

```junos
exit
```

---

## Validation and Testing

### Verify Dynamic Address Object

Check the dynamic address object status:

```junos
show security dynamic-address summary
```

Should show:
- Address name: `Vesper-Allowed-IPs`
- Status: Active
- Number of entries

### View Dynamic Address Contents

See the IPs currently in the dynamic address:

```junos
show security dynamic-address address-name Vesper-Allowed-IPs
```

This will list all IP addresses currently loaded from the Dynamic IP List.

### Check Feed Status

Verify the feed is updating:

```junos
show services security-intelligence url Vesper-DIPL-Profile
```

Should show:
- Last update time
- Update status (success/failure)
- Number of entries

### Force Manual Update

To force an immediate update:

```junos
request services security-intelligence url Vesper-DIPL-Profile update
```

Then verify:

```junos
show services security-intelligence url Vesper-DIPL-Profile
```

### Test with User Registration

1. Have a test user register their IP through Vesper portal  
2. Wait for SRX update interval (5-15 minutes depending on setting)  
3. Force manual update using command above  
4. Verify the test user's IP appears:  
   ```junos
   show security dynamic-address address-name Vesper-Allowed-IPs | match test-user-ip
   ```
5. Test VPN connection from test user's location (should succeed)  

### Check Security Policy Logs

View policy hits:

```junos
show security policies hit-count
```

View security logs:

```junos
show log messages | match "Allow-Vesper-Users"
```

Or view in J-Web:
- **Monitor** → **Logs** → **Security Logs**

---

## Troubleshooting

### Feed Download Fails

**Symptoms:**
- Dynamic address object is empty  
- Feed status shows error  
- System logs show update failures

**Solutions:**

1. **Check Connectivity:**  
   ```junos
   ping list.vespersecure.com
   traceroute list.vespersecure.com
   ```

2. **Test HTTPS Connectivity:**  
   ```junos
   show system connections | match 443
   ```

3. **Verify DNS Resolution:**  
   ```junos
   show host list.vespersecure.com
   ```

4. **Check System Logs:**  
   ```junos
   show log messages | match security-intelligence
   ```
   Look for error messages related to URL feeds

5. **Verify Credentials:**  
   - Review configuration:
     ```junos
     show services security-intelligence url Vesper-DIPL-Profile
     ```
   - Re-enter password if needed:
     ```junos
     set services security-intelligence url Vesper-DIPL-Profile password new-password
     commit
     ```

6. **Test URL Manually:**  
   
   From a system with internet access:
   ```bash
   curl -u "organizationid:password" https://list.vespersecure.com/organizationid
   ```

### Authentication Errors

**Symptoms:**
- Feed shows 401 authentication failure  
- Logs indicate credential issues

**Solutions:**

1. **Verify Credentials:**  
   - Check username is correct
   - Confirm password is correct in Vesper admin panel
   
2. **Re-enter Credentials:**  
   ```junos
   configure
   delete services security-intelligence url Vesper-DIPL-Profile username
   delete services security-intelligence url Vesper-DIPL-Profile password
   set services security-intelligence url Vesper-DIPL-Profile username organizationid
   set services security-intelligence url Vesper-DIPL-Profile password new-password
   commit
   ```

3. **Test with Curl:**  
   ```bash
   curl -v -u "organizationid:password" https://list.vespersecure.com/organizationid
   ```
   Should return 200 OK, not 401

### Traffic Not Being Allowed

**Symptoms:**
- User registered but VPN connection fails  
- Logs show denies instead of permits

**Solutions:**

1. **Verify Policy Order:**  
   ```junos
   show security policies from-zone untrust to-zone trust
   ```
   Ensure `Allow-Vesper-Users` appears before deny policies

2. **Reorder Policies if Needed:**  
   ```junos
   configure
   insert security policies from-zone untrust to-zone trust policy Allow-Vesper-Users before policy Deny-All
   commit
   ```

3. **Check Dynamic Address Contents:**  
   ```junos
   show security dynamic-address address-name Vesper-Allowed-IPs
   ```
   Verify user's IP is in the list

4. **Force Update:**  
   ```junos
   request services security-intelligence url Vesper-DIPL-Profile update
   ```

5. **Review Policy Matches:**  
   ```junos
   show security policies hit-count
   ```
   Check if allow policy is being hit

6. **Check Zones:**  
   ```junos
   show security zones
   ```
   Verify interfaces are in correct zones

7. **Review Logs:**  
   ```junos
   show log messages | match "RT_FLOW"
   ```
   Look for session creation/denial messages

### Dynamic Address Object Empty

**Symptoms:**
- Object shows 0 entries  
- No IPs loaded

**Solutions:**

1. **Check if Users Registered:**  
   - Verify users have registered in Vesper portal
   - Empty Dynamic IP List will result in empty dynamic address

2. **Force Update:**  
   ```junos
   request services security-intelligence url Vesper-DIPL-Profile update
   ```

3. **Check Feed Status:**  
   ```junos
   show services security-intelligence url Vesper-DIPL-Profile
   ```

4. **Test URL Manually:**  
   - Should return IP addresses if users registered
   - Empty response means no users registered (this is OK)

---

## Advanced Configuration

### Zone-Specific Address Books

Instead of global address book, use zone-specific:

```junos
set security zones security-zone untrust address-book address Vesper-Allowed-IPs-Entry dynamic-address Vesper-Allowed-IPs
```

### Multiple Feed Sources

If you have multiple EDLs:

```junos
set security dynamic-address address-name Vesper-Remote-Workers profile Vesper-Remote-Profile
set security dynamic-address address-name Vesper-Contractors profile Vesper-Contractors-Profile
```

Create separate policies or combine in address-sets.

### High Availability Configuration

For SRX HA cluster:

1. Configure on primary node  
2. Configuration syncs to secondary via chassis cluster  
3. Both nodes independently fetch feeds  
4. Ensure both nodes have internet connectivity  

### IPv6 Support

To support IPv6 addresses:

1. Create separate dynamic address for IPv6  
2. Use IPv6-enabled feed URL if available  
3. Create IPv6 security policies  
4. Use `inet6` address family in policies  

### Backup Static Access

Maintain backup access for critical admins:

```junos
set security zones security-zone untrust address-book address Critical-Admin-IP 203.0.113.100/32
set security policies from-zone untrust to-zone trust policy Allow-Critical-Admins match source-address Critical-Admin-IP
```

---

## Best Practices

!!! success "Recommended Settings"
    - **Update Interval**: 900 seconds (15 minutes) for balanced approach
    - **Logging**: Enable session-init and session-close logs
    - **Policy Order**: Place allow policies before deny policies
    - **Testing**: Test with pilot users before production deployment

!!! info "Monitoring"
    - Check `show security dynamic-address summary` regularly
    - Monitor feed update status with `show services security-intelligence`
    - Review security logs for allowed and denied sessions
    - Set up SNMP or syslog alerting for feed failures

!!! tip "Optimization"
    - Use zone-specific address books for better organization
    - Document policy names and purposes
    - Schedule regular manual updates during maintenance
    - Keep backup configuration files

!!! warning "Important"
    - Protect CLI/J-Web access - configuration contains credentials
    - Use strong Dynamic IP List password
    - Regularly review security logs
    - Test configuration changes in lab environment first

---

## J-Web GUI Configuration

For those preferring GUI configuration:

### Create Dynamic Address Object

1. Log in to J-Web interface  
2. Go to **Configure** → **Security** → **Dynamic Address**  
3. Click **Create**  
4. Configure:  
   - Address Name: `Vesper-Allowed-IPs`
   - Profile: `Vesper-DIPL-Profile` (create profile first)

### Create Feed Profile

1. Go to **Configure** → **Services** → **Security Intelligence**  
2. Click **Create** under URL Feeds  
3. Configure:  
   - Profile Name: `Vesper-DIPL-Profile`
   - URL: `https://list.vespersecure.com/organizationid`
   - Username: `organizationid`
   - Password: Your Dynamic IP List password
   - Update Interval: `900` seconds

### Create Security Policy

1. Go to **Configure** → **Security** → **Policies**  
2. Select appropriate zone pair  
3. Click **Create Policy**  
4. Configure match conditions and permit action  
5. Apply configuration  

---

## Next Steps

After Juniper SRX integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### Juniper Documentation

- [JunOS Security Configuration Guide](https://www.juniper.net/documentation)
- [Dynamic Address Objects Guide](https://www.juniper.net/documentation)
- [Security Intelligence Feeds](https://www.juniper.net/documentation)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
