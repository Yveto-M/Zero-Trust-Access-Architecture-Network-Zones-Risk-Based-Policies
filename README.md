# Zero-Trust-Access-Architecture-Network-Zones-Risk-Based-Policies

Technologies: Okta Adaptive MFA, Network Zones, Sign-On Policies
Status: Enforced & Verified

**🛡️ Executive Summary**

This project shifts the organization's security posture from "Perimeter-Based" to "Zero Trust Identity." By implementing granular Network Zones and Risk-Based Authentication Policies, I enforced strict access controls that validate user location and device context before granting access to critical SaaS applications (GitHub).

**Key Security Controls:**

Geofencing: Explicitly blocking high-risk nation-states and anonymizer IPs.

Context-Aware Access: Enforcing policies that adapt based on the user's network location.

Least Privilege: Prioritizing "Deny" rules to ensure high-risk traffic is dropped immediately.

**🌐 Phase 1: Network Perimeter Architecture**

**Objective: Define the boundaries of trust by categorizing IP traffic into "Safe" and "Blocked" zones.**

1.1 Defining the "Safe" Zone (North America)

Configured a Dynamic Network Zone to define the baseline for legitimate traffic. This allows for granular policy application (e.g., "Allow but Require MFA" for US/Canada).



<img width="1021" height="415" alt="creating-dynamic-zone-1" src="https://github.com/user-attachments/assets/225691b1-349e-43e7-8b0c-23f56d5306bc" />
Figure 1: Defining the 'North America' dynamic zone for authorized access.

1.2 Defining the "High Risk" Block List

Created a Dynamic Zone to target specific high-risk geographies (e.g., Switzerland, Bahrain) to simulate sanctioned regions.




Figure 2: Configuring the 'High Risk Block' dynamic zone.

1.3 The Manual Override (Troubleshooting Fix)

During testing, standard Geo-IP detection for the VPN proved inconsistent. I engineered a Static IP Zone to explicitly define the malicious gateway, ensuring 100% enforcement.




Figure 3: Configuring the 'IP Block Zone' to target specific malicious gateways manually.

**🔒 Phase 2: Policy Engineering**

Objective: Translate business risk requirements into technical enforcement rules.

2.1 Policy Creation

Established a new User Authentication Policy dedicated to high-security applications.




Figure 4: Initializing the 'IP Allow/Block List' policy.

2.2 Configuring the "Allow" Logic

Engineered the rule for legitimate users: Traffic from "North America" is Allowed but must pass an MFA Challenge.




Figure 5: Configuring the 'North America' rule to require MFA.

2.3 Configuring the "Block" Logic

Engineered the rule for high-risk users: Traffic from the "High Risk" or "IP Block" zones is Denied immediately.




Figure 6: Setting the 'IF' condition to match against the custom Block Zones.

2.4 Final Rule Priority

Validated the policy stack. The Block Rule is explicitly set to Priority 1 to ensure it is evaluated before any Allow rules.




Figure 7: Final policy stack showing 'Block High Risk' at Priority 1.

**🧪 Phase 3: Verification & User Experience**

Objective: Validate the security controls by simulating both legitimate and malicious access attempts.

3.1 The "Good User" Experience

Simulated a login from a compliant North American IP. The system correctly challenged the user for MFA/Enrollment instead of blocking them.




Figure 8: Authorized user receives the expected MFA setup/prompt.

3.2 The "Bad Actor" Experience (Access Denied)

Simulated an unauthorized access attempt using the restricted VPN IP. The system evaluated the context and terminated the session immediately.




Figure 9: User receives an explicit "Access Denied" message despite having valid credentials.

**🔍 Phase 4: Forensic Audit (Logs)**

Objective: Confirm the enforcement logic via system telemetry.

4.1 Verifying the Block Event

Analyzed the System Log to confirm the Policy Engine decision. The log clearly shows the Block High Risk rule triggering a DENY outcome for the target IP.




Figure 10: System Log proving the 'Block High Risk' rule was triggered.

4.2 Verifying the Zone Match

Confirmed that the incoming IP was correctly mapped to the High Risk Block zone, validating the Zone definition fix.




Figure 11: Expanded log details confirming the Actor, IP, and Policy Outcome.

**📊 Impact Summary**

Attack Surface Reduction: 100% of traffic from non-business regions is blocked at the identity perimeter.

Credential Guard: Valid credentials are rendered useless if stolen and used from high-risk locations.

Visibility: Granular logging provides actionable intelligence for the SOC.
