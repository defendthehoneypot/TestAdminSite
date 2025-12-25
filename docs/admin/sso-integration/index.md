# SSO Integration Overview

Single Sign-On (SSO) integration allows your organization to use existing identity provider credentials to access Vesper Secure, providing a seamless and secure authentication experience.

---

## What is SSO Integration?

SSO integration enables your users to authenticate to Vesper Secure using their existing corporate identity provider credentials. Instead of managing separate passwords, users sign in once through your organization's identity provider and gain access to Vesper Secure automatically.

### Benefits of SSO Integration

- **Enhanced Security**: Leverage your organization's existing MFA and security policies
- **Simplified User Management**: Centrally manage user access through your identity provider
- **Improved User Experience**: Single sign-on reduces password fatigue
- **Automated Provisioning**: Users are automatically provisioned based on group membership
- **Centralized Audit Trail**: All authentication events are logged in your identity provider
- **Reduced IT Overhead**: Less password reset requests and account management

---

## Supported Identity Providers

Vesper Secure supports SSO integration with the following identity providers:

### SAML 2.0 Providers

**[Microsoft Azure AD (Entra ID) SAML](azure-saml.md)**

Enterprise-grade SSO using Microsoft's identity platform with SAML 2.0 protocol.

- Full support for Azure AD Premium features
- Group-based access control
- Automatic user provisioning
- Conditional access policies

**[Okta SAML](okta-saml.md)**

Industry-leading identity platform with robust SAML 2.0 support.

- Universal Directory integration
- Advanced group management
- Lifecycle management
- Flexible attribute mapping

### OAuth/OIDC Providers (Coming Soon)

- Google Workspace OAuth
- Generic OAuth 2.0/OIDC

!!! info "Need a Different Provider?"
    If your organization uses a different identity provider that supports SAML 2.0, it may still be compatible. Contact Vesper Secure support for assistance with configuration.

---

## General Prerequisites

Before configuring SSO integration with any provider, ensure you have:

- **Administrative Access**: Admin rights in both your identity provider and Vesper Secure
- **Identity Provider Subscription**: Active subscription with your chosen identity provider (Azure AD Premium, Okta, etc.)
- **User Information**: List of users and groups that need access to Vesper Secure
- **Network Access**: Ability to configure SAML endpoints and metadata URLs
- **Certificate Management**: Understanding of certificate lifecycle management for SAML

---

## How SSO Works with Vesper Secure

1. **User Initiates Login**: User navigates to Vesper Secure and clicks "Sign in with SSO"  
2. **SAML Request**: Vesper Secure sends a SAML authentication request to your identity provider  
3. **User Authenticates**: User logs in with their corporate credentials (with MFA if configured)  
4. **SAML Response**: Identity provider sends a SAML response with user attributes back to Vesper Secure  
5. **Access Granted**: Vesper Secure validates the response and grants access based on group membership  

!!! tip "Test with a Pilot Group"
    We recommend testing SSO configuration with a small pilot group before rolling out to your entire organization.

---

## Setup Guides

Choose your identity provider to get started:

### Microsoft Azure AD (Entra ID)

**[Set Up Azure AD SAML SSO →](azure-saml.md)**

Complete guide for configuring SAML-based SSO with Microsoft Azure Active Directory (Entra ID).

**Estimated time**: 20-30 minutes

### Okta

**[Set Up Okta SAML SSO →](okta-saml.md)**

Complete guide for configuring SAML-based SSO with Okta.

**Estimated time**: 15-25 minutes

---

## Common Configuration Steps

While each identity provider has specific configuration requirements, the general process includes:

1. **Create Application**: Register Vesper Secure as an application in your identity provider  
2. **Configure SAML Settings**: Set up Entity ID, Reply URL, and Sign-on URL  
3. **Map Attributes**: Configure user attribute mapping (email, name, groups)  
4. **Configure Certificates**: Upload or generate SAML signing certificates  
5. **Assign Users/Groups**: Grant access to specific users or groups  
6. **Configure Vesper Secure**: Enter identity provider metadata in Vesper Secure admin panel  
7. **Test Connection**: Verify SSO authentication works correctly  
8. **Roll Out**: Deploy to your organization  

---

## Security Considerations

!!! warning "Important Security Notes"
    - **Certificate Expiration**: SAML certificates expire. Set up monitoring and renewal processes.
    - **Secure Metadata**: Protect your identity provider metadata XML files. They contain sensitive configuration.
    - **User Attribute Validation**: Ensure NameID and email attributes are properly mapped and cannot be spoofed.
    - **Group Membership**: Use group-based access control to limit who can authenticate.
    - **MFA Enforcement**: Enable multi-factor authentication in your identity provider for enhanced security.

---

## Best Practices

- **Start Small**: Test with a pilot group before full deployment
- **Use Groups**: Leverage group-based access control for easier management
- **Document Configuration**: Keep records of your SAML settings and attribute mappings
- **Monitor Access**: Regularly review who has access through SSO
- **Rotate Certificates**: Plan for certificate renewal before expiration
- **Enable MFA**: Require multi-factor authentication in your identity provider
- **Test Regularly**: Periodically verify SSO authentication is working correctly

---

## Troubleshooting

For provider-specific troubleshooting, see the individual setup guides:

- [Azure AD SAML Troubleshooting](azure-saml.md#troubleshooting)
- [Okta SAML Troubleshooting](okta-saml.md#troubleshooting)

### Common Issues Across All Providers

**Authentication fails with "Invalid SAML Response"**

- Verify that certificates haven't expired
- Check that the Entity ID and Reply URL match exactly
- Ensure clock sync between identity provider and Vesper Secure

**Users can't access after successful authentication**

- Verify user group membership in your identity provider
- Check that group claims are being sent in SAML response
- Ensure users are assigned to the application in your identity provider

**Certificate expiration warnings**

- Renew certificates before they expire (typically 1-3 years)
- Update the certificate in both identity provider and Vesper Secure
- Test after updating to ensure SSO still works

---

## Next Steps

After configuring SSO integration:

1. **[Set up Firewall Integration](../firewall-integration/index.md)** - Connect the Dynamic IP List to your firewall  
2. **[Configure User Management](../user-management.md)** - Monitor and manage user access  
3. **Review Best Practices** - Ensure your deployment follows security best practices  

---

## Additional Resources

- [Back to Administrator Guide](../index.md)
- [Setup & Configuration](../setup.md)
- [Troubleshooting Guide](../../resources/troubleshooting.md)
- [Best Practices](../../resources/best-practices.md)

---

*© 2025 Evenstar Security, LLC. All rights reserved.*
