<div class="hero" markdown>

<p class="badge">ADMIN SETUP GUIDE • COMPLETE CONFIGURATION</p>

# Administrator Setup Guide

Complete guide for configuring Vesper Secure, integrating with your firewall, and managing your deployment.

</div>

---

## Overview

This guide covers everything administrators need to deploy and manage Vesper Secure in your environment. You'll configure authentication, integrate with your firewall, and set up policies for your users.

<div class="grid cards" markdown>

-   <span class="icon-badge">⚙️</span>
    
    **Configure Vesper Secure**
    
    Set up authentication, policies, and generate firewall credentials.
    
-   <span class="icon-badge">🧱</span>
    
    **Firewall Integration**
    
    Add Vesper as an External Dynamic List (EDL) to your firewall.

-   <span class="icon-badge">👥</span>
    
    **User Management**
    
    Manage users, approve requests, and monitor access.

</div>

---

## Prerequisites

Before you begin:

- [ ] Admin access to the [Vesper Secure admin panel](https://app.vespersecure.com){:target="_blank"}
- [ ] Firewall admin access (Palo Alto, Fortinet, Cisco, or similar)
- [ ] Microsoft Entra ID (Azure AD) tenant admin rights (optional, for SSO)
- [ ] List of users who will need VPN access

---

## Part 1: Configure Vesper Secure

### Step 1: Access the Admin Panel

1. Navigate to [app.vespersecure.com](https://app.vespersecure.com){:target="_blank"}
2. Sign in with your administrator credentials
3. Complete initial setup if this is your first login

### Step 2: Choose Authentication Method

Vesper Secure supports two authentication methods:

#### Option A: Microsoft Entra ID (Recommended)

Automatically sync users from your Microsoft Entra ID (Azure AD) tenant.

**Benefits:**
- Automatic user provisioning
- Single sign-on (SSO) support  
- Centralized user management
- MFA enforcement

**Configuration:**

1. Go to **Settings** → **Authentication**
2. Click **Configure Entra ID**
3. Enter your Azure AD tenant ID
4. Grant required permissions when prompted
5. Select which groups should have access
6. Click **Save Configuration**

!!! success "Recommended"
    Entra ID integration provides the best user experience and simplifies administration.

#### Option B: Manual User Management

Manually add and manage users within Vesper Secure.

**Use cases:**
- Organizations not using Microsoft Entra ID
- Small deployments
- Testing environments

**Configuration:**

1. Go to **Users** → **Add Users**
2. Enter user email addresses (one per line)
3. Assign roles and permissions
4. Click **Create Users**
5. Users will receive email invitations

### Step 3: Configure Organization Policies

Define how Vesper Secure handles access requests:

1. Navigate to **Settings** → **Policies**
2. Configure approval settings:

```
□ Auto-approve permanent location requests
□ Auto-approve temporary location requests  
□ Require admin approval for all requests
□ Send notification for new requests
```

3. Set location settings:

```
Temporary location default duration: [24 hours ▼]
Maximum temporary duration: [30 days ▼]
□ Allow users to extend temporary access
□ Require reason for temporary access
```

4. Configure security settings:

```
□ Require MFA for access requests
□ Block known VPN/proxy IPs
□ Restrict to specific countries
□ Enable geo-fencing alerts
```

5. Click **Save Policies**

### Step 4: Generate List URL and Credentials

Create the credentials needed for firewall integration:

1. Go to **Settings** → **Firewall Integration**
2. Click **Generate External Dynamic List (EDL)**
3. Save these credentials securely:

```
List URL: https://edl.vespersecure.com/lists/abc123def456
Username: org_abc123
Password: •••••••••••••••••••••
```

!!! warning "Important"
    Save these credentials! You'll need them for firewall integration.
    The password is only displayed once and cannot be recovered.

**Test the EDL:**

```bash
curl -u "org_abc123:your-password" \
  https://edl.vespersecure.com/lists/abc123def456
```

You should see an empty list or IP addresses if users have already registered.

---

## Part 2: Firewall Integration

Add Vesper's External Dynamic List to your firewall to automatically allow verified IPs.

### Supported Firewalls

- ✅ Palo Alto Networks (PAN-OS 8.0+)
- ✅ Fortinet FortiGate (FortiOS 6.0+)
- ✅ Cisco ASA/FTD (9.8+)
- ✅ Check Point (R80.20+)
- ✅ pfSense/OPNsense
- ✅ Any firewall supporting HTTP/HTTPS IP lists

### Palo Alto Networks Configuration

#### 1. Add External Dynamic List

1. Log in to Palo Alto web interface
2. Go to **Objects** → **External Dynamic Lists**
3. Click **Add**

Configure:

```
Name: Vesper-Secure-Allowed-IPs
Type: IP List
Source URL: https://edl.vespersecure.com/lists/your-id
Check for updates: Hourly (or every 5 minutes)
Username: org_xxxxx
Password: ••••••••
```

4. Click **OK**

#### 2. Test EDL Connectivity

1. Select your EDL object
2. Click **Test Source**
3. Verify "Success" message or IP list

#### 3. Create Security Policy

1. Go to **Policies** → **Security**
2. Add new policy:

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

#### 4. Create Block Policy (Recommended)

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

Place **below** allow rule, **above** general denies.

---

### Fortinet FortiGate Configuration

#### 1. Create External Resource

1. Go to **Security Fabric** → **External Connectors**
2. Click **Create New** → **IP Address**

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

#### 2. Create Address Object

1. Go to **Policy & Objects** → **Addresses**
2. Create new address:

```
Name: Vesper-Allowed-IPs
Type: Dynamic - IP Address
Sub-type: Fabric Connector Address
Connector: Vesper_Secure_EDL
```

#### 3. Create Firewall Policy

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

### Cisco ASA/FTD Configuration

Via CLI:

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

### Generic Firewall (URL-based)

For firewalls supporting URL-based IP lists:

1. Add URL source: `https://edl.vespersecure.com/lists/your-id`
2. Configure basic auth (username/password)
3. Set update interval: 5-60 minutes
4. Create alias/object referencing the URL table
5. Create allow rule using the alias

---

## Validation and Testing

### Test EDL Connectivity

```bash
curl -u "org_xxxxx:your-password" \
  https://edl.vespersecure.com/lists/your-id
```

Should return IP addresses or empty list.

### Test User Access

1. Have test user submit access request through Vesper
2. Wait 5 minutes for firewall EDL refresh
3. Check EDL object for test user's IP
4. Test VPN connection (should succeed)

### Verify Blocking

1. Try VPN from unregistered IP
2. Should be blocked by firewall
3. Check firewall logs for deny entry

---

## Part 3: User Management

### Approve Access Requests

1. Go to **Requests** in admin panel
2. Review pending requests
3. Approve or deny with optional note

### Monitor Active Users

1. Go to **Users** → **Active Locations**
2. View all registered locations
3. See when each location expires (for temporary)

### Revoke Access

To immediately revoke a user's access:

1. Go to **Users**
2. Find the user
3. Click **Revoke All Locations**
4. Wait for next EDL refresh (up to 5 minutes)

### View Audit Logs

1. Go to **Reports** → **Audit Log**
2. See all access requests, approvals, and changes
3. Export logs for compliance

---

## Monitoring and Maintenance

### Regular Tasks

**Daily:**
- Review new access requests
- Check for failed EDL updates

**Weekly:**
- Review temporary locations nearing expiration
- Check firewall logs for blocked attempts

**Monthly:**
- Audit user list (remove departed employees)
- Review and update policies
- Check EDL credentials are working

### Enable Alerts

Configure email alerts for:
- New access requests (if manual approval enabled)
- EDL update failures
- Unusual access patterns
- Failed login attempts

---

## Troubleshooting

### EDL Update Failures

**Symptoms:** Firewall shows EDL as "Failed"

**Solutions:**
1. Verify firewall can reach `edl.vespersecure.com` on port 443
2. Check DNS resolution
3. Verify no SSL inspection blocking connection
4. Test manually with curl

### 401 Unauthorized Errors

**Symptoms:** EDL returns authentication error

**Solutions:**
1. Double-check username and password
2. Ensure no extra spaces in credentials
3. Re-generate credentials in admin panel
4. Test with: `curl -v -u "user:pass" URL`

### User Can't Access After Approval

**Symptoms:** User approved but still blocked

**Solutions:**
1. Wait for EDL refresh (up to refresh interval)
2. Force manual EDL refresh on firewall
3. Verify firewall policy order (allow above deny)
4. Check user's IP matches EDL
5. Verify user's access hasn't expired

### EDL Empty Despite Approved Users

**Symptoms:** No IPs in EDL but users are approved

**Solutions:**
1. Verify requests are actually approved
2. Check temporary locations haven't expired
3. Force EDL refresh
4. Check admin panel shows locations

---

## Best Practices

!!! tip "EDL Refresh Interval"
    Set to 5 minutes for responsive access, hourly for reduced load.

!!! warning "Policy Ordering"
    Always place Vesper allow rules **above** general deny rules.

!!! info "Logging"
    Log both allowed and denied attempts for security auditing.

!!! success "Backup Access"
    Add static IPs for critical admin users as backup.

!!! note "User Communication"
    Clearly communicate the process to users before rollout.

---

## Next Steps

After completing admin setup:

1. **Test the configuration** with a test user account
2. **Distribute user instructions** - Share the [User Instructions](user-instructions.md) page
3. **Monitor initial rollout** - Be available for user questions
4. **Review and adjust** - Fine-tune policies based on usage

---

## Additional Resources

- [User Instructions for End Users](user-instructions.md)
- [FAQ](resources/faq.md)
- [Best Practices](resources/best-practices.md)
- [Troubleshooting Guide](resources/troubleshooting.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
