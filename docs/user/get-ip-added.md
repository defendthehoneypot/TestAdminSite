<div class="hero" markdown>

<p class="badge">USER GUIDE • QUICK SETUP</p>

# How to Get Your IP Added

Follow these simple steps to register your location and gain VPN access. Takes less than 5 minutes.

</div>

---

## Before You Start

Make sure you:

- Know your corporate login credentials (Google, Microsoft, or Okta)
- Are at the location you want to register
- Have completed MFA setup if your organization requires it

---

## Step 1: Go to the Access Portal

Open the Vesper Secure access portal:

<a href="https://access.vespersecure.com" target="_blank" class="md-button md-button--primary" style="display: inline-block; width: 100%; text-align: center; font-weight: 800; margin: 1rem 0;">
access.vespersecure.com →
</a>

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

<div class="grid cards" markdown>

-   <span class="icon-badge">🏠</span>
    
    ### Permanent Place
    
    **Choose this for:**
    - Your home
    - Your office
    - Any place you regularly work from
    
    **Effect:** Works indefinitely until revoked

-   <span class="icon-badge">☕</span>
    
    ### Temporary Place
    
    **Choose this for:**
    - Hotels
    - Coffee shops
    - Libraries
    - Friend's house
    - Any short-term location
    
    **Effect:** Expires after configured time (usually 24 hours)

</div>

!!! question "Which Should I Choose?"
    - If you'll work from here regularly → **Permanent**
    - If this is just for today/this trip → **Temporary**

---

## Step 4: Submit Your Request

1. Click **"Send my access request"**
2. You'll see a confirmation message

---

## Step 5: Wait for Approval

<div class="step-card" markdown>

!!! warning "Please Wait 5 Minutes"
    After submitting, wait up to 5 minutes for:
    
    1. **Administrator approval** (may be automatic depending on your organization's policy)
    2. **System update** - Firewall refreshes the approved IP list
    
    After 5 minutes, you should be able to connect to VPN.

</div>

---

## Step 6: Connect to VPN

After waiting 5 minutes:

1. Open your VPN client as normal
2. Connect using your usual VPN credentials
3. Access should work!

!!! info "Still Can't Connect?"
    - Wait the full 5 minutes
    - Try disconnecting and reconnecting VPN
    - Check with your IT administrator if still blocked

---

## What Happens Next?

### For Permanent Locations

- ✅ Your IP is approved indefinitely
- ✅ You can connect to VPN anytime from this location
- ✅ No need to re-register unless you move

### For Temporary Locations

- ✅ Your IP is approved for configured duration (typically 24 hours)
- ⏰ After expiration, you'll need to register again
- 📧 You may receive a notification before it expires

---

## Frequently Asked Questions

### How often do I need to do this?

**Permanent locations:** Once per location (home, office, etc.)

**Temporary locations:** Each time you use that location

### What if I'm traveling?

Before connecting to VPN from a new location while traveling:

1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"}
2. Choose **Temporary**
3. Submit request and wait 5 minutes
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
1. Visit [access.vespersecure.com](https://access.vespersecure.com){:target="_blank"} from the new location
2. Register it as Temporary
3. Wait 5 minutes
4. Connect to VPN

### Does this work on mobile devices?

Yes! Open the access portal on your phone or tablet browser and follow the same steps.

### Will this slow down my VPN?

No. Vesper Secure only controls access (whether you can connect). Once connected, VPN performance is unchanged.

### What if my request is denied?

If your administrator denies your request:
- You'll receive a notification with the reason
- Contact your IT administrator for clarification
- You may need to submit a new request with additional information

---

## Need Help?

**For technical issues or questions:**

Contact your organization's IT support team. Provide:
- Your email address
- Your current IP address (visit [whatismyipaddress.com](https://whatismyipaddress.com){:target="_blank"})
- Any error messages
- Whether you're trying permanent or temporary access

!!! danger "Important"
    End users should not contact Vesper Secure support directly. Always contact your organization's IT administrator first.

---

## Additional Resources

- [Back to User Guide](index.md)
- [User FAQ](faq.md)
- [Home Page](../index.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
