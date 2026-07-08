# Lab Solution: IAM Policies and Roles

**Student Name:** Balint Lojt_______ 
**Date:** 08/07/2026________________  
**Lab Completion Time:** ___________ minutes

---

## Part 1: Understanding IAM Policy Structure

### Task 1: Policy Components Explanation

**Explain each component in your own words:**

**Version:**
It is the language edition that I'm writing in. I can specify here which rules and features I want to use when reading the document. For AWS, it usually is "2012-10-17"


**Statement:**
This is the main body of the policy, acting as the container for all your rules. It’s basically a big list where I write down all the individual permission blocks I want to enforce in the policy.


**Sid:**
optional but useful, this works as a nickname giving option. Makes it easier to remember what the given policy is about without digging deep inside the file itself. 

**Effect:**
It states whether the person can do a certain action or not. The only 2 options here are "Allow" or "Deny". It is straightforward that "Allow" would give permit to do the action, and "Deny" would not. 


**Action:**
It specifies what actions can be done (edit drafts, delete users, view invoices), and only the specified tasks here can be executed; anything that is not described here, the person has no permission.


**Resource:**
This draws the boundary line for the user. Specifies where exactly they can perform their actions. Which folder, which server, etc. 


## Part 2: Custom IAM Policies Created

### S3 Read-Only Policy

**Policy Name: S3-ReadOnly-SpecificBucket

**Bucket Name Used:** S3-ReadOnly-SpecificBucket

**Policy JSON:**
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListSpecificBucket",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME"
    },
    {
      "Sid": "ReadObjectsInBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:GetObjectVersion"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}

**Screenshot 1: S3 Custom Policy**
<img width="1469" height="790" alt="01-s3-policy" src="https://github.com/user-attachments/assets/5e359a0d-12f6-4a06-924e-db6deaed91aa" />


---

### EC2 Start/Stop Policy

**Policy Name: EC2-StartStop-Only

**Policy ARN: arn:aws:iam::743631836010:policy/EC2-StartStop-Only

**Screenshot 2: EC2 Custom Policy**
<img width="1492" height="798" alt="02-ec2-policy" src="https://github.com/user-attachments/assets/2ce39113-010d-46ce-a3f8-cf999306d7c3" />


---

### CloudWatch Logs Write Policy

**Policy Name: CloudWatch-Logs-Write-Only

**Policy ARN: arn:aws:iam::743631836010:policy/CloudWatch-Logs-Write-Only
**Screenshot 3: CloudWatch Logs Policy**
<img width="1463" height="782" alt="03-cloudwatch-policy" src="https://github.com/user-attachments/assets/a73cd74e-601e-40e2-8967-72de6fe47c0d" />


---

## Part 3: Policy Attachments

### Policy Attached to User

**User Name: alice

**Policy Attached: S3-ReadOnly-SpecificBucket

**Attachment Method: Console 

**CLI Command (if used):**
N/A

**Screenshot 4: Policy Attached**
<img width="1951" height="1025" alt="04-policy-attached" src="https://github.com/user-attachments/assets/8008395e-2737-4505-9e37-715e3bec81c1" />


---

## Part 4: IAM Roles Created

### EC2 Service Role

**Role Name: EC2-S3-ReadOnly-Role

**Role ARN:arn:aws:iam::743631836010:role/EC2-S3-ReadOnly-Role

**Trusted Entity:ec2.amazonaws.com

**Attached Policies:**
1. AmazonS3ReadOnlyAccess
2. N/A

**Trust Relationship JSON:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    
    
  ]
}
```

**Screenshot 5: EC2 Service Role**
<img width="1472" height="784" alt="05-ec2-role" src="https://github.com/user-attachments/assets/458e2ba8-48ba-4e99-9b0a-223de19ab1b8" />


---

### Lambda Execution Role

**Role Name: Lambda-Basic-Execution-Role

**Role ARN: arn:aws:iam::743631836010:role/Lambda-Basic-Execution-Role

**Attached Policies:**
1. AWSLambdaBasicExecutionRole
2. CloudWatch-Logs-Write-Only

**Screenshot 6: Lambda Role**
<img width="1955" height="788" alt="06-lambda-role" src="https://github.com/user-attachments/assets/6e453640-d4c7-4539-ba75-9c24aafa0da9" />


---

### Cross-Account Access Role

**Role Name: CrossAccount-ReadOnly-Role

**Role ARN: arn:aws:iam::743631836010:role/CrossAccount-ReadOnly-Role

**External Account ID:********6010

**External ID: lojt-cloud

**Attached Policies:**
1. ReadOnlyAccess

**Screenshot 7: Cross-Account Role**
<img width="1980" height="975" alt="07-cross-account-role" src="https://github.com/user-attachments/assets/8e47ba6c-23c8-43e6-84ea-44378967f2fb" />


---

## Part 5: Policy Testing

### Policy Simulator Results

**Policy Tested: Yes

**Test Results:**

| Action | Expected Result | Actual Result | Pass/Fail |
|--------|----------------|---------------|-----------|
| s3:GetObject    | Allowed| | Pass |
| s3:PutObject    | Denied | | Fail |
| s3:DeleteObject | Denied | | Fail |
| ec2:StartInstances         | N/A  |
| ec2:TerminateInstances     | N/A  |

**Screenshot 8: Policy Simulator**
<img width="1863" height="599" alt="08-policy-simulator" src="https://github.com/user-attachments/assets/dfe8ba4a-9139-4131-ba9c-995d4c9bae42" />


---

### AWS CLI Testing

**Test 1: S3 List Bucket**
```bash
# Command:

# Output:
_____________________________________________________________
_____________________________________________________________

# Result: ☐ Success ☐ Access Denied
```

**Test 2: S3 Upload File**
```bash
# Command:

# Output:
_____________________________________________________________
_____________________________________________________________

# Result: ☐ Success ☐ Access Denied (Expected)
```

**Test 3: S3 Download File**
```bash
# Command:

# Output:
_____________________________________________________________
_____________________________________________________________

# Result: ☐ Success ☐ Access Denied
```

---

## Part 6: Least Privilege Implementation

### Custom Policy with Conditions

**Policy Name:** ___________________________

**Condition Type Used:** ☐ IP Address ☐ Time Window ☐ MFA ☐ Other: _______

**Policy JSON:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        
      ],
      "Resource": "",
      "Condition": {
        
      }
    }
  ]
}
```

**Rationale for this policy:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

---

## Part 7: Troubleshooting

### Issue Encountered (if any)

**Issue Description:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Commands Used to Diagnose:**
```bash
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Resolution:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Screenshot 9: Troubleshooting Output**
![Troubleshooting](screenshots/09-troubleshooting.png)

---

## Reflection Questions

### 1. Why are IAM roles preferred over access keys for EC2 instances?

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

### 2. Explain the principle of least privilege and how you applied it in this lab.

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

### 3. What is the difference between identity-based and resource-based policies?

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

### 4. When would you use an explicit "Deny" in a policy?

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

### 5. Describe a scenario where you'd use conditions in IAM policies.

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

---

## Summary of Resources Created

**IAM Policies:**
1. ___________________________  (ARN: ___________________________)
2. ___________________________  (ARN: ___________________________)
3. ___________________________  (ARN: ___________________________)

**IAM Roles:**
1. ___________________________  (ARN: ___________________________)
2. ___________________________  (ARN: ___________________________)
3. ___________________________  (ARN: ___________________________)

**Users Modified:**
1. ___________________________

---

## Cleanup Confirmation

- [ ] Detached all custom policies from users
- [ ] Deleted custom IAM policies
- [ ] Detached policies from roles
- [ ] Deleted test IAM roles
- [ ] Verified no resources remain

**Cleanup Commands:**
```bash
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

---

## Self-Assessment

**Rate your understanding (1-5):**

| Concept | Before Lab | After Lab | Improvement |
|---------|-----------|-----------|-------------|
| IAM Policy Structure | ___/5 | ___/5 | +___ |
| Custom Policy Creation | ___/5 | ___/5 | +___ |
| IAM Roles | ___/5 | ___/5 | +___ |
| Service Roles | ___/5 | ___/5 | +___ |
| Trust Relationships | ___/5 | ___/5 | +___ |
| Policy Testing | ___/5 | ___/5 | +___ |
| Least Privilege | ___/5 | ___/5 | +___ |
| Troubleshooting IAM | ___/5 | ___/5 | +___ |

---

## Instructor Verification

**Instructor Name:** ___________________________

**Date Reviewed:** ___________________________

**All policies validated:** ☐ Yes ☐ No

**Roles properly configured:** ☐ Yes ☐ No

**Comments:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Grade/Status:** ___________________________

---

**Lab Status:** ☐ Complete ☐ Needs Revision

**Submission Date:** ___________________________
