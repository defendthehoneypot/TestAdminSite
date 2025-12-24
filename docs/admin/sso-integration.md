<div class="hero" markdown>

<p class="badge">ADMINISTRATOR GUIDE</p>

# SSO Integration

Configure single sign-on with EntraID, Google, or Okta for user authentication.

</div>

---

## Overview

Vesper Secure supports multiple identity providers for user authentication. SSO integration provides:

- **Automatic user provisioning** from your identity provider
- **Single sign-on** for seamless user experience
- **Centralized user management** in your existing system
- **MFA enforcement** from your identity provider

---

## Microsoft Entra ID (Recommended)

### Benefits

- Automatic user provisioning and deprovisioning
- Group-based access control
- Single sign-on (SSO) support  
- Native MFA enforcement
- Seamless integration with Microsoft 365

### Configuration Steps

1. Go to **Settings** → **Authentication** in the Vesper admin panel
2. Click **Configure Entra ID**
3. Enter your Azure AD tenant ID (found in Azure Portal → Azure Active Directory → Overview)
4. Click **Grant Permissions** and sign in with Global Admin credentials
5. Grant the requested API permissions when prompted:
   - `User.Read.All` - Read user profiles
   - `Group.Read.All` - Read group memberships
   - `offline_access` - Maintain access
6. Select which Azure AD groups should have access to Vesper Secure
7. Click **Save Configuration**

!!! success "Recommended"
    Entra ID integration provides the best user experience and simplifies administration for Microsoft-based organizations.

### Troubleshooting Entra ID

**Issue: Can't grant permissions**

- Ensure you have Global Administrator or Application Administrator role
- Check that API permissions are enabled for your tenant
- Verify no conditional access policies are blocking the grant

**Issue: Users not syncing**

- Verify selected groups contain users
- Check users have valid email addresses
- Allow up to 15 minutes for initial sync
- Check sync logs in **Settings** → **Authentication** → **Sync Status**

---

## Google Workspace

### Benefits

- Integration with Google Workspace
- Group-based access via Google Groups
- Single sign-on for Google users

### Configuration Steps

1. Go to **Settings** → **Authentication** in the Vesper admin panel
2. Click **Configure Google Workspace**
3. Enter your Google Workspace domain (e.g., `yourcompany.com`)
4. Click **Authorize with Google** and sign in with Super Admin credentials
5. Grant the requested OAuth scopes:
   - View and manage users
   - View groups
6. Select which Google Groups should have access
7. Click **Save Configuration**

### Troubleshooting Google

**Issue: Authorization fails**

- Ensure you have Super Admin role in Google Workspace
- Check that API access is enabled in Google Workspace Admin Console
- Verify domain ownership

**Issue: Groups not appearing**

- Ensure groups are visible in the directory
- Check group membership includes users
- Refresh the group list after changes

---

## Okta

### Benefits

- Works with Okta identity management
- Group-based access control
- Single sign-on via SAML or OIDC

### Configuration Steps

1. Go to **Settings** → **Authentication** in the Vesper admin panel
2. Click **Configure Okta**
3. Enter your Okta domain (e.g., `yourcompany.okta.com`)
4. Choose authentication method:
   - **OIDC** (recommended) - OAuth 2.0 / OpenID Connect
   - **SAML** - SAML 2.0 federation
5. For OIDC:
   - Enter your Okta Client ID
   - Enter your Okta Client Secret
   - Click **Test Connection**
6. For SAML:
   - Download the metadata XML from Okta
   - Upload it to Vesper Secure
7. Select which Okta groups should have access
8. Click **Save Configuration**

### Troubleshooting Okta

**Issue: Connection test fails**

- Verify Client ID and Secret are correct
- Check Okta application is assigned to users
- Ensure redirect URI is configured in Okta

**Issue: Users can't authenticate**

- Verify users are assigned to the Okta application
- Check group assignments match selected groups
- Review Okta system logs for authentication errors

---

## Manual User Management (Alternative)

If you don't use EntraID, Google, or Okta, you can manually manage users:

1. Go to **Users** → **Add Users**
2. Enter user email addresses (one per line)
3. Assign roles and permissions
4. Click **Create Users**
5. Users will receive email invitations

**Use cases for manual management:**
- Small organizations without SSO
- Testing environments
- Organizations with different identity providers

---

## Best Practices

!!! tip "Use SSO Integration"
    SSO integration is strongly recommended for:
    - Automatic user lifecycle management
    - Better security with centralized authentication
    - Improved user experience
    - Reduced administrative overhead

!!! info "Group-Based Access"
    Use groups to manage access at scale:
    - Create a "VesperSecure-Users" group in your identity provider
    - Add/remove users from the group to manage access
    - Changes sync automatically

!!! warning "Test Before Production"
    Test SSO configuration with a small pilot group before rolling out organization-wide.

---

## Next Steps

After configuring SSO:

1. **[Set up Firewall Integration](./firewall-integration.md)** - Connect your firewall to the EDL
2. **[Configure User Management](./user-management.md)** - Set up monitoring workflows
3. Test with a pilot user group

---

## Additional Resources

- [Back to Administrator Guide](./index.md)
- [Setup & Configuration](./setup.md)
- [Troubleshooting Guide](../resources/troubleshooting.md)

---

<p style="text-align: center; color: #64748b; font-size: 0.875rem; margin-top: 2rem;">
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
