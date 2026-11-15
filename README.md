# web-app-security-assessment

## Overview
A structured web application penetration test conducted against two intentionally vulnerable targets - DVWA (Damn Vulnerable Web Application) and OWASP Juice Shop - using Burp Suite as the primary tool. The assessment identified six vulnerabilities across four OWASP Top 10:2025 categories, with each finding documented as a professional security report entry including proof-of-concept evidence, real-world impact and remediation guidance.

Find the PDF Report here: **[PDF Report](https://github.com/SheetalKaswan/web-app-security-assessment/blob/main/web_app_security_assessment.pdf)**

## Targets
| Target | Stack | Purpose |
| ------ | ----- | ------- |
| DVWA | PHP/ MySQL (Docker) | CLassic web vulnerability training platform |
| OWASP Juice Shop | Node.js (Docker) | Modern web app with 105 built-in vulnerabilities |

Both targets are intentionally vulnerable applications running in an isolated local environment. No real systems, production infrastructure or real user data were involved.


## Tools Used
- Burp Suite Community Edition
- Firefox
- Docker

## OWASP Top 10:2025 Coverage
| OWASP Category | Tested | Vulnerabitilities Found |
| -------------- | ------ | ---------- |
| A01 - Broken Access Control | Yes | IDOR - unauthenticated access to other users' data |
| A03 - Injection | Yes | SQL Injection, XSS (stored+reflected), Command Injection |
| A04 - Cryptographic Failures | Yes | Unauthenticated sensitive file exposure vis /ftp/ directory |
| A07 - Authentication Failures | Partial | - | 

## Findings Summary 
| ID | Vulnerability | Target | OWASP | Severity | CVSS |
| -- | ------ | ------ | ----- | ----- | ---- |
| F-001 | SQL Injection | DVWA | A03 | Critical | 9.8 |
| F-002 | Broken Access Control (IDOR) | DVWA+Juice Shop | A01 | High | 8.1 |
| F-003 | Reflected XSS | DVWA | A03 | High | 7.4 |
| F-004 | Stored XSS | DVWA | A03 | Critical | 9.0 |
| F-005 | OS Command Injection | DVWA | A03 | Critical | 9.8 |
| F-006 | Cryptographic Failure - Sensitive Data Exposure | Juice Shop | A04 | High | 7.5 |


## Burp Suite Workflow 
Each vulnerability was exploited using a specific Burp Suite capability:
| Burp Tool | Used For | 
| ----- | ---- |
| Proxy | Intercepting all browser <--> application traffic |
| Repeater | Manually crafting and refining SQL injection and command injection payloads |
| Intruder | Automating XSS payload delivery across multiple payload variants |
| HTTP History | Mapping all application endpoints and identifying injection points |

## Key Finding Highlight - SQL Injection (F-001)
The User ID parameter passed unsanitised input directly into a SQL query. Exploitation:
1. Basic authentication bypass: 1' OR '1'='1
2. Data extraction via UNION: 1' UNION SELECT null, database()--

The second payload extracted the database name directly - demonstrating that SQL Injection is not just a login bypass but a full data-exfiltration primitive. Remediation: parameterised queries (prepared statements).

## Screenshots
<table>
  <thead>
    <tr>
      <th>#</th>
      <th>Description</th>
      <th>Screenshots</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>01</td>
      <td>Burp Repeater - SQL injection payload with all user records in response</td>
      <td>
        <img width="464" alt="repeater 1" src="https://github.com/user-attachments/assets/1dcbeeb6-0f9c-4771-9861-85bab5e991c4" />
      </td>
    </tr>
    <tr>
      <td>02</td>
      <td>Burp repeater - UNION SELECT with database name extracted</td>
      <td>
        <img width="467" alt="repeater 2" src="https://github.com/user-attachments/assets/a64546f8-a393-469e-a11c-633b3d1c52b6" />
      </td>
    </tr>
    <tr>
      <td>03</td>
      <td>Broken Access Control</td>
      <td>
        <img width="466" alt="other user's basket" src="https://github.com/user-attachments/assets/50e2f939-258b-416e-9073-67e522cdd68c" />
        <img width="466" alt="owasp proof" src="https://github.com/user-attachments/assets/3105ae4d-a0a1-4a25-800a-82c62a8c5a39" />
      </td>
    </tr>
    <tr>
      <td>04</td>
      <td>Reflected XSS</td>
      <td>
        <img width="461" alt="xss popup" src="https://github.com/user-attachments/assets/8420dff2-2614-404a-985b-ea9435778f86" />
      </td>
    </tr>
    <tr>
      <td>05</td>
      <td>Burp Intruder - XSS payload list</td>
      <td>
        <img width="472" alt="rendered page" src="https://github.com/user-attachments/assets/0bb3ee3c-e90c-417d-ac7c-c8c0f9d750eb" />
      </td>
    </tr>
    <tr>
      <td>06</td>
      <td>Command Injection</td>
      <td>
        <img width="466" alt="cat request" src="https://github.com/user-attachments/assets/fde541d6-ed86-4344-85ff-5f002614b748" />
        <img width="464" alt="cat response" src="https://github.com/user-attachments/assets/a2db2758-152d-44af-8fb3-00428d3cbe13" />
      </td>
    </tr>
    <tr>
      <td>07</td>
      <td>Juice Shop /ftp/</td>
      <td>
        <img width="470" alt="ftp" src="https://github.com/user-attachments/assets/7de1dff9-f740-4f51-b9b1-f6a3b77200ac" />
      </td>
    </tr>
    <tr>
      <td>08</td>
      <td>Burp intercept of /api/users registration response-usernames and MD-5 hashed passwords</td>
      <td>
        <img width="465" alt="MD5 hashes" src="https://github.com/user-attachments/assets/44f31ad5-f0b1-45bb-bf00-baae9c8e0ba5" />
      </td>
    </tr>
  </tbody>
</table>


## MITRE ATT&CK Mapping

| Finding | Tactic | Technique |
| -- | -- | -- |
| SQL Injection | Initial Access | T1190 - Exploit Public Facing Application |
| Broken Access Control | Discovery | T1078 - Valid Accounts |
| XSS (Reflected+Stored) | Execution | T1059.007 - JavaScript |
| Command Injection | Execution | T1059.004 - Unix Shell |
| Cryptographic Failure (MD5 hashes) | Credential Access | T1212 - Exploitation for Credential Access |

## Skills Demonstrated
- Web application penetration testing methodology
- Burp Suite (Proxy, Repeater, Intruder) - manual and automated exploitation
- OWASP Top 10:2025 - category identification and exploitation
- SQL Injection (authentication bypass + UNION-based data extraction)
- Cross-site scripting - reflected vs. stored, real-world impact distinction
- OS Command Injection - chaining shell metacharacters, reading system files
- Cryptographic analysis - identifying broken hash algorithms
- professional web application security report writing

## Legal Disclaimer
All testing was performed exclusively against intentionally vulnerable applications (DVWA and OWASP Juice Shop) running in an isolated, local Docker environment. No third-party systems, live websites, or real user data were involved at any stage.














