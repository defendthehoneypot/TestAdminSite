# Frequently Asked Questions

## General Questions

### What is Vesper Secure?

Vesper Secure is a VPN defense and access control solution that verifies user locations before allowing VPN access. It uses External Dynamic Lists (EDL) to automatically update firewall rules based on verified user locations.

### How does it work?

1. Users register their location through the access portal
2. Vesper verifies the location and adds the IP to the approved list
3. Your firewall pulls this list and allows traffic from approved IPs only
4. Unregistered locations are automatically blocked

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

### Do I need to register every time I connect?

No - only when you're in a new location:
- **Permanent locations** (home, office): Register once, works forever
- **Temporary locations** (hotel, coffee shop): Register each time

### What if I forget to register before traveling?

Just visit access.vespersecure.com from your new location, complete the registration, and wait 5 minutes.

### Can I use this on mobile devices?

Yes! The access portal works on phones and tablets.

### Who do I contact for help?

Contact your organization's admin - they can troubleshoot and escalate to Vesper support if needed.

## Technical Questions

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
