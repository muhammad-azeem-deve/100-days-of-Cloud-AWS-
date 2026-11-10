# Day 41 - AWS KMS Verification Commands

This file contains only the verification commands used for **Day 41** of my **100 Days of Cloud (AWS) Challenge by KodeKloud**.

The objective was to verify the AWS KMS key configuration, encrypted file, decrypted file, and data integrity.

---

## 1. Verify AWS Identity

```bash
aws sts get-caller-identity
```

---

## 2. Verify KMS Key

```bash
aws kms describe-key \
    --key-id alias/devops-KMS-Key \
    --region us-east-1
```

---

## 3. Verify Key Configuration

```bash
aws kms describe-key \
    --key-id alias/devops-KMS-Key \
    --region us-east-1 \
    --query 'KeyMetadata.[KeyId,KeyState,KeyUsage,KeySpec,Enabled]' \
    --output table
```

Expected configuration:

```text
KeyState : Enabled
KeyUsage : ENCRYPT_DECRYPT
KeySpec  : SYMMETRIC_DEFAULT
Enabled  : True
```

---

## 4. Verify KMS Alias

```bash
aws kms list-aliases \
    --region us-east-1 \
    --query "Aliases[?AliasName=='alias/devops-KMS-Key'].[AliasName,TargetKeyId]" \
    --output table
```

---

## 5. Verify Encrypted File Exists

```bash
ls -lh /root/EncryptedData.bin
```

---

## 6. Verify Decrypted File Exists

```bash
ls -lh /root/DecryptedData.txt
```

---

## 7. Verify Original File Exists

```bash
ls -lh /root/SensitiveData.txt
```

---

## 8. Verify File Types

```bash
file /root/SensitiveData.txt
file /root/EncryptedData.bin
file /root/DecryptedData.txt
```

---

## 9. Compare Original and Decrypted Files

```bash
diff /root/SensitiveData.txt /root/DecryptedData.txt
```

If both files are identical, no differences will be displayed.

---

## 10. Verify Files Using CMP

```bash
cmp /root/SensitiveData.txt /root/DecryptedData.txt
```

No output indicates that both files are identical.

For an explicit success message:

```bash
cmp -s /root/SensitiveData.txt /root/DecryptedData.txt \
    && echo "SUCCESS: Original and decrypted files match." \
    || echo "ERROR: Files do not match."
```

---

## 11. Compare File Checksums

```bash
sha256sum /root/SensitiveData.txt /root/DecryptedData.txt
```

Both files should have the same SHA-256 checksum.

---

## 12. Final KMS Verification

```bash
aws kms describe-key \
    --key-id alias/devops-KMS-Key \
    --region us-east-1 \
    --query 'KeyMetadata.[KeyId,KeyState,KeyUsage,KeySpec,Enabled]' \
    --output table
```

---

## Day 41 Verification Result

```text
KMS Key Alias  : devops-KMS-Key
Key Type       : Symmetric
Key Usage      : ENCRYPT_DECRYPT
Key Status     : Enabled
AWS Region     : us-east-1

Original File  : SensitiveData.txt
Encrypted File : EncryptedData.bin
Decrypted File : DecryptedData.txt

Data Integrity : Verified
Status         : Successfully Completed
```

**Day 41 — Completed Successfully ✅**

100 Days. 100 Challenges. One Cloud Journey. ☁️🚀
