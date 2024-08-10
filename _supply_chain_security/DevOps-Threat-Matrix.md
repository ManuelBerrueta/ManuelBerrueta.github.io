---
title: "DevOps Threat Matrix Table"
parent: "Supply_Chain_Security"
---

> ⚠ **The content is sourced straight from the "[DevOps threat matrix](https://www.microsoft.com/en-us/security/blog/2023/04/06/devops-threat-matrix/)" Microsoft Security blog and the credit for the hard work goes to [Ariel Brukman](https://www.linkedin.com/in/ariel-brukman-43126a149/)**. I just created the table below with links (for my own visual ready to click reference) to the appropriate sections for easier consumption. I think it would b great to elaborate more on these in the future to include further insights 


## DevOps Threat Matrix Table

| [**Initial Access**](#initial-access)                  | [**Execution**](#execution)                      | [**Persistence**](#persistence)                   | [**Privilege Escalation**](#privilege-escalation)                   | [**Credential Access**](#credential-access)                          | [**Lateral Movement**](#lateral-movement)                        | [**Defense Evasion**](#defense-evasion)               | [**Impact**](#impact)                                       | [**Exfiltration**](#exfiltration)                                |
|-------------------------------------|-----------------------------------|-----------------------------------|---------------------------------------------|------------------------------------------------|----------------------------------------------|-----------------------------------|------------------------------------------------|------------------------------------------------|
| [SCM authentication](#scm-authentication)                  | [Poisoned pipeline execution (3)](#poisoned-pipeline-execution-ppe)   | [Change code/pipeline configuration in repository (3)](#changes-in-repository) | [Secrets stored in private repositories](#secrets-in-private-repositories)       | [User credentials](#user-credentials)                               | [Compromise build artifacts](#compromise-build-artifacts)                    | [Service logs manipulation](#service-logs-manipulation)         | [DDoS using pipeline compute resources](#ddos)            | [Clone for private repositories](#clone-private-repositories)                  |
| [CI/CD service authentication](#cicd-service-authentication)        | [Dependencies tampering (3)](#dependency-tampering)        | [Inject in artifacts](#inject-in-artifacts)               | [Commit from pipeline to protected branches](#commitpush-to-protected-branches) | [Service credentials](#service-credentials)                            | [Registry injection](#registry-injection)                            | [Compilation manipulation (2)](#compilation-manipulation)      | [Crypto mining over pipeline compute resources](#cryptocurrency-mining)    | [Access to pipeline logs](#pipeline-logs)                         |
| [Configured webhooks](#configured-webhooks)                 | [DevOps resources compromise](#devops-resources-compromise)       | [Modify images in registry](#modify-images-in-registry)         | [Certificates and identities from metadata services](#certificates-and-identities-from-metadata-services) |  | [Spread from pipeline into deployment resources](#spread-to-deployment-resources) | [Reconfigure branch protections](#reconfigure-branch-protections)                | [Local DoS to CI/CD pipelines](#local-dos)     | [Exfiltrate data from production resources](#exfiltrate-data-from-production-resources)       |
| [Organization’s public repositories](#organizations-public-repositories)  | [Control of common registry](#control-of-common-registry)        | [Create service credentials](#create-service-credentials)        |   |   |                                                                                        |                                                                                              | [Resource deletion](#resource-deletion)                             |
| [Endpoint compromise](#endpoint-compromise)                 |                                    |                                   |                                                                                       |                                                                                              |                                              |

---    
# Initial Access
The initial access tactic refers to techniques an attacker may use for gaining access to the DevOps resources – repositories, pipelines, and dependencies. The following techniques may be a precondition for the next steps:

### SCM Authentication
Access by having an authentication method to the organization’s source code management. It may be a personal access token (PAT), an SSH key, or any other allowed authentication credential. An example of a method an attacker can use to achieve this technique is using a phishing attack against an organization.

### CI/CD Service Authentication
Similar to SCM authentication, an attacker can leverage authentication to the CI/CD service in order to attack the organization’s DevOps.

### Organization’s Public Repositories
Access to the organization’s public repositories that are configured with CI/CD capabilities. Depending on the organization’s configuration, these repositories may have the ability to trigger a pipeline run after a pull request (PR) is created.

### Endpoint Compromise
Using an existing compromise, an attacker can leverage the compromised developer’s workstation, thus gaining access to the organization’s SCM, registry, or any other resource the developer has access to.

### Configured Webhooks
When an organization has a webhook configured, an attacker could use it as an initial access method into the organization’s network by using the SCM itself to trigger requests into that network. This could grant the attacker access to services that are not meant to be publicly exposed, or that are running old and vulnerable software versions inside the private network.

---    
# Execution

### Poisoned Pipeline Execution (PPE)
The execution tactic refers to techniques that may be used by a malicious adversary to gain execution access on pipeline resources – the pipeline itself or the deployment resources. Some of the techniques in this section contain explanations about the different ways to perform them, or what we call sub-techniques:

Poisoned pipeline execution (PPE) – Refers to a technique where an attacker can inject code to an organization’s repository, resulting in code execution in the repository’s CI/CD system. There are different sub-techniques to achieve code execution:

#### Direct PPE (d-PPE)
Cases where the attacker can directly modify the configuration file inside the repository. Since the pipeline is triggered by a new PR and run according to the configuration file – the attacker can inject malicious commands to the configuration file, and these commands are executed in the pipeline.

#### Indirect PPE (i-PPE)
Cases where the attacker cannot directly change the configuration files, or that these changes are not taken into account when triggered. In these cases, the attacker can infect scripts used by the pipeline in order to run code, for example, make-files, test scripts, build scripts, etc.

#### Public PPE
Cases where the pipeline is triggered by an open-source project. In these cases, the attacker can use d-PPE or i-PPE on the public repository in order to infect the pipeline.

### Dependency Tampering
Refers to a technique where an attacker can execute code in the DevOps environment or production environment by injecting malicious code into a repository’s dependencies. Thus, when the dependency is downloaded, the malicious code is executed. Some sub-techniques that can be used to achieve code execution include:

#### Public Dependency Confusion
A technique where the adversary publishes public malicious packages with the same name as private packages. In this case, because package search in package-control mechanisms typically looks in public registries first, the malicious package is downloaded.

#### Public Package Hijack (“repo-jacking”)
Hijacking a public package by taking control of the maintainer account, for example, by exploiting the GitHub user rename feature.

#### Typosquatting
Publishing malicious packages with similar names to known public packages. In this way, an attacker can confuse users to download the malicious package instead of the desired one.

### DevOps Resources Compromise
Pipelines are, at the core, a set of compute resources executing the CI/CD agents, alongside other software. An attacker can target these resources by exploiting a vulnerability in the OS, the agent’s code, other software installed in the VMs, or other devices in the network to gain access to the pipeline.

### Control of Common Registry
An attacker can gain control of a registry used by the organization, resulting in malicious images or packages executed by the pipeline VMs or production VMs.

---   
# Persistence

### Changes in Repository
Adversaries can use the automatic tokens from inside the pipeline to access and push code to the repository (assuming the automatic token has enough permissions to do so). They can achieve persistency this way using several sub-techniques:

#### Change/Add Scripts in Code
Attackers can modify existing initialization scripts or add new ones, which download a backdoor or starter for the attacker. This means each time the pipeline executes these scripts, the attacker’s code will also be executed.

#### Change the Pipeline Configuration
Attackers can modify the pipeline configuration to include new steps that download an attacker-controlled script before continuing with the build process.

#### Change the Configuration for Dependencies Locations
Attackers can alter the configuration to point to attacker-controlled packages.

### Inject in Artifacts
Some CI environments allow for the creation of artifacts to be shared between different pipeline executions. For example, in GitHub, artifacts can be stored and downloaded using a GitHub action from the pipeline configuration. Attackers can inject malicious code into these artifacts.

### Modify Images in Registry
If pipelines have permissions to access the image registry (for example, to write back images after a build is completed), attackers could modify and plant malicious images in the registry, which would continue to be executed by user containers.

### Create Service Credentials
A malicious adversary can leverage the access they already have in the environment and create new credentials for use if the initial access method is lost. This can be done by creating access tokens to the SCM, application itself, cloud resources, and more.

---    
# Privilege Escalation

### Secrets in Private Repositories
Leveraging an already gained initial access method, an attacker could scan private repositories for hidden secrets. The chances of finding hidden secrets in a private repository are higher than in a public one, as developers might mistakenly assume private repositories are inaccessible from outside the organization.

### Commit/Push to Protected Branches
The pipeline may have access to the repository with permissive access configurations, allowing it to push code directly to protected branches. This could enable an adversary to inject code directly into important branches without team intervention.

### Certificates and Identities from Metadata Services
Once an attacker is operating within cloud-hosted pipelines, they could access metadata services from inside the pipeline and extract certificates and identities from these services (requires high privileges).

---    
# Credential Access

### User Credentials
In cases where customers require access to external services from the CI pipeline (e.g., an external database), these credentials reside inside the pipeline (can be set by CI secrets, environment variables, etc.) and could be accessible to an adversary.

### Service Credentials
Attackers can sometimes find service credentials, such as service-principal-names (SPN), shared-access-signature (SAS) tokens, and more, allowing access to other services directly from the pipeline.

---   
# Lateral Movement

### Compromise Build Artifacts
Similar to other supply chain attacks, once attackers control the CI pipelines, they can interfere with the build artifacts, injecting malicious functionality before the build is complete.

### Registry Injection
If the pipeline is configured with a registry for the build artifacts, attackers could infect the registry with malicious images. These images could later be downloaded and executed by containers using this registry.

### Spread to Deployment Resources
If the pipeline has access to deployment resources, attackers can leverage this access to spread further, potentially leading to code execution, data exfiltration, and more, depending on the permissions granted to the pipelines.

---    
# Defense Evasion

### Service Logs Manipulation
Service logs enable defenders to detect attacks in their environment. An attacker operating within the environment (e.g., in the build pipelines) could alter logs to prevent defenders from detecting the attack. This is similar to altering history logs on a Linux machine to hide executed commands.

### Compilation Manipulation
Attackers may alter the compilation process to inject malicious code without leaving traces in the SCM service. This can be done in several ways:

#### Changing the Code on the Fly
Changing the code right before the build process begins, without altering it in the repository and leaving traces.

#### Tampered Compiler
Changing the compiler in the build environment to introduce malicious code without leaving traces prior to the build process.

### Reconfigure Branch Protections
Branch protection tools allow organizations to configure steps before a PR/commit is approved into a branch. Attackers with admin permissions may alter these configurations, allowing them to introduce code into the branch without user intervention.

---   
# Impact

### DDoS
An adversary could use the compute resources they've accessed to execute distributed denial-of-service (DDoS) attacks on external targets.

### Cryptocurrency Mining
The compromised compute resources could be utilized for cryptocurrency mining, benefiting the attacker.

### Local DoS
Once an attacker is operating on the CI pipelines, they can perform a denial-of-service (DoS) attack on customers by shutting down agents, rebooting, or overloading the VMs.

### Resource Deletion
An attacker with access to resources (e.g., cloud resources, repositories) could delete them, causing a denial of service.

---    
# Exfiltration

### Clone Private Repositories
Once attackers have access to CI pipelines, they can also access private repositories (e.g., using the GITHUB_TOKEN in GitHub), allowing them to clone and access code, gaining access to private intellectual property.

### Pipeline Logs
An adversary could access the pipeline execution logs, viewing access history, build steps, and potentially sensitive information, including credentials to services and user accounts.

### Exfiltrate Data from Production Resources
If pipelines can access production resources, attackers can also access these resources and abuse this access to exfiltrate production data.
