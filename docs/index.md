# Vesper Secure

---

## What is Vesper Secure?

Vesper Secure provides a simple solution to a common problem: **managing which IP addresses can access your VPN or protected resources**.

Instead of manually maintaining static IP allow-lists, Vesper Secure:

- Provides a **web portal** where users register their IP addresses
- Creates a **dynamic Dynamic IP List** of registered IPs
- Serves this list via **HTTPS** for your firewall to consume
- **Automatically updates** your firewall ACLs as users register new locations

---

## How It Works

!!! note "1. User Registers IP Address"
    Users visit the Vesper Secure portal and authenticate using SSO (EntraID, Google, or Okta). They register their current location as either permanent (home/office) or temporary (hotel/coffee shop).

!!! note "2. IP Automatically Added to Dynamic IP List"
    The user's IP address is immediately added to the Dynamic IP List served at your organization's unique HTTPS endpoint. No admin approval is required.

!!! note "3. Firewall Updates ACL"
    Your firewall periodically polls the Dynamic IP List (typically every 5-60 minutes) and automatically updates its access control lists to allow traffic from registered IPs.

!!! note "4. Admin Monitoring (Optional)"
    Administrators can monitor registered locations and revoke access if needed, but approval is not required as part of the standard workflow.

---

## Who Uses Vesper Secure?

### Administrators

IT and security professionals who configure and manage the system.

**You will:**

- Configure SSO integration (EntraID, Google, Okta)
- Integrate the Dynamic IP List with your firewall
- Monitor user registrations
- Revoke access when needed
- Audit access logs

[Go to Administrator Guide](./admin/index.md)

### End Users

Employees who need their IP addresses added to the allow list.

**You will:**

- Visit the Vesper Secure portal
- Sign in with corporate credentials
- Register your current location
- Wait for the firewall to refresh (typically 5-60 minutes)

[Go to User Guide](./user/index.md)

---

## Quick Links

- **[Admin Panel](./admin/index.md)** - Configure your organization's Vesper Secure tenant and monitor users
- **[User Portal](./user/index.md)** - Register your IP address to access VPN and protected resources
- **[Troubleshooting](./resources/troubleshooting.md)** - Solutions to common issues
- **[Best Practices](./resources/best-practices.md)** - Recommended configurations

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
