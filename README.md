# CI/CD Implementation for Azure Data Factory with Azure DevOps  

This project demonstrates how to implement a **CI/CD pipeline for Azure Data Factory (ADF)** using **Azure DevOps**. It covers environment setup, version control, build and release pipelines, and secure deployments across **Development**, **Testing**, and **Production** environments.  

### Architecture
![CI/CD Architecture](https://github.com/Alex-Ajim/CI-CD-for-Data-Factory-using-Azure-DevOps/blob/main/CICD.png)

### 1. Environment Setup  
- Three **resource groups** were created: `Dev`, `Test`, and `Prod`.  
- For each environment, a separate **Data Factory** instance was provisioned (`ADF-Dev`, `ADF-Test`, `ADF-Prod`).  
- External dependencies such as **Data Lake** and **Databricks** were also provisioned per environment.  
- Each Data Factory was granted **Blob Storage Contributor** access to its respective Data Lake.  

### 2. Version Control Integration  
- An **Azure DevOps Project** was created with a Git repository.  
- The **Development Data Factory** was connected to this repository.  
- Workflow:  
  - Developers create **feature branches** for changes.  
  - Changes are submitted via **pull requests** and merged into the **main branch** after review.  
  - The **Dev Data Factory** acts as the design surface, but all changes are stored and tracked in Git.  

### 3. Build Pipeline  
Once changes are merged into the main branch:  
- Developers can manually publish to generate ARM templates, or  
- A **YAML Build Pipeline** can automatically:  
  1. Install Node.js and NPM dependencies.  
  2. Validate ADF resources.  
  3. Generate ARM templates.  
  4. Publish ARM templates to the build artifact folder.  

### 4. Release Pipeline 
![Release Pipeline Architecture](https://github.com/Alex-Ajim/CI-CD-for-Data-Factory-using-Azure-DevOps/blob/main/Release%20Pipeline.png)

When a new build artifact is detected, the **Release Pipeline** is triggered automatically.  
It consists of three stages (**Dev**, **Test**, and **Prod**) and each stage executes the following tasks:  

1. **Azure PowerShell Pre-Deployment**  
   - Switch off triggers in the ARM template.  
   - Clean up deleted activities.  

2. **ARM Template Deployment**  
   - Deploy the ARM template into the target environment (Dev, Test, or Prod).  
   - Ensure linked services point to the correct resources for each environment (e.g., Test Data Lake vs Dev Data Lake).  
   - ARM template variables are used to replace hardcoded values.  

3. **Azure PowerShell Post-Deployment**  
   - Switch triggers back on after successful deployment.

![CI/CD Architecture](https://github.com/Alex-Ajim/CI-CD-for-Data-Factory-using-Azure-DevOps/blob/main/Release%20stages.png)

![CI/CD Architecture](https://github.com/Alex-Ajim/CI-CD-for-Data-Factory-using-Azure-DevOps/blob/main/Release%20stage%20closer%20view.png)

### 5. Security & Permissions  
- A **Service Principal** was created for Azure DevOps and assigned **Contributor** roles on all three Data Factories.  
- Any external resources (Data Lakes, Databricks) requiring permissions were also configured via ARM template variables and Key Vault secrets.  

### 6. Approval Gates  
- Before deploying to **Production**, a **manual approval step** was added to the pipeline to ensure controlled releases.  

---

This setup ensures:  
- **Version-controlled development** through Git.  
- **Automated validation and deployment** of ADF resources.  
- **Environment-specific configurations** for Dev, Test, and Prod.  
- **Secure and auditable CI/CD pipelines** with Azure DevOps.  
