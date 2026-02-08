## IAM Fundamentals – Roles, Policies, and Trust (Hands-on Learning)

This section documents my ground-up understanding of AWS IAM using a practical, real-world approach instead of theory-first learning.

---

## Core IAM Concepts (Mental Model)

IAM is built on **three separate objects**, each with a distinct responsibility:

1. **IAM Policy** – Defines permissions (WHAT is allowed)
2. **IAM Role** – A container for policies that can be assumed
3. **Entity** – A user or AWS service that assumes a role (WHO uses it)

Nothing works unless all three are connected correctly.

---

## IAM Policy

**What it is**
- A JSON document that defines permissions
- Contains `Effect`, `Action`, and `Resource`
- Has no identity and cannot act on its own

**Key rule**
> A policy does nothing until it is attached to a role (or user).

**Example**
```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::example-bucket/*"
}
