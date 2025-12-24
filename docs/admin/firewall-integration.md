<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# Firewall Integration

Integrate Vesper Secure's External Dynamic List (EDL) with your firewall to automate ACL updates.

</div>

---

## Overview

The External Dynamic List (EDL) is an HTTPS endpoint that serves approved IP addresses. Your firewall polls this endpoint and automatically updates access control lists.

### Supported Firewalls

- ✅ Palo Alto Networks (PAN-OS 8.0+)
- ✅ Fortinet FortiGate (FortiOS 6.0+)
- ✅ Cisco ASA/FTD (9.8+)
- ✅ Check Point (R80.20+)
- ✅ pfSense/OPNsense
- ✅ Any firewall supporting HTTP/HTTPS IP lists

---

## Before You Begin

Ensure you have:

- [ ] EDL URL, username, and password from [Setup & Configuration](setup.md)
- [ ] Firewall admin access
- [ ] Outbound HTTPS (port 443) allowed from firewall to `edl.vespersecure.com`

---

## Palo Alto Networks

### 1. Add External Dynamic List

1. Log in to Palo Alto web interface
2. Go to **Objects** → **External Dynamic Lists**
3. Click **Add**

Configure the EDL:

```
Name: Vesper-Secure-Allowed-IPs
Type: IP List
Source URL: https://edl.vespersecure.com/lists/your-id
Check for updates: Hourly (or every 5 minutes)
Username: org_xxxxx
Password: ••••••••
```

4. Click **OK**

### 2. Test EDL Connectivity

1. Select your EDL object
2. Click **Test Source**
3. Verify "Success" message or IP list appears

### 3. Create Security Policy

1. Go to **Policies** → **Security**
2. Add new allow policy:

```
Name: Allow-Vesper-Verified-VPN-Access
Source Zone: untrust
Source Address: Vesper-Secure-Allowed-IPs
Destination Zone: vpn-gateway
Destination Address: your-vpn-gateway-ip
Application: ssl, ipsec-esp, ipsec-ah
Service: application-default
Action: Allow
Log at Session End: Yes
```

3. Place **above** any deny rules
4. Commit changes

### 4. Create Block Policy (Recommended)

Add a deny rule below the allow rule to explicitly block unverified IPs:

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

---

## Fortinet FortiGate

### 1. Create External Resource

1. Go to **Security Fabric** → **External Connectors**
2. Click **Create New** → **IP Address**

Configure:

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

### 2. Create Address Object

1. Go to **Policy & Objects** → **Addresses**
2. Create new address:

```
Name: Vesper-Allowed-IPs
Type: Dynamic - IP Address
Sub-type: Fabric Connector Address
Connector: Vesper_Secure_EDL
```

### 3. Create Firewall Policy

```
Name: Allow-Vesper-Verified-Users
Incoming Interface: wan1
Outgoing Interface: vpn
Source: Vesper-Allowed-IPs
Destination: VPN-Gateway-IP
Service: HTTPS, IPSEC
Action: Accept
Log Allowed Traffic: All Sessions
```

---

## Cisco ASA/FTD

Configure via CLI:

```cisco
configure terminal

! Create dynamic object group
object-group network Vesper-Allowed-IPs
  description Vesper Secure verified user IPs

! Configure EDL
dynamic-object update-url https://edl.vespersecure.com/lists/your-id
  poll-period 5
  basic-auth username org_xxxxx password your-password

! Apply to object group
object-group network Vesper-Allowed-IPs
  dynamic-object your-edl-name

! Create ACL
access-list outside_access_in extended permit tcp object-group Vesper-Allowed-IPs any eq https
access-list outside_access_in extended permit udp object-group Vesper-Allowed-IPs any eq isakmp
access-list outside_access_in extended permit esp object-group Vesper-Allowed-IPs any

access-group outside_access_in in interface outside
```

---

## Generic Firewall (URL-based IP Lists)

For firewalls supporting URL-based IP lists:

1. Add URL source: `https://edl.vespersecure.com/lists/your-id`
2. Configure basic authentication (username/password)
3. Set update interval: 5-60 minutes
4. Create alias/object referencing the URL table
5. Create allow rule using the alias

---

## Validation and Testing

### Test EDL Connectivity

From a system with network access:

```bash
curl -u "org_xxxxx:your-password" \
  https://edl.vespersecure.com/lists/your-id
```

Should return IP addresses or an empty list.

### Test User Access Flow

1. Have a test user submit access request through Vesper portal
2. Approve the request in admin panel
3. Wait for EDL refresh interval (e.g., 5 minutes)
4. Verify test user's IP appears in EDL:
   ```bash
   curl -u "user:pass" https://edl.vespersecure.com/lists/your-id | grep "test-user-ip"
   ```
5. Test VPN connection from test user's location (should succeed)

### Verify Blocking

1. Try VPN connection from an unregistered IP address
2. Should be blocked by firewall
3. Check firewall logs for deny entry

---

## Best Practices

!!! tip "EDL Refresh Interval"
    - **5 minutes**: Most responsive, higher firewall load
    - **15 minutes**: Balanced approach (recommended)
    - **1 hour**: Lower load, slower access grants

!!! warning "Policy Ordering Critical"
    Always place Vesper allow rules **above** general deny rules. Incorrect ordering will block all traffic.

!!! info "Enable Logging"
    Log both allowed and denied attempts for security auditing and troubleshooting.

!!! success "Backup Access"
    Maintain static IP allow-list entries for critical administrators as backup in case EDL is unavailable.

---

## Troubleshooting

### EDL Update Failures

**Symptoms:** Firewall shows EDL as "Failed"

**Solutions:**
1. Verify firewall can reach `edl.vespersecure.com` on port 443
2. Check DNS resolution works
3. Verify no SSL inspection blocking connection
4. Test manually with curl command

### Authentication Errors

**Symptoms:** EDL returns 401 Unauthorized

**Solutions:**
1. Double-check username and password
2. Ensure no extra spaces in credentials
3. Re-generate credentials in admin panel if needed
4. Test with: `curl -v -u "user:pass" URL`

### User Blocked After Approval

**Symptoms:** User approved but still can't access

**Solutions:**
1. Wait for EDL refresh interval
2. Force manual EDL refresh on firewall
3. Verify firewall policy order (allow above deny)
4. Check user's current IP matches approved IP
5. Verify user's access hasn't expired

---

## Next Steps

After firewall integration:

1. **[Configure User Management](user-management.md)** - Set up approval workflows
2. **Test with pilot users** - Validate the complete flow
3. **Monitor and adjust** - Review logs and optimize policies

---

## Additional Resources

- [Back to Administrator Guide](index.md)
- [Troubleshooting Guide](../resources/troubleshooting.md)
- [Best Practices](../resources/best-practices.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
