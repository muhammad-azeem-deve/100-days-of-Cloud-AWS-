# Day 01 - AWS EC2 Key Pair Commands

This file contains the AWS CLI commands used for **Day 01** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The task was to create an AWS EC2 key pair named `datacenter-kp` using the **RSA** key type in the **us-east-1** region.

---

# 1. Check AWS CLI

First, I checked whether the AWS CLI was available on the AWS client machine:

```bash
aws --version
```

Example:

```text
aws-cli/2.x.x Python/3.x Linux/x86_64
```

---

# 2. Retrieve KodeKloud Lab Credentials

The KodeKloud lab provides a `showcreds` command to retrieve the temporary AWS credentials.

```bash
showcreds
```

The credentials provided by the lab should be used only during the lab session.

> **Security Note:** Never save AWS credentials in `README.md`, `commands.md`, GitHub repositories, screenshots, or other public locations.

---

# 3. Configure AWS CLI

The AWS CLI can be configured using:

```bash
aws configure
```

The required region for this challenge was:

```text
us-east-1
```

Example configuration:

```text
AWS Access Key ID: <temporary-access-key>
AWS Secret Access Key: <temporary-secret-key>
Default region name: us-east-1
Default output format: json
```

The actual credentials should never be written into the repository.

---

# 4. Verify AWS Identity

I verified that the AWS CLI was connected to the correct lab account:

```bash
aws sts get-caller-identity
```

This command returns information about the AWS identity associated with the current credentials.

---

# 5. Create the RSA Key Pair

The required key pair was:

```text
Name:        datacenter-kp
Type:        RSA
Region:      us-east-1
```

The AWS CLI command to create the key pair was:

```bash
aws ec2 create-key-pair \
    --key-name datacenter-kp \
    --key-type rsa \
    --region us-east-1 \
    --query 'KeyMaterial' \
    --output text > datacenter-kp.pem
```

This command:

* Creates a key pair named `datacenter-kp`.
* Uses the RSA key type.
* Creates the resource in `us-east-1`.
* Saves the private key material into `datacenter-kp.pem`.

---

# 6. Set Private Key Permissions

On Linux, the private key should have restricted permissions:

```bash
chmod 400 datacenter-kp.pem
```

This prevents unauthorized users from accessing the private key.

---

# 7. Verify the Created Key Pair

I verified the key pair using:

```bash
aws ec2 describe-key-pairs \
    --key-names datacenter-kp \
    --region us-east-1
```

If the key pair exists, AWS returns information about:

```text
KeyName
KeyPairId
KeyType
KeyFingerprint
```

---

# 8. Display Key Pair Information

To display the key pair name and type in a simple table:

```bash
aws ec2 describe-key-pairs \
    --key-names datacenter-kp \
    --region us-east-1 \
    --query 'KeyPairs[0].[KeyName,KeyType]' \
    --output table
```

Expected result:

```text
--------------------------------
|       DescribeKeyPairs       |
+----------------+-------------+
| datacenter-kp  | rsa         |
+----------------+-------------+
```

---


# 9. Verify the AWS Region

The resource was required to be created in:

```text
us-east-1
```

The region can be explicitly specified with every EC2 command:

```bash
--region us-east-1
```

For example:

```bash
aws ec2 describe-key-pairs --region us-east-1
```

Using the region explicitly helps ensure that the command operates on the correct AWS region.

---

# 10. Optional Cleanup

If the lab requires the key pair to be deleted after completing the task, the following command can be used:

```bash
aws ec2 delete-key-pair \
    --key-name datacenter-kp \
    --region us-east-1
```

This command should only be used when cleanup is required.

---

# Important Security Notes

Never commit the private key to GitHub.

Add the following entries to `.gitignore`:

```gitignore
*.pem
*.key
.env
```

Never commit:

```text
AWS Access Key ID
AWS Secret Access Key
AWS Session Token
AWS Console Password
datacenter-kp.pem
```

---

# Final Verification

The final configuration was:

```text
Key Pair Name : datacenter-kp
Key Pair Type : RSA
AWS Region    : us-east-1
Status        : Successfully Created
```

---

# Day 01 Result

```text
AWS EC2 Key Pair
       |
       +-- Name: datacenter-kp
       |
       +-- Type: RSA
       |
       +-- Region: us-east-1
       |
       +-- Status: Created Successfully
```

**Day 01 — Completed Successfully **

100 Days. 100 Challenges. One Cloud Journey. 
