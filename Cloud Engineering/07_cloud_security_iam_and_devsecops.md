# 07. Cloud Security, IAM & DevSecOps: Master Architecture Guide

A comprehensive, production-grade guide to cloud identity and access management, Microsoft Entra ID vs. AWS IAM, envelope encryption mechanics, KMS key management, Zero Trust architecture, Cloud Security Posture Management (CSPM), Web Application Firewalls (WAF), container runtime security with Falco, and compliance governance tailored for Cloud Security Engineers, DevSecOps Specialists, and Enterprise Architects.

---

## Architectural Overview: Multi-Layered Zero Trust Cloud Security Architecture

In modern cloud systems, the traditional network perimeter is dead. With distributed microservices, remote workforces, and multi-tenant cloud fabrics, organizations cannot rely on firewalls alone to keep attackers out. Security must be implemented using **Defense-in-Depth** and **Zero Trust**: *Never Trust, Always Verify, Assume Breach*. Every request must be authenticated, authorized, encrypted, and audited at every layer of the infrastructure stack:

```mermaid
flowchart TD
    subgraph IdentityPerimeter ["1. Identity Perimeter (Zero Trust Authentication & Governance)"]
        Entra["Identity Provider: Microsoft Entra ID / AWS IAM<br/>(Conditional Access, MFA, PIM / Just-In-Time Elevation)"]
        WorkloadID["Workload Identity Federation / OIDC<br/>(Short-lived STS Tokens - Zero Static Credentials)"]
        Entra --> WorkloadID
    end

    subgraph NetworkPerimeter ["2. Network & Edge Defense"]
        EdgeWAF["Edge WAF & DDoS Protection<br/>(Azure Front Door WAF / AWS Shield & WAF: OWASP 3.2, Bot Mitigation)"]
        PrivateLink["Private Link & Micro-Segmentation<br/>(No public IPs, zero-trust east-west NSG filtering)"]
        EdgeWAF --> PrivateLink
    end

    subgraph CryptographicCore ["3. Cryptographic Core & Key Management"]
        KeyVault["Key Management: Azure Key Vault / AWS KMS<br/>(Hardware Security Modules FIPS 140-3 Level 3)"]
        EnvelopeEnc["Envelope Encryption Engine (DEK / KEK)<br/>(AES-256-GCM data at rest, TLS 1.3 in transit)"]
        KeyVault --> EnvelopeEnc
    end

    subgraph RuntimeSecurity ["4. Workload & Container Runtime Security"]
        K8sAdmission["Admission Controller (Kyverno / OPA Gatekeeper)<br/>(Blocks root containers, enforces signed Cosign images)"]
        FalcoDaemon["Runtime eBPF Intrusion Detection (Falco)<br/>(Intercepts kernel syscalls: detects reverse shells, CVE exploits)"]
        K8sAdmission --> FalcoDaemon
    end

    subgraph GovernancePlane ["5. Governance, SIEM & Incident Response"]
        CSPM["CSPM: Defender for Cloud / AWS GuardDuty<br/>(Continuous CIS & SOC 2 Benchmark Compliance Auditing)"]
        SIEM["Central SIEM / SOAR (Microsoft Sentinel / OpenSearch)<br/>(Immutable WORM Audit Logs & Automated Security Playbooks)"]
        CSPM --> SIEM
    end

    IdentityPerimeter --> NetworkPerimeter
    NetworkPerimeter --> CryptographicCore
    CryptographicCore --> RuntimeSecurity
    RuntimeSecurity --> GovernancePlane

    style IdentityPerimeter fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style NetworkPerimeter fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style CryptographicCore fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style RuntimeSecurity fill:#0f172a,stroke:#ec4899,stroke-width:1.5px,color:#f472b6
    style GovernancePlane fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c084fc

    style Entra fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style WorkloadID fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style EdgeWAF fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style PrivateLink fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style KeyVault fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style EnvelopeEnc fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style K8sAdmission fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
    style FalcoDaemon fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
    style CSPM fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style SIEM fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

---

## Table of Contents
1. [The Zero Trust Security Paradigm & Core Tenets](#1-the-zero-trust-security-paradigm--core-tenets)
   - [1.1 The Castle-and-Moat vs. International Airport Analogy](#11-the-castle-and-moat-vs-international-airport-analogy)
   - [1.2 The Three Fundamental Zero Trust Principles (NIST SP 800-207)](#12-the-three-fundamental-zero-trust-principles-nist-sp-800-207)
2. [Cloud Identity & Access Management: Entra ID vs. AWS IAM](#2-cloud-identity--access-management-entra-id-vs-aws-iam)
   - [2.1 Microsoft Entra ID: Tenants, Directories, App Registrations, and SPNs](#21-microsoft-entra-id-tenants-directories-app-registrations-and-spns)
   - [2.2 AWS IAM Policy Evaluation Algorithm: The 6-Step Evaluation Engine](#22-aws-iam-policy-evaluation-algorithm-the-6-step-evaluation-engine)
   - [2.3 Azure RBAC Scope Inheritance vs. AWS Resource Policies](#23-azure-rbac-scope-inheritance-vs-aws-resource-policies)
   - [2.4 Privilege Escalation Hazards, Permission Boundaries, and PIM](#24-privilege-escalation-hazards-permission-boundaries-and-pim)
3. [Managed Identities & Workload Identity Federation (OIDC)](#3-managed-identities--workload-identity-federation-oidc)
   - [3.1 System-Assigned vs. User-Assigned Managed Identities](#31-system-assigned-vs-user-assigned-managed-identities)
   - [3.2 The OIDC Cryptographic Handshake: Eliminating Static Cloud Secrets](#32-the-oidc-cryptographic-handshake-eliminating-static-cloud-secrets)
   - [3.3 Production Terraform Configuration for Workload Identity Federation](#33-production-terraform-configuration-for-workload-identity-federation)
4. [Cryptographic Engineering: KMS & Envelope Encryption](#8-cryptographic-engineering-kms--envelope-encryption)
   - [8.1 Why Direct KMS Encryption Fails at Scale: The 4KB Payload Limit](#81-why-direct-kms-encryption-fails-at-scale-the-4kb-payload-limit)
   - [8.2 The Envelope Encryption Mathematical Model (DEK vs. KEK)](#82-the-envelope-encryption-mathematical-model-dek-vs-kek)
   - [8.3 Hardware Security Modules (HSM): FIPS 140-2 vs. FIPS 140-3 Levels](#83-hardware-security-modules-hsm-fips-140-2-vs-fips-140-3-levels)
   - [8.4 Customer-Managed Keys (CMEK) & Crypto-Shredding Mechanics](#84-customer-managed-keys-cmek--crypto-shredding-mechanics)
5. [Edge Defense: Web Application Firewalls (WAF) & DDoS Mitigation](#9-edge-defense-web-application-firewalls-waf--ddos-mitigation)
   - [9.1 WAF Inspection Mechanics: Mitigating OWASP Top 10 Threats](#91-waf-inspection-mechanics-mitigating-owasp-top-10-threats)
   - [9.2 Layer 3/4 Volumetric DDoS Defense vs. Layer 7 Application Flood Defense](#92-layer-34-volumetric-ddos-defense-vs-layer-7-application-flood-defense)
6. [Container & Kubernetes Runtime Security: Falco and eBPF](#10-container--kubernetes-runtime-security-falco-and-ebpf)
   - [10.1 The Blind Spots of Static Container Image Scanning](#101-the-blind-spots-of-static-container-image-scanning)
   - [10.2 Kernel Syscall Interception with Extended Berkeley Packet Filter (eBPF)](#102-kernel-syscall-interception-with-extended-berkeley-packet-filter-ebpf)
   - [10.3 Authoring Production Falco Rules: Detecting CVE-2024-21626 & Shell Execution](#103-authoring-production-falco-rules-detecting-cve-2024-21626--shell-execution)
7. [Threat Detection, CSPM, and SIEM Governance](#11-threat-detection-cspm-and-siem-governance)
   - [11.1 Cloud Security Posture Management (CSPM): Defender for Cloud vs. GuardDuty](#111-cloud-security-posture-management-cspm-defender-for-cloud-vs-guardduty)
   - [11.2 Immutable Audit Logging (WORM Storage) & SIEM Analytics](#112-immutable-audit-logging-worm-storage--siem-analytics)
8. [Automated Cloud Security Audit & Misconfiguration Scanner](#12-automated-cloud-security-audit--misconfiguration-scanner)
   - [12.1 Baseline Security Audit Inspection Framework](#121-baseline-security-audit-inspection-framework)
   - [12.2 Production Python Security Auditor Script](#122-production-python-security-auditor-script)
9. [Senior Security SRE Incident Response Runbooks](#13-senior-security-sre-incident-response-runbooks)
   - [13.1 Runbook 1: Leaked Cloud API Key / IAM Credential Compromise](#131-runbook-1-leaked-cloud-api-key--iam-credential-compromise)
   - [18.2 Runbook 2: Triage of a Reverse Shell in Production (Falco Alert)](#182-runbook-2-triage-of-a-reverse-shell-in-production-falco-alert)
   - [23.3 Runbook 3: Azure Key Vault Access Policy vs. Azure RBAC Conflict Recovery](#233-runbook-3-azure-key-vault-access-policy-vs-azure-rbac-conflict-recovery)
10. [Summary Comparison & Production Engineering Reference Matrix](#26-summary-comparison--production-engineering-reference-matrix)
11. [References & Further Reading](#27-references--further-reading)
---

# 1. The Zero Trust Security Paradigm & Core Tenets

### 1.1 The Castle-and-Moat vs. International Airport Analogy

#### The Castle-and-Moat Model (Legacy Enterprise IT):
In traditional on-premises networking, security was treated like a medieval castle:
- The organization erected a massive stone perimeter wall and a deep moat (the corporate VPN, hardware firewalls, and DMZ).
- Anyone outside the castle was treated as hostile.
- However, once someone crossed the drawbridge (authenticated through the corporate VPN or physically connected an Ethernet cable at the office desk), **they were implicitly trusted**. They had unrestricted lateral access to the corporate intranet, file shares, database servers, and internal web applications.
- **The Fatal Flaw:** If an attacker breached the perimeter—via a phishing email, an unpatched VPN vulnerability, or a compromised employee laptop—they were inside the castle. They could execute lateral movement, dump Active Directory domain hashes, and ransom the entire company in hours.

#### The Zero Trust Model (Modern International Airport):
Modern cloud architecture abandons the concept of a trusted internal network. It treats every internal network packet as hostile, operating like a high-security international airport:

```mermaid
flowchart TD
    subgraph AirportAnalogy ["The Zero Trust Airport Security Checkpoint Pipeline"]
        OuterTerminal["1. Terminal Entrance<br/>(Anyone can walk in, but zero access to gates or aircraft)"]
        --> TicketCheck["2. TSA Boarding Pass Verification<br/>(Identity Verification & Biometric Passport Scan)"]
        --> Scanner["3. Full-Body X-Ray & Carry-On Inspection<br/>(Payload Inspection: WAF / Packet Sanitization)"]
        --> Concourse["4. Secure Duty-Free Concourse<br/>(Still no access to aircraft: Micro-Segmentation)"]
        --> GateCheck["5. Gate Boarding Scanner<br/>(Just-In-Time Authorization for specific Flight ID)"]
        --> CockpitDoor["6. Armored Cockpit Access<br/>(Biometric + PIN: Privileged Identity Management / PIM)"]
    end

    style AirportAnalogy fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style OuterTerminal fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style TicketCheck fill:#1f2937,stroke:#06b6d4,stroke-width:2px,color:#fff
    style Scanner fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style Concourse fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style GateCheck fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
    style CockpitDoor fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
```

- **No Single Trusted Zone:** You cannot board a plane simply because you made it through the front doors of the terminal.
- **Continuous Step-Up Verification:** You prove your identity at check-in, pass physical scanners at security, scan your boarding pass at the gate, and show identification when stepping onto the jet bridge.
- **Role Isolation:** A passenger cannot enter the baggage handling area; baggage handlers cannot board the aircraft without passenger tickets; and even commercial pilots cannot unlock the cockpit door without entering a dynamic security PIN code.

---

### 1.2 The Three Fundamental Zero Trust Principles (NIST SP 800-207)

According to the United States National Institute of Standards and Technology (**NIST SP 800-207**), every cloud-native system must strictly enforce three core architectural pillars:

1. **Verify Explicitly:**
   - Always authenticate and authorize based on all available contextual data points: user identity, client device health, geographic location, workload identity, service context, and real-time risk telemetry.
   - Never assume trust based purely on network physical location (e.g. "traffic is originating from within our private VPC subnet").
2. **Use Least Privileged Access:**
   - Limit user and machine privileges with Just-In-Time (JIT) and Just-Enough-Access (JEA).
   - Enforce explicit permissions boundaries and time-bound role activations (e.g. Azure PIM, AWS IAM Temporary Credentials).
3. **Assume Breach:**
   - Architect the system under the assumption that attackers are already present inside the environment.
   - Minimize blast radius by micro-segmenting networks, encrypting all data in transit (TLS 1.3) and at rest (AES-256), mandating isolated trust boundaries, and streaming audit logs to immutable WORM storage.

---

# 2. Cloud Identity & Access Management: Entra ID vs. AWS IAM

### 2.1 Microsoft Entra ID: Tenants, Directories, App Registrations, and SPNs

Understanding identity terminology is vital for designing enterprise authentication and governance:
- **Tenant:** A dedicated, cryptographically isolated instance of Microsoft Entra ID (formerly Azure Active Directory) representing an entire enterprise organization (e.g. `corp.onmicrosoft.com`).
- **App Registration:** The global architectural definition and metadata blueprint of an application (client ID, redirect URIs, required API permissions) defined in its home tenant.
- **Service Principal (SPN):** The local concrete instantiation (identity object) of an application created within a specific Azure tenant or subscription. When you grant an application RBAC permissions on an Azure subscription, you are binding those permissions to the Service Principal, not the App Registration.
- **Managed Identity:** An automatically managed Service Principal whose lifecycle, authentication certificates, and token rotations are completely abstracted and handled natively by the Azure control plane.

---

### 2.2 AWS IAM Policy Evaluation Algorithm: The 6-Step Evaluation Engine

AWS IAM evaluates authorization requests using a deterministic, multi-stage algorithm. It evaluates all policies applicable to the context (Identity policies, Resource policies, SCPs, Permission Boundaries, and Session Policies) in a strict mathematical order:

```mermaid
flowchart TD
    subgraph AWSEngine ["AWS IAM Deterministic Policy Evaluation Engine"]
        Req["Inbound Request<br/>(Principal, Action, Resource, Context)"]
        --> Step1{"1. Explicit Deny in ANY Policy?<br/>(Identity, Resource, SCP, Boundary, Session)"}
        Step1 -->|"Yes (Matched Deny)"| Denied["FINAL DECISION: DENY<br/>(Immediate short-circuit)"]
        Step1 -->|"No"| Step2{"2. Service Control Policy (SCP) Allows?<br/>(AWS Organizations Guardrail)"}
        Step2 -->|"No"| Denied
        Step2 -->|"Yes"| Step3{"3. Resource-Based Policy Allows?<br/>(e.g. S3 Bucket Policy, KMS Key Policy)"}
        Step3 -->|"Yes"| Step4{"4. Permission Boundary Exists?"}
        Step3 -->|"No"| Step5{"5. Identity-Based Policy (IAM Role/User) Allows?"}
        Step5 -->|"No"| Denied
        Step5 -->|"Yes"| Step4
        Step4 -->|"Yes, but Boundary denies"| Denied
        Step4 -->|"Allowed or No Boundary"| Step6{"6. Session Policy Allows?<br/>(Passed during AssumeRole)"}
        Step6 -->|"No"| Denied
        Step6 -->|"Yes"| Allowed["FINAL DECISION: ALLOW"]
    end

    style AWSEngine fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Req fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Step1 fill:#1e293b,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Denied fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fff
    style Step2 fill:#1e293b,stroke:#f59e0b,stroke-width:1.5px,color:#fff
    style Step3 fill:#1e293b,stroke:#06b6d4,stroke-width:1.5px,color:#fff
    style Step4 fill:#1e293b,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style Step5 fill:#1e293b,stroke:#06b6d4,stroke-width:1.5px,color:#fff
    style Step6 fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#fff
    style Allowed fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#fff
```

#### The Golden Evaluation Rules:
1. **Default Deny (Implicit Deny):** By default, all requests are implicitly denied unless explicitly permitted.
2. **Explicit Deny Always Wins:** An explicit `"Effect": "Deny"` in **any** policy strictly supersedes any number of `"Effect": "Allow"` statements across all policy types.
3. **Organizations SCPs Set the Ceiling:** An SCP does not grant permissions; it sets the maximum allowable permissions. If an SCP denies `s3:DeleteBucket`, no IAM administrator in that account can delete an S3 bucket, even with full `AdministratorAccess` policies!

---

### 2.3 Azure RBAC Scope Inheritance vs. AWS Resource Policies

```mermaid
flowchart TD
    subgraph AzureHierarchy ["Azure RBAC Scope Inheritance"]
        RootMG["Root Management Group"]
        --> ProdMG["Production Management Group"]
        --> Sub["Subscription (e.g. Payment Prod)"]
        --> RG["Resource Group (e.g. rg-database-prod)"]
        --> Res["Individual Resource (e.g. Azure SQL DB)"]
    end

    style AzureHierarchy fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style RootMG fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style ProdMG fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Sub fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style RG fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Res fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
```

- **Azure RBAC Inheritance:**
  - Role assignments inherit downward hierarchically: `Management Group` $\to$ `Subscription` $\to$ `Resource Group` $\to$ `Resource`.
  - Permissions are **additive**. If a user is granted `Reader` at the Subscription scope and `Contributor` on a specific Resource Group, they possess full `Contributor` privileges within that Resource Group.
  - **`DenyAssignment`:** Exists in Azure (primarily applied by Blueprints or Managed Applications). A DenyAssignment strictly overrides all parent and child Allow assignments.
- **AWS Model:**
  - AWS does not use hierarchical folder inheritance for IAM policies. Instead, identity policies are attached to Users/Roles/Groups, while **Resource Policies** (S3 Bucket Policies, KMS Key Policies, SQS Queue Policies) are attached directly to target resources, evaluating concurrently.

---

### 2.4 Privilege Escalation Hazards, Permission Boundaries, and PIM

> [!CAUTION]
> **Common Cloud Privilege Escalation Attack Vectors:**
> Granting seemingly benign permissions can lead to catastrophic cluster takeover:
> 1. `iam:PassRole` + `ec2:RunInstances`: An attacker launches an EC2 instance attached to a high-privilege IAM instance profile (e.g. Admin) and SSHs into it to steal admin credentials.
> 2. `iam:CreatePolicyVersion`: An attacker creates a new default version of an existing policy containing `"Action": "*"`.
> 3. `iam:AttachUserPolicy`: An attacker directly attaches `AdministratorAccess` to their own identity.
> 4. `Microsoft.Authorization/roleAssignments/write`: An attacker grants themselves `Owner` role on the Azure subscription.

#### Defensive Guardrail 1: AWS IAM Permission Boundaries
An IAM Permission Boundary is an advanced policy that sets the maximum allowable permissions an IAM entity can ever achieve, preventing privilege escalation:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowSafeDeveloperActions",
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "ec2:*",
        "lambda:*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DenyIAMModification",
      "Effect": "Deny",
      "Action": [
        "iam:CreateUser",
        "iam:AttachUserPolicy",
        "iam:DeleteUserPolicy"
      ],
      "Resource": "*"
    }
  ]
}
```

#### Defensive Guardrail 2: Privileged Identity Management (PIM)
In Microsoft Entra ID, enterprise organizations enforce **Zero Standing Access**:
- Engineers are configured as **Eligible** for privileged roles (e.g. `Owner`, `Security Administrator`, `Contributor`), with zero active privileges during normal operations.
- When production changes or incidents occur, engineers submit a **Just-In-Time (JIT)** activation request:
  - Must complete Phishing-Resistant MFA (FIDO2 WebAuthn).
  - Must provide a justified business reason and ServiceNow change-ticket number.
  - Requires automated workflow approval from an authorized security lead.
  - The elevated role activates for a maximum window (e.g. 4 hours) and automatically revokes upon expiry.

---

# 3. Managed Identities & Workload Identity Federation (OIDC)

### 3.1 System-Assigned vs. User-Assigned Managed Identities

Managed Identities eliminate the need for developers to manage, store, or rotate cloud API credentials in code or configuration files:

| Dimension | System-Assigned Managed Identity | User-Assigned Managed Identity |
| :--- | :--- | :--- |
| **Lifecycle Binding** | Tied 1:1 to the lifecycle of the host resource (VM, Function App, AKS). | Standalone Azure resource lifecycle; created and deleted independently. |
| **Cardinality** | Exactly 1 identity per resource. | Multiple user-assigned identities can be bound to a single compute resource. |
| **Automatic Deletion**| Deleted automatically by Azure when the host resource is deleted. | Persists even if all compute instances using it are deleted. |
| **Sharing** | Cannot be shared across multiple resources. | Can be shared across heterogeneous fleets (e.g. 50 VMSS instances). |
| **Best Production Use**| Dedicated, isolated microservices with unique database access. | Centralized batch processing agents or multi-node logging collectors. |

---

### 3.2 The OIDC Cryptographic Handshake: Eliminating Static Cloud Secrets

Static cloud API keys committed to code repositories (GitHub, GitLab) represent one of the leading causes of enterprise cloud breaches. **Workload Identity Federation** uses standard **OpenID Connect (OIDC)** to replace static secrets with short-lived, cryptographically verified tokens:

```mermaid
%%{init: {'theme': 'dark', 'themeVariables': { 'actorBkg': '#1e293b', 'actorBorder': '#3b82f6', 'actorTextColor': '#ffffff', 'signalColor': '#93c5fd', 'signalTextColor': '#ffffff', 'noteBkgColor': '#1e293b', 'noteBorderColor': '#f59e0b', 'noteTextColor': '#fde68a', 'activationBkgColor': '#3b82f6', 'sequenceNumberColor': '#ffffff' }}}%%
sequenceDiagram
    autonumber
    participant Workload as Workload Container / GitHub Runner
    participant K8sHost as K8s Token Service / GitHub OIDC
    participant CloudSTS as Cloud Security Token Service (AWS STS / Entra ID)
    participant CloudAPI as Target Cloud API (Azure Key Vault / AWS S3)

    Workload->>K8sHost: Request Projected ServiceAccount JWT Token
    K8sHost-->>Workload: Returns signed JWT (Claims: iss, sub, aud, exp)
    Workload->>CloudSTS: AssumeRoleWithWebIdentity(FederatedToken)
    CloudSTS->>K8sHost: Fetch /.well-known/openid-configuration & jwks.json
    K8sHost-->>CloudSTS: Return Public RSA / ECDSA Verification Keys
    Note over CloudSTS: Cloud STS validates cryptographic signature & audience
    CloudSTS-->>Workload: Issues short-lived Cloud Access Token (Valid for 1 Hour)
    Workload->>CloudAPI: Access cloud resources using Bearer Token
```

#### Token Validation Steps:
1. **Issuer Verification (`iss`):** Cloud STS verifies the token was issued by a trusted OIDC authority (e.g. `https://token.actions.githubusercontent.com` or AKS OIDC issuer URL).
2. **Subject Match (`sub`):** Confirms the caller matches the authorized repository, branch, or Kubernetes service account (e.g. `repo:mycorp/payment-service:ref:refs/heads/main`).
3. **Audience Check (`aud`):** Validates the token was intended specifically for cloud authentication (`api://AzureADTokenExchange` or `sts.amazonaws.com`).
4. **Signature Verification:** Cloud STS verifies the token signature against the public JWKS keys downloaded directly over TLS.

---

### 3.3 Production Terraform Configuration for Workload Identity Federation

Below is a complete, production-grade Terraform declaration binding a Kubernetes Service Account to an AWS IAM Role via OIDC:

```hcl

# 4. Fetch AWS TLS Certificate for EKS OIDC Issuer

data "tls_certificate" "eks" {
  url = aws_eks_cluster.prod_cluster.identity[0].oidc[0].issuer
}

# 5. Register OIDC Identity Provider in AWS IAM

resource "aws_iam_openid_connect_provider" "eks" {
  client_id_list  = ["sts.amazonaws.com"]
  thumbprint_list = [data.tls_certificate.eks.certificates[0].sha1_fingerprint]
  url             = aws_eks_cluster.prod_cluster.identity[0].oidc[0].issuer
}

# 6. Create IAM Role with OIDC Federated Trust Relationship

resource "aws_iam_role" "payment_processor_role" {
  name = "payment-processor-workload-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Federated = aws_iam_openid_connect_provider.eks.arn
        }
        Action = "sts:AssumeRoleWithWebIdentity"
        Condition = {
          StringEquals = {
            "${replace(aws_iam_openid_connect_provider.eks.url, "https://", "")}:sub" = "system:serviceaccount:production:payment-processor-sa"
            "${replace(aws_iam_openid_connect_provider.eks.url, "https://", "")}:aud" = "sts.amazonaws.com"
          }
        }
      }
    ]
  })
}

# 7. Attach Least-Privilege Policy to the Workload Role

resource "aws_iam_role_policy" "s3_payment_policy" {
  name = "payment-records-least-privilege"
  role = aws_iam_role.payment_processor_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject",
          "s3:PutObject"
        ]
        Resource = "arn:aws:s3:::mycorp-payment-records-prod/*"
      }
    ]
  })
}
```

---

# 8. Cryptographic Engineering: KMS & Envelope Encryption

### 8.1 Why Direct KMS Encryption Fails at Scale: The 4KB Payload Limit

Engineers entering cloud security frequently assume they can upload large files (database dumps, audio streams, container images) directly to AWS KMS or Azure Key Vault for encryption.
- **The 4KB Cryptographic Ceiling:** Cloud KMS APIs (such as `kms:Encrypt`) have a strict maximum payload size of **4,096 bytes (4 KB)**.
- **API Rate Limiting & Latency:** Cloud KMS clusters are shared cryptographic hardware appliances with strict throttling thresholds (e.g. 10,000 requests/second per account). Sending every database transaction or disk read/write directly to KMS would introduce massive latency bottlenecks (10–30ms per network round-trip) and bankrupt the organization with API billing fees.

---

### 8.2 The Envelope Encryption Mathematical Model (DEK vs. KEK)

To encrypt gigabyte- and terabyte-scale data with microsecond performance while maintaining centralized hardware security, production architectures use **Envelope Encryption**:

```mermaid
flowchart TD
    subgraph HSM_Boundary ["Cloud Key Management Hardware (FIPS 140-3 Level 3 HSM)"]
        KEK["Key Encryption Key (KEK / Master Key)<br/>(256-bit AES / RSA Key - NEVER leaves HSM silicon)"]
    end

    subgraph HostApplicationRAM ["Application Server Memory (Compute Node)"]
        PlainData["Plaintext Large Data Payload<br/>(100 GB Database Backup)"]
        PlainDEK["Plaintext Data Encryption Key (DEK)<br/>(Generated randomly in software: 256-bit AES)"]
        CiphertextData["Encrypted Data Payload (Ciphertext)"]
        EncryptedDEK["Ciphertext DEK (Encrypted by KEK)"]

        PlainData -->|"AES-256-GCM Hardware Acceleration (AES-NI)"| CiphertextData
        PlainDEK --> PlainData
    end

    HSM_Boundary -->|"Encrypts small 32-byte DEK only"| EncryptedDEK
    KEK <-->|"GenerateDataKey API"| PlainDEK

    style HSM_Boundary fill:#0f172a,stroke:#f59e0b,stroke-width:1.5px,color:#fde68a
    style HostApplicationRAM fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style KEK fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style PlainData fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style PlainDEK fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style CiphertextData fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style EncryptedDEK fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Step-by-Step Encryption Walkthrough:
1. The application invokes the KMS API: `GenerateDataKey(KeyId=KEK, KeySpec=AES_256)`.
2. KMS generates a cryptographically secure random 256-bit symmetric key inside the HSM. It returns two objects:
   - **Plaintext DEK** (32 bytes).
   - **Ciphertext DEK** (encrypted under the master KEK).
3. The application encrypts the multi-gigabyte data locally in user space using the **Plaintext DEK** via **AES-256-GCM**, leveraging modern CPU hardware acceleration instructions (**AES-NI**) to achieve gigabytes-per-second encryption throughput.
4. **The Critical Security Invariant:** The application **immediately zeros out and wipes the Plaintext DEK from RAM memory buffers**.
5. The application packages the **Ciphertext DEK** directly alongside the encrypted data file on disk (as a header/metadata envelope).

#### Step-by-Step Decryption Walkthrough:
1. The application reads the Ciphertext DEK from the file header and sends it to KMS: `Decrypt(CiphertextBlob=EncryptedDEK)`.
2. KMS decrypts the DEK inside the hardware HSM using the master KEK and returns the Plaintext DEK to the application.
3. The application decrypts the data payload locally and promptly zeroes out the Plaintext DEK from memory.

---

### 8.3 Hardware Security Modules (HSM): FIPS 140-2 vs. FIPS 140-3 Levels

Enterprise security compliance standards (PCI-DSS, FedRAMP, HIPAA) mandate Hardware Security Modules certified under NIST FIPS:

| FIPS 140 Level | Physical Security Requirements | Cryptographic Separation | Cloud Implementation |
| :--- | :--- | :--- | :--- |
| **Level 1** | Standard production-grade hardware. No physical security mechanisms required. | Pure software cryptographic implementations. | Standard OS crypto libraries (`openssl`). |
| **Level 2** | **Tamper-Evident Coatings:** Physical seals or pick-resistant locks indicating physical intrusion attempts. | Role-based authentication; checks operator authorization. | Standard cloud KMS multi-tenant software layer. |
| **Level 3** | **Tamper-Response Mechanisms:** Enclosures detect physical opening, temperature/voltage spikes, and **automatically zeroize (erase)** all cryptographic keys! | Identity-based authentication; complete physical and logical separation. | **AWS CloudHSM**, **Azure Dedicated HSM / Key Vault Managed HSM**. |
| **Level 4** | Complete environmental failure protection against radiation, chemical drilling, and zero-gravity penetration. | Immediate zeroization upon any physical breach detection. | Defense and aerospace military satellites. |

---

### 8.4 Customer-Managed Keys (CMEK) & Crypto-Shredding Mechanics

- **Platform-Managed Keys (Default):** Cloud provider owns and manages the root KEK. Zero operational friction, but the provider holds the cryptographic keys.
- **Customer-Managed Keys (CMEK):** Keys generated inside customer-controlled KMS/HSM instances. Enables enterprise key rotation policies and access logging.
- **Crypto-Shredding (Cryptographic Erasure):**
  - When customer data is distributed across petabytes of multi-tenant cloud storage, multi-region snapshots, and offline cold archives, physically wiping every storage sector is technically impossible.
  - **The Solution:** Data is encrypted under a unique customer KEK. When a contract terminates or a compliance deletion order is received, the enterprise **permanently destroys the KEK** inside the HSM.
  - Without the KEK, the encrypted data blobs in cold storage become mathematically indistinguishable from random noise, achieving permanent, certified data destruction within seconds!

---

# 9. Edge Defense: Web Application Firewalls (WAF) & DDoS Mitigation

### 9.1 WAF Inspection Mechanics: Mitigating OWASP Top 10 Threats

A Web Application Firewall (WAF) operates at Layer 7 of the OSI stack, inspecting HTTP/HTTPS request payloads, headers, cookies, and query strings before forwarding traffic to origin servers:

```mermaid
flowchart TD
    Client["<b>Inbound Client Request</b><br/>Public untrusted ingress"]

    subgraph InspectionPhase ["Phase 1: Ingress Decryption & Sanitization"]
        direction LR
        TLS["<b>1. TLS Termination & Parsing</b><br/>Cipher negotiation & HTTP decode"] --> Norm["<b>2. URL & Header Normalization</b><br/>Hex decode & Unicode canonicalization"]
        Norm --> Rules{"3. Core Rule Set (CRS 3.2)<br/>Regex & Anomaly Scoring"}
    end

    subgraph EnforcementPhase ["Phase 2: Security Enforcement Decision"]
        direction LR
        Block["<b>HTTP 403 Forbidden</b><br/>Request dropped at perimeter"]
        Origin["<b>Forward to Backend</b><br/>Traffic routed to internal Service"]
    end

    Client --> InspectionPhase
    Rules -->|"Anomaly Score > Threshold"| Block
    Rules -->|"Clean Request"| Origin

    style Client fill:#0f172a,stroke:#64748b,stroke-width:1.5px,color:#f8fafc
    style InspectionPhase fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style EnforcementPhase fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style TLS fill:#1f2937,stroke:#06b6d4,stroke-width:1.5px,color:#fff
    style Norm fill:#1f2937,stroke:#f59e0b,stroke-width:1.5px,color:#fff
    style Rules fill:#1e293b,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style Block fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#fecaca
    style Origin fill:#064e3b,stroke:#10b981,stroke-width:2px,color:#a7f3d0
```

#### Core Attack Signatures Mitigated:
- **SQL Injection (SQLi):** Detects Boolean injection (`' OR 1=1 --`), stacked queries (`; DROP TABLE users;`), and sleep-based blind SQLi (`WAITFOR DELAY '0:0:5'`).
- **Cross-Site Scripting (XSS):** Blocks payloads with `<script>`, inline event handlers (`onerror=alert(1)`), or `javascript:` URI schemes.
- **Remote Code Execution (RCE):** Detects Unix shell piping and command substitution syntax (`$(whoami)`, `| cat /etc/passwd`).
- **Rate-Limiting & Bot Mitigation:** Tracks client IPs, JA3 TLS fingerprints, and user-agent tokens. If a single IP executes $>100$ requests per 60 seconds against `/api/v1/login`, the WAF automatically applies CAPTCHA challenges or IP drops.

---

### 9.2 Layer 3/4 Volumetric DDoS Defense vs. Layer 7 Application Flood Defense

| Dimension | Layer 3/4 Volumetric DDoS Attacks | Layer 7 Application Flood Attacks |
| :--- | :--- | :--- |
| **Attack Vector** | SYN Floods, UDP Reflection/Amplification (DNS, NTP, Memcached). | HTTP GET floods, Slowloris, complex GraphQL search queries. |
| **Attack Objective** | Saturate physical network interface bandwidth and router state tables. | Exhaust server application CPU/RAM, thread pools, or database connections. |
| **Traffic Legitimacy** | Packets are malformed, spoofed, or originate from massive botnet reflectors. | Packets establish valid 3-way TCP handshakes and send syntactically valid HTTP requests. |
| **Defense Mechanism** | **BGP Anycast Edge Scrubbing:** Global Anycast routes traffic to 100+ edge PoPs (AWS Shield, Azure DDoS), filtering packets via hardware ASICs. | **Layer 7 Inspection:** WAF rate-limiting, challenge-response pages (Cloudflare Turnstile / AWS WAF CAPTCHA), and bot heuristics. |

---

# 10. Container & Kubernetes Runtime Security: Falco and eBPF

### 10.1 The Blind Spots of Static Container Image Scanning

Static container vulnerability scanners (Trivy, Clair, Grype) parse package manifests and file hashes during CI/CD build cycles. While essential, static analysis has critical blind spots:
1. **Zero-Day Vulnerabilities:** Cannot detect novel exploits that lack published CVE entries in NIST databases.
2. **Fileless Runtime Exploits:** If an attacker exploits an unpatched Java Deserialization bug to execute in-memory shellcode, no static file is created.
3. **Insider Threats & Compromised Keys:** Static scanners cannot detect when a developer uses `kubectl exec` to run an unauthorized cryptominer inside a production container.

---

### 10.2 Kernel Syscall Interception with Extended Berkeley Packet Filter (eBPF)

**Falco** is the CNCF standard for cloud-native runtime security. Rather than running intrusive agents inside individual containers, Falco attaches **eBPF programs** directly to the host Linux operating system kernel:

```mermaid
flowchart TD
    subgraph ContainerSpace ["Container Workload (Isolated Pod)"]
        AttackerProcess["Compromised Node.js App<br/>(Attacker executes: /bin/bash reverse shell)"]
    end

    subgraph LinuxKernelSpace ["Host Linux Kernel (Ring 0)"]
        SyscallHandler["Syscall Table (sys_enter_execve, sys_enter_connect)"]
        eBPFHook["Falco eBPF Kernel Probe<br/>(Intercepts syscall arguments in nanoseconds)"]
        RingBuf["eBPF Ring Buffer<br/>(Lockless Kernel-to-Userspace Circular Memory)"]

        SyscallHandler --> eBPFHook --> RingBuf
    end

    subgraph UserspaceDaemon ["Falco Security Daemon (User Space)"]
        Engine["Falco Rule Matching Engine<br/>(Compares syscall against security policies)"]
        AlertSink["Security Alerting / Automated Kill<br/>(Streams to SIEM, triggers Kyverno pod eviction)"]

        RingBuf --> Engine --> AlertSink
    end

    AttackerProcess -->|"Executes execve() syscall"| SyscallHandler

    style ContainerSpace fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style LinuxKernelSpace fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style UserspaceDaemon fill:#0f172a,stroke:#ef4444,stroke-width:1.5px,color:#fca5a5
    style AttackerProcess fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style SyscallHandler fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style eBPFHook fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style RingBuf fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fff
    style Engine fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
    style AlertSink fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fff
```

- **Zero Container Overhead:** Containers require zero modifications, zero agent sidecars, and zero code instrumentations.
- **Tamper-Proof:** Even if an attacker achieves full `root` privileges inside a container, they cannot disable the host kernel's eBPF probes!

---

### 10.3 Authoring Production Falco Rules: Detecting CVE-2024-21626 & Shell Execution

#### Rule 1: Detecting Interactive Shell Execution in Production
Production containers should be immutable appliances containing only the compiled binary and minimal runtime. Spawning a shell (`sh`, `bash`, `zsh`) is a primary indicator of compromise (IoC):

```yaml
- rule: Unauthorized Shell Spawned in Production
  desc: Detects interactive bash or sh execution inside production Kubernetes pods
  condition: >
    spawned_process and container and
    k8s.ns.name = "production" and
    proc.name in (bash, sh, zsh, ksh, dash) and
    not proc.pname in (entrypoint.sh, run.sh)
  output: >
    CRITICAL: Shell spawned in container 
    (user=%user.name pod=%k8s.pod.name ns=%k8s.ns.name container=%container.name 
    image=%container.image.repository cmdline=%proc.cmdline parent=%proc.pname)
  priority: CRITICAL
  tags: [mitre_execution, pci_dss, production]
```

#### Rule 2: Detecting CVE-2024-21626 (`runc` Container Breakout)
CVE-2024-21626 allows an attacker to leak host file descriptors (`/proc/self/fd/`) during container initiation, allowing direct escape to the host filesystem:

```yaml
- rule: Container Breakout Attempt (CVE-2024-21626)
  desc: Detects attempt to access host filesystem via leaked runc proc file descriptor
  condition: >
    spawned_process and container and
    (proc.args contains "/proc/self/fd" or proc.cwd startswith "/proc/self/fd")
  output: >
    EMERGENCY: Potential runc container breakout detected! 
    (pod=%k8s.pod.name ns=%k8s.ns.name cmd=%proc.cmdline cwd=%proc.cwd)
  priority: EMERGENCY
  tags: [cve, container_escape, mitre_privilege_escalation]
```

---

# 11. Threat Detection, CSPM, and SIEM Governance

### 11.1 Cloud Security Posture Management (CSPM): Defender for Cloud vs. GuardDuty

- **Cloud Security Posture Management (CSPM):** Continuously scans cloud control plane configurations against CIS Benchmarks, NIST SP 800-53, and SOC 2 Type II:
  - Discovers open public S3/Blob storage accounts.
  - Detects unencrypted virtual disks and missing audit logging.
  - Automatically remediates drifted configurations via Cloud Policy engines.
- **Behavioral Threat Detection Engines:**
  - **Amazon GuardDuty:** Continuously analyzes AWS CloudTrail management events, VPC Flow Logs, and EKS audit logs using machine learning to detect compromised credentials, Tor exit node communication, and anomalous API invocations.
  - **Microsoft Defender for Cloud:** Provides unified cross-cloud posture management, vulnerability scanning, and behavioral alerting across Azure, AWS, and GCP.

---

### 11.2 Immutable Audit Logging (WORM Storage) & SIEM Analytics

In an enterprise intrusion, attackers attempt to cover their footprints by deleting or truncating cloud audit logs (AWS CloudTrail, Azure Activity Logs).
- **Write-Once-Read-Many (WORM) Storage:**
  - Audit logs must be shipped directly to an isolated security archive bucket configured with an **Immutability Policy / S3 Object Lock in Compliance Mode**.
  - In Compliance Mode, logs cannot be modified, deleted, or truncated by anyone—not even the Cloud Root Account or Global Administrator—until the mandatory retention period (e.g. 7 years) expires.
- **Central SIEM/SOAR Ingestion:**
  - Audit streams feed into central SIEM engines (**Microsoft Sentinel**, **Splunk**, or **OpenSearch**).
  - SOAR (Security Orchestration, Automation, and Response) playbooks execute automated containment: revoking compromised IAM access keys and applying network quarantine rules within seconds of alert generation.

---

# 12. Automated Cloud Security Audit & Misconfiguration Scanner

### 12.1 Baseline Security Audit Inspection Framework

Continuous security validation mandates automated scripts executing in CI/CD pipelines to ensure infrastructure definitions strictly adhere to corporate compliance baselines.

---

### 12.2 Production Python Security Auditor Script

The complete, runnable Python engine below audits simulated cloud storage resources and IAM policy definitions, detecting open public access, weak TLS versions, unencrypted disks, and dangerous administrative wildcard combinations:

```python
#!/usr/bin/env python3
"""
Enterprise Cloud Security & IAM Least-Privilege Auditor.
Scans simulated cloud storage buckets and IAM policy JSONs for security violations:
wildcard permissions, disabled encryption, and public access leaks.
Zero external dependencies.
"""

from dataclasses import asdict, dataclass
import json
import logging
import sys
from typing import Any, Dict, List, Optional

logging.basicConfig(
    level=logging.INFO,
    format='{"time":"%(asctime)s","logger":"%(name)s","level":"%(levelname)s","message":"%(message)s"}',
    handlers=[logging.StreamHandler(sys.stdout)],
)
logger = logging.getLogger("sec_auditor")

@dataclass(frozen=True)
class SecurityViolation:
    resource_id: str
    severity: str
    rule_id: str
    issue: str
    remediation: str

class CloudSecurityScanner:
    def audit_storage_resource(
        self, storage_spec: Dict[str, Any]
    ) -> List[SecurityViolation]:
        """Audits cloud storage configurations for public exposure and encryption."""
        violations: List[SecurityViolation] = []
        name = storage_spec.get("name", "unknown")

        # Check 1: Public Anonymous Access
        if storage_spec.get("public_access_enabled", False):
            violations.append(
                SecurityViolation(
                    resource_id=name,
                    severity="CRITICAL",
                    rule_id="SEC-001-PUBLIC-STORAGE",
                    issue="Storage account permits public anonymous internet access.",
                    remediation="Disable public anonymous access and enforce Private Endpoints.",
                )
            )

        # Check 2: Minimum TLS Version Compliance
        tls_ver = storage_spec.get("minimum_tls_version", "TLS1_0")
        if tls_ver in ["TLS1_0", "TLS1_1"]:
            violations.append(
                SecurityViolation(
                    resource_id=name,
                    severity="HIGH",
                    rule_id="SEC-002-INSECURE-TLS",
                    issue=f"Storage permits deprecated, insecure TLS version: {tls_ver}",
                    remediation="Enforce minimum_tls_version = 'TLS1_2' or 'TLS1_3'.",
                )
            )

        # Check 3: Encryption at Rest Enforcement
        if not storage_spec.get("encryption_enabled", True):
            violations.append(
                SecurityViolation(
                    resource_id=name,
                    severity="CRITICAL",
                    rule_id="SEC-003-UNENCRYPTED-STORAGE",
                    issue="Storage account does not enforce encryption at rest.",
                    remediation="Enable CMEK or platform-managed AES-256 encryption.",
                )
            )

        return violations

    def audit_iam_policy(
        self, policy_name: str, policy_doc: Dict[str, Any]
    ) -> List[SecurityViolation]:
        """Audits IAM policies for over-privileged administrative wildcards."""
        violations: List[SecurityViolation] = []
        statements = policy_doc.get("Statement", [])

        for idx, stmt in enumerate(statements):
            effect = stmt.get("Effect", "")
            actions = stmt.get("Action", [])
            resources = stmt.get("Resource", [])

            if isinstance(actions, str):
                actions = [actions]
            if isinstance(resources, str):
                resources = [resources]

            if effect == "Allow":
                # Check for absolute admin wildcard: Action:* on Resource:*
                if "*" in actions and "*" in resources:
                    violations.append(
                        SecurityViolation(
                            resource_id=f"{policy_name}::Statement[{idx}]",
                            severity="CRITICAL",
                            rule_id="IAM-001-ADMIN-WILDCARD",
                            issue="IAM policy grants unrestricted admin privileges ('Action:*' on 'Resource:*').",
                            remediation="Scope permissions strictly down to least-privilege actions and resource ARNs.",
                        )
                    )
                # Check for privilege escalation actions without boundary
                dangerous_actions = {
                    "iam:PassRole",
                    "iam:CreatePolicyVersion",
                    "iam:AttachUserPolicy",
                }
                matched_dangerous = set(actions).intersection(
                    dangerous_actions
                )
                if matched_dangerous and "*" in resources:
                    violations.append(
                        SecurityViolation(
                            resource_id=f"{policy_name}::Statement[{idx}]",
                            severity="HIGH",
                            rule_id="IAM-002-PRIVILEGE-ESCALATION",
                            issue=f"IAM policy allows privilege escalation actions: {list(matched_dangerous)} on all resources.",
                            remediation="Restrict Resource ARN and attach an IAM Permission Boundary.",
                        )
                    )

        return violations

if __name__ == "__main__":
    scanner = CloudSecurityScanner()

    mock_storage_bucket = {
        "name": "stprodcustomerdata01",
        "public_access_enabled": True,
        "minimum_tls_version": "TLS1_0",
        "encryption_enabled": True,
    }

    mock_iam_policy_doc = {
        "Statement": [
            {
                "Sid": "DangerousAdminAccess",
                "Effect": "Allow",
                "Action": "*",
                "Resource": "*",
            },
            {
                "Sid": "DangerousPassRole",
                "Effect": "Allow",
                "Action": ["iam:PassRole"],
                "Resource": "*",
            },
        ]
    }

    findings: List[SecurityViolation] = []
    findings.extend(scanner.audit_storage_resource(mock_storage_bucket))
    findings.extend(
        scanner.audit_iam_policy("AppDeploymentPolicy", mock_iam_policy_doc)
    )

    report = {
        "audit_status": "FAILED" if findings else "PASSED",
        "total_violations": len(findings),
        "violations": [asdict(v) for v in findings],
    }
    print(json.dumps(report, indent=2))
```

---

# 13. Senior Security SRE Incident Response Runbooks

### 13.1 Runbook 1: Leaked Cloud API Key / IAM Credential Compromise

#### Incident Severity: P1 — CRITICAL
- **Symptom:** Automated alert from GitHub Secret Scanning or AWS Health: *"AWS Access Key ID `AKIAIOSFODNN7EXAMPLE` has been identified publicly on GitHub."*

#### Step-by-Step Remediation Protocol:
```bash

# 14. Immediately deactivate the compromised access key to halt active exploitation

aws iam update-access-key \
  --access-key-id AKIAIOSFODNN7EXAMPLE \
  --status Inactive \
  --user-name payment-service-user

# 15. Query CloudTrail to identify all API actions executed by the compromised credential in past 24h

aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=AKIAIOSFODNN7EXAMPLE \
  --max-results 50 \
  --output table

# 16. Check for persistence mechanisms created by the attacker:

# - Newly created IAM users, access keys, or login profiles
aws iam list-users --query 'Users[?CreateDate>=`2026-10-01`]'
# - Modified trust relationships on IAM roles
# - Rogue EC2 instances or unauthorized Lambda functions

# 17. Permanently delete the compromised credential

aws iam delete-access-key \
  --access-key-id AKIAIOSFODNN7EXAMPLE \
  --user-name payment-service-user

# 18. Long-term hardening: Migrate workload to OIDC Workload Identity Federation

```

---

### 18.2 Runbook 2: Triage of a Reverse Shell in Production (Falco Alert)

#### Incident Severity: P1 — CRITICAL
- **Symptom:** PagerDuty alert from Falco runtime engine: `CRITICAL: Unauthorized Shell Spawned in Production (pod=payment-api-7b89-z4r8q)`.

#### Step-by-Step Remediation Protocol:
```bash

# 19. DO NOT delete the pod immediately (preserves in-memory forensic evidence!)

# Apply an emergency network quarantine NetworkPolicy blocking all ingress & egress:
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: quarantine-compromised-pod
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: payment-api
  policyTypes:
    - Ingress
    - Egress
EOF

# 20. Inspect active process tree inside the quarantined container

kubectl exec -n production payment-api-7b89-z4r8q -- ps -ef --forest

# 21. Capture open network socket connections and listening ports

kubectl exec -n production payment-api-7b89-z4r8q -- ss -tulpn

# 22. Cordon and drain the host Kubernetes worker node to prevent cluster breakout

kubectl cordon aks-nodepool1-18274-vmss000003
kubectl drain aks-nodepool1-18274-vmss000003 --ignore-daemonsets --delete-emptydir-data

# 23. Capture node volatile memory dump for forensics, then terminate the VM instance.

```

---

### 23.3 Runbook 3: Azure Key Vault Access Policy vs. Azure RBAC Conflict Recovery

#### Incident Severity: P2 — HIGH
- **Symptom:** Application reports `Forbidden: Caller is not authorized to perform action on resource Key Vault`, even after adding Azure RBAC `Key Vault Secrets Officer` permissions.

#### Step-by-Step Remediation Protocol:
```bash

# 24. Inspect the Key Vault Permission Authorization Model:

# Azure Key Vault operates in ONE of two mutually exclusive permission models:
# a) Legacy Vault Access Policies
# b) Modern Azure Role-Based Access Control (RBAC)
az keyvault show \
  --name "kv-payment-prod-01" \
  --query "properties.enableRbacAuthorization"

# 25. If 'enableRbacAuthorization' is FALSE:

# Azure RBAC role assignments are COMPLETELY IGNORED by the Key Vault!
# Remediation Option A: Switch vault to modern Azure RBAC model:
az keyvault update \
  --name "kv-payment-prod-01" \
  --enable-rbac-authorization true

# Remediation Option B: Grant permissions via legacy Access Policy:
az keyvault set-policy \
  --name "kv-payment-prod-01" \
  --spn "<APPLICATION_SERVICE_PRINCIPAL_ID>" \
  --secret-permissions get list
```

---

# 26. Summary Comparison & Production Engineering Reference Matrix

| Security Domain | Azure Architecture | AWS Architecture | Core Invariant |
| :--- | :--- | :--- | :--- |
| **Identity & Access** | Microsoft Entra ID (PIM, RBAC) | AWS IAM (SCPs, Roles, Boundaries) | Explicit Deny always overrides Allow; enforce zero standing admin access. |
| **Secret Management** | Azure Key Vault (Managed HSM) | AWS KMS & Secrets Manager | Envelope encryption: Master KEK protects DEK inside FIPS 140-3 HSM. |
| **Posture & Compliance**| Defender for Cloud | AWS Security Hub & GuardDuty | Continuous CIS benchmark auditing and automated drift remediation. |
| **Web Defense** | Azure Front Door WAF (OWASP 3.2)| AWS WAF | Inspects Layer 7 HTTP payloads before traffic reaches backend origin. |
| **Runtime Detection** | Defender for Containers | GuardDuty for EKS / Falco | Kernel-level eBPF syscall inspection for container anomaly detection. |
| **Cross-Cloud Auth** | Workload Identity Federation | IAM Roles Anywhere / OIDC | Short-lived OIDC tokens eliminate all static cloud credentials. |

---

# 27. References & Further Reading

- [NIST SP 800-207: Zero Trust Architecture](https://csrc.nist.gov/publications/detail/sp/800-207/final) — Official US Government Zero Trust standard.
- [AWS IAM Policy Evaluation Logic Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html) — Comprehensive guide to AWS authorization evaluation.
- [CIS Cloud Benchmarks (Azure & AWS)](https://www.cisecurity.org/cis-benchmarks/) — Center for Internet Security cloud baseline controls.
- [OWASP Top 10 Web Application Security Risks](https://owasp.org/www-project-top-ten/) — Canonical web vulnerability guide.
- [Falco Runtime Security Documentation](https://falco.org/docs/) — eBPF kernel intrusion detection.
- [AWS KMS Cryptographic Details](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html) — Envelope encryption technical whitepaper.