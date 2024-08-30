---
title: "DevOps Process tldr;"
parent: "Supply_Chain_Security"
layout: default
---   
  
# The DevOps CI/CD Process TLDR;
I find that sometimes folks find it confusing to visualize and understand the DevOps process and to understand the security around it. My intention for this write-up is to make what can be a complex topic to understand, into a simple quick read to help you understand the process.

### Diagram of Execution
This is a high level overview of how to visualize the process:
![](/images/2024/SupplyChain/DeVOps-Process-TLDR/DevOpsProcess.png)

# 1. Repository (Code)
This is where the source code of your application resides. It could be hosted on Azure Repos, GitHub, or any other supported version control system.

### Possible Risks + Attacks
- **Pushing malicious code**
  - This could be to a different branch other than main and then attempt to merge it via a PR or if you have the permissions to merge it yourself
  - If there are "weak" permissions you could push straight to the main branch
- **Persistence**
  - Adding external/guest users for persistence
  - Creating an additional account
- **Secrets in source code**
  - You may find credentials/secrets in the source code files such as settings and configurations
  - You may also find secrets in the code history 
- **Bypassing PR approvals & Code Reviews**
  - You may be able to override branch/repo settings to allow you to bypass PR requirements and branch restrictions
- **Code Exfiltration:** You gain access to the code and you clone/download for analysis and possible modifications.

### Mitigations
- **`Main` Branch Protections and Restrictions:** You should protect whatever your **`main`** branch is and use the common standard to develop in a dev branch and not allow pushing into your **`main`** branch.
- **Pull Request:** It is good practice to require pull requests to prevent code to directly be pushed to the main branch and be directly integrated into the product. This could prevent threat actors from poisoning the software with malicious code.
- **Code Review:** Code should be reviewed to check for good code practices, style, bugs and as I pointed above, possible malicious code.
- **Require 2+ Approval** It is also good practice to require multiple engineers to approve the code/pull request, as an additional check to make sure everyone is aligned that this code is to be integrated into the software.
- **Detections:** Watch for unusual activity/access like mass cloning and downloading of the code.

# 2. Build Pipeline (CI)
The build pipeline is responsible for fetching the code from the repository, compiling it, running tests, and producing an artifact as part of the Continuous Integration (CI). It typically involves:
- **Source Code Retrieval:** The pipeline pulls the latest code from the repository.
- **Compilation/Build:** The code is compiled or built, depending on the language and framework.
- **Unit Testing:** Automated tests are run to ensure the code works as expected.
- **Artifact Creation:** The output of the build process, such as binaries, packages, or Docker images, is generated and stored.
- Note that at any time during this process a threat actor could poison the code or gain access to the resources used in the pipeline, thus we should use the principle of least privilege and tightened permissions & security around the pipeline(s). 

### Possible Risks + Attacks
- **Malicious Tasks:** Can be inserted in the build process or replace/modify and original one to do many of the following below.
- **Code Poisoning:** Poison the code during the build process by replacing or adding malicious code to the original code.
- **Code Exfiltration:** If you didn't have access to code, now you do (maybe).
- **Dump environment variables:** The environment variables can often have secrets to access resources needed during the build process which could allow you to pivot/privesc.
-  **Secret exfiltration:** If credentials are being handled as part of the process, you can often exfiltrate them and use them to pivot to other resources such as cloud infrastructure.
-  **Pivot into build server/infrastructure:** If you can execute code you might be able to pivot into the build server.
- Modifying the code/infrastructure that is running the pipelines could end up with similar results to malicious tasks.
- **Logs:** Great source of information.
  - Potential of creds in logs that you could leverage.
  - They can provide information about other infrastructure used during the build process as well as internal information.

### Mitigations
- Practice least privilege and only allow the users who are in charge of the builds to run those pipelines.
- Consider using 2 person approval to run the pipeline(s).


# 3. Artifact (Output of Build)
The artifact is the result of the build process. It is a packaged version of the application, ready for deployment. This could be in the form of compiled binaries, container images, or other deployable packages.
- If a threat actor gains access to this directly, they could replace the artifact with a poisoned/malicious artifact to run code downstream.
- They could also exfiltrate it and analyze the code as well as share it publicly exposing possibly unreleased or internal code.

# 4. Release Pipeline (CD)
The release pipeline takes the artifacts produced by the build pipeline and manages the deployment process as part of Continuous Delivery/Deployment (CD). It typically involves:
- **Staging:** Deploying the artifact to a test environment for further testing (integration, performance, etc.).
- **Approval Gates:** Steps where manual approvals may be required before moving to the next stage.
- **Deployment:** Automated deployment to the production environment, or other environments, as defined in the pipeline.
- Note that at any time during this process Threat Actors could gain access to resources and poison the application's code by replacing it (similar to the CD step) and once again we should use the principle of least privilege and tightened permissions & security around the pipeline(s). 

### Possible Risks + Attacks
These are very similar to the ones of the build pipelines, please reference those above. But some additional quirks:
- Possible access to the infrastructure where the application is being deployed to.
- Possible access to other servers/agents where the release pipelines are being executed, since these could be different from the build pipeline.

### Mitigations
- Practice least privilege and only allow the users who are in charge of the builds/release to run those pipelines.
- Consider using 2 person approval to run the pipeline(s).
- Detections for unusual actions and modifications to the pipeline.
- Use stages and gates for releases that require approval along the way. This can help you catch the attacks and they often have time requirements which can buy you time for incident response efforts and reverting the attacks.

# 5. Deployment of App/Code
The final product of this process involves deploying the application or code to the target environment, such as a web server, cloud infrastructure, or a set of virtual machines. While the deployment is often handled by the Release pipeline, there are a few things to highlight about this step of the process.

The deployment can include tasks like:
- **Configuration Management:** Applying configuration settings specific to the environment.
- **Database Migrations:** Applying database changes if necessary.
- **Verification:** Running smoke tests or other checks to ensure the deployment was successful.
