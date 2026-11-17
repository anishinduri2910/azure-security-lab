# Day 2: Contributor, Owner, and the Activity Log

## Goal
Find out what a Contributor can and can't do, see what Owner adds, and use the Activity log to trace who did what.

## Setup
Assigned Bob the Contributor role on rg-lab-dev only (directly to the user, for this lab).

## Test A: Create a storage account in dev
The first attempt failed with "Microsoft.Storage is not registered for the subscription, and you don't have permissions to register a resource provider."
- Cause: Azure needs a resource provider registered on the subscription before you can create that type of resource. Registering a provider is a subscription-level action, which Contributor on one resource group doesn't include.
- Fix: as admin, registered Microsoft.Storage under Subscriptions > Resource providers.
- Result: after registration, Bob created the storage account in rg-lab-dev.
- Lesson: a failed deployment isn't always an RBAC problem. Read the exact error message before deciding what the cause is.

## Test B: Create a storage account in prod
Bob couldn't see rg-lab-prod at all, because he has no role at that scope. Azure hides resource groups you have no access to, so he couldn't create anything there.

## Test C: Assign roles as Contributor
The Add role assignment option was greyed out. Contributor can build and change resources but can't grant access to others.

## Test D: Create a user in Entra ID
Denied. Azure roles control Azure resources, not the directory. Creating users needs an Entra role.

## Break and fix: Owner
Gave Bob Owner on rg-lab-dev and removed Contributor. Bob could then open Add role assignment. During the assignment, Azure asked how much Bob should be allowed to delegate:
- Only selected roles to selected principals (fewer privileges)
- All roles except privileged administrator roles (Owner, User Access Administrator, RBAC Administrator), which is the recommended setting
- All roles (highly privileged)
I picked "all roles" for the lab only, because I removed it afterwards. In a real environment I'd choose the recommended or the restricted option so a role assigner can't create more admins.
Fix: restored Contributor and removed Owner. The Add role assignment option became greyed out again.

## Activity log
Checked the Activity log on rg-lab-dev. It recorded Bob's storage account creation and my role assignment changes, with who did each action, what it was, when, and whether it succeeded. This is how a security team answers "who did what, and when?" during an investigation or audit.

## Cleanup
Deleted the storage account. Confirmed Bob ended with Contributor on rg-lab-dev and nothing extra.

## Azure RBAC roles vs Entra roles
- Entra roles manage the identity directory (users, groups, app registrations). Example: User Administrator.
- Azure RBAC roles manage Azure resources and apply at a scope that's inherited downward. Example: Virtual Machine Contributor.
- They are separate systems. Bob's Contributor role didn't let him create users.
- A Global Administrator can elevate access to manage Azure subscriptions, so I treat that role as highly sensitive.

## GRC link
Separation of duties and least privilege (NIST 800-53 AC-5 and AC-6). Contributor can do work but can't grant access, which keeps building and approving access separate. The Activity log supports audit logging (NIST 800-53 AU family).

## Interview question
Q: What's the difference between an Entra ID role and an Azure RBAC role? Give an example of each.

A: Entra roles control the identity directory, such as users, groups and app registrations. For example, User Administrator can create and manage users. Azure RBAC roles control Azure resources, and they apply at a scope that's inherited downward, such as a subscription or resource group. For example, Virtual Machine Contributor can manage VMs within its assigned scope. They're separate systems, so a Contributor on a resource group can't create users, which I tested in my lab.
