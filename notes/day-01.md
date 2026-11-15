# Day 1: Azure RBAC and Scope

## Setup
Created an Azure free account with a personal email, turned on MFA with Microsoft Authenticator, and set up a monthly budget with alerts at 50%, 80% and 100%. The budget is a detective control: it warns early about unexpected spending, which can be a sign of compromise.

## Azure hierarchy
Azure is organized as tenant, then subscription, then resource group, then resource. The tenant (Microsoft Entra ID) holds identities. The subscription holds resources and billing. Resource groups are containers for related resources. Permissions are given at a scope and inherited downward.

## Lab setup
Created two resource groups (rg-lab-dev and rg-lab-prod), two test users (alice.reader and bob.contrib), and a security group (sg-lab-readers) with Alice as a member. Assigned access to the group instead of the individual because groups are easier to manage and audit.

## Test 1: Reader on rg-lab-dev only
Gave sg-lab-readers the Reader role on rg-lab-dev only. Signed in as Alice in a private window:
- She could see rg-lab-dev.
- She could not see rg-lab-prod.
- She tried to create a storage account. The form opened, but validation failed and nothing was created, because Reader allows viewing only, not creating.
This is least privilege working: access only where it was granted.

## Test 2: Reader at subscription scope (the mistake)
Assigned Reader to the same group at subscription scope. Alice could then see rg-lab-prod as well. Nothing changed on prod itself. The permission was inherited from the subscription. One assignment at too high a scope exposed a whole environment.
Fix: removed the subscription-level assignment. After a few minutes rg-lab-prod disappeared for Alice and rg-lab-dev stayed visible.

## How I verified access
Used Access control (IAM), then Check access, on each resource group:
- rg-lab-prod: Alice had no access.
- rg-lab-dev: Alice had Reader, coming from the group sg-lab-readers.

## Cloud Shell
- az account show: shows which tenant, subscription and identity my commands run as. Useful first check when a command fails with a permissions error.
- az group list -o table: lists the resource groups in the subscription (rg-lab-dev and rg-lab-prod).

## GRC link
This is a technical implementation of least privilege (NIST 800-53 AC-6, and the access control requirement in ISO 27001). In audits I checked whether least privilege was documented. Here I configured it, tested it, and saw what happens when it is misconfigured.

## Interview question
Q: A user has Reader at subscription scope and Contributor on one resource group. What can they do, and how would you verify that?

A: On that one resource group, they can create, modify and delete resources, but they can’t assign roles to others. Everywhere else in the subscription they can only view, because Reader is inherited from the subscription scope. To verify it, I’d open Access control (IAM) on the subscription and on the resource group, use Check access for that user, and look at the Role assignments tab to see which roles apply at which scope. I could also confirm from the CLI with az role assignment list, and test it by signing in as that user and trying to create a resource in the resource group and outside it
