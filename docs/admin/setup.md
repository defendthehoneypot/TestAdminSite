# Setup & Configuration

Initial setup and configuration of Vesper Secure for your organization.

---

## Step 1: Access the Admin Panel

1. Navigate to [app.vespersecure.com](https://app.vespersecure.com)  
2. Sign in with your administrator credentials  
3. Complete initial setup wizard if this is your first login  

---

## Step 2: Configure Organization Policies

Define how Vesper Secure handles user registrations:

1. Navigate to **Settings** → **Policies**  
2. Configure registration settings:  

```
□ Send notification for new registrations
□ Enable monitoring alerts for unusual patterns
□ Restrict registrations to specific countries
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

---

## Step 3: Generate EDL Credentials

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

## Next Steps

After completing basic setup:

1. **[Configure SSO Integration](sso-integration/index.md)** - Connect your identity provider  
2. **[Set up Firewall Integration](firewall-integration/index.md)** - Add EDL to your firewall  
3. **[Start Managing Users](./user-management.md)** - Begin monitoring user registrations  

---

## Additional Resources

- [Back to Administrator Guide](./index.md)
- [Troubleshooting Guide](../resources/troubleshooting.md)
- [Best Practices](../resources/best-practices.md)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
