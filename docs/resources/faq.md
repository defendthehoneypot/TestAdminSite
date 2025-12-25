# Frequently Asked Questions

Common questions about Vesper Secure for administrators and general information.

---

## General Questions

### What is Vesper Secure?

Vesper Secure is a dynamic IP list management system that serves registered IP addresses via a web service for firewall ACL ingestion. Users register their locations, IPs are automatically added to the list, and firewalls automatically update access control lists based on the registered IP list.

### How does it work?

1. Users register their location through the access portal  
2. IPs are automatically added to the Dynamic IP List (DIPL)  
3. Your firewall polls the DIPL and automatically updates ACLs  
4. Administrators can monitor and revoke access if needed  
5. Only registered IPs can access protected resources  

### What makes Vesper Secure different from static IP lists?

**Static IP Lists:**
- Manual updates required
- Error-prone
- Slow to add/remove access
- No self-service for users

**Vesper Secure:**
- Automatic firewall updates
- Self-service user registration
- Automatic IP addition to DIPL
- Audit logs and compliance
- Temporary access management

## For Administrators

### Which firewalls are supported?

Vesper Secure works with any firewall that supports Dynamic IP Lists (DIPL) or URL-based IP lists, including:

- Palo Alto Networks (External Dynamic Lists)
- Fortinet FortiGate (External Connectors)
- Cisco ASA/FTD (Dynamic Objects)
- Check Point R80.20+ (Updatable Objects)
- pfSense 2.4+ (URL Table Aliases / pfBlockerNG)
- OPNsense 19.1+ (URL Table Aliases)
- Juniper SRX (Dynamic Address Objects)

**Not supported:** Sophos XG Firewall, WatchGuard, SonicWall, and Zyxel do not have comparable DIPL functionality for custom IP lists. These firewalls require manual IP list management and are not recommended for use with Vesper Secure.

### How often does the DIPL update?

You can configure the refresh interval on your firewall. We recommend 5 minutes for responsive access, or hourly for reduced load.

### How can admins monitor registrations?

Administrators can monitor all registrations through the admin panel and receive alerts for new registrations or unusual patterns. They can revoke access at any time if needed.

### How do I handle emergency access?

Users can register immediately through the portal. If you need faster access than the normal firewall refresh interval, you can force a firewall refresh manually (if your firewall supports it), or maintain a backup static IP allow-list for critical admin users.

## For End Users

### How do I register my location?

See the [User Guide](../user/get-ip-added.md) for detailed instructions. Quick version:
1. Visit [access.vespersecure.com](https://access.vespersecure.com)  
2. Log in with corporate credentials  
3. Choose Permanent or Temporary  
4. Wait for firewall refresh (typically 5-60 minutes)  

### Do I need to register every time I connect?

No - only when you're in a new location:
- **Permanent locations** (home, office): Register once, works indefinitely
- **Temporary locations** (hotel, coffee shop): Register each time, expires after configured duration

### What if I forget to register before traveling?

Just visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"} from your new location, complete the registration, and wait 5 minutes. See the [User Guide](../user/index.md) for more details.

### Can I use this on mobile devices?

Yes! The access portal works on phones and tablets.

### Who do I contact for help?

**End users:** Contact your organization's IT administrator

**Administrators:** 
- Check the [Troubleshooting Guide](troubleshooting.md)
- Contact Vesper support at support@vespersecure.com
- Submit ticket via admin panel

---

[Back to Home](../index.md) • [Administrator Guide](../admin/index.md) • [User Guide](../user/index.md)

### What information is collected?

Vesper only sees:
- Your corporate email/identity
- Your IP address
- Location type (permanent/temporary)
- Registration timestamp

### Does this affect VPN performance?

No - Vesper only controls access. Once connected, VPN performance is unchanged.

### What happens if Vesper is unavailable?

Your firewall will continue using the last cached IP list. Configure backup access methods for critical users.

### Can users have multiple locations?

Yes! Users can have multiple permanent locations and register temporary locations as needed.

---

[Back to Home](../index.md) • [Administrator Guide](../admin/index.md) • [User Guide](../user/index.md)
