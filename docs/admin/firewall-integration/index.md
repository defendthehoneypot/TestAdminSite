# Firewall Integration Overview

Integrate Vesper Secure's Dynamic IP List with your firewall to automate ACL updates.

---

## What is Firewall Integration?

The Dynamic IP List is an HTTPS endpoint that serves registered IP addresses. Your firewall polls this endpoint and automatically updates access control lists.

### How It Works

1. **Users Register**: Users register their IP addresses through the Vesper Secure portal  
2. **Dynamic IP List Updates**: IP addresses are automatically added to the Dynamic IP List  
3. **Firewall Polls**: Your firewall periodically checks the Dynamic IP List for updates  
4. **ACLs Update**: Firewall automatically updates access control lists  
5. **Access Granted**: Only registered IPs can access protected resources  

### Benefits

- **Automated Access Control**: No manual firewall updates required
- **Self-Service**: Users register their own locations
- **Real-Time Updates**: Changes reflected within minutes
- **Audit Trail**: Complete logs of all access grants
- **Temporary Access**: Built-in support for time-limited locations

---

## Supported Firewalls

Vesper Secure works with any firewall that supports Dynamic IP Lists or URL-based IP lists:

### Enterprise Firewalls

**[Palo Alto Networks (PAN-OS 8.0+)](palo-alto.md)**

Industry-leading next-generation firewall with native External Dynamic List support.

- External Dynamic Lists
- Test Source functionality
- Security policy integration
- Comprehensive logging

**[Fortinet FortiGate (FortiOS 6.0+)](fortinet.md)**

Popular enterprise firewall with External Connector support.

- External Connectors (Fabric)
- Dynamic address objects
- Flexible refresh intervals
- Policy integration

**[Cisco ASA/FTD (9.8+)](cisco.md)**

Cisco's security appliances with dynamic object group support.

- Dynamic object groups
- CLI-based configuration
- URL-based IP lists
- ACL integration

**[Check Point (R80.20+)](check-point.md)**

Enterprise firewall with Updatable Objects support.

- Updatable Objects (formerly External Objects)
- HTTP/HTTPS URL-based IP lists
- Periodic automatic updates
- SmartConsole configuration

**[Juniper SRX (JunOS 15.1X49+)](juniper-srx.md)**

Enterprise security gateway with dynamic address object support.

- Dynamic Address Objects
- Security Intelligence feeds
- URL-based threat intelligence
- CLI and J-Web configuration

### Open Source Firewalls

**[pfSense (2.4+)](pfsense.md)**

Popular open-source firewall with URL Table Alias support.

- URL Table Aliases
- pfBlockerNG package for enhanced functionality
- Native HTTP/HTTPS IP list support
- Flexible refresh intervals

**[OPNsense (19.1+)](opnsense.md)**

Open-source firewall based on FreeBSD with native URL alias support.

- URL Table Aliases
- Built-in periodic refresh
- HTTP/HTTPS authentication support
- Web UI and CLI configuration

### Other Firewalls

**[Generic/Other Firewalls](generic.md)**

Any firewall supporting URL-based IP lists can work with Vesper Secure. Check the generic guide for general configuration steps.

!!! success "Fully Supported Platforms"
    All firewalls listed above have been tested and verified to work with Vesper Secure's Dynamic IP List functionality.

!!! info "Need Help with Your Firewall?"
    If your firewall supports URL-based IP lists but isn't listed here, check the [Generic Firewall Guide](generic.md) for general configuration steps that apply to most firewalls.

---

## Unsupported Firewalls

The following firewalls do **NOT** have comparable Dynamic IP List functionality for custom IP lists and are **not recommended** for use with Vesper Secure:

### Firewalls Without Dynamic IP List Support

**Sophos XG Firewall**

- Limited to vendor threat intelligence feeds only
- Does not support custom URL-based IP lists
- No external dynamic list functionality

**WatchGuard**

- No native URL-based dynamic IP list support
- Limited to static IP configurations
- Requires manual IP list management

**SonicWall**

- Does not support custom external dynamic lists
- Limited to vendor-provided security services
- No HTTP/HTTPS IP list ingestion

**Zyxel**

- No comparable Dynamic IP List functionality
- Lacks URL-based IP list support
- Static configuration only

!!! warning "Alternative Approaches"
    If you're using one of these unsupported firewalls, consider:
    
    - Manually exporting IPs from Vesper and updating firewall rules periodically
    - Using a script to fetch the Dynamic IP List and update firewall via API (if available)
    - Upgrading to a firewall platform with native Dynamic IP List support
    - See the [Generic Firewall Guide](generic.md) for potential workarounds

---

## General Prerequisites

Before configuring any firewall integration, ensure you have:

- [ ] **Dynamic IP List URL, username, and password** from [Setup & Configuration](../setup.md)
- [ ] **Firewall admin access** with permission to modify policies
- [ ] **Outbound HTTPS access** from firewall to `list.vespersecure.com` (port 443)
- [ ] **DNS resolution** working from firewall management interface
- [ ] **Certificate validation** working (no SSL inspection blocking)

---

## Setup Guides

Choose your firewall platform to get started:

### Palo Alto Networks

**[Configure Palo Alto Networks →](palo-alto.md)**

Complete guide for PAN-OS firewalls including External Dynamic Lists, security policies, and testing.

**Estimated time**: 15-20 minutes

### Fortinet FortiGate

**[Configure Fortinet FortiGate →](fortinet.md)**

Complete guide for FortiGate firewalls including External Connectors, address objects, and policies.

**Estimated time**: 15-20 minutes

### Cisco ASA/FTD

**[Configure Cisco ASA/FTD →](cisco.md)**

Complete guide for Cisco firewalls including dynamic object groups and ACL configuration.

**Estimated time**: 20-25 minutes

### Check Point

**[Configure Check Point →](check-point.md)**

Complete guide for Check Point firewalls including Updatable Objects and security policies.

**Estimated time**: 15-20 minutes

### pfSense

**[Configure pfSense →](pfsense.md)**

Complete guide for pfSense including URL Table Aliases and pfBlockerNG setup.

**Estimated time**: 15-20 minutes

### OPNsense

**[Configure OPNsense →](opnsense.md)**

Complete guide for OPNsense including URL Table Aliases and firewall rules.

**Estimated time**: 15-20 minutes

### Juniper SRX

**[Configure Juniper SRX →](juniper-srx.md)**

Complete guide for Juniper SRX including Dynamic Address Objects and security policies.

**Estimated time**: 20-25 minutes

### Generic/Other Firewalls

**[Configure Generic Firewall →](generic.md)**

General configuration steps for any firewall supporting URL-based IP lists.

**Estimated time**: 15-30 minutes (varies by platform)

---

## General Best Practices

!!! tip "Dynamic IP List Refresh Interval"
    - **5 minutes**: Most responsive, higher firewall load
    - **15 minutes**: Balanced approach (recommended)
    - **1 hour**: Lower load, slower access grants

!!! warning "Policy Ordering Critical"
    Always place Vesper allow rules **above** general deny rules. Incorrect ordering will block all traffic.

!!! info "Enable Logging"
    Log both allowed and denied attempts for security auditing and troubleshooting.

!!! success "Backup Access"
    Maintain static IP allow-list entries for critical administrators as backup in case Dynamic IP List is unavailable.

---

## Validation and Testing

After configuring your firewall, validate the integration:

### Test Dynamic IP List Connectivity

From a system with network access:

```bash
curl -u "organizationid:your-password" \
  https://list.vespersecure.com/organizationid
```

Should return IP addresses or an empty list.

### Test User Access Flow

1. Have a test user register through Vesper portal  
2. User's IP is automatically added to Dynamic IP List  
3. Wait for Dynamic IP List refresh interval (e.g., 5 minutes)  
4. Verify test user's IP appears in Dynamic IP List:  
   ```bash
   curl -u "user:pass" https://list.vespersecure.com/organizationid | grep "test-user-ip"
   ```
5. Test VPN connection from test user's location (should succeed)  

### Verify Blocking

1. Try VPN connection from an unregistered IP address  
2. Should be blocked by firewall  
3. Check firewall logs for deny entry  

---

## Troubleshooting

For platform-specific troubleshooting, see the individual setup guides:

- [Palo Alto Networks Troubleshooting](palo-alto.md#troubleshooting)
- [Fortinet FortiGate Troubleshooting](fortinet.md#troubleshooting)
- [Cisco ASA/FTD Troubleshooting](cisco.md#troubleshooting)
- [Generic Firewall Troubleshooting](generic.md#troubleshooting)

### Common Issues Across All Platforms

**Dynamic IP List shows as "Failed" or "Unreachable"**

- Verify firewall can reach `list.vespersecure.com` on port 443
- Check DNS resolution works
- Verify no SSL inspection blocking connection
- Test manually with curl command

**Authentication Errors (401 Unauthorized)**

- Double-check username and password
- Ensure no extra spaces in credentials
- Re-generate credentials in admin panel if needed
- Test with: `curl -v -u "user:pass" URL`

**User Blocked After Registration**

- Wait for Dynamic IP List refresh interval
- Force manual Dynamic IP List refresh on firewall
- Verify firewall policy order (allow above deny)
- Check user's current IP matches registered IP
- Verify user's access hasn't expired

---

## Next Steps

After firewall integration:

1. **[Configure User Management](../user-management.md)** - Set up monitoring workflows  
2. **Test with pilot users** - Validate the complete flow  
3. **Monitor and adjust** - Review logs and optimize policies  

---

## Additional Resources

- [Back to Administrator Guide](../index.md)
- [Setup & Configuration](../setup.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
