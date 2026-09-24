# Ultimate Entra Advisor

## Security Assessment

The purpose of this assessment is to evaluate core Microsoft Entra ID security controls using proven security and governance practices.

---

# Security Control 1: Privileged Role Assignments

## Objective

Evaluate the use of privileged directory roles and identify excessive or potentially risky administrative access.

## MCP Data Collection

Collect:

- All Global Administrators and 
- All Privileged Role Administrators
- All Security Administrators
- For all Priviledge Role Users collect whether MFA is both registered and enforced

Example MCP Queries:

```text
List all users assigned to privileged roles
List all Global Administrators and whether MFA is registered and enforced
List all Privileged Role Administrators
List all Security Administrators
```

## Assessment Focus

Evaluate:

- Number of Global Administrators
- List all Global Administrators with either no MFA registered or MFA enforced and list these specific accounts
- Users with multiple privileged roles
- Potential over-assignment of administrative access

---

# Security Control 2: Potential Risky Users

## Objective

Evaluate tenant-wide users that could be considers a risk in my tenant

## MCP Data Collection

Collect:

- All users Entra ID flagged as risky and the reason why
- All users and their authentication methods
- All user accounts that have not signed in for 30 days

Example MCP Queries:

```text
List authentication methods for all users
List users without MFA authentication configured for their account
List all users that have not signed in for 30 days
```

## Assessment Focus

Evaluate:

- Overall MFA adoption as a % of all users
- Accounts lacking MFA registration
- Dormant accounts that have not signed in for 30 days. List the accounts in a Findings section
- List the specific accounts that Entra ID has flagged as risky, and the specific accounts that are considered dormant


# Governance Control #1: Enterprise Application Permissions

## Objective

Evaluate enterprise applications and service principals for excessive permissions and governance concerns.

## MCP Data Collection

Collect:

- List applications registered in my tenant ownwers
- List all Enterprise applications and Service principals
- Application permissions and consent grants

Example MCP Queries:

```text
List applications registered in my tenant their owners
List all enterprise applications
List all service principals
List application permissions and delegated permissions
List application consent grants
```

## Assessment Focus

Evaluate:

- High privilege application permissions
- Applications with broad tenant access
- Potentially over-permissioned applications
- Legacy or unknown applications
- Provide the specific list of applications with the AllPrincipals grants or high-impact scopes. List the app name, app-id, and grants and scope




