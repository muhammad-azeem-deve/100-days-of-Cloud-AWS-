# Day 41 - Securing Data with AWS KMS

## Challenge Overview

This is Day 41 of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The challenge was related to **AWS Key Management Service (KMS), Data Encryption, Decryption, and Secure Key Management**.

The Nautilus DevOps team wanted to improve the security of sensitive data by using AWS Key Management Service (KMS).

The task was to create a symmetric KMS key, use it to encrypt an existing sensitive file, save the encrypted data, and then decrypt the file to verify that the original data was recovered successfully.

### Challenge Requirement

The requirements were:

1. Access the AWS Management Console.
2. Use the `us-east-1` AWS region.
3. Create a symmetric AWS KMS key.
4. Configure the key for encryption and decryption.
5. Create an alias named `devops-KMS-Key`.
6. Encrypt the existing `/root/SensitiveData.txt` file.
7. Save the encrypted data as `/root/EncryptedData.bin`.
8. Decrypt the encrypted file.
9. Save the decrypted content as `/root/DecryptedData.txt`.
10. Verify that the decrypted file matches the original file.

---

## Objectives

The objectives of this challenge are:

- Understand AWS Key Management Service (KMS).
- Create a customer-managed symmetric encryption key.
- Understand KMS key aliases.
- Configure encryption and decryption permissions.
- Encrypt sensitive data using AWS KMS.
- Understand Base64 encoding and decoding.
- Decrypt encrypted data using the same KMS key.
- Verify data integrity after decryption.
- Understand the importance of secure key management.

---

## Environment

| Item | Details |
| ----------------- | -------------------------------- |
| Challenge | 100 Days of Cloud (AWS) |
| Platform | KodeKloud |
| Day | 41 |
| Cloud Provider | Amazon Web Services (AWS) |
| Service | AWS Key Management Service (KMS) |
| Region | `us-east-1` |
| Key Type | Symmetric |
| Key Usage | Encrypt and Decrypt |
| Key Alias | `devops-KMS-Key` |
| Original File | `/root/SensitiveData.txt` |
| Encrypted File | `/root/EncryptedData.bin` |
| Decrypted File | `/root/DecryptedData.txt` |
| Status | Completed |

---

# Solution

## Step 1: Access the AWS Management Console

First, I accessed the AWS Management Console using the temporary credentials provided by the KodeKloud lab environment.

I selected the required AWS region:

```text
us-east-1
```

All resources for this challenge were created in the specified region.

---

## Step 2: Open AWS Key Management Service

From the AWS Management Console, I searched for:

```text
KMS
```

I opened **Key Management Service (KMS)**.

The navigation path was:

```text
AWS Management Console
        |
        v
      Search
        |
        v
       KMS
        |
        v
  Key Management Service
```

---

## Step 3: Create a Customer-Managed KMS Key

Inside the KMS dashboard, I navigated to:

```text
Customer managed keys
        |
        v
    Create key
```

I selected the following configuration:

### Key Configuration

```text
Key Type: Symmetric
Key Usage: Encrypt and decrypt
```

A symmetric key uses the same cryptographic key for both encryption and decryption.

I continued to the next configuration screen.

---

## Step 4: Configure Key Administration and Permissions

During the key configuration process, I reviewed the key administrators and key users.

I retained the appropriate lab account permissions so that the KMS key could be managed and used for encryption and decryption.

The key usage was configured for:

```text
Encrypt and decrypt
```

I then proceeded to the next step.

---

## Step 5: Add the KMS Key Alias

I configured the alias for the newly created KMS key:

```text
devops-KMS-Key
```

The alias provides a friendly name for identifying and referencing the KMS key instead of using its complete key ID.

The final alias reference used for the task was:

```text
alias/devops-KMS-Key
```

I completed the key creation process.

---

## Step 6: Verify the KMS Key in AWS Console

After creating the key, I navigated to:

```text
KMS
  -> Customer managed keys
```

I located the newly created key and verified its configuration.

The expected configuration was:

```text
Key Alias  : devops-KMS-Key
Key Type   : Symmetric
Key Usage  : Encrypt and decrypt
Key Status : Enabled
Region     : us-east-1
```

This confirmed that the KMS key had been created successfully.

---

## Step 7: Locate the Sensitive Data File

After creating the KMS key, I accessed the AWS client machine terminal.

The original sensitive file was available at:

```text
/root/SensitiveData.txt
```

This was the file that needed to be protected using AWS KMS encryption.

The encryption and decryption operations were performed using the AWS CLI because the file was stored on the client machine.

---

## Step 8: Encrypt the Sensitive Data File

I used the newly created KMS key to encrypt the original file.

The encryption command was:

```bash
aws kms encrypt \
    --key-id alias/devops-KMS-Key \
    --plaintext fileb:///root/SensitiveData.txt \
    --query CiphertextBlob \
    --output text | base64 --decode > /root/EncryptedData.bin
```

### Understanding the Command

- `aws kms encrypt`: Performs the encryption operation using AWS KMS.
- `--key-id`: Specifies the KMS key alias.
- `--plaintext`: Specifies the original file that needs encryption.
- `--query CiphertextBlob`: Extracts the encrypted data from the response.
- `--output text`: Returns the ciphertext as Base64-encoded text.
- `base64 --decode`: Converts the Base64 output into its binary representation.
- `> /root/EncryptedData.bin`: Saves the encrypted data to the required file.

After executing the command, the encrypted file was saved at:

```text
/root/EncryptedData.bin
```

**Important:** The Base64 decoding step is necessary here because the AWS CLI returns the ciphertext in Base64 form. It converts that representation into the binary ciphertext file.

---

## Step 9: Decrypt the Encrypted File

After successfully encrypting the sensitive data, I performed the decryption operation using the same KMS key.

The decryption command was:

```bash
aws kms decrypt \
    --key-id alias/devops-KMS-Key \
    --ciphertext-blob fileb:///root/EncryptedData.bin \
    --query Plaintext \
    --output text | base64 --decode > /root/DecryptedData.txt
```

### Understanding the Command

- `aws kms decrypt`: Performs the decryption operation.
- `--key-id`: Specifies the KMS key used for decryption.
- `--ciphertext-blob`: Specifies the encrypted file.
- `--query Plaintext`: Extracts the decrypted plaintext from the response.
- `--output text`: Returns the plaintext in Base64-encoded form.
- `base64 --decode`: Converts the Base64 output back into the original data.
- `> /root/DecryptedData.txt`: Saves the decrypted content into a new file.

The decrypted file was saved at:

```text
/root/DecryptedData.txt
```

---

## Step 10: Verify the Decrypted Data

After decryption, I compared the original file with the decrypted file.

The original file was:

```text
/root/SensitiveData.txt
```

The decrypted file was:

```text
/root/DecryptedData.txt
```

I used the following command to compare both files:

```bash
diff /root/SensitiveData.txt /root/DecryptedData.txt
```

If both files contain identical data, the command produces no differences.

I also verified the file contents and confirmed that the decrypted data matched the original sensitive data.

---

## Step 11: Verify the Encrypted File

I checked that the encrypted file existed:

```text
/root/EncryptedData.bin
```

The encrypted file contained ciphertext rather than the original readable plaintext.

The file was generated successfully using the KMS key.

---

## Step 12: Final Verification in AWS Console

Finally, I returned to the AWS Management Console and opened:

```text
KMS
  -> Customer managed keys
```

I verified the following:

- The KMS key was present.
- The alias was `devops-KMS-Key`.
- The key type was Symmetric.
- The key usage was Encrypt and decrypt.
- The key status was Enabled.
- The resource was created in `us-east-1`.

This confirmed that the required KMS configuration was available and correctly configured.

---

# AWS KMS Encryption Workflow

The overall workflow for this challenge was:

```text
       AWS Management Console
                 |
                 v
           AWS KMS
                 |
                 v
       Create Customer Key
                 |
                 v
          Symmetric Key
                 |
                 v
       Add Alias: devops-KMS-Key
                 |
                 v
          Key Created
                 |
                 v
       SensitiveData.txt
                 |
                 v
          AWS KMS Encrypt
                 |
                 v
         EncryptedData.bin
                 |
                 v
          AWS KMS Decrypt
                 |
                 v
         DecryptedData.txt
                 |
                 v
       Compare Original Data
                 |
                 v
         Data Verified
```

---

# What I Learned

From this challenge, I learned and practiced:

- How to use AWS Key Management Service (KMS).
- How to create a customer-managed KMS key.
- The difference between symmetric and asymmetric encryption.
- How symmetric keys perform encryption and decryption.
- How to create and use KMS aliases.
- How to encrypt sensitive files using AWS KMS.
- How to decrypt encrypted data.
- How Base64 encoding and decoding work with AWS KMS responses.
- How to verify decrypted data against the original file.
- Why encryption keys and sensitive data must be protected.
- How AWS KMS helps manage encryption keys centrally.

# Challenge Status

Day 41 — Completed Successfully ✅

**Created a symmetric AWS KMS key with the alias `devops-KMS-Key`, encrypted `SensitiveData.txt`, saved the encrypted data as `EncryptedData.bin`, decrypted the encrypted file, and verified that the decrypted content matched the original file.**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀

I will continue documenting each challenge and building my practical AWS cloud and security skills step by step.
