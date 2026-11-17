# Day 3: Sign-in Protection and Sign-in Logs

## Security defaults
Checked Entra ID > Properties > Manage security defaults. Status: Enabled. Security defaults is a free baseline that requires MFA registration and challenges, and blocks legacy authentication. It explains why Alice was asked to set up MFA at first login.

## A successful sign-in event
Read Alice's successful sign-in in Entra ID > Sign-in logs. Each event shows the user, app (Azure Portal), time, location, device browser and OS, result, and authentication details. The successful event showed multifactor authentication, as expected with Security defaults on.

## Failed sign-ins
Entered a wrong password for Alice 3 times, then the correct one.
- 3 failures, error code 50126 ("Error validating credentials due to invalid username or password"), single-factor because they failed at the password stage.
- Then a success showing multifactor authentication, 29 seconds after the last failure.
- Conditional Access: Not Applied (no policies configured; Security defaults was protecting the account).
Sequence: fail, fail, fail, success (with MFA).

## What this pattern means
- Failures followed by a success with MFA completed: the second factor still stands between an attacker and the account.
- Failures followed by a success with single-factor only would be serious.
- Many failures on one user suggests brute force. A few failures across many users suggests password spray.

## Authentication methods
Alice has Microsoft Authenticator (notification) as her default method. With only one method registered, losing her phone locks her out, so a backup method is recommended.

## Security defaults vs Conditional Access
- Security defaults: free, one switch, fixed baseline for everyone.
- Conditional Access: rule-based (for example, require MFA only from certain locations, or block risky sign-ins). Needs a paid Entra license and can't be used together with Security defaults.

## GRC link
- IA-2(1): MFA for access.
- AC-7: unsuccessful logon attempts.
- AU family: audit logging and review of sign-in events.

## Interview question
Q: A user reports they can't sign in, and your security team suspects their account is under attack. Where do you look first in Azure, and what are you looking for?

A: I’d start in Microsoft Entra ID, under Monitoring, in the Sign-in logs. I’d filter by that user and the time window of the report, then look at the status, error codes, locations, IP addresses and whether MFA was used. Repeated 50126 failures would suggest password guessing. A success after failures with only single-factor authentication, or a sign-in from an unusual country, would be a red flag. If I suspect compromise, I’d confirm with the user, reset their password, revoke their sessions, and check the audit and Activity logs for anything the account changed after signing in. If we have the licensing, I’d also check the risky sign-ins report.
