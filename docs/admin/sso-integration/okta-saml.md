# Set Up Okta SAML SSO

Configure SAML-based Single Sign-On with Okta to enable secure authentication for Vesper Secure users.

---

## Overview

This guide walks you through configuring SAML 2.0 SSO between Okta and Vesper Secure. After completion, your users will authenticate using their Okta credentials with support for MFA and adaptive authentication policies.

**Estimated time to complete**: 15-25 minutes

### What You'll Configure

- SAML 2.0 application in Okta Admin Console
- SAML single sign-on settings
- User attribute statements
- Group attribute statements for access control
- User and group assignments
- Vesper Secure SSO integration

---

## Prerequisites

Before you begin, ensure you have:

- **Okta Administrator Access**: Super Admin or Application Administrator role in Okta
- **Okta Subscription**: Active Okta subscription (Workforce Identity, Customer Identity, or trial)
- **Vesper Secure Admin Access**: Administrator access to [Vesper Secure admin panel](https://app.vespersecure.com)
- **User Groups Configured**: Okta groups created for users who need Vesper Secure access
- **Two Browser Windows**: One for Okta Admin Console and one for Vesper Secure admin panel

!!! info "Required Okta Permissions"
    You must have one of the following roles in Okta:
    - Super Administrator
    - Application Administrator
    - Organization Administrator

---

## Step 1: Create a SAML Application in Okta

### 1.1 Access Okta Admin Console

1. Sign in to your [Okta Admin Console](https://your-domain.okta.com/admin)
2. From the left navigation, go to **Applications** → **Applications**

### 1.2 Create New SAML Application

1. Click **Create App Integration**
2. Select **SAML 2.0** as the sign-in method
3. Click **Next**

### 1.3 Configure General Settings

On the **General Settings** page:

1. **App name**: Enter `Vesper Secure`
2. **App logo** (optional): Upload a logo if desired
3. **App visibility**: 
   - Check **Do not display application icon to users** if you don't want it in the Okta End-User Dashboard
   - Or leave unchecked to show it to users
4. Click **Next**

!!! tip "Application Name"
    Use a clear name like "Vesper Secure" so users can easily identify the application in their Okta dashboard.

---

## Step 2: Configure SAML Settings

### 2.1 Configure SAML General Settings

On the **Configure SAML** page, enter the following values:

| Field | Value | Description |
|-------|-------|-------------|
| **Single sign-on URL** | `https://app.vespersecure.com/auth/saml/okta/callback` | Where Okta sends SAML responses |
| **Audience URI (SP Entity ID)** | `https://app.vespersecure.com/auth/saml/okta` | Unique identifier for Vesper Secure |
| **Default RelayState** | Leave empty | Optional, not required |
| **Name ID format** | `EmailAddress` | Select from dropdown |
| **Application username** | `Email` | Select from dropdown |

!!! warning "Exact URLs Required"
    The Single sign-on URL and Audience URI must match exactly, including the protocol (`https://`) and paths. Any mismatch will cause authentication to fail.

### 2.2 Advanced Settings (Optional)

You can leave these at their defaults or configure as needed:

- **Response**: `Signed`
- **Assertion Signature**: `Signed`
- **Signature Algorithm**: `RSA-SHA256`
- **Digest Algorithm**: `SHA256`
- **Assertion Encryption**: `Unencrypted` (unless required by your security policy)

---

## Step 3: Configure Attribute Statements

Attribute statements map Okta user profile attributes to SAML attributes that Vesper Secure will receive.

### 3.1 Add Required Attribute Statements

In the **Attribute Statements (optional)** section, click **Add Another** to add each of the following attributes:

| Name | Name format | Value |
|------|-------------|-------|
| `email` | `Unspecified` | `user.email` |
| `firstName` | `Unspecified` | `user.firstName` |
| `lastName` | `Unspecified` | `user.lastName` |

!!! info "Attribute Mapping"
    These attribute names must match exactly what Vesper Secure expects. The "Name format" should be set to "Unspecified" for all attributes.

### 3.2 Add Group Attribute Statements (Optional but Recommended)

To enable group-based access control in Vesper Secure:

1. Scroll to the **Group Attribute Statements (optional)** section
2. Click **Add Another**
3. Configure the group attribute:
   - **Name**: `groups`
   - **Name format**: `Unspecified`
   - **Filter**: Select your preferred filter option:
     - **Matches regex**: `.*` (sends all groups) - recommended for flexibility
     - **Starts with**: `VesperSecure` (sends only groups starting with "VesperSecure")
     - **Equals**: Specific group name (sends only that specific group)

!!! tip "Group Filtering Best Practice"
    Use "Starts with" filter with a prefix like "VesperSecure" to send only relevant groups. This reduces the SAML response size and ensures only intended groups are shared.

### 3.3 Review and Continue

1. Review all attribute statements to ensure they're correct
2. Click **Next** to continue to the feedback section

---

## Step 4: Complete Application Setup

### 4.1 Provide Feedback to Okta

On the **Feedback** page:

1. Select **I'm an Okta customer adding an internal app**
2. Select **This is an internal app that we have created**
3. Click **Finish**

### 4.2 Navigate to Application Settings

You'll be redirected to your new application's page. Keep this page open - you'll need information from it.

---

## Step 5: Get Okta Identity Provider Metadata

### 5.1 Access SAML Setup Instructions

1. In your Vesper Secure application in Okta, go to the **Sign On** tab
2. Click **View SAML setup instructions** (or **View Setup Instructions**)
3. A new page will open with the SAML configuration details

### 5.2 Download or Note Metadata

You have two options for providing Okta metadata to Vesper Secure:

#### Option A: Copy Metadata URL (Recommended)

In the setup instructions page, you'll see a section called **Identity Provider metadata**:

1. Right-click on the metadata URL (usually looks like `https://your-domain.okta.com/app/xxx/sso/saml/metadata`)
2. Copy the link address
3. Save this URL - you'll use it in Vesper Secure

#### Option B: Download Metadata XML

Alternatively, you can download the XML file:

1. In the setup instructions, find the **IDP metadata** section
2. Copy the entire XML content
3. Save it as a file named `okta-metadata.xml`
4. You'll upload this to Vesper Secure later

### 5.3 Note Configuration Values

For reference, note these values from the setup instructions (also available in the metadata):

- **Identity Provider Single Sign-On URL**: `https://your-domain.okta.com/app/xxx/sso/saml`
- **Identity Provider Issuer**: `http://www.okta.com/xxx`
- **X.509 Certificate**: The certificate displayed on the page

!!! info "Metadata vs Manual Configuration"
    Using the metadata URL or XML file is recommended as it automatically populates all required values. Manual configuration requires copying each value individually.

---

## Step 6: Assign Users and Groups

### 6.1 Access Assignment Settings

1. In your Vesper Secure application, click the **Assignments** tab
2. You'll see options to assign the application to users and groups

### 6.2 Assign Groups (Recommended Approach)

For easier management, assign Okta groups:

1. Click **Assign** → **Assign to Groups**
2. Find your desired group(s) (e.g., "VesperSecure-Users")
3. Click **Assign** next to each group
4. Click **Done**

!!! tip "Best Practice: Use Groups"
    Create a dedicated Okta group (e.g., "VesperSecure-Users" or "VesperSecure-Admins") and assign that group to the application. This makes access management easier - simply add/remove users from the group.

### 6.3 Assign Individual Users (Alternative)

If you prefer to assign individual users:

1. Click **Assign** → **Assign to People**
2. Find the user you want to assign
3. Click **Assign** next to their name
4. Configure any user-specific settings if needed
5. Click **Save and Go Back**
6. Repeat for additional users
7. Click **Done** when finished

!!! warning "Assignment Required"
    Users must be assigned to the application to authenticate via SSO. Unassigned users will receive an error when attempting to sign in.

---

## Step 7: Configure Vesper Secure

### 7.1 Access Vesper Secure Admin Panel

1. Open a new browser tab and navigate to [https://app.vespersecure.com](https://app.vespersecure.com)
2. Sign in with your Vesper Secure administrator credentials
3. Navigate to **Settings** → **Authentication** → **SSO Configuration**

### 7.2 Configure Okta SAML

1. Click **+ Add SSO Provider** or **Configure Okta SAML**
2. Fill in the configuration form:

| Field | Value |
|-------|-------|
| **Provider Name** | Okta |
| **Identity Provider** | Okta SAML 2.0 |
| **Entity ID** | `https://app.vespersecure.com/auth/saml/okta` |
| **Single Sign-On URL** | `https://app.vespersecure.com/auth/saml/okta/callback` |

### 7.3 Provide Okta Metadata

Choose one of the following methods:

#### Method A: Metadata URL (Recommended)

1. Select **Import from Metadata URL**
2. Paste the Okta metadata URL you copied in Step 5
3. Click **Import**
4. The following fields should auto-populate:
   - Identity Provider Issuer
   - Identity Provider SSO URL
   - Identity Provider Certificate

#### Method B: Upload Metadata XML

1. Select **Upload Metadata XML**
2. Click **Choose File** and select the `okta-metadata.xml` file you saved
3. Click **Upload**
4. Fields will auto-populate from the XML

#### Method C: Manual Configuration

If neither metadata option works:

1. Select **Manual Configuration**
2. Enter the values from Step 5:
   - **Identity Provider Issuer**: `http://www.okta.com/xxx`
   - **Identity Provider SSO URL**: `https://your-domain.okta.com/app/xxx/sso/saml`
   - **Identity Provider Certificate**: Paste the X.509 certificate (including BEGIN and END lines)

### 7.4 Configure Attribute Mapping

Verify the attribute mapping matches your Okta configuration:

| Vesper Secure Field | SAML Attribute |
|---------------------|----------------|
| **User ID** | NameID (email) |
| **Email** | `email` |
| **First Name** | `firstName` |
| **Last Name** | `lastName` |
| **Groups** | `groups` (if configured) |

### 7.5 Save Configuration

1. Review all settings carefully
2. Click **Test Connection** (if available) to verify the configuration
3. Click **Save** to enable Okta SSO

!!! success "Configuration Complete"
    Okta SAML SSO is now configured. Proceed to testing to verify everything works correctly.

---

## Step 8: Test SSO Connection

### 8.1 Test from Okta Admin Console

1. In Okta Admin Console, go to your Vesper Secure application
2. Click the **General** tab
3. In the **App Embed Link** section, you'll see a link like:
   - `https://your-domain.okta.com/home/oidc_client/xxx/xxx`
4. Copy this link and open it in a new incognito/private browser window
5. You should be redirected to Vesper Secure and automatically signed in

### 8.2 Test from Okta End-User Dashboard

1. Open a new incognito/private browser window
2. Navigate to your Okta end-user dashboard (e.g., `https://your-domain.okta.com`)
3. Sign in with your Okta credentials
4. Look for the Vesper Secure application tile (if you made it visible)
5. Click on the Vesper Secure tile
6. You should be redirected to Vesper Secure and automatically signed in

### 8.3 Test from Vesper Secure Login Page

1. Open a new incognito/private browser window
2. Navigate to [https://app.vespersecure.com](https://app.vespersecure.com)
3. Click **Sign in with Okta** or **SSO Sign In**
4. Enter your Okta organization URL if prompted
5. Enter your Okta credentials (you may be prompted for MFA)
6. Upon successful authentication, you should be redirected back to Vesper Secure and logged in

### 8.4 Verify User Profile

After successful login:

1. Check your user profile in Vesper Secure
2. Verify that your email, first name, and last name are populated correctly
3. If using group claims, verify your group memberships appear correctly

!!! tip "Testing Tips"
    - Use an incognito/private browser window to avoid cached sessions
    - Test with both administrator and regular user accounts
    - Verify that users NOT assigned to the application cannot sign in
    - Test group-based access if you configured group attributes

---

## Step 9: Enable for All Users

Once testing is successful:

### 9.1 Communicate to Users

Before rolling out, inform your users:

- SSO is now available for Vesper Secure
- They can access it via Okta dashboard or "Sign in with Okta" button
- Their Okta credentials (including MFA) will be required
- Any existing Vesper Secure passwords will no longer work (if migrating)

### 9.2 Roll Out in Phases

Consider a phased rollout:

1. **Phase 1**: Pilot group (already tested)
2. **Phase 2**: Department or team
3. **Phase 3**: Entire organization

### 9.3 Monitor Initial Logins

After rollout:

- Monitor system logs in Okta (Reports → System Log)
- Check for any failed authentication attempts
- Address any user issues promptly

---

## Troubleshooting

### Issue: "User is not assigned to the client application"

**Cause**: The user attempting to sign in is not assigned to the Vesper Secure application in Okta.

**Solution**:
1. Go to the Vesper Secure application in Okta Admin Console
2. Click the **Assignments** tab
3. Assign the user or a group containing the user
4. Have the user try signing in again

### Issue: "The SAML response is invalid" or "Signature validation failed"

**Cause**: Certificate mismatch or expired certificate.

**Solution**:
1. Verify the certificate in Vesper Secure matches the current Okta certificate
2. In Okta, go to your application → **Sign On** tab → **View SAML setup instructions**
3. Copy the latest X.509 certificate
4. Update the certificate in Vesper Secure
5. Test SSO again

### Issue: User profile missing first name or last name in Vesper Secure

**Cause**: Attribute statements are not configured correctly or the user's Okta profile is incomplete.

**Solution**:
1. Verify the user has `firstName` and `lastName` populated in their Okta profile
2. Check the attribute statements in your SAML application configuration
3. Ensure attribute names are exactly `firstName` and `lastName` (case-sensitive)
4. Have the user sign out and sign in again

### Issue: Group membership not appearing in Vesper Secure

**Cause**: Group attribute statements are not configured or the user is not a member of matching groups.

**Solution**:
1. Verify group attribute statement is configured with name `groups`
2. Check the filter - ensure it matches the groups the user is a member of
3. Verify the user is a member of Okta groups
4. Check that those groups are assigned to the Vesper Secure application
5. Have the user sign out and sign in again

### Issue: "Audience restriction validation failed"

**Cause**: The Audience URI (SP Entity ID) in Okta doesn't match what Vesper Secure expects.

**Solution**:
1. In Okta, edit your SAML application settings
2. Verify the **Audience URI (SP Entity ID)** is exactly: `https://app.vespersecure.com/auth/saml/okta`
3. Check for typos, trailing slashes, or case differences
4. Save changes and test again

### Issue: "The response was received at an invalid URL"

**Cause**: The Single sign-on URL in Okta doesn't match the actual callback URL.

**Solution**:
1. In Okta, edit your SAML application settings
2. Verify the **Single sign-on URL** is exactly: `https://app.vespersecure.com/auth/saml/okta/callback`
3. Ensure it uses `https://` not `http://`
4. Save changes and test again

### Issue: Users redirected to login repeatedly

**Cause**: Session cookie issues or incorrect RelayState configuration.

**Solution**:
1. Clear browser cookies and cache
2. Try in an incognito/private window
3. In Okta SAML settings, verify **Default RelayState** is empty
4. Check Vesper Secure logs for detailed error messages

### Issue: Certificate expiration warning

**Cause**: The SAML signing certificate is approaching expiration.

**Solution**:
1. Okta certificates typically auto-rotate before expiration
2. When Okta generates a new certificate, re-import the metadata in Vesper Secure
3. Test SSO after updating to ensure it works with the new certificate
4. Set up monitoring for certificate changes in Okta

---

## Best Practices

### Security

- **Enable MFA**: Require multi-factor authentication in Okta sign-on policies for Vesper Secure access
- **Use Sign-On Policies**: Configure location-based, network-based, or device-based access policies
- **Monitor Access**: Regularly review Okta system logs for suspicious activity
- **Rotate Certificates**: Although Okta auto-rotates certificates, monitor for changes
- **Least Privilege**: Only assign necessary users/groups to the application

### Management

- **Use Groups**: Assign groups rather than individual users for easier management
- **Document Configuration**: Keep records of your Entity ID, callback URL, and attribute mappings
- **Test Regularly**: Periodically test SSO authentication to ensure it's working
- **Group Naming Convention**: Use a clear prefix for Vesper Secure groups (e.g., "VesperSecure-")
- **Backup Configuration**: Take screenshots or export your SAML configuration settings

### User Experience

- **Clear Communication**: Inform users about SSO before deployment
- **Add to Dashboard**: Make the Vesper Secure tile visible in Okta dashboard for easy access
- **Provide Documentation**: Create internal documentation or training materials
- **Test with Real Users**: Have pilot users test before full rollout
- **Support Plan**: Ensure your helpdesk knows how to troubleshoot SSO issues

---

## Advanced Configuration

### Sign-On Policies

You can require additional conditions for Vesper Secure access:

1. In Okta Admin Console, go to **Security** → **Authentication Policies**
2. Create a new policy or edit an existing one
3. Add the Vesper Secure application to the policy
4. Configure rules such as:
   - Require MFA
   - Require password and MFA from specific network zones
   - Block access from certain locations
   - Require re-authentication after specified time

### Custom Attributes

If you need to pass custom attributes to Vesper Secure:

1. Add custom attributes to your Okta user profile schema (Directory → Profile Editor)
2. Populate the custom attributes for your users
3. Add custom attribute statements in your SAML application configuration
4. Coordinate with Vesper Secure support for custom attribute mapping

### Multiple Environments

If you have separate Vesper Secure environments (dev, staging, production):

1. Create separate SAML applications in Okta for each environment
2. Use unique Entity IDs for each (e.g., `https://dev.vespersecure.com/auth/saml/okta`)
3. Use different callback URLs for each environment
4. Assign different user groups to each environment application

### Automatic User Provisioning (SCIM)

If Vesper Secure supports SCIM provisioning (check with support):

1. In your application, go to the **Provisioning** tab
2. Click **Configure API Integration**
3. Enable API integration
4. Enter the SCIM endpoint URL and authentication details from Vesper Secure
5. Configure provisioning settings (create users, update attributes, deactivate users)

---

## Certificate Management

### Monitor Certificate Changes

Okta automatically rotates SAML signing certificates before they expire:

1. Okta will notify admins before certificate rotation
2. After rotation, you may need to update Vesper Secure with the new certificate
3. Set up Okta workflows or alerts to notify you of certificate changes

### Update Certificate in Vesper Secure

When Okta rotates the certificate:

1. In Okta, go to your Vesper Secure application → **Sign On** tab
2. View the SAML setup instructions to get the new certificate
3. In Vesper Secure, update the IdP certificate or re-import the metadata
4. Test SSO to ensure it works with the new certificate
5. Both old and new certificates can coexist briefly during transition

---

## Integration with Okta Features

### Okta Verify MFA

Require Okta Verify (or other MFA factors) for Vesper Secure access:

1. Go to **Security** → **Authentication Policies**
2. Select or create a policy for Vesper Secure
3. Add a rule that requires MFA
4. Specify allowed MFA factors (Okta Verify, SMS, Voice, etc.)

### Okta Network Zones

Restrict access based on network location:

1. Define network zones in **Security** → **Networks**
2. Create IP zones for trusted networks (office, VPN, etc.)
3. In authentication policies, require MFA or block access from untrusted zones

### Okta ThreatInsight

Enable ThreatInsight to detect and respond to suspicious authentications:

1. Go to **Security** → **General**
2. Enable **Okta ThreatInsight**
3. Configure whether to log only or block suspicious requests

---

## Next Steps

After successfully configuring Okta SAML SSO:

1. **[Set up Firewall Integration](../firewall-integration.md)** - Connect the EDL to your firewall
2. **[Configure User Management](../user-management.md)** - Monitor and manage user access
3. **Configure Sign-On Policies** - Add additional security policies in Okta
4. **Review System Logs** - Regularly check Okta logs for authentication events

---

## Additional Resources

- [Back to SSO Integration Overview](index.md)
- [Back to Administrator Guide](../index.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Okta Documentation](https://help.okta.com/)
- [Okta SAML Technical Overview](https://developer.okta.com/docs/concepts/saml/)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
