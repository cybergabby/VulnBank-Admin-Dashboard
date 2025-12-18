# VulnBank-Admin-Dashboard
Breaking Into the VulnBank Admin Dashboard: A Lesson in Broken Access Control

Application Security Challenge

Today’s exercise focused on VulnBank, an intentionally vulnerable web application created by Al-Amir Badmus for security learning purposes.

The objective was simple but deliberate: gain access to the admin dashboard.

Initial Recon
<img width="1366" height="674" alt="xjwt io2" src="https://github.com/user-attachments/assets/a500608e-df6d-4572-b288-6b815b3d22aa" />

At first glance, the application felt unfamiliar and slightly disorienting. The flows weren’t obvious, and nothing immediately stood out as a straightforward entry point. But rather than brute-forcing or guessing, I approached it the way real-world AppSec work demands: observe, intercept, and understand the logic.

<img width="1349" height="651" alt="secretadminpanel" src="https://github.com/user-attachments/assets/600499fc-1f37-47b8-8142-b79aee4ad85e" />

Using Burp Suite, I mapped requests, responses, and authorization behavior across the app. During this process, I discovered a hidden admin endpoint. Accessing it as a normal user returned an Access Denied response exactly what you’d expect in a properly secured system.

Or so it seemed.

The Core Issue: Trusting the Client
Further inspection showed that authorization was being enforced based on claims embedded in a Bearer token supplied by the client.

After decoding the token using xjwt.io, I noticed a role flag:


<img width="1364" height="735" alt="xjwt io1" src="https://github.com/user-attachments/assets/61a433de-49f8-438b-8270-f8f85ad34dc3" />

"is_Admin": false
The system trusted this value entirely.

By modifying the claim to:

"is_Admin": true
Press enter or click to view image in full size

and replaying the request, the application granted full admin access.

No additional server-side validation.
No role verification against a trusted data store.
No privilege enforcement beyond what the client declared.

Within minutes, I had access to the admin dashboard.

<img width="1360" height="686" alt="vulnbankadminpanel" src="https://github.com/user-attachments/assets/0d1e90c4-f850-4f5a-83c0-22be1801b5c6" />

<img width="1365" height="679" alt="VulnBankAdmin1" src="https://github.com/user-attachments/assets/ebdfd18b-31c9-4ba6-bfae-b3eb97eaf65a" />


What Went Wrong
This vulnerability is best categorized as:

Broken Access Control
Broken Role-Based Access Control (RBAC)
With elements of Broken Authentication design
The root problem wasn’t the token itself — it was blind trust in client-controlled authorization data.

If an organization makes this mistake in production, the impact is severe:
Unauthorized admin access
Data exposure
Account takeover
Complete loss of trust
In real-world environments, this is the kind of flaw that leads to breach disclosures and regulatory consequences.



# **Key Takeaways**
Authorization must always be enforced server-side
Tokens should identify users, not define what they’re allowed to do.
Never trust role or privilege claims from the client
Roles must be validated against a secure backend source.
Use appropriate authorization models
If using JWTs, prefer asymmetric signing (RS256) so tokens can’t be trivially forged.
For larger ecosystems, OAuth 2.0 with proper scopes and audience validation is more appropriate.
RAAC failures are rarely “complex” bugs
They’re usually design flaws — and design flaws scale dangerously.


# **Closing Thoughts**
This exercise reinforced a core AppSec principle:
Most critical vulnerabilities don’t come from advanced exploits — they come from misplaced trust.
Security isn’t about adding more controls; it’s about putting the right controls in the right place.

https://medium.com/@gabbytech01/breaking-into-the-vulnbank-admin-dashboard-a-lesson-in-broken-access-control-a38840f25584
