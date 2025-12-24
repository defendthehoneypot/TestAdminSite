<div class="hero" markdown>

<p class="badge">DYNAMIC IP LIST MANAGEMENT • FIREWALL ACL AUTOMATION</p>

# Vesper Secure: Dynamic IP List Management for Firewalls

Vesper Secure is a dynamic IP list management system that serves approved IP addresses via a web service for firewall ACL ingestion. Your firewall automatically updates access control lists based on verified user locations.

</div>

---

## What is Vesper Secure?

Vesper Secure provides a simple solution to a common problem: **managing which IP addresses can access your VPN or protected resources**.

Instead of manually maintaining static IP allow-lists, Vesper Secure:

- Provides a **web portal** where users register their IP addresses
- Creates a **dynamic External Dynamic List (EDL)** of approved IPs
- Serves this list via **HTTPS** for your firewall to consume
- **Automatically updates** your firewall ACLs as users register new locations

---

## How It Works

<div class="step-card" markdown>

### <span class="step-number">1</span> User Registers IP Address

Users visit the Vesper Secure portal and authenticate using SSO (EntraID, Google, or Okta). They register their current location as either permanent (home/office) or temporary (hotel/coffee shop).

### <span class="step-number">2</span> Administrator Approves

Administrators review and approve access requests through the Vesper Secure admin panel. Approval can be manual or automatic based on your policies.

### <span class="step-number">3</span> IP Added to EDL

Once approved, the user's IP address is added to the External Dynamic List served at your organization's unique HTTPS endpoint.

### <span class="step-number">4</span> Firewall Updates ACL

Your firewall periodically polls the EDL (typically every 5-60 minutes) and automatically updates its access control lists to allow traffic from approved IPs.

</div>

---

## Who Uses Vesper Secure?

Vesper Secure serves two distinct audiences:

<div class="grid cards" markdown>

-   <div class="icon-badge">⚙️</div>
    
    ### Administrators
    
    IT and security professionals who configure and manage the system.
    
    **You will:**
    
    - Configure SSO integration (EntraID, Google, Okta)
    - Integrate the EDL with your firewall
    - Set approval policies
    - Manage user access requests
    - Monitor and audit access
    
    [:octicons-arrow-right-24: Go to Administrator Guide](admin/index.md)

-   <div class="icon-badge">👤</div>
    
    ### End Users
    
    Employees who need their IP addresses added to the allow list.
    
    **You will:**
    
    - Visit the Vesper Secure portal
    - Sign in with corporate credentials
    - Register your current location
    - Wait for approval and automatic ACL update
    
    [:octicons-arrow-right-24: Go to User Guide](user/index.md)

</div>

---

## Quick Links

<div class="grid cards" markdown>

<div markdown>
**[Admin Panel](https://app.vespersecure.com){:target="_blank"}**

Configure your organization's Vesper Secure tenant, manage users, and approve access requests.
</div>

<div markdown>
**[User Portal](https://access.vespersecure.com){:target="_blank"}**

Register your IP address to access VPN and protected resources.
</div>

<div markdown>
**[Troubleshooting](resources/troubleshooting.md)**

Solutions to common issues with EDL integration and user access.
</div>

<div markdown>
**[Best Practices](resources/best-practices.md)**

Recommended configurations and deployment strategies.
</div>

</div>

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
