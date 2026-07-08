# Lab Solution: IAM Policies and Roles

**Student Name:** Balint Lojt_______ 
**Date:** 08/07/2026________________  
**Lab Completion Time:** 2 hours and 40 minutes

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



### AWS CLI Testing

**Test 1: S3 List Bucket**
```bash
# Command:aws s3 ls s3://bob-dev-test/

# Output:
2026-07-08 15:40:30      87814 sage.jpg

# Result:  Success 


**Test 2: S3 Upload File**
```bash
# Command: aws s3 cp test.txt s3://s3-bob-test/test.txt --profile bob --region eu-north-1

# Output:
upload failed: ./test.txt to s3://s3-bob-test/test.txt An error occurred (AccessDenied) when calling the PutObject operation: User: arn:aws:iam::743631836010:user/bob is not authorized to perform: s3:PutObject on resource: "arn:aws:s3:::s3-bob-test/test.txt" because no identity-based policy allows the s3:PutObject action

# Result: Failed as expected
```

**Test 3: S3 Download File**
```bash
# Command: aws s3 cp s3://s3-bob-test/sage.jpg ./sage-downloaded.jpg

# Output:
download: s3://s3-bob-test/sage.jpg to ./sage-downloaded.jpg

# Result:  Success 


---

## Part 6: Least Privilege Implementation

### Custom Policy with Conditions

**Condition Type Used: ABAC

Policy JSON:
json{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ABACStartStopOwnedInstances",
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Owner": "${aws:username}"
        }
      }
    },
    {
      "Sid": "AllowDescribe",
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    }
  ]
}

**Rationale for this policy:**

I wanted to challenge myself a bit and try ABAC (tag-based access) instead of a
resource-based policy, even though we hadn't covered it in depth yet. The idea is
that instead of naming a specific resource in the policy, I use a condition
that compares a tag on the resource to something about the user (in this case,
the instance's "Owner" tag vs. the username making the request).

I got it working and tested it successfully - I tagged an instance with
Owner=charlie, attached the same policy to both bob and charlie, and confirmed
charlie could start/stop the instance while bob was correctly denied. So I know
it works and roughly why, but I'll admit I don't have a fully solid grasp of
policy variables and conditions yet.
But looking forward for the deep diving CLI day to build it up properly. 



## Part 7: Troubleshooting

### Issue Encountered
While trying to tag an EC2 instance (charlie-ec2-test) and later run
start/stop commands against it, I kept getting errors saying the instance ID
didn't exist - even though I could clearly see it running in the AWS Console.

**Issue Description:**
Running `aws ec2 create-tags` and `aws ec2 describe-instances` against the
known instance ID returned "instance does not exist" errors, despite the
instance being visible and running in the AWS Console.

This confirms the S3-ReadOnly-SpecificBucket policy is working correctly - bob
can read objects (GetObject), but is correctly blocked from uploading
(PutObject), since that action was never granted in the policy.

**Commands Used to Diagnose:**
```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=charlie-ec2-test"
aws configure get region

**Resolution:**
Comparing the AWS Console URL (eu-north-1) against my CLI's default region
(eu-west-2, from `aws configure get region`) showed a mismatch - my CLI was
searching the wrong region for the instance.

I fixed it by adding --region eu-north-1 explicitly to the
affected commands. To avoid hitting this again on future commands, I then
permanently updated my CLI's default region:

aws configure set region eu-north-1

**Screenshot 9: Troubleshooting Output**
<img width="1169" height="913" alt="09-troubleshooting-1" src="https://github.com/user-attachments/assets/26b44517-5e79-44de-a70e-a5cadbc125ad" />

<img width="765" height="1270" alt="09-troubleshooting-3" src="https://github.com/user-attachments/assets/e01972c6-5e61-4ca1-81e9-3099e6f61917" />
<img width="765" height="1335" alt="09-troubleshooting-2" src="https://github.com/user-attachments/assets/fef1c46f-c669-4ce8-8410-b51344926fd9" />
<img width="936" height="1024" alt="09-troubleshooting-5" src="https://github.com/user-attachments/assets/e85c6f89-25fb-4cb8-8821-00d38aa5839f" />
<img width="726" height="1324" alt="09-troubleshooting-4" src="https://github.com/user-attachments/assets/777f05a1-ffed-45bf-857d-cbbbab0d089e" />



## Reflection Questions

### 1. Why are IAM roles preferred over access keys for EC2 instances?

IAM roles are preferred over access keys for EC2 instances because they're
easier to manage and more secure. Roles don't require storing long-term access
keys on the instance itself, which removes the risk of keys leaking or getting
hardcoded somewhere. It's also easier to attach a role to an instance than to
generate and distribute individual keys for every instance one by one.

Roles are also better for auditing - actions taken by the instance are
trackable back to the role, making it clear what was done and by whom, rather
than tracing everything back to a shared or long-lived key.

IAM roles are also more secure than access keys because they use temporary,
automatically-rotating credentials instead of long-lived secret keys sitting
on the instance. This removes the risk of a key being hardcoded somewhere,
leaked, or accidentally pushed to a public repo and staying valid indefinitely
until someone notices.

### 2. Explain the principle of least privilege and how you applied it in this lab.

The principle of least privilege means a user should only have access to the
exact resources and actions needed to do their job - nothing more.

I applied this throughout the lab by creating specific policies rather than
giving broad access. For example, I created a policy that only let bob read
objects from a specific S3 bucket, without giving him upload or delete
permissions. I also created an EC2 policy that only allowed starting,
stopping, and rebooting instances, while explicitly denying termination -
even though that action wasn't granted anyway, adding an explicit deny makes
it clearer and safer.

I also attempted a more advanced version of this using tag-based (ABAC)
policies, where users could only start/stop EC2 instances tagged with their
own username, rather than any instance the group could access. This was a bit
of a stretch for where I'm currently at - I don't have a full grasp of policy
variables and conditions yet - but I got it working and tested it
successfully, which helped me understand the concept better in practice.


### 3. What is the difference between identity-based and resource-based policies?

Identity-based policies are attached to a user, group, or role. They define
what that identity is allowed to do, and the permissions travel with the
identity wherever they go. 

Resource-based policies are attached directly to the resource itself (for
example, an S3 bucket policy), rather than to a user. They define who is
allowed to access that specific resource.
The key difference is that a resource-based policy can grant access to a user in a completely different
AWS account without that user needing any IAM policy of their own, since the
permission is defined on the resource's side instead of the user's side.


### 4. When would you use an explicit "Deny" in a policy?
You use an explicit Deny when you want a rule to be a guardrail. Something that stays true even if other policies elsewhere accidentally grant broader access.


### 5. Describe a scenario where you'd use conditions in IAM policies.

 tag-based (ABAC) access for EC2 instances.
Instead of writing a separate policy for every developer who needs to start/stop their own instances, I tagged each instance with an
"Owner" tag and wrote one policy with a condition that only lets someone start/stop an instance if the tag matches their username. So the same policy works for bob, charlie, or anyone else added later, without needing to touch IAM every time someone joins the team.

## Summary of Resources Created

**IAM Policies:**
1. AmazonEC2ReadOnlyAccess     arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
2. S3-ReadOnly-SpecificBucket  arn:aws:iam::743631836010:policy/S3-ReadOnly-SpecificBucket
3. IAMUserChangePassword       arn:aws:iam::aws:policy/IAMUserChangePassword

**IAM Roles:**
1. AWSServiceRoleForTrustedAdvisor arn:aws:iam::743631836010:role/awsservicerole/trustedadvisor.amazonaws.com/AWSServiceRoleForTrustedAdvisor

2.AWSServiceRoleForResourceExplorer
arn:aws:iam::743631836010:role/aws-service-role/resource-explorer-2.amazonaws.com/AWSServiceRoleForResourceExplorer

3.EC2-S3-ReadOnly-Role arn:aws:iam::743631836010:instance-profile/EC2-S3-ReadOnly-Role

**Users Modified:**
1.bob



## Cleanup Confirmation

- [x] Detached all custom policies from users
- [x] Deleted custom IAM policies
- [x] Detached policies from roles
- [x] Deleted test IAM roles
- [x] Verified no resources remain

**Cleanup Commands:**
N/A
Executed in console.

---

## Self-Assessment

**Rate your understanding (1-5):**

| Concept | Before Lab | After Lab | Improvement |
|---------|-----------|-----------|-------------|
| IAM Policy Structure | 2/5 | 3/5 | +1 |
| Custom Policy Creation | 3/5 | 4/5 | +1 |
| IAM Roles | 3/5 | 4/5 | +1 |
| Service Roles | 1/5 | 1,5/5 | +0,5 |
| Trust Relationships | 2/5 | 2/5 | +0|
| Policy Testing | 1/5 | 2/5 | +1|
| Least Privilege | 3/5 | 3/5 | +0 |
| Troubleshooting IAM | 1/5 | 1,5/5 | +0,5 |

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
