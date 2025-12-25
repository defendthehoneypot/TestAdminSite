# Set Up Azure AD (Entra ID) SAML SSO

Configure SAML-based Single Sign-On with Microsoft Azure Active Directory (Entra ID) to enable secure authentication for Vesper Secure users.

---

## Overview

This guide walks you through configuring SAML 2.0 SSO between Microsoft Azure Active Directory (now called Microsoft Entra ID) and Vesper Secure. After completion, your users will authenticate using their Azure AD credentials with support for MFA and conditional access policies.

**Estimated time to complete**: 20-30 minutes

### What You'll Configure

- Enterprise Application in Azure Portal
- SAML single sign-on settings
- User attribute claims mapping
- Group claims for access control
- User and group assignments
- Vesper Secure SSO integration

---

## Prerequisites

Before you begin, ensure you have:

- **Azure AD Administrator Access**: Global Administrator or Application Administrator role
- **Azure AD Premium License**: Required for group claims and advanced features (P1 or P2)
- **Vesper Secure Admin Access**: Administrator access to [Vesper Secure admin panel](https://app.vespersecure.com)
- **User Groups Configured**: Azure AD groups created for users who need Vesper Secure access
- **Two Browser Windows**: One for Azure Portal and one for Vesper Secure admin panel

!!! info "Required Azure Permissions"
    You must have one of the following roles in Azure AD:
    - Global Administrator
    - Application Administrator
    - Cloud Application Administrator

---

## Step 1: Create an Enterprise Application in Azure

### 1.1 Access Azure Portal

1. Sign in to the [Azure Portal](https://portal.azure.com)  
2. Navigate to **Azure Active Directory** (or **Microsoft Entra ID**)  
3. Select **Enterprise applications** from the left navigation menu  

### 1.2 Create New Application

1. Click **+ New application** at the top of the Enterprise applications page  
2. Click **+ Create your own application**  
3. Enter the application name: `Vesper Secure`  
4. Select **Integrate any other application you don't find in the gallery (Non-gallery)**  
5. Click **Create**  

!!! tip "Application Name"
    Use a descriptive name like "Vesper Secure" or "Vesper Secure SSO" so users can easily identify it in their app portal.

### 1.3 Note Application Details

Once created, you'll be redirected to the application overview page. Keep this page open - you'll need information from it later.

---

## Step 2: Configure SAML Single Sign-On

### 2.1 Start SAML Configuration

1. In your Vesper Secure enterprise application, click **Single sign-on** from the left menu  
2. Select **SAML** as the single sign-on method  
3. You'll see the SAML-based Sign-on configuration page with 5 sections  

### 2.2 Configure Basic SAML Configuration

Click **Edit** in the **Basic SAML Configuration** section and enter the following values:

| Field | Value | Description |
|-------|-------|-------------|
| **Identifier (Entity ID)** | `https://app.vespersecure.com/auth/saml/azure` | Unique identifier for Vesper Secure |
| **Reply URL (Assertion Consumer Service URL)** | `https://app.vespersecure.com/auth/saml/azure/callback` | Where Azure sends SAML responses |
| **Sign on URL** | `https://app.vespersecure.com` | Vesper Secure login page |
| **Relay State** | Leave empty | Optional, not required for Vesper Secure |
| **Logout URL** | `https://app.vespersecure.com/auth/logout` | Single logout endpoint |

!!! warning "Exact URLs Required"
    These URLs must match exactly, including the protocol (`https://`) and trailing paths. Any mismatch will cause authentication to fail.

Click **Save** at the top of the Basic SAML Configuration section.

---

## Step 3: Configure User Attributes and Claims

### 3.1 Edit Attributes & Claims

In the **Attributes & Claims** section, click **Edit** to configure the user attribute mappings.

### 3.2 Configure NameID (Required)

The Unique User Identifier (NameID) is automatically configured, but verify it's set correctly:

1. Click on **Unique User Identifier (Name ID)**  
2. Ensure the following settings:  
   - **Name identifier format**: Email address
   - **Source**: Attribute
   - **Source attribute**: `user.mail`
3. Click **Save**  

!!! info "Why Email as NameID?"
    Vesper Secure uses the email address as the primary user identifier. This must match the user's email in the Vesper Secure system.

### 3.3 Add Required Claims

Vesper Secure requires the following additional claims. Add each one by clicking **+ Add new claim**:

#### Email Claim

- **Name**: `email`
- **Namespace**: Leave empty
- **Source**: Attribute
- **Source attribute**: `user.mail`

#### First Name Claim

- **Name**: `firstName`
- **Namespace**: Leave empty
- **Source**: Attribute
- **Source attribute**: `user.givenname`

#### Last Name Claim

- **Name**: `lastName`
- **Namespace**: Leave empty
- **Source**: Attribute
- **Source attribute**: `user.surname`

!!! tip "Attribute Mapping"
    If your Azure AD uses custom attributes or extensions, adjust the source attributes accordingly. The claim names (email, firstName, lastName) must remain as specified.

### 3.4 Configure Group Claims (Recommended)

To enable group-based access control in Vesper Secure:

1. Click **+ Add a group claim**  
2. Select **Groups assigned to the application** (recommended) or **Security groups**  
3. In **Source attribute**, select **Group ID**  
4. Check **Customize the name of the group claim**  
5. Set **Name**: `groups`  
6. Leave **Namespace** empty  
7. Click **Save**  

!!! warning "Azure AD Premium Required"
    Group claims require Azure AD Premium P1 or P2. If you don't have Premium, you can skip this step, but you'll need to manually manage user access in Vesper Secure.

---

## Step 4: Download SAML Signing Certificate

### 4.1 Download Certificate

In the **SAML Signing Certificate** section:

1. Locate **Federation Metadata XML**  
2. Click **Download** next to it  
3. Save the XML file to your computer - you'll upload this to Vesper Secure later  

!!! info "Certificate Expiration"
    The SAML signing certificate typically expires after 3 years. Azure will notify you before expiration, but it's good practice to set a reminder.

### 4.2 Note Certificate Details

Optionally, note the certificate thumbprint and expiration date for your records:

- **Thumbprint**: Displayed in the Certificate section
- **Expiration Date**: Shown next to the certificate

---

## Step 5: Note Azure AD Configuration URLs

In the **Set up Vesper Secure** section, you'll see several URLs. These are included in the Federation Metadata XML you downloaded, but you can note them for reference:

- **Login URL**: `https://login.microsoftonline.com/<tenant-id>/saml2`
- **Azure AD Identifier**: `https://sts.windows.net/<tenant-id>/`
- **Logout URL**: `https://login.microsoftonline.com/<tenant-id>/saml2`

You don't need to manually copy these if you're using the Federation Metadata XML file.

---

## Step 6: Assign Users and Groups

### 6.1 Access User Assignment

1. In your enterprise application, click **Users and groups** from the left menu  
2. Click **+ Add user/group**  

### 6.2 Add Groups (Recommended Approach)

For easier management, assign Azure AD groups:

1. Click **None Selected** under Users and groups  
2. Search for and select your Azure AD group(s) (e.g., "VesperSecure-Users")  
3. Click **Select**  
4. Click **Assign**  

!!! tip "Best Practice: Use Groups"
    Create a dedicated Azure AD security group (e.g., "VesperSecure-Users") and assign that group to the application. This makes it easier to manage access - simply add/remove users from the group.

### 6.3 Add Individual Users (Alternative)

If you prefer to assign individual users:

1. Click **None Selected** under Users and groups  
2. Search for and select individual users  
3. Click **Select**  
4. Click **Assign**  

!!! warning "User Assignment Required"
    Users must be assigned to the application to authenticate via SSO. Unassigned users will receive an error when attempting to sign in.

---

## Step 7: Configure Vesper Secure

### 7.1 Access Vesper Secure Admin Panel

1. Open a new browser tab and navigate to [https://app.vespersecure.com](https://app.vespersecure.com)  
2. Sign in with your Vesper Secure administrator credentials  
3. Navigate to **Settings** → **Authentication** → **SSO Configuration**  

### 7.2 Configure Azure AD SAML

1. Click **+ Add SSO Provider** or **Configure Azure AD SAML**  
2. Fill in the configuration form:  

| Field | Value |
|-------|-------|
| **Provider Name** | Azure AD |
| **Identity Provider** | Microsoft Azure AD (Entra ID) |
| **Entity ID** | `https://app.vespersecure.com/auth/saml/azure` |
| **Single Sign-On URL** | `https://app.vespersecure.com/auth/saml/azure/callback` |

### 7.3 Upload Federation Metadata

1. Click **Upload Metadata XML** or **Choose File**  
2. Select the Federation Metadata XML file you downloaded from Azure in Step 4  
3. The following fields should auto-populate from the metadata:  
   - Identity Provider Issuer URL
   - Identity Provider SSO URL
   - Identity Provider Certificate

### 7.4 Configure Attribute Mapping

Verify the attribute mapping matches your Azure AD configuration:

| Vesper Secure Field | SAML Attribute |
|---------------------|----------------|
| **User ID** | NameID or `email` |
| **Email** | `email` |
| **First Name** | `firstName` |
| **Last Name** | `lastName` |
| **Groups** | `groups` |

### 7.5 Save Configuration

1. Review all settings carefully  
2. Click **Test Connection** (if available) to verify the configuration  
3. Click **Save** to enable Azure AD SSO  

!!! success "Configuration Complete"
    Azure AD SAML SSO is now configured. Proceed to testing to verify everything works correctly.

---

## Step 8: Test SSO Connection

### 8.1 Test from Azure Portal (Recommended)

1. Return to the Azure Portal  
2. Go to your Vesper Secure enterprise application  
3. Click **Single sign-on** from the left menu  
4. Scroll to the bottom of the page  
5. Click **Test** in the **Test single sign-on with Vesper Secure** section  
6. Choose **Test sign in** or **Test as current user**  
7. You should be redirected to Vesper Secure and automatically signed in  

### 8.2 Test from Vesper Secure Login Page

1. Open a new incognito/private browser window  
2. Navigate to [https://app.vespersecure.com](https://app.vespersecure.com)  
3. Click **Sign in with Azure AD** or **SSO Sign In**  
4. You should be redirected to Azure AD login  
5. Enter your Azure AD credentials (you may be prompted for MFA)  
6. Upon successful authentication, you should be redirected back to Vesper Secure and logged in  

### 8.3 Verify User Profile

After successful login:

1. Check your user profile in Vesper Secure  
2. Verify that your email, first name, and last name are populated correctly  
3. If using group claims, verify your group memberships appear correctly  

!!! tip "Testing Tips"
    - Use an incognito/private browser window to avoid cached sessions
    - Test with both administrator and regular user accounts
    - Verify that users NOT assigned to the application cannot sign in

---

## Step 9: Enable for All Users

Once testing is successful:

### 9.1 Communicate to Users

Before rolling out, inform your users:

- SSO is now available for Vesper Secure
- They should use "Sign in with Azure AD" on the login page
- Their Azure AD credentials (including MFA) will be required
- Any existing Vesper Secure passwords will no longer work (if migrating)

### 9.2 Roll Out in Phases

Consider a phased rollout:

1. **Phase 1**: Pilot group (already tested)  
2. **Phase 2**: Department or team  
3. **Phase 3**: Entire organization  

### 9.3 Monitor Initial Logins

After rollout:

- Monitor authentication logs in Azure AD
- Check for any failed login attempts
- Address any user issues promptly

---

## Troubleshooting

### Issue: "AADSTS50011: The reply URL specified in the request does not match"

**Cause**: The Reply URL in Azure doesn't match what Vesper Secure is sending.

**Solution**:
1. Verify the Reply URL in Azure is exactly: `https://app.vespersecure.com/auth/saml/azure/callback`  
2. Check for trailing slashes or typos  
3. Ensure the URL uses `https://` not `http://`  

### Issue: "AADSTS50105: The signed in user is not assigned to a role for the application"

**Cause**: The user attempting to sign in is not assigned to the Vesper Secure application.

**Solution**:
1. Go to the enterprise application in Azure  
2. Click **Users and groups**  
3. Add the user or a group containing the user  
4. Wait a few minutes for assignment to propagate  

### Issue: User profile missing first name or last name in Vesper Secure

**Cause**: Attribute claims are not configured correctly or the user's Azure AD profile is incomplete.

**Solution**:
1. Verify the user has `givenname` and `surname` populated in Azure AD  
2. Check the Attributes & Claims configuration in the enterprise application  
3. Ensure `firstName` and `lastName` claims are mapped correctly  
4. Have the user sign out and sign in again  

### Issue: Group membership not appearing in Vesper Secure

**Cause**: Group claims are not configured or the user is not a member of any assigned groups.

**Solution**:
1. Verify you have Azure AD Premium (required for group claims)  
2. Check that group claim is configured in Attributes & Claims  
3. Verify the user is a member of groups assigned to the application  
4. Ensure the group claim name is `groups` (lowercase)  

### Issue: "Invalid SAML response" error

**Cause**: Certificate mismatch, expired certificate, or clock skew.

**Solution**:
1. Check that the certificate hasn't expired in Azure  
2. Re-download and re-upload the Federation Metadata XML to Vesper Secure  
3. Verify system clocks are synchronized (use NTP)  
4. Check Vesper Secure logs for detailed error messages  

### Issue: Certificate expiration warning

**Cause**: The SAML signing certificate is approaching expiration.

**Solution**:
1. In Azure, go to the enterprise application → Single sign-on  
2. In the SAML Signing Certificate section, create a new certificate  
3. Download the new Federation Metadata XML  
4. Upload it to Vesper Secure before the old certificate expires  
5. Test SSO with the new certificate  

### Issue: Users redirected to login repeatedly

**Cause**: Session cookie issues or redirect loop.

**Solution**:
1. Clear browser cookies and cache  
2. Try in an incognito/private window  
3. Check that the Sign on URL in Azure matches Vesper Secure's login page  
4. Verify the RelayState parameter is empty or correctly configured  

---

## Best Practices

### Security

- **Enable MFA**: Require multi-factor authentication in Azure AD Conditional Access policies for Vesper Secure access
- **Use Conditional Access**: Configure location-based or device-based access policies
- **Monitor Sign-ins**: Regularly review Azure AD sign-in logs for suspicious activity
- **Rotate Certificates**: Set up monitoring for certificate expiration and renew before expiry
- **Least Privilege**: Only assign necessary users/groups to the application

### Management

- **Use Security Groups**: Assign groups rather than individual users for easier management
- **Document Configuration**: Keep records of your Entity ID, Reply URL, and attribute mappings
- **Test Regularly**: Periodically test SSO authentication to ensure it's working
- **Plan for Certificate Renewal**: Set calendar reminders for certificate expiration (typically 3 years)
- **Backup Metadata**: Keep a copy of the Federation Metadata XML file in a secure location

### User Experience

- **Clear Communication**: Inform users about SSO before deployment
- **Provide Documentation**: Create internal documentation or training materials for users
- **Test with Real Users**: Have pilot users test before full rollout
- **Support Plan**: Ensure your helpdesk knows how to troubleshoot SSO issues

---

## Advanced Configuration

### Conditional Access Policies

You can require additional conditions for Vesper Secure access:

1. In Azure AD, go to **Security** → **Conditional Access**  
2. Create a new policy targeting the Vesper Secure application  
3. Configure conditions such as:  
   - Require MFA
   - Require compliant or domain-joined device
   - Block access from specific countries
   - Require approved client apps

### Custom Attributes

If you need to pass custom attributes to Vesper Secure:

1. Extend your Azure AD schema with custom attributes (if needed)  
2. Add custom claims in the Attributes & Claims section  
3. Coordinate with Vesper Secure support for custom attribute mapping  

### Multiple Environments

If you have separate Vesper Secure environments (dev, staging, production):

1. Create separate enterprise applications for each environment  
2. Use unique Entity IDs for each (e.g., `https://dev.vespersecure.com/auth/saml/azure`)  
3. Assign different user groups to each environment  

---

## Certificate Lifecycle Management

### Monitor Certificate Expiration

The SAML signing certificate typically expires after 3 years. Azure will send notifications, but you should also:

1. Set a calendar reminder for 90 days before expiration  
2. Monitor the certificate expiration date in the Azure Portal  
3. Plan for certificate renewal during a maintenance window  

### Renew Certificate

When the certificate is nearing expiration:

1. In the enterprise application, go to **Single sign-on**  
2. In **SAML Signing Certificate**, click **+ New Certificate**  
3. Download the new Federation Metadata XML with the new certificate  
4. Upload it to Vesper Secure (both old and new certificates can coexist briefly)  
5. After confirming the new certificate works, remove the old one from Vesper Secure  
6. In Azure, set the new certificate as active  

---

## Next Steps

After successfully configuring Azure AD SAML SSO:

1. **[Set up Firewall Integration](../firewall-integration/index.md)** - Connect the Dynamic IP List to your firewall  
2. **[Configure User Management](../user-management.md)** - Monitor and manage user access  
3. **Configure Conditional Access** - Add additional security policies in Azure AD  
4. **Review Audit Logs** - Regularly check Azure AD sign-in logs  

---

## Additional Resources

- [Back to SSO Integration Overview](index.md)
- [Back to Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Microsoft Entra ID Documentation](https://learn.microsoft.com/entra/identity/)
- [Azure AD SAML Protocol](https://learn.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
