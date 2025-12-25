# Troubleshooting Guide

## Common Issues and Solutions

### DIPL (Dynamic IP List) Issues

#### DIPL shows as "Failed" or "Unreachable"

**Symptoms:**
- Firewall indicates DIPL update failed
- No IPs in the list
- Error in firewall logs

**Causes:**
- Firewall can't reach edl.vespersecure.com
- DNS resolution failure
- SSL inspection blocking connection
- Outbound HTTPS blocked

**Solutions:**

1. **Test connectivity from firewall:**  
   ```bash
   curl -v https://edl.vespersecure.com
   ```

2. **Check DNS:**  
   ```bash
   nslookup edl.vespersecure.com
   ```

3. **Verify outbound HTTPS (port 443) is allowed**  

4. **Check SSL inspection isn't blocking**  

5. **Test with credentials:**  
   ```bash
   curl -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id
   ```

---

#### DIPL returns "401 Unauthorized"

**Symptoms:**
- Authentication error in firewall logs
- DIPL shows error status

**Causes:**
- Incorrect username or password
- Extra spaces in credentials
- Credentials expired or regenerated

**Solutions:**

1. **Verify credentials in Vesper admin panel**  
2. **Check for typos or extra spaces**  
3. **Re-enter credentials on firewall** (copy/paste carefully)  
4. **Test manually:**  
   ```bash
   curl -v -u "username:password" https://edl.vespersecure.com/lists/your-id
   ```
5. **If needed, regenerate credentials in admin panel**  

---

#### DIPL is empty but users have registered

**Symptoms:**
- Users registered but DIPL shows no IPs
- VPN still blocked after registration

**Causes:**
- Temporary locations expired
- DIPL not refreshing
- Users' registrations failed

**Solutions:**

1. **Check admin panel** - Are registrations showing as active?  
2. **Verify temporary locations haven't expired**  
3. **Force manual DIPL refresh on firewall**  
4. **Check user's registration status in admin panel**  
5. **Wait for next scheduled DIPL refresh**  

---

### User Access Issues

#### User registered but still can't access VPN

**Symptoms:**
- User registered in Vesper
- User's IP still blocked by firewall

**Causes:**
- DIPL hasn't refreshed yet
- Firewall policy order incorrect
- User's IP doesn't match registered IP
- Temporary access expired

**Solutions:**

1. **Wait for DIPL refresh** (up to refresh interval)  
2. **Force manual DIPL refresh**  
3. **Check firewall policy order:**  
   - Vesper allow rule must be ABOVE deny rules
4. **Verify user's current IP:**  
   - Have user visit whatismyipaddress.com
   - Compare to IP in DIPL
5. **Check request status in admin panel**  
6. **Review firewall logs** for deny reason  

---

#### User can't access portal (access.vespersecure.com)

**Symptoms:**
- Portal won't load
- Login fails
- Identity provider error

**Causes:**
- User's internet connectivity
- Identity provider (Google/Microsoft) issue
- User not in configured group (if using Entra ID)
- Browser compatibility

**Solutions:**

1. **Check internet connectivity**  
2. **Try different browser** (Chrome, Firefox, Edge)  
3. **Clear browser cache and cookies**  
4. **Verify user in correct Entra ID group** (if applicable)  
5. **Check identity provider status** (Google/Microsoft)  
6. **Try incognito/private mode**  

---

#### MFA prompt not appearing

**Symptoms:**
- Login process skips MFA
- No authentication prompt

**Causes:**
- User already authenticated in browser
- MFA not enforced in identity provider
- Browser session cached

**Solutions:**

1. **Expected behavior** if recently authenticated  
2. **Verify MFA policy** in identity provider  
3. **Try private/incognito mode** to test fresh login  
4. **Check identity provider MFA settings**  

---

### Admin Panel Issues

#### Can't log in to admin panel

**Symptoms:**
- Login fails at app.vespersecure.com
- Credentials not working

**Solutions:**

1. **Verify admin email address**  
2. **Use password reset** if needed  
3. **Check organization is active**  
4. **Clear browser cache**  
5. **Try different browser**  
6. **Contact Vesper support** if locked out  

---

#### Entra ID integration not working

**Symptoms:**
- Can't connect Entra ID
- Sync failures
- Users not appearing

**Causes:**
- Insufficient permissions
- Incorrect tenant ID
- API permissions not granted

**Solutions:**

1. **Verify tenant ID is correct**  
2. **Ensure you have Global Admin or Application Admin role**  
3. **Grant all required API permissions** in Azure portal  
4. **Re-authorize the application**  
5. **Check selected groups have users**  

---

### Firewall-Specific Issues

#### Palo Alto: External Dynamic List test fails

**Solutions:**
1. Check certificate profile settings  
2. Verify management interface can reach internet  
3. Test from CLI: `test external-list <name>`  
4. Check system logs  

#### FortiGate: External connector not updating

**Solutions:**
1. Verify interface selection  
2. Check routing for management traffic  
3. Test with `diagnose test application fcnacd 4`  
4. Review external connector logs  

#### Cisco ASA: Dynamic object not populating

**Solutions:**
1. Verify basic-auth configured correctly  
2. Check crypto configuration if using SSL  
3. Test with `show dynamic-object update-url`  
4. Review object-group membership  

---

## Diagnostic Commands

### Test DIPL from Command Line

```bash
# Basic test
curl https://edl.vespersecure.com/lists/your-id

# With authentication
curl -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id

# Verbose for debugging
curl -v -u "org_xxxxx:password" https://edl.vespersecure.com/lists/your-id

# Check IP in list
curl -u "user:pass" https://edl.vespersecure.com/lists/your-id | grep "1.2.3.4"
```

### Check DNS Resolution

```bash
nslookup edl.vespersecure.com
dig edl.vespersecure.com
```

### Test Connectivity

```bash
ping edl.vespersecure.com
telnet edl.vespersecure.com 443
```

---

## Getting Additional Help

### Information to Gather

Before contacting support, collect:

- [ ] Firewall type and version
- [ ] DIPL URL and username (NOT password)
- [ ] Error messages from firewall
- [ ] Screenshots of configuration
- [ ] Output of test commands
- [ ] User's email and IP address (for user issues)
- [ ] Timeline of when issue started

### Contact Channels

**For Administrators:**
- Vesper admin panel support ticket
- Email: support@vespersecure.com
- Include: Organization ID, detailed description, logs

**For End Users:**
- Contact your organization's IT admin
- Do NOT contact Vesper directly

---

## Prevention

### Proactive Monitoring

Set up alerts for:
- DIPL update failures
- Authentication errors
- High denial rates
- Unusual access patterns

### Regular Maintenance

- **Weekly**: Review DIPL update status
- **Monthly**: Test credentials and connectivity
- **Quarterly**: Audit firewall policy order
- **Annually**: Review and update security policies

---

[Back to Home](../index.md) • [Administrator Guide](../admin/index.md) • [FAQ](faq.md) • [Best Practices](best-practices.md)
