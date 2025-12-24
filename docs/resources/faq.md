# Frequently Asked Questions

Common questions about Vesper Secure for administrators and general information.

---

## General Questions

### What is Vesper Secure?

Vesper Secure is a dynamic IP list management system that serves approved IP addresses via a web service for firewall ACL ingestion. Users register their locations, administrators approve them, and firewalls automatically update access control lists based on the approved IP list.

### How does it work?

1. Users register their location through the access portal
2. Administrators review and approve requests
3. Approved IPs are added to the External Dynamic List (EDL)
4. Your firewall polls the EDL and automatically updates ACLs
5. Only approved IPs can access protected resources

### What makes Vesper Secure different from static IP lists?

**Static IP Lists:**
- Manual updates required
- Error-prone
- Slow to add/remove access
- No self-service for users

**Vesper Secure:**
- Automatic firewall updates
- Self-service user registration
- Approval workflows
- Audit logs and compliance
- Temporary access management

## For Administrators

### Which firewalls are supported?

Vesper Secure works with any firewall that supports External Dynamic Lists (EDL) or URL-based IP lists, including:
- Palo Alto Networks
- Fortinet FortiGate
- Cisco ASA/FTD
- Check Point
- pfSense/OPNsense

### How often does the EDL update?

You can configure the refresh interval on your firewall. We recommend 5 minutes for responsive access, or hourly for reduced load.

### Can I auto-approve requests?

Yes! You can configure automatic approval for permanent locations, temporary locations, or both in the admin panel settings.

### How do I handle emergency access?

You can manually approve requests instantly in the admin panel, or maintain a backup static IP allow-list for critical admin users.

## For End Users

### How do I register my location?

See the [User Guide](../user/get-ip-added.md) for detailed instructions. Quick version:
1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"}
2. Log in with corporate credentials
3. Choose Permanent or Temporary
4. Wait 5 minutes for approval

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

[Back to Home](../index.md) • [Admin Setup](../admin-setup.md) • [User Instructions](../user-instructions.md)
