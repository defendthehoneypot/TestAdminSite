# How to Get Your IP Added

Follow these simple steps to register your location and gain VPN access. The registration itself takes less than 1 minute.

---

## Before You Start

Make sure you:

- Know your corporate login credentials (Google, Microsoft, or Okta)
- Are at the location you want to register
- Have completed MFA setup if your organization requires it

---

## Step 1: Go to the Access Portal

Open the Vesper Secure access portal: [access.vespersecure.com](https://access.vespersecure.com)

The portal will show login options for your organization's identity provider.

---

## Step 2: Log In with Corporate Credentials

1. Select your organization's identity provider:
   - **Microsoft** (for Entra ID / Azure AD / Office 365)
   - **Google** (for Google Workspace)
   - **Okta** (if your organization uses Okta)

2. Sign in with your corporate email and password

3. Complete MFA prompt if required

!!! tip "Already Signed In?"
    If you're already logged into your corporate account in the browser, this step may be instant.

---

## Step 3: Choose Your Location Type

Select whether this location is **Permanent** or **Temporary**:

### Permanent Place

**Choose this for:**
- Your home
- Your office
- Any place you regularly work from

**Effect:** Works indefinitely until revoked

### Temporary Place

**Choose this for:**
- Hotels
- Coffee shops
- Libraries
- Friend's house
- Any short-term location

**Effect:** Expires after configured time (usually 24 hours)

!!! question "Which Should I Choose?"
    - If you'll work from here regularly → **Permanent**
    - If this is just for today/this trip → **Temporary**

---

## Step 4: Submit Your Registration

1. Click **"Register my location"**
2. You'll see a confirmation message
3. Your IP is now added to the External Dynamic List (EDL)

---

## Step 5: Wait for Firewall Refresh

!!! info "Please Wait Up to 60 Minutes"
    After submitting, your IP is immediately added to the EDL. However, you need to wait for:
    
    1. **Firewall refresh** - Your firewall polls the EDL periodically (typically every 5-60 minutes)
    2. **ACL update** - The firewall updates its access control lists
    
    After the firewall refreshes, you should be able to connect to VPN.

---

## Step 6: Connect to VPN

After waiting for the firewall refresh:

1. Open your VPN client as normal
2. Connect using your usual VPN credentials
3. Access should work!

!!! info "Still Can't Connect?"
    - Wait for the full firewall refresh interval (check with your IT admin)
    - Try disconnecting and reconnecting VPN
    - Verify your current IP hasn't changed
    - Check with your IT administrator if still blocked

---

## What Happens Next?

### For Permanent Locations

- ✅ Your IP is registered indefinitely
- ✅ You can connect to VPN anytime from this location
- ✅ No need to re-register unless you move

### For Temporary Locations

- ✅ Your IP is registered for configured duration (typically 24 hours)
- ⏰ After expiration, you'll need to register again
- 📧 You may receive a notification before it expires

---

## Frequently Asked Questions

### How often do I need to do this?

**Permanent locations:** Once per location (home, office, etc.)

**Temporary locations:** Each time you use that location

### What if I'm traveling?

Before connecting to VPN from a new location while traveling:

1. Visit [access.vespersecure.com](https://access.vespersecure.com)
2. Choose **Temporary**
3. Submit registration and wait for firewall refresh (typically 5-60 minutes)
4. Connect to VPN

### Can I register multiple locations?

Yes! You can have:
- Multiple permanent locations (home, office, second home, etc.)
- Multiple temporary locations at once (though typically you're only in one place at a time)

### What if I moved?

If you permanently moved:
1. Register your new location as **Permanent**
2. Your old location will eventually be removed by your administrator

### What if I forget to register before traveling?

No problem! Just register from your new location:
1. Visit [access.vespersecure.com](https://access.vespersecure.com) from the new location
2. Register it as Temporary
3. Wait for firewall refresh (typically 5-60 minutes)
4. Connect to VPN

### Does this work on mobile devices?

Yes! Open the access portal on your phone or tablet browser and follow the same steps.

### Will this slow down my VPN?

No. Vesper Secure only controls access (whether you can connect). Once connected, VPN performance is unchanged.

### What if my request is denied?

Administrators can revoke access to registered locations if needed. If your location is revoked:
- You'll receive a notification with the reason
- Contact your IT administrator for clarification
- You may need to submit a new registration with additional information

---

## Need Help?

**For technical issues or questions:**

Contact your organization's IT support team. Provide:
- Your email address
- Your current IP address (visit [whatismyipaddress.com](https://whatismyipaddress.com))
- Any error messages
- Whether you're trying permanent or temporary access

!!! danger "Important"
    End users should not contact Vesper Secure support directly. Always contact your organization's IT administrator first.

---

## Additional Resources

- [Back to User Guide](./index.md)
- [User FAQ](./faq.md)
- [Home Page](../index.md)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
