# User FAQ

Common questions from end users about using Vesper Secure.

---

## Getting Started

### What is Vesper Secure?

Vesper Secure is a system that manages which IP addresses can access your organization's VPN and protected resources. It ensures only approved locations can connect.

### Why do I need to register?

Your organization uses Vesper Secure to control VPN access. Before you can connect from a new location, that location's IP address must be approved.

### How long does registration take?

The registration itself takes less than 1 minute. After submitting, wait up to 5 minutes for approval and system updates before trying to connect to VPN.

---

## Registration

### How do I register my location?

1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"}
2. Log in with your corporate credentials
3. Choose Permanent or Temporary
4. Submit your request
5. Wait 5 minutes

[See detailed instructions](get-ip-added.md)

### When should I choose "Permanent"?

Choose Permanent for locations you use regularly:
- Your home
- Your office
- Any regular workplace

### When should I choose "Temporary"?

Choose Temporary for short-term locations:
- Hotels while traveling
- Coffee shops
- Libraries  
- Friend's/family's houses
- Any place you'll only use briefly

### How long does temporary access last?

Typically 24 hours, but your organization may configure different durations. Check with your IT administrator.

---

## Using Vesper Secure

### How often do I need to register?

**Permanent locations:** Once per location

**Temporary locations:** Each time you use that location (after expiration)

### Can I register multiple locations?

Yes! You can have multiple permanent locations and register temporary ones as needed.

### What if I'm traveling?

Register each new location as Temporary before connecting to VPN:
1. Arrive at new location
2. Register at [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"}
3. Wait 5 minutes
4. Connect to VPN

### What if I forgot to register before leaving?

Register from your current location:
1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"} 
2. Register as Temporary
3. Wait 5 minutes
4. Connect

### What if I moved to a new home?

Register your new address as Permanent. Your administrator will eventually remove the old address.

---

## Troubleshooting

### I registered but still can't connect

**Wait the full 5 minutes** - The system needs time to update.

**Still not working?**
1. Disconnect and reconnect VPN
2. Check your internet connection
3. Verify you're at the location you registered
4. Contact your IT administrator

### My VPN was working, now it's not

**Possible reasons:**
- Temporary access expired (register again)
- Your IP address changed (re-register)
- Your access was revoked (contact IT administrator)
- Internet connectivity issues

### I can't access the portal

**Try these steps:**
1. Check your internet connection
2. Try a different browser
3. Clear browser cache and cookies
4. Try incognito/private mode
5. Contact your IT administrator

### The portal won't accept my login

**Check:**
- You're using your **corporate** credentials, not personal
- You selected the correct identity provider (Google/Microsoft/Okta)
- Your account is active
- You're completing MFA if required

**Still having issues?** Contact your IT administrator.

### What if my request is denied?

1. You'll receive a notification with the reason
2. Contact your IT administrator for clarification
3. They may ask for more information
4. Submit a new request if approved

### My temporary access expired

Simply register again:
1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"}
2. Choose Temporary
3. Wait 5 minutes
4. Connect

---

## Technical Questions

### Does this work on mobile devices?

Yes! Use your phone or tablet's browser to access the portal and register.

### Will this slow down my VPN?

No. Vesper Secure only controls whether you can connect. Once connected, VPN performance is the same.

### Does this work internationally?

Yes, unless your organization has restricted certain countries. Check with your IT administrator.

### What information does Vesper collect?

Vesper Secure collects:
- Your corporate email/identity
- Your IP address
- Location type (permanent/temporary)
- Registration timestamp

Your administrator can see your registered locations.

### Is my data secure?

Yes. Vesper Secure uses:
- Encrypted HTTPS connections
- Corporate SSO authentication
- No storage of VPN credentials
- Industry-standard security practices

### Can I see what locations I have registered?

Not directly in the user portal. Contact your IT administrator if you need to know your registered locations.

---

## Policy Questions

### Who approves my requests?

Your organization's IT administrator reviews and approves requests. Depending on policies, approval may be automatic.

### Why was my request denied?

Common reasons:
- Location appears suspicious (VPN/proxy)
- Geographic restrictions
- Account not authorized
- Additional information needed

Contact your IT administrator for specific reasons.

### Can I extend temporary access?

Contact your IT administrator. They can extend the duration if needed.

### What if I need emergency access?

Contact your IT administrator immediately. They can manually approve your request for faster access.

---

## Getting Help

### Who do I contact for help?

**Always contact your organization's IT support first**, not Vesper Secure directly.

### What information should I provide?

When contacting IT support, include:
- Your email address
- Your current IP address (from [whatismyipaddress.com](https://whatismyipaddress.com){:target="_blank"})
- What you're trying to do
- Any error messages
- Screenshots if relevant

### Can I contact Vesper Secure support?

No. End users should only contact their organization's IT support. Your IT team will coordinate with Vesper Secure if needed.

---

## Additional Resources

- [Back to User Guide](index.md)
- [Registration Instructions](get-ip-added.md)
- [Home Page](../index.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
