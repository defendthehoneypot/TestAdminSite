# Vesper Secure

**Dynamic IP List Management for Firewalls**

Vesper Secure is a dynamic IP list management system that serves registered IP addresses via a web service for firewall ACL ingestion. Your firewall automatically updates access control lists based on verified user locations.

---

## What is Vesper Secure?

Vesper Secure provides a simple solution to a common problem: **managing which IP addresses can access your VPN or protected resources**.

Instead of manually maintaining static IP allow-lists, Vesper Secure:

- Provides a **web portal** where users register their IP addresses
- Creates a **dynamic External Dynamic List (EDL)** of registered IPs
- Serves this list via **HTTPS** for your firewall to consume
- **Automatically updates** your firewall ACLs as users register new locations

---

## How It Works

### 1. User Registers IP Address

Users visit the Vesper Secure portal and authenticate using SSO (EntraID, Google, or Okta). They register their current location as either permanent (home/office) or temporary (hotel/coffee shop).

### 2. IP Automatically Added to EDL

The user's IP address is immediately added to the External Dynamic List served at your organization's unique HTTPS endpoint. No admin approval is required.

### 3. Firewall Updates ACL

Your firewall periodically polls the EDL (typically every 5-60 minutes) and automatically updates its access control lists to allow traffic from registered IPs.

### 4. Admin Monitoring (Optional)

Administrators can monitor registered locations and revoke access if needed, but approval is not required as part of the standard workflow.

---

## Who Uses Vesper Secure?

### Administrators

IT and security professionals who configure and manage the system.

**You will:**

- Configure SSO integration (EntraID, Google, Okta)
- Integrate the EDL with your firewall
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

- **[Admin Panel](https://app.vespersecure.com)** - Configure your organization's Vesper Secure tenant and monitor users
- **[User Portal](https://access.vespersecure.com)** - Register your IP address to access VPN and protected resources
- **[Troubleshooting](./resources/troubleshooting.md)** - Solutions to common issues
- **[Best Practices](./resources/best-practices.md)** - Recommended configurations

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
