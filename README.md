# Zero-Trust-Access-Architecture-Network-Zones-Risk-Based-Policies

**Technologies:** Okta Adaptive MFA, Network Zones, Sign-On Policies
**Status:** Enforced & Verified


&nbsp;


**🛡️ Executive Summary**

This project shifts the organization's security posture from "Perimeter-Based" to "Zero Trust Identity." By implementing granular Network Zones and Risk-Based Authentication Policies, I enforced strict access controls that validate user location and device context before granting access to critical SaaS applications (GitHub).


&nbsp;


**Key Security Controls:**

**Geofencing** Explicitly blocking high-risk nation-states and anonymizer IPs.

**Context-Aware Access:** Enforcing policies that adapt based on the user's network location.

**Least Privilege:** Prioritizing "Deny" rules to ensure high-risk traffic is dropped immediately.


&nbsp;


**🌐 Phase 1: Network Perimeter Architecture**

Objective: Define the boundaries of trust by categorizing IP traffic into "Safe" and "Blocked" zones.

**1.1 Defining the "Safe" Zone (North America)**

Configured a Dynamic Network Zone to define the baseline for legitimate traffic. This allows for granular policy application (e.g., "Allow but Require MFA" for US/Canada).



<img width="1021" height="415" alt="creating-dynamic-zone-1" src="https://github.com/user-attachments/assets/bef7a26b-b222-427f-879d-9d5015bc382f" />

Figure 1: Initial configuration of the 'North America' dynamic zone. (creating-dynamic-zone-1.png)


&nbsp;


**1.2 Verifying Safe Zone Locations**

Validated the geographic parameters to ensure only United States and Canada are included in the whitelist logic.



<img width="945" height="452" alt="task-a-safezone-2" src="https://github.com/user-attachments/assets/49b748d7-d673-46c8-95b0-5737d88f129d" />

Figure 2: Verifying the location parameters for the Safe Zone. (task-a-safezone-2.png)


&nbsp;


**1.3 Defining the "High Risk" Block List**

Created a Dynamic Zone to target specific high-risk geographies (e.g., Switzerland, Bahrain) to simulate sanctioned regions.



<img width="947" height="696" alt="task-b-block-3" src="https://github.com/user-attachments/assets/e79ba97e-e88a-4bda-8835-bb8d011f76c4" />

**Figure 3: Configuring the 'High Risk Block' dynamic zone. (task-b-block-3.png)**


&nbsp;


**🔒 Phase 2: Policy Engineering**

**Objective: Translate business risk requirements into technical enforcement rules.**

&nbsp;


**2.1 Policy Creation**

Established a new User Authentication Policy dedicated to high-security applications to replace the default policy.



<img width="603" height="421" alt="creating-policy-4" src="https://github.com/user-attachments/assets/5c6a071a-ba7e-4758-9be9-e3a625224880" />

Figure 4: Initializing the 'IP Allow/Block List' policy. (creating-policy-4.png)


&nbsp;


**2.2 Configuring the "Allow" Logic**

Engineered the rule for legitimate users: Traffic from "North America" is Allowed but must pass an MFA Challenge.



<img width="902" height="794" alt="allow-list-rule-5" src="https://github.com/user-attachments/assets/598c932b-9319-44ce-a7cf-39f7d82a26e1" />

Figure 5: Configuring the 'North America' rule to require MFA. (allow-list-rule-5.png)


&nbsp;


**2.3 Policy Stack & Rule Priority**

Validated the policy stack. The Block Rule is explicitly set to Priority 2 (Deny) and the Allow Rule is set to Priority 1 (Allow) to ensure proper evaluation order. Note: In this specific config, the Allow list is prioritized for known good zones.



<img width="975" height="499" alt="Policy-List-6" src="https://github.com/user-attachments/assets/2c2b164d-41e1-4945-a68f-772231ae5e37" />

Figure 6: Final policy stack showing rule priority logic. (Policy-List-6.png)

&nbsp;


**🧪 Phase 3: Verification & User Experience**

**Objective: Validate the security controls by simulating both legitimate and malicious access attempts.**


&nbsp;


**3.1 The "Good User" Experience**

Simulated a login from a compliant North American IP. The system correctly challenged the user for MFA/Enrollment instead of blocking them.



<img width="447" height="552" alt="good-user-auth-7" src="https://github.com/user-attachments/assets/1489e285-5e15-4e61-9aec-266fde0a3b8c" />

Figure 7: Authorized user receives the expected MFA setup/prompt. (good-user-auth-7.png)


&nbsp;


**3.2 Security Monitoring (Initial Logs)**

Analyzed the logs during initial testing to verify how the policy engine evaluates incoming traffic and zones.



<img width="974" height="350" alt="ghost-block-ip-login-8" src="https://github.com/user-attachments/assets/4b50c73a-5dcd-4dcd-be9d-a75f87497dec" />

Figure 8: Reviewing system logs to verify IP zone detection. (ghost-block-ip-login-8.png)


&nbsp;


**🔧 Phase 4: Troubleshooting & Hardening**

**Objective: Overcoming VPN detection issues by enforcing static blocks.**


&nbsp;


**4.1 The Manual Override (Fixing VPN Leak)**

During testing, standard Geo-IP detection for the VPN proved inconsistent. I engineered a Static IP Zone to explicitly define the malicious gateway, ensuring 100% enforcement.



<img width="952" height="612" alt="added-new-block-ip-9" src="https://github.com/user-attachments/assets/0f7ad4e9-f77f-431c-9e20-1987ab31eb5c" />

Figure 9: Configuring the 'IP Block Zone' to target specific malicious gateways manually. (added-new-block-ip-9.png)


&nbsp;


**4.2 The "Bad Actor" Experience (Access Denied)**

Retested the unauthorized access attempt using the restricted VPN IP. The system evaluated the context against the new IP Zone and terminated the session immediately.



<img width="520" height="476" alt="mallone-deny-10" src="https://github.com/user-attachments/assets/cbe79ee7-fd7b-4d5b-b995-cac5aaca0082" />

Figure 10: User receives an explicit "Access Denied" message despite having valid credentials. (mallone-deny-10.png)

**4.3 Final Forensic Audit**

Confirmed the enforcement in the System Log. The telemetry clearly shows the Policy Evaluation triggering a DENY outcome for the target IP.



<img width="1019" height="404" alt="user-block-highrisk-11" src="https://github.com/user-attachments/assets/85cbc70c-c993-47f6-a9e3-ce2ae8f9c6eb" />

Figure 11: Expanded log details confirming the Actor, IP, and Policy Outcome. (user-block-highrisk-11.png)


&nbsp;


**📊 Impact Summary**

**Attack Surface Reduction:** 100% of traffic from non-business regions is blocked at the identity perimeter.

**Credential Guard:** Valid credentials are rendered useless if stolen and used from high-risk locations.

**Visibility:** Granular logging provides actionable intelligence for the SOC.
