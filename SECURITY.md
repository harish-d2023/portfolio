# 🔒 Security Documentation

This document outlines the security measures implemented in this portfolio website, demonstrating secure coding practices and defense-in-depth principles.

---

## 🛡️ Security Features Implemented

### 1. Client-Side Rate Limiting

**Status:** ✅ Active  
**Priority:** P0 (Critical)  
**File:** `script.js`

**Implementation:**
- Limits contact form submissions to **3 attempts per 60 seconds**
- Automatic reset after time window expires
- User-friendly countdown timer in error messages
- Prevents automated spam and API quota exhaustion

**Code Location:** Lines 651-681

**Protection Against:**
- Automated spam attacks
- API quota exhaustion
- Email bombing
- Bot-driven form abuse

---

### 2. Content Security Policy (CSP)

**Status:** ✅ Active  
**Priority:** P1 (High)  
**File:** `index.html`

**Implementation:**
Comprehensive CSP meta tag restricting resource loading to trusted sources only.

**Policy Details:**
```
default-src 'self'
script-src 'self' 'unsafe-inline' https://cdn.jsdelivr.net https://www.google.com https://www.gstatic.com
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com
font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com
img-src 'self' data: https:
connect-src 'self' https://api.emailjs.com https://www.google.com
frame-ancestors 'none'
base-uri 'self'
form-action 'self'
```

**Code Location:** Lines 10-11

**Protection Against:**
- Cross-Site Scripting (XSS)
- Clickjacking attacks
- Unauthorized resource loading
- Data exfiltration
- Code injection attacks

---

### 3. Console Log Sanitization

**Status:** ✅ Active  
**Priority:** P1 (High)  
**File:** `script.js`

**Implementation:**
- Removed email address from console easter egg
- Success/error logs only display in development environment (localhost)
- Minimal information disclosure in production

**Code Locations:**
- Success log: Line 734-737
- Error log: Line 746-749
- Easter egg: Lines 763-766

**Protection Against:**
- Email harvesting by bots
- Information disclosure
- API response leakage
- Debugging information exposure

---

### 4. Subresource Integrity (SRI)

**Status:** ✅ Active  
**Priority:** P2 (Medium)  
**File:** `index.html`

**Implementation:**
- SHA-512 integrity hashes for all CDN resources
- Font Awesome CDN (line 24-28)
- EmailJS SDK (line 516-519)
- Prevents supply chain attacks via CDN tampering

**Code Locations:**
- Font Awesome: Lines 24-28
- EmailJS SDK: Lines 516-519

**Protection Against:**
- CDN compromise/tampering
- Supply chain attacks
- Man-in-the-middle attacks
- Malicious code injection via CDN

---

### 5. Improved Email Validation

**Status:** ✅ Active  
**Priority:** P2 (Medium)  
**File:** `script.js`

**Implementation:**
- RFC 5322 compliant email regex
- Maximum length validation (254 characters)
- Consecutive dots detection
- Improved data quality and bounce rate reduction

**Code Location:** Lines 739-754

**Protection Against:**
- Invalid email submissions
- Email injection attempts
- Malformed email addresses
- Data quality issues

---

### 6. Input Sanitization

**Status:** ✅ Active  
**Priority:** P2 (Medium)  
**File:** `script.js`

**Implementation:**
- Comprehensive `sanitizeInput()` function
- Removes null bytes and control characters
- Unicode normalization (NFC)
- Length limits: Name (100), Email (254), Message (2000)
- Minimum length validation: Name (2), Message (10)

**Code Location:** Lines 696-732

**Protection Against:**
- Injection attacks
- Control character exploitation
- Unicode-based attacks
- Data integrity issues

---

### 7. Clickjacking Protection

**Status:** ✅ Active  
**Priority:** P2 (Medium)  
**File:** `index.html`

**Implementation:**
- `X-Frame-Options: DENY` meta tag
- Defense-in-depth with CSP `frame-ancestors 'none'`
- Prevents iframe embedding

**Code Location:** Line 14-15

**Protection Against:**
- Clickjacking attacks
- UI redressing
- Unauthorized iframe embedding

---

### 8. Secure External Links

**Status:** ✅ Active  
**Priority:** P2 (Medium)  
**File:** `index.html`

**Implementation:**
All external links use `rel="noopener noreferrer"` attributes.

**Protection Against:**
- Tabnabbing attacks
- Referrer leakage
- Window.opener exploitation

---

### 9. Secure DOM Manipulation

**Status:** ✅ Active  
**Priority:** P3-P4 (Low)  
**File:** `script.js`

**Implementation:**
- Sanitizes `data-text` attribute in glitch effect
- Removes HTML special characters (`<>"'&`)
- Defense-in-depth against potential XSS

**Code Location:** Lines 397-399

**Protection Against:**
- XSS if data-text is dynamically set
- HTML injection via attributes
- Defense-in-depth security

---

### 10. Google reCAPTCHA v2 (Invisible)

**Status:** ✅ Active  
**Priority:** P0 (Critical)

**Implementation:**
- Invisible reCAPTCHA v2 integrated with contact form
- Server-side verification via EmailJS
- Site Key embedded in button `data-sitekey` attribute
- Secret Key configured in EmailJS template settings
- Automatic token generation and validation
- Badge appears in bottom-right corner

**Code Locations:**
- HTML button: `index.html` lines 559-564
- reCAPTCHA script: `index.html` line 32
- Callback function: `script.js` lines 690-813
- CSP updated: `index.html` line 11-12 (added frame-src)

**Protection Against:**
- Automated bot attacks
- Form spam and abuse
- Credential stuffing
- Brute force attempts
- Distributed spam campaigns

**How It Works:**
1. User fills contact form
2. Clicks submit button
3. reCAPTCHA executes invisibly (or shows challenge if suspicious)
4. Google generates verification token
5. Token sent to EmailJS with form data
6. EmailJS verifies token with Google using Secret Key
7. If valid → email sent; if invalid → 400 error returned

**Security Notes:**
- ✅ Secret Key never exposed in frontend code
- ✅ Token verification happens server-side (EmailJS)
- ✅ Tokens are single-use and time-limited (~2 minutes)
- ✅ Domain restrictions enforced by Google
- ✅ Works on localhost (127.0.0.1) and production (GitHub Pages)

---

## 📊 Security Compliance

### OWASP Top 10 (2021) Alignment

| Risk | Status | Implementation |
|------|--------|----------------|
| A01: Broken Access Control | ✅ | Rate limiting prevents abuse |
| A02: Cryptographic Failures | ✅ | No sensitive data stored client-side |
| A03: Injection | ✅ | CSP prevents XSS, input validation active |
| A04: Insecure Design | ✅ | Rate limiting + CSP implemented |
| A05: Security Misconfiguration | ✅ | CSP configured, secure defaults |
| A06: Vulnerable Components | ✅ | SRI hashes implemented |
| A07: Authentication Failures | N/A | No authentication required |
| A08: Software/Data Integrity | ✅ | SRI hashes active |
| A09: Logging Failures | ✅ | Sensitive logs removed |
| A10: SSRF | N/A | Static site, no server-side requests |

---

## 🔍 Security Testing

### Automated Tests Performed

1. **Rate Limiting Test**
   - ✅ 4 rapid submissions attempted
   - ✅ First 3 allowed, 4th blocked
   - ✅ Countdown timer displayed correctly

2. **CSP Validation**
   - ✅ No violations in browser console
   - ✅ All resources loaded from allowed origins
   - ✅ Form submission working

3. **Console Log Verification**
   - ✅ Email address removed from easter egg
   - ✅ No sensitive data logged in production mode
   - ✅ Easter egg displays professionally

4. **SRI Verification**
   - ✅ Font Awesome CDN loaded with valid SRI hash
   - ✅ EmailJS SDK loaded with valid SRI hash
   - ✅ No SRI violations in console

5. **Input Validation Testing**
   - ✅ Name length validation (minimum 2 characters)
   - ✅ Message length validation (minimum 10 characters)
   - ✅ Email format validation (RFC 5322 compliant)
   - ✅ Consecutive dots detection in email
   - ✅ Input sanitization removes control characters

### Manual Security Review

- ✅ No `eval()` or `Function()` constructor usage
- ✅ No `innerHTML` usage (using `textContent` throughout)
- ✅ External links use `rel="noopener noreferrer"`
- ✅ Form uses HTML5 validation attributes
- ✅ HTTPS for all external resources
- ✅ No sensitive data in localStorage/sessionStorage

---

## 🚀 Deployment Security Checklist

When deploying this website, complete the following:

- [ ] Verify HTTPS is enabled on production
- [ ] Add production domain to reCAPTCHA allowed domains
- [ ] Test reCAPTCHA on live site
- [ ] Test rate limiting on live site
- [ ] Verify CSP has no violations
- [ ] Test contact form functionality end-to-end
- [x] (Completed) Implement Subresource Integrity (SRI) hashes
- [x] (Completed) Implement reCAPTCHA v2 (Invisible)



---

## 📈 Security Metrics

### Before Security Hardening
- ❌ No rate limiting
- ❌ No Content Security Policy
- ❌ Email address exposed in console
- ❌ Verbose error logging
- ❌ Unrestricted resource loading

### After Security Hardening
- ✅ Rate limiting: 3 submissions per minute
- ✅ Content Security Policy active
- ✅ Email address removed from logs
- ✅ Minimal information disclosure
- ✅ Restricted resource loading to trusted sources
- ✅ SRI hashes for CDN resources
- ✅ RFC 5322 compliant email validation
- ✅ Comprehensive input sanitization
- ✅ Clickjacking protection (X-Frame-Options + CSP)
- ✅ reCAPTCHA v2 (Invisible) bot protection

### Security Posture: **Significantly Improved** 🎯

---

## 🔐 Security Best Practices Followed

1. **Defense in Depth**
   - Multiple layers of protection (rate limiting + CSP + input validation)
   - No single point of failure

2. **Principle of Least Privilege**
   - CSP restricts resources to minimum required
   - API calls limited to necessary endpoints only

3. **Secure by Default**
   - All security features active by default
   - No user configuration required

4. **Fail Securely**
   - Rate limiting blocks on failure
   - CSP blocks unauthorized resources
   - Form validation prevents invalid submissions

5. **Information Hiding**
   - Minimal error messages in production
   - No sensitive data in console logs
   - Email address protected from harvesting

---

## 📝 Vulnerability Disclosure

If you discover a security vulnerability in this portfolio website, please report it responsibly:

**Contact:** harishd.2025@gmail.com  
**Subject:** [SECURITY] Portfolio Vulnerability Report

Please include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

I appreciate responsible disclosure and will credit security researchers who report valid vulnerabilities.

---

## 🔄 Security Maintenance

This security documentation is maintained and updated with each new security feature implementation.

**Last Updated:** December 25, 2025  
**Version:** 2.2  
**Security Audit Status:** P0, P1, P2, and P3-P4 Complete + reCAPTCHA v2 Implemented

---

## 📚 References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [EmailJS Security Best Practices](https://www.emailjs.com/docs/security/)
- [Google reCAPTCHA Documentation](https://developers.google.com/recaptcha)

---

## 🏆 Security Achievements

This portfolio demonstrates:
- ✅ Secure coding practices
- ✅ OWASP compliance
- ✅ Defense-in-depth architecture
- ✅ Professional security engineering
- ✅ Proactive vulnerability mitigation

**Built with security in mind from the ground up.**

---

*This security documentation demonstrates the implementation of industry-standard security practices in a client-side web application, showcasing expertise in web security, secure development lifecycle, and defensive programming.*
