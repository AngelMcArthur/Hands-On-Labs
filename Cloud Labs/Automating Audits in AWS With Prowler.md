
---

# [GRC Project - Automating Audits in AWS With Prowler](https://www.youtube.com/watch?v=FpdqG3Cy6ow&list=TLPQMjgwMzIwMjV8BR6dtEPoWw)

---

**Credits**

- This project was inspired by:
	- [Nicole Enesse - GRC For Mere Mortals](https://www.youtube.com/@nicoleenesse)

---

![image](https://github.com/user-attachments/assets/e95b7901-afb1-4b71-b6b1-60108f13d9f1)

**Summary:**
- This project demonstrates how to automate security audits in AWS using the tool Prowler. The goal is to automate the process of checking for security vulnerabilities and compliance gaps in an AWS environment. **Prowler** is an open-source tool that scans AWS resources for security best practices and compliance standards. It automates scans against various compliance frameworks, including **CIS Benchmarks, NIST 800-53, HIPAA, PCI DSS, and GDPR**.

**Benefits:**
- Saves time and effort compared to manual auditing.
- Improves efficiency and accuracy of security assessments.
- Helps organizations meet compliance requirements.
- Demonstrates valuable skills for cybersecurity professionals.

- **Key Skills/Tools:** Prowler, AWS CLI, compliance frameworks (CIS, NIST, HIPAA, PCI, GDPR)

---

## **I. Prowler CLI Installation**

- _**Note:** We'll be using the [Prowler documentation](https://docs.prowler.com/projects/prowler-open-source/en/latest/) to install Prowler. Head there if you want to follow along that way. Otherwise, we can continue with the process below._

- **Log in to the AWS Management Console:**
    - As a root user (or a user with the necessary administrative privileges).
    - ![image](https://github.com/user-attachments/assets/efc69aa8-4a8d-4ced-9a8a-8802eda09969)
- **Open AWS CloudShell:**
    - Click on the CloudShell icon in the top navigation bar.
    - ![image](https://github.com/user-attachments/assets/7af3d6ec-fa9e-4c49-8070-fc3654bb2c2f)
    - _**Note:** CloudShell provides a command-line interface within the AWS console, based on Bash (similar to Linux)._
- **Install Dependencies:**
    - Clicking the icon opens a new terminal window in CloudShell. After the migration of AWS CloudShell from Amazon Linux 2 to Amazon Linux 2023, there is no longer a need to manually compile Python 3.9 as it's already included in AL2023. Prowler can thus be easily installed following the Generic method of installation via pip.

```bash
sudo bash
adduser prowler
su prowler
python3 -m pip install --user pipx
python3 -m pipx ensurepath
pipx install prowler
cd /tmp
prowler aws
```

>  💡 **What is this code doing?**
> 
> Here's the breakdown of each command you ran in **AWS CloudShell**.
> 
> - **`sudo bash`**
> 	- This command runs a new Bash shell with elevated (root) privileges using `sudo`. This allows unrestricted control over the environment and is typically used for system-level installations or configurations.
> - **`adduser prowler`**
> 	- Creates a new user account named `prowler`. This is a good GRC practice for separation of duties and least privilege—running security tools as a non-root user when possible.
> - **`su prowler`**
> 	- Switches the current shell to the newly created `prowler` user. This ensures that Prowler runs with limited privileges, reducing the risk of system-wide damage or unauthorized access.
> - **`python3 -m pip install --user pipx`**
> 	- Installs `pipx` in the user space using Python’s module execution. It Installs `pipx` without affecting global Python packages, reducing the risk of version conflicts or permission issues. `pipx` is a tool that allows you to install and run Python applications in isolated environments.
> - **`python3 -m pipx ensurepath`**
> 	- Ensures the user's binary path for `pipx` is added to the shell’s `$PATH` variable. This allows you to run pipx-installed apps from anywhere.
> - **`pipx install prowler`**
> 	- Installs the Prowler tool via pipx. Prowler is an AWS Security Best Practices Assessment tool that evaluates your account against CIS benchmarks, compliance frameworks (like PCI, GDPR, HIPAA), and general security posture. When the command is run, it deploys Prowler in a sandboxed Python environment for isolated, safe execution.
> - **`cd /tmp`**
> 	- Changes the working directory to `/tmp`, a common temporary space. Prowler generates reports and logs here unless otherwise specified.
> - **`prowler aws`**
> 	- Executes Prowler's default AWS scan. This command launches a scan of the current AWS account using the identity assumed by CloudShell, checking for security misconfigurations.

- ![image](https://github.com/user-attachments/assets/e447abb2-8532-4bac-859e-f781ac947aac)
- _**Note:** This process might take a while._
- Here is what the last command `prowler aws` produces:
- ![image](https://github.com/user-attachments/assets/49925746-d00b-4ccf-9157-5e39e2e57ccd)
- **ERROR** - It seems the default scan has frozen here:
- ![image](https://github.com/user-attachments/assets/8b058d55-0f15-475a-80d1-f35d94ad46cd)

> We'll move onto the next step and see if prowler actually works.

---

## **II. Create S3 Bucket to Store and View Results**

- Head to Amazon S3
- ![image](https://github.com/user-attachments/assets/171c9a8a-bfe9-42f0-a63e-10c6a94040e1)
- Click "Create bucket".
- ![image](https://github.com/user-attachments/assets/52650f29-fd7f-47b3-ab27-69539e0ed962)
- Name the bucket what you want (make sure it is not taken) and leave everything else as default. Then click "Create bucket".
- ![image](https://github.com/user-attachments/assets/da0fd1a9-2de7-47a3-8394-834c1a447dce)
- ![image](https://github.com/user-attachments/assets/f4d8b4a2-760d-44c7-8b81-7fe98f23120d)
- ![image](https://github.com/user-attachments/assets/6690c42f-dc01-4bc4-9875-e57a97ff3ec6)
- ![image](https://github.com/user-attachments/assets/c3243077-e40c-4fd2-a89f-aaf963d8c13e)
- Success!
- ![image](https://github.com/user-attachments/assets/58fa3942-ecfc-4e4d-a171-370cf05fcb14)
- Now that the S3 bucket is created, we can store the results in here.

---

## **III. Using Prowler CLI for Compliance**

- Prowler allows you to execute checks based on requirements defined in compliance frameworks. By default, it will execute and give you an overview of the status of each compliance framework:
- ![image](https://github.com/user-attachments/assets/4f964e74-ea30-4345-a2fb-3fbbff5863af)
	- _**Note:** You can find CSVs containing detailed compliance results inside the compliance folder within Prowler's output folder._

- Now let's get started with using Prowler. List all of the Compliance Frameworks with `prowler aws --list-compliance`.
- ![image](https://github.com/user-attachments/assets/46ba7c3a-bc81-473a-8c6f-3b925447627c)
- List Requirements of Compliance Frameworks with `prowler aws --list-compliance-requirements <compliance_framework(s)>`. I'll use `prowler aws --list-compliance-requirements gdpr_aws` for an example.
- ![image](https://github.com/user-attachments/assets/32ccead1-23f4-4050-b086-f022863110c2)
- ![image](https://github.com/user-attachments/assets/23d1a373-8126-4da1-931b-faf71f67319f)
- ![image](https://github.com/user-attachments/assets/5b74b41c-9f41-4ea0-a18c-353802500bf0)
- Now to Execute Prowler based on Compliance Frameworks we can run `prowler aws --compliance <compliance_framework>`. I'll use `prowler aws --compliance gdpr_aws` for this example.
- ![image](https://github.com/user-attachments/assets/4ffe19db-4789-4722-a855-fc640332655d)
- Here are the results! They have been stored in `/tmp/output/compliance/prowler-output-009245113520-20250424083604_gdpr_aws.csv`
- ![image](https://github.com/user-attachments/assets/1cd1a30b-d67f-4155-8066-47676d4e79c2)
- Let's run a few more scans and then store the results in our S3 bucket. Let's go with HIPAA as our next framework.
- ![image](https://github.com/user-attachments/assets/ebf4d6de-2aab-4f03-92ec-4da201b0a86f)
- HIPAA results!
- ![image](https://github.com/user-attachments/assets/289dc0d9-83e2-4f05-9e54-76fd1ac932c2)
- Now the SOC 2 framework.
- ![image](https://github.com/user-attachments/assets/251c4c12-5622-4208-8846-66cb96d4dba4)
- SOC 2 Results!
- ![image](https://github.com/user-attachments/assets/34b26af9-9013-4322-84dc-91f4a1cbd3f2)
- I'm curious on PCI, NIST 800-53, NIST CSF, CIS, ISO/IEC 27001, & FedRAMP, so we will do those as well!
	- PCI
	- ![image](https://github.com/user-attachments/assets/60a033e3-cc3d-4c61-b0a0-95ed28dd49c3)
	- NIST 800-53
	- ![image](https://github.com/user-attachments/assets/5303c7d0-649c-413d-bbf8-ac51afca09cf)
	- NIST CSF
	- ![image](https://github.com/user-attachments/assets/a05294ff-ad73-4e40-ae9b-54fc08b8c725)
	- CIS
	- ![image](https://github.com/user-attachments/assets/602d6971-0b71-4437-a377-c40a7fe16d27)
	- ISO/IEC 27001
	- ![image](https://github.com/user-attachments/assets/2d11e01d-fa60-4791-bd30-ada72dd3d7d0)
	- FedRAMP
	- ![image](https://github.com/user-attachments/assets/a903a251-d619-49fb-bc84-c1ba3d759cc1)
- That completes the scanning portion. Now we will copy the results into the bucket we created with this command `aws s3 cp /tmp/output/compliance s3://prowler-am-grc-project/ --recursive --exclude "*" --include "*.csv"` (replace the bucket name with your bucket name).
- ![image](https://github.com/user-attachments/assets/e3aaa3a7-9291-4c24-b4cb-538b21993f9c)

> 💡 **What code did I just run?**
> 
> - **`aws s3 cp`**
> 	- This copies files to/from S3.
> - **`/tmp/output/compliance`**
> 	- This is the local source directory.
> - **`s3://prowler-am-grc-project/`**
> 	- This is your destination S3 bucket.
> - **`--recursive`**
> 	- Ensures all `.csv` files in the directory and subdirectories are copied.
> - **`--exclude "*"`**
> 	- Excludes all files by default.
> - **`--include "*.csv"`**
> 	- Overrides the exclusion and includes only `.csv` files for upload.


> - That completes this step. Now we will see the results in the bucket.

---

## **IV. Reviewing Results**

- **Download the Results:**
    - Go to your S3 bucket.
    - ![image](https://github.com/user-attachments/assets/3e53af9f-72fd-4710-acf3-0fbf972c0e97)
    - Find the scan report you want to review. Let's choose GDPR. Download the report.
    - ![image](https://github.com/user-attachments/assets/d197e0e2-50ee-4138-b784-4c390cc3541b)
- **Open and Analyze:**
    - Open the report in the appropriate software. Now I am not sure if you will have the same issues I had, but for me the report was almost completely unreadable. I did a bit of data cleaning to make it viewable for this project, but ultimately it was not too helpful looking at this information. It was a `csv` file, so when it was imported, it was separated by commas, but this may have backfired. I deleted all the duplicated columns and did "CTRL+F" to find only the word "FAIL", and then highlighted all of the cells in red. There are a few "PASS" cells in here too, but we want to focus on what is wrong. It seems like most of the issues comes with there not being any CloudTrail trails set up to log anything.
    - ![image](https://github.com/user-attachments/assets/014d5a9d-129e-4e02-80fb-60c007e3951a)
    - The report will detail the findings, including:
        - **Attributes:** Severity, description, etc.
        - **Status:** Pass or fail.
        - **Remediation Notes:** Suggestions for fixing identified vulnerabilities.

---

## **V. Optional: Remediation**

> - I will not be completing this step because I will be deleting this account after completing all of my AWS projects, but here are some steps to help you remediate any vulnerabilities you find with prowler.

- **Identify Vulnerabilities:**
    - Review the scan report and identify any failed checks.
- **Consult Remediation Notes:**
    - For each failed check, refer to the corresponding remediation notes in the video or the official compliance framework documentation.
- **Implement Changes:**
    - Follow the remediation steps to address the vulnerabilities.
    - This might involve making changes to AWS configurations, security policies, or other resources.
- **Retest:**
    - After making changes, rerun the Prowler scan to verify that the vulnerabilities have been resolved.

---

## Summary

**Here's a summary of what we learned in this Cybersecurity Project: Automating Audits in AWS with Prowler:**

**I. AWS Security Concept:**

- **Data Protection:** We explored how to safeguard sensitive information by copying audit results to an S3 bucket.

**II. Tools and Automation:**

- **AWS Cloud Shell:** We utilized the Cloud Shell command-line interface to interact with AWS services.
- **Prowler:** We learned how to install and configure Prowler, an automation tool for conducting security audits.
- **Compliance Frameworks:** We discovered how Prowler can be used to run scans against various industry compliance frameworks, including CIS benchmarks, NIST 800-53, HIPAA, PCI DSS, and GDPR.

**III. Practical Skills:**

- **Automating Security Audits:** We practiced automating security scans, saving time and effort compared to manual audits.
- **Report Analysis:** We learned how to interpret the results of the Prowler scan, identifying findings and their status.
- **Remediation:** We understood the basic principles of remediating security vulnerabilities identified by the scan.

**IV. Job Relevance:**

- **GRC Analyst Skills:** These skills are directly applicable to roles focused on governance, risk, and compliance in cybersecurity.
- **Cloud Security Expertise:** We gained practical experience in securing AWS environments, a highly sought-after skill in the industry.

**By completing this project, you have laid a solid foundation for a career in cloud security and compliance. Keep practicing and exploring to further develop your expertise!**

---
