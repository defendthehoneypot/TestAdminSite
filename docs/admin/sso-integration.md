<p>ADMINISTRATOR GUIDE</p>

# SSO Integration (Microsoft Entra ID)

This page describes how to configure Microsoft Entra ID (Azure AD) so your organization can use single sign‑on (SSO) with Vesper Secure.

---

## Overview

Integrating Entra ID provides:

- Single sign‑on for users
- Automatic user provisioning (when configured)
- Group‑based access control
- Centralized management and MFA enforcement

This guide walks through creating an Azure AD application, granting the required permissions, and copying the information into the Vesper Secure admin panel.

---

## Before you begin

- You must be a Global Administrator or Application Administrator in your Azure tenant.
- You will need access to the Vesper Secure admin panel (Settings → Authentication).
- Keep a browser open to both the Azure portal and the Vesper admin panel for copying IDs and secrets.

---

## Step 1 — Register an application in Entra ID

1. Sign in to the Azure Portal: https://portal.azure.com
2. Go to Azure Active Directory → App registrations → New registration.
3. Configure:
   - Name: Vesper Secure (or another descriptive name)
   - Supported account types: Choose the option that matches your organization (e.g., "Accounts in this organizational directory only").
   - Redirect URI (optional at this step): set to the URL provided by the Vesper Secure admin panel (for example: https://app.vespersecure.com/auth/azure/callback). If Vesper Secure lists a specific redirect URI, use that exact value.
4. Click Register.

After registration, note these values from the Overview page:
- Application (client) ID
- Directory (tenant) ID

You will enter these in the Vesper Secure admin panel.

---

## Step 2 — Create a client secret (if using OAuth / OIDC)

If Vesper Secure requires a client secret (for OIDC/OAuth integration):

1. In the app registration, go to Certificates & secrets → New client secret.
2. Add a description (e.g., "Vesper Secure secret") and choose an expiration.
3. Click Add — copy the secret value immediately. You will not be able to see it again.
4. Store the secret securely and paste it into the Vesper Secure admin panel when prompted.

---

## Step 3 — Configure API permissions

Vesper Secure needs access to read users and group membership. Configure delegated permissions:

1. In the app registration, go to API permissions → Add a permission → Microsoft Graph → Delegated permissions.
2. Add the following permissions:
   - openid
   - profile
   - email
   - offline_access
   - User.Read.All
   - Group.Read.All
3. Click Add permissions.
4. Click Grant admin consent for [Your Tenant] and confirm. (Admin consent is required for User.Read.All and Group.Read.All.)

Notes:
- If you prefer more constrained permissions, consult Vesper Secure for the minimum required scopes for your deployment.
- If you will use provisioning or advanced group sync features, additional permissions may be required; Vesper Secure will indicate those in the admin panel.

---

## Step 4 — Configure token & group claims (optional but recommended)

If you want Vesper Secure to receive group membership information in tokens:

1. In the app registration, go to Token configuration → Add group claim.
2. Choose which groups to include (All groups, Security groups, or Groups assigned to the application).
3. Optionally add optional claims (such as email, upn) depending on Vesper Secure's mapping requirements.

Alternatively, Vesper Secure can use Microsoft Graph APIs to query group membership; ensure Group.Read.All is consented if that method is used.

---

## Step 5 — (SAML only) Configure SAML settings

If Vesper Secure supports SAML instead of OIDC:

1. In the app registration, go to Enterprise applications → create a new application from gallery or configure one for SAML.
2. Set the Identifier (Entity ID) and Reply URL (Assertion Consumer Service URL) to the values provided by Vesper Secure in the admin panel.
3. Upload or copy the Federation Metadata / Certificate as required by Vesper Secure.
4. Map attributes/claims (NameID, email, givenName, surname) as documented in the Vesper admin panel.

Follow Vesper Secure's specific SAML attribute mapping guidance if present.

---

## Step 6 — Assign users or groups to the application

1. Go to Enterprise applications → Select the application you created.
2. Under Users and groups, add the Azure AD groups or users that should be allowed to authenticate to Vesper Secure.
3. If you used group claims, ensure the selected groups match Vesper's access control configuration.

Note: If you plan to allow sign-in for the entire tenant, you can skip assigning individual users, but consider restricting access via group assignments for better control.

---

## Step 7 — Copy values into the Vesper Secure admin panel

In the Vesper Secure admin panel, go to Settings → Authentication → Configure Entra ID (or Azure AD) and enter:

- Tenant ID: Directory (tenant) ID from Azure
- Client ID: Application (client) ID
- Client Secret: the secret value you created (if applicable)
- Redirect URI: must match the redirect URI registered in Azure

Click Test Connection (if available), then Save Configuration.

---

## Troubleshooting

- Issue: "Can't grant permissions" — Ensure you are a Global Administrator and there are no Conditional Access policies preventing consent. If tenant-level consent restrictions exist, follow your corporate policy for granting permissions.
- Issue: "Users not syncing" — Ensure the application has been granted admin consent for User.Read.All and Group.Read.All and that the groups assigned to the app contain users. Allow up to 15 minutes for initial sync.
- Issue: "Redirect URI mismatch" — The redirect URI entered in the Vesper admin panel must exactly match a URI in Azure app registration (including trailing slash and protocol).

---

## Best practices

- Use group-based access: create a dedicated "VesperSecure-Users" group in Azure AD and assign that group to the app.
- Use short-lived client secrets and rotate them regularly, or prefer certificate-based authentication if available.
- Test configuration with a small pilot group before rolling out to the entire organization.
- Enable Conditional Access / MFA on your Entra ID policies to enforce stronger authentication for Vesper Secure access.

---

## Next steps

After configuring Entra ID in Vesper Secure:

1. Set up Firewall Integration (connect the EDL)
2. Configure user management and monitoring
3. Pilot with a subset of users, then roll out organization‑wide

---

## Additional resources

- Microsoft Entra ID / Azure AD documentation: https://docs.microsoft.com/azure/active-directory
- Vesper Secure admin panel: Settings → Authentication

---

<p>
© 2025 Evenstar Security, LLC. All rights reserved.
</p>
