# User Management

Monitor user registrations, manage locations, and revoke access when needed.

---

## Overview

As an administrator, you'll monitor user registrations through the Vesper Secure admin panel. This includes reviewing active locations, monitoring usage patterns, and revoking access when needed. User IP addresses are automatically added to the EDL upon registration - no approval is required.

---

## Monitor User Registrations

### View Recent Registrations

1. Log in to [app.vespersecure.com](https://app.vespersecure.com)  
2. Go to **Registrations** or check the dashboard for recent activity  
3. Review each registration showing:  
   - User email/name
   - IP address
   - Location type (permanent or temporary)
   - Registration timestamp
   - Reason (if provided)

### Review for Unusual Activity

Monitor registrations for:
- Unusual geographic locations
- Multiple registrations from the same IP
- Suspicious patterns or timing
- Requests from unexpected countries

If you notice suspicious activity, you can revoke the registration immediately.

---

## Monitor Active Users

### View Active Locations

1. Go to **Users** → **Active Locations**  
2. See all currently registered locations:  
   - User information
   - IP address
   - Location type
   - Registration date
   - Expiration date (for temporary)

### Filter and Search

- **Filter by type**: Permanent or Temporary
- **Filter by expiration**: Expiring soon, expired
- **Search by user**: Find specific user's locations
- **Search by IP**: Locate specific IP address

---

## Remove Users

To remove a user and revoke their access:

1. Go to **Users**  
2. Find the user you want to remove  
3. Click **Delete** next to the user  
4. Confirm the deletion  
5. User and all their registered locations are removed immediately  

!!! warning "Immediate Effect"
    After deletion, the user's IPs will be removed from the EDL at the next firewall refresh (typically 5-60 minutes).

---

## User Lifecycle Management

### Onboarding New Users

1. Ensure user is added to SSO group (if using SSO)  
2. User receives access to portal automatically  
3. User registers their location(s)  
4. User's IP is automatically added to EDL  
5. User gains VPN access after firewall refresh  

### Offboarding Departing Users

1. Go to **Users**  
2. Find the user  
3. Click **Delete** to remove them  
4. Confirm the deletion  
5. Verify removal from active users list  

---

## View Audit Logs

Track all actions for compliance and security:

1. Go to **Reports** → **Audit Log**  
2. View logs of all activities:  
   - User registrations submitted
   - Location revocations
   - Admin actions
   - Configuration changes

### Export Logs

- Click **Export** to download logs
- Choose format: CSV or JSON
- Use for compliance reporting or analysis

---

## Monitoring and Alerts

### Enable Email Alerts

Configure alerts in **Settings** → **Notifications**:

- New user registrations
- EDL update failures
- Unusual access patterns
- Failed login attempts
- User account changes

### Dashboard Metrics

The dashboard shows:
- Recent registrations count
- Active locations count
- Recent revocations
- EDL sync status
- User activity trends

---

## Common Management Tasks

### Handle Emergency Access

If a user needs immediate access:

1. User registers their location through the portal  
2. Force EDL refresh on your firewall (if supported)  
3. Or wait for next scheduled refresh  
4. User can connect after refresh  

---

## Best Practices

!!! success "Regular Reviews"
    - Review active locations weekly
    - Audit user list monthly against employee roster
    - Clean up expired temporary locations

!!! info "Communication"
    - Notify users when removing access (include reason)
    - Set expectations for firewall refresh timeframes
    - Provide user guide link in communications

!!! warning "Security"
    - Monitor for unusual patterns (many registrations from one IP)
    - Investigate registrations from unexpected countries

!!! tip "Monitoring"
    - Set reasonable temporary durations (24 hours typical)
    - Enable notifications for new registrations
    - Review active locations regularly

---

## Troubleshooting

### User Not Seeing Access After Registration

1. Check EDL sync status (should show "Success")  
2. Verify user's IP is in the EDL  
3. Confirm firewall has refreshed  
4. Check firewall policy order  
5. Review firewall logs for blocks  

### Can't Find User's Registration

1. Check if registration is in active locations  
2. Look in audit logs for the registration  
3. Verify user submitted from correct portal  
4. Check user is in correct SSO group  

### Location Expired Unexpectedly

1. Review policy settings for temporary durations  
2. Check if admin deleted the user  
3. Verify expiration dates were set correctly  
4. Review audit logs for changes  

---

## Next Steps

Now that you've configured user management:

1. **Test the complete flow** with pilot users  
2. **Distribute user guide** - Share the [User Guide](../user/index.md)  
3. **Monitor initial usage** - Be available for questions  
4. **Review patterns** - Watch for unusual activity  

---

## Additional Resources

- [Back to Administrator Guide](./index.md)
- [User Guide](../user/index.md) - Share this with end users
- [Troubleshooting Guide](../resources/troubleshooting.md)
- [Best Practices](../resources/best-practices.md)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
