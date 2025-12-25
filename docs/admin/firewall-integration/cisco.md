<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Cisco ASA/FTD Integration

Configure Cisco ASA and Firepower Threat Defense (FTD) firewalls to use Vesper Secure's Dynamic IP List for automated ACL updates.

</div>

---

## Overview

Cisco ASA and FTD devices support dynamic object groups that can pull IP addresses from external URLs. This guide covers ASA/FTD version 9.8 and later.

### What You'll Configure

1. Dynamic object group for IP addresses  
2. External URL configuration with authentication  
3. ACL entries using the dynamic object group  
4. Testing and validation  

### Time Required

**20-25 minutes** for complete configuration and testing.

---

## Before You Begin

Ensure you have:

- [ ] DIPL URL, username, and password from [Setup & Configuration](../setup.md)
- [ ] Cisco ASA/FTD CLI access (SSH or console)
- [ ] Enable mode password
- [ ] Outbound HTTPS (port 443) allowed from firewall to `edl.vespersecure.com`
- [ ] Knowledge of your VPN gateway interface and configuration

!!! info "Configuration Method"
    This guide uses CLI configuration. While some settings can be configured via ASDM or FMC, CLI provides the most consistent experience across ASA and FTD versions.

---

## Step 1: Create Dynamic Object Group

Connect to your ASA/FTD device via CLI and enter configuration mode:

```cisco
enable
configure terminal
```

### Create the Object Group

Create a network object group for the dynamic IPs:

```cisco
object-group network Vesper-Allowed-IPs
  description Vesper Secure verified user IPs
```

This creates an empty object group that we'll populate from the DIPL.

---

## Step 2: Configure Dynamic Object URL

### Create the Dynamic Object Update URL

Configure the DIPL source with authentication:

```cisco
dynamic-object update-url Vesper-DIPL https://edl.vespersecure.com/lists/your-id
  poll-period 5
  basic-auth username org_xxxxx password your-password
```

**Parameter Details:**

- **Vesper-DIPL**: Name for this dynamic object update URL
- **URL**: Your unique DIPL URL from Vesper admin panel
- **poll-period**: Update interval in minutes
  - `5` = 5 minutes (most responsive)
  - `15` = 15 minutes (recommended)
  - `60` = 1 hour (lower load)
- **username**: Your organization ID (starts with `org_`)
- **password**: DIPL password from Vesper admin panel

!!! warning "Password in Configuration"
    The password will be stored in the configuration. Ensure you protect access to the configuration files.

---

## Step 3: Link Dynamic Object to Object Group

Associate the dynamic object with the object group:

```cisco
object-group network Vesper-Allowed-IPs
  dynamic-object Vesper-DIPL
```

This tells the firewall to populate the `Vesper-Allowed-IPs` object group with IPs from the `Vesper-DIPL` dynamic object.

---

## Step 4: Create ACL Entries

### For SSL VPN Access

If protecting SSL VPN access:

```cisco
access-list outside_access_in extended permit tcp object-group Vesper-Allowed-IPs any eq https log
```

### For IPSec VPN Access

If protecting IPSec VPN access:

```cisco
access-list outside_access_in extended permit udp object-group Vesper-Allowed-IPs any eq isakmp log
access-list outside_access_in extended permit esp object-group Vesper-Allowed-IPs any log
```

### Apply ACL to Interface

If not already applied, apply the ACL to your outside interface:

```cisco
access-group outside_access_in in interface outside
```

**ACL Details:**

- **outside_access_in**: Name of your outside interface ACL (adjust as needed)
- **object-group Vesper-Allowed-IPs**: References the dynamic object group
- **any**: Destination (adjust to specific VPN gateway if desired)
- **eq https**: HTTPS port 443 for SSL VPN
- **eq isakmp**: IKE/ISAKMP port 500 for IPSec
- **esp**: ESP protocol for IPSec
- **log**: Enables logging for these connections

!!! tip "ACL Placement"
    Place these permit statements **before** any deny statements that would block VPN traffic.

---

## Step 5: Save Configuration

Save your configuration:

```cisco
write memory
```

Or:

```cisco
copy running-config startup-config
```

Exit configuration mode:

```cisco
end
```

---

## Validation and Testing

### Verify Dynamic Object Configuration

Check that the dynamic object is configured:

```cisco
show run dynamic-object
```

**Expected output:**
```
dynamic-object update-url Vesper-DIPL https://edl.vespersecure.com/lists/your-id
  poll-period 5
  basic-auth username org_xxxxx password ********
```

### Verify Object Group Configuration

Check the object group:

```cisco
show run object-group id Vesper-Allowed-IPs
```

**Expected output:**
```
object-group network Vesper-Allowed-IPs
  description Vesper Secure verified user IPs
  dynamic-object Vesper-DIPL
```

### Check Dynamic Object Status

View the status of the dynamic object:

```cisco
show dynamic-object update-url
```

This shows:
- Update URL
- Last update time
- Update status (success/failure)
- Poll interval

### View Current IPs

See which IPs are currently in the dynamic object group:

```cisco
show dynamic-object Vesper-DIPL
```

This lists all IPs currently fetched from the DIPL.

!!! tip "Empty List is OK"
    If no users have registered yet, this may show no entries. This is normal.

### Force Manual Update

To force an immediate update of the DIPL:

```cisco
clear dynamic-object Vesper-DIPL
```

Wait a moment, then check again:

```cisco
show dynamic-object Vesper-DIPL
```

---

## Testing with User Registration

### Test the Complete Flow

1. **User Registers**: Have a test user register their IP through Vesper portal  

2. **Wait for Update**: Wait for the poll interval (5 minutes, 15 minutes, etc.)  

3. **Verify IP in Object**: Check that the IP appears:  
   ```cisco
   show dynamic-object Vesper-DIPL | grep test-user-ip
   ```

4. **Check ACL Hits**: Verify ACL is being hit:  
   ```cisco
   show access-list outside_access_in | grep Vesper-Allowed-IPs
   ```

5. **Test Connection**: Have the test user attempt VPN connection  

6. **Review Logs**: Check syslog or ASDM logs for allowed connections  

---

## Troubleshooting

### Dynamic Object Update Fails

**Symptoms:**
- `show dynamic-object update-url` shows failure
- No IPs being fetched
- Error messages in logs

**Solutions:**

1. **Test DNS Resolution:**  
   ```cisco
   ping edl.vespersecure.com
   ```

2. **Verify Outbound HTTPS:**  
   - Ensure management interface can reach internet
   - Check ACLs don't block outbound HTTPS from firewall

3. **Test from CLI:**  
   ```cisco
   test dynamic-object update Vesper-DIPL
   ```

4. **Check Credentials:**  
   - Verify username and password are correct
   - Ensure no extra spaces or special characters causing issues
   - Test manually with curl from another system

5. **Review Configuration:**  
   ```cisco
   show run dynamic-object
   ```
   Verify URL, username, and authentication are correct

6. **Check SSL/TLS:**  
   - Ensure ASA/FTD has proper CA certificates
   - Verify SSL/TLS version compatibility

### Object Group is Empty

**Symptoms:**
- `show dynamic-object Vesper-DIPL` returns no IPs
- Update status shows success but no IPs

**Solutions:**

1. **Verify Users Have Registered:**  
   - Check Vesper admin panel
   - Confirm active registrations exist

2. **Check URL Format:**  
   - Ensure DIPL URL is correct
   - Test the URL manually: `curl -u "user:pass" URL`

3. **Force Update:**  
   ```cisco
   clear dynamic-object Vesper-DIPL
   ```
   Wait 30 seconds, then check again

4. **Review Logs:**  
   ```cisco
   show logging | grep dynamic-object
   ```

### Traffic Not Being Allowed

**Symptoms:**
- User registered but still can't connect
- Logs show denies

**Solutions:**

1. **Verify ACL Order:**  
   ```cisco
   show access-list outside_access_in
   ```
   Ensure Vesper permit rules are **before** any deny rules

2. **Check ACL Configuration:**  
   - Verify ACL references correct object group
   - Ensure ACL is applied to correct interface
   - Confirm protocol and port match your VPN type

3. **Verify IP in Object:**  
   ```cisco
   show dynamic-object Vesper-DIPL | include user-ip-address
   ```

4. **Check ACL Hits:**  
   ```cisco
   show access-list outside_access_in | grep Vesper-Allowed-IPs
   ```
   Look for hit counts to confirm traffic is matching

5. **Review Connection Logs:**  
   ```cisco
   show conn | grep user-ip-address
   show logging | grep user-ip-address
   ```

### Authentication Errors

**Symptoms:**
- 401 Unauthorized in logs
- Update fails with auth error

**Solutions:**

1. Remove and re-create the dynamic object with correct credentials:  
   ```cisco
   configure terminal
   no dynamic-object update-url Vesper-DIPL
   dynamic-object update-url Vesper-DIPL https://edl.vespersecure.com/lists/your-id
     poll-period 5
     basic-auth username org_xxxxx password your-password
   ```

2. Test credentials manually from another system:  
   ```bash
   curl -v -u "org_xxxxx:your-password" https://edl.vespersecure.com/lists/your-id
   ```

---

## Advanced Configuration

### Custom Poll Intervals

Adjust based on your needs:

```cisco
dynamic-object update-url Vesper-DIPL https://edl.vespersecure.com/lists/your-id
  poll-period 15
```

Values: 5-1440 minutes (5 minutes to 24 hours)

### Multiple Object Groups

Use the same dynamic object in multiple object groups:

```cisco
object-group network Vesper-VPN-Users
  dynamic-object Vesper-DIPL

object-group network Vesper-Admin-Access
  dynamic-object Vesper-DIPL
```

### Combining with Static IPs

Add static backup IPs to the same object group:

```cisco
object-group network Vesper-Allowed-IPs
  dynamic-object Vesper-DIPL
  network-object host 203.0.113.10
  network-object 198.51.100.0 255.255.255.0
```

### High Availability

For HA pairs:

1. Configure on the active unit  
2. Configuration syncs to standby automatically  
3. Both units fetch the DIPL independently  
4. Verify connectivity from both units  

---

## Best Practices

!!! success "Recommended Settings"
    - **Poll Interval**: 15 minutes for balanced approach
    - **Logging**: Enable logging on ACL entries
    - **ACL Order**: Review quarterly to ensure correct ordering
    - **Backup Access**: Include static IPs for critical admins

!!! info "Monitoring"
    - Check `show dynamic-object update-url` daily
    - Monitor syslog for update failures
    - Review ACL hit counts regularly
    - Keep backup static IP list for critical admins

!!! tip "Security"
    - Protect configuration files (password is visible)
    - Use strong DIPL password
    - Enable logging for audit trail
    - Regularly review allowed connections

!!! warning "Important Notes"
    - Poll interval cannot be less than 5 minutes
    - Maximum 1440 minutes (24 hours)
    - Password stored in plain text in config
    - Ensure configuration backups are secured

---

## Command Reference

### Essential Commands

```cisco
! View dynamic object configuration
show run dynamic-object

! Check dynamic object status
show dynamic-object update-url

! View current IPs in dynamic object
show dynamic-object Vesper-DIPL

! Force immediate update
clear dynamic-object Vesper-DIPL

! Test update manually
test dynamic-object update Vesper-DIPL

! View object group
show run object-group id Vesper-Allowed-IPs

! Check ACL hits
show access-list outside_access_in

! View logs
show logging | grep dynamic-object
```

---

## Next Steps

After Cisco ASA/FTD integration:

1. **[Configure User Management](../user-management.md)** - Monitor user registrations  
2. **[Test with pilot users](../user-management.md#onboarding-new-users)** - Validate the complete flow  
3. **[Review best practices](../../resources/best-practices.md)** - Optimize your deployment  

---

## Additional Resources

- [Back to Firewall Integration Overview](index.md)
- [Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

### Cisco Documentation

- [Dynamic Objects Configuration Guide](https://www.cisco.com/c/en/us/support/security/adaptive-security-appliance-asa-software/products-installation-and-configuration-guides-list.html)
- [Access Control Lists Configuration](https://www.cisco.com/c/en/us/support/security/adaptive-security-appliance-asa-software/products-installation-and-configuration-guides-list.html)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
