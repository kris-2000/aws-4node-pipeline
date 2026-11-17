# 🚀 Automated CI/CD Pipeline on AWS (4-Node Architecture)

An end-to-end, VM-native Continuous Integration and Continuous Deployment (CI/CD) pipeline built on AWS EC2. This project automates the source checkout, compilation, static code analysis, artifact archival, and deployment of a Java web application without containerization overhead, maintaining strict process isolation across four dedicated servers.

## 🏗️ System Architecture

The pipeline is orchestrated by a central Jenkins controller and spans a Virtual Private Cloud (VPC) protected by a shared Security Group for exact port allowances (Ports 22, 8080, 8081, 9000).

| Server Node | OS | Primary Tool Stack | Pipeline Role |
| :--- | :--- | :--- | :--- |
| **Node 1: Jenkins** | Ubuntu Server 22.04 LTS | OpenJDK 21, Git, Jenkins, Maven 3.9.16 | **Controller:** Orchestrates all stages, manages credentials, and executes SSH deployments. |
| **Node 2: SonarQube** | Amazon Linux 2023 | Amazon Corretto 21, SonarQube 26.11 | **SAST:** Executes static application security testing and tracks the Quality Gate. |
| **Node 3: Nexus** | Amazon Linux 2023 | Amazon Corretto 8, Sonatype Nexus 3.68 | **Artifacts:** Stores versioned snapshot `WAR` binaries for auditability and rollback. |
| **Node 4: Tomcat** | Amazon Linux 2023 | Amazon Corretto 17, Apache Tomcat 9 | **Production:** Runtime servlet container hosting the live Java web application. |

## ⚙️ Pipeline Execution Flow (Jenkinsfile)

The scripted pipeline consists of 5 automated stages executed upon code commit:

1. **Stage 1 - Source Checkout:** Clones the upstream Java web application from the public GitHub SCM.
2. **Stage 2 - Maven Build:** Executes `mvn clean package` to resolve dependencies, compile resources, and build `maven-web-app.war`.
3. **Stage 3 - Static Code Analysis (SAST):** Injects a secure authentication token and executes the SonarQube Maven scanner to detect bugs, vulnerabilities, and code smells.
4. **Stage 4 - Artifact Upload:** Pushes the verified `WAR` artifact to a hosted snapshot repository inside Sonatype Nexus.
5. **Stage 5 - Deployment:** Binds the EC2 `.pem` private key via Jenkins SSH Agent and executes a zero-touch `scp` transfer of the `WAR` file directly into Tomcat's `webapps/` directory for hot-deployment.

## 🔐 Production Readiness & Best Practices Applied

* **Cryptographic Credential Isolation:** Zero hardcoded passwords or SSH keys in the `Jenkinsfile`. All secrets (GitHub login, Sonar-Token, Nexus login, and Tomcat SSH Key) are dynamically injected at runtime via the Jenkins Credential Manager.
* **Kernel & Memory Hardening:** Configured kernel virtual memory limits (`vm.max_map_count=262144`) and tuned JVM heap parameters so Elasticsearch (SonarQube) and Nexus remain stable under peak memory loads.
* **Tool Version Consistency:** Standardized the Maven 3.9.16 environment across both compilation and Sonar scanner stages to eliminate runtime resolution failures.

## 📂 Repository Structure

```text
├── Jenkinsfile               # The complete 5-stage scripted pipeline code
├── README.md                 # Project documentation and architecture
├── report/                   # Comprehensive LaTeX engineering report & PDF
└── screenshots/              # Evidence of execution and live deployment
    ├── screenshot1_aws_ec2.png
    ├── screenshot2_jenkins_tools.png
    ├── screenshot2_jenkins-sonarqube-server-conf.png
    ├── screenshot4_jenkins_stage_view.png
    ├── screenshot5_console_output.png
    ├── screenshot6_sonarqube_dashboard.png
    ├── screenshot7_nexus_artifact.png
    └── screenshot8_tomcat_live_app.png

## 👤 Author

**KRISHNA PRASAD S**
* **GitHub:** [@kris-2000](https://github.com/kris-2000)
