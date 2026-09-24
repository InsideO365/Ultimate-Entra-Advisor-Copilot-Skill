# Getting Started with the Microsoft MCP Server for Enterprise

> **Preview notice:** The Microsoft MCP Server for Enterprise is currently in preview. Features, permissions, and setup steps may change. Always compare this guide with the latest Microsoft documentation before deploying it in a production tenant.

## Overview

The Microsoft MCP Server for Enterprise lets MCP-enabled AI clients query Microsoft Entra tenant data using natural language. It translates requests into Microsoft Graph API calls and executes them within the permissions of the signed-in user and the delegated scopes granted to the MCP client.

This community guide provides a simplified path for getting started with **Visual Studio Code and GitHub Copilot**.

## Prerequisites Checklist

Before starting, confirm that you have:

- [ ] A Microsoft Entra tenant
- [ ] Visual Studio Code with GitHub Copilot available
- [ ] PowerShell running with administrator rights for initial provisioning
- [ ] Microsoft.Entra.Beta PowerShell module version **1.0.13 or later**
- [ ] Either the **Application Administrator** or **Cloud Application Administrator** role for the one-time provisioning and consent process
- [ ] Appropriate Microsoft Entra directory roles for the data you intend to query
- [ ] Approval to use the required delegated Microsoft Graph permissions in the target tenant
- [ ] A test or demo tenant where possible, especially while the service remains in preview

> **Security note:** The MCP Server does not bypass Microsoft Entra authorization. Results depend on both the signed-in user's privileges and the delegated scopes granted to the MCP client. Use least privilege and review requested permissions before granting consent.

## Key Official Microsoft References

- [Overview of Microsoft MCP Server for Enterprise](https://learn.microsoft.com/en-us/graph/mcp-server/overview)
- [Get started with the Microsoft MCP Server for Enterprise](https://learn.microsoft.com/en-us/graph/mcp-server/get-started)
- [Sample prompts for Microsoft MCP Server for Enterprise](https://learn.microsoft.com/en-us/graph/mcp-server/mcp-server-sample-prompts)
- [Official Microsoft EnterpriseMCP repository](https://github.com/microsoft/EnterpriseMCP)

## Authentication Options

Authentication: The Microsoft MCP Server for Enterprise is typically used with interactive user sign-in and delegated permissions, allowing Copilot to access Microsoft Entra data using the privileges of the signed-in user.

The MCP Server can also be used in non-interactive scenarios using a registered application (service principal) as the MCP client. Access is governed by the delegated Microsoft Graph permissions granted to that client and the identity used to acquire the access token.

## 1. Provision the MCP Server and Visual Studio Code

The following provisioning process is performed once per tenant.

Open PowerShell as an administrator and install or update the required module:

```powershell
Install-Module Microsoft.Entra.Beta -Force -AllowClobber
```

Connect to the tenant with the scopes required for provisioning:

```powershell
Connect-Entra -Scopes 'Application.ReadWrite.All', 'Directory.Read.All', 'DelegatedPermissionGrant.ReadWrite.All'
```

Confirm the active account, tenant, and scopes:

```powershell
Get-EntraContext
```

Register the Microsoft MCP Server for Enterprise and grant the supported permissions to Visual Studio Code:

```powershell
Grant-EntraBetaMCPServerPermission -ApplicationName VisualStudioCode
```

## 2. Confirm the Registration

After provisioning, confirm that these enterprise applications exist in the tenant:

| Application | Application (client) ID |
|---|---|
| Microsoft MCP Server for Enterprise | `e8c77dc2-69b3-43f4-bc51-3213c9d915b4` |
| Visual Studio Code | `aebc6443-996d-45c2-90f0-388ff96faa56` |

You can verify them in the Microsoft Entra admin center under **Enterprise applications**, or use the Microsoft Graph verification query provided in the official getting-started documentation.

## 3. Connect from Visual Studio Code

Use the official **Install Microsoft MCP Server for Enterprise** action in the Microsoft getting-started documentation to add the server to Visual Studio Code.

The Enterprise MCP endpoint is:

```text
https://mcp.svc.cloud.microsoft/enterprise
```

After the server is added:

1. Authenticate with the intended Microsoft Entra account.
2. Open GitHub Copilot Chat in Visual Studio Code.
3. Select **Agent** mode.
4. Open the tools list and confirm that the Microsoft MCP Server for Enterprise tools are enabled.
5. Start with a simple read-only prompt.

## 4. Verify the Connection

Try these basic prompts:

```text
How many users are in my tenant?
```

```text
List the first 10 users in my tenant.
```

```text
How many guest users do we have?
```

The AI client should show the Microsoft Graph operation proposed or executed. Review tool calls before allowing them, particularly when using prompts that could modify tenant data.

## Sample Prompts

### Users and Sign-in Activity

```text
Show me recently created users.
```

```text
Which users haven't signed in for the last 30 days?
```

```text
List disabled user accounts.
```

```text
List users synced from on-premises Active Directory.
```

### Privileged Access

```text
List all users assigned to the Global Administrator role.
Include display name, user principal name, user type, and whether the account is enabled.
```

```text
Show all directory role assignments for [user principal name].
```

```text
Identify guest users with privileged directory roles.
```

> Validate unexpected findings in the Microsoft Entra admin center. Copilot accelerates investigation, but the administrator remains responsible for interpreting and confirming the result.

### App Registrations and Enterprise Applications

```text
List all app registrations created in this tenant and include their owners.
```

```text
Identify app registrations that do not have an owner.
```

```text
List app registrations with expired passwords or certificates.
```

```text
List service principals with Microsoft Graph application permissions.
```

```text
Identify service principals with highly privileged application permissions.
Explain why each permission could present risk.
```

### Non-Human Identities

```text
Identify workload identities in this tenant and categorize them as app registrations, service principals, or managed identities.
```

```text
List non-human identities with privileged directory roles or high-impact Microsoft Graph permissions.
```

```text
Identify non-human identities with no owner, expired credentials, or other governance concerns.
```

### Assessment-Oriented Prompts

```text
Assess privileged role assignments in this tenant.
Identify unusual assignments, guest administrators, disabled privileged accounts, and users holding multiple privileged roles.
Present the evidence separately from recommendations.
```

```text
Review app registrations created in this tenant for ownership and credential hygiene.
Prioritize findings as high, medium, or low risk and explain the criteria used.
```

```text
Create a concise management summary of the findings.
Include the business risk, supporting evidence, recommended action, and priority.
```

## Prompting Tips

- Begin with a narrow, read-only question.
- Be explicit about the object type: **app registration**, **service principal**, **managed identity**, or **user**.
- Request the fields needed to verify the answer, such as object ID, application ID, owner, user type, account status, role, or permission.
- Ask Copilot to separate **evidence**, **interpretation**, and **recommendations**.
- Validate surprising or high-impact results in the Microsoft Entra admin center or directly through Microsoft Graph.
- Do not place secrets, passwords, access tokens, or private keys in prompts or workspace files.

## Troubleshooting

### Authentication registration error in Visual Studio Code

If Visual Studio Code reports the following error:

```text
Error getting token from server metadata: Error: Cannot force new registration for a non-dynamic authentication provider.
```

Change the Visual Studio Code setting below from `msal` to `msal-no-broker`:

```json
"microsoft-authentication.implementation": "msal-no-broker"
```

### A prompt returns incomplete or unexpected results

- Make the object type and requested fields more explicit.
- Confirm that the signed-in user holds the required directory role.
- Confirm that the MCP client has the delegated `MCP.*` scopes needed for the operation.
- Review the Microsoft Graph call selected by the agent.
- Validate important findings directly in Microsoft Entra or Microsoft Graph.

## Important Considerations

- The Microsoft MCP Server for Enterprise is a preview service and provides only read-only tools at this time
- The current Microsoft documentation lists availability in the global service and not in the US Government, US Department of Defense, or China operated by 21Vianet deployments.
- Natural-language responses should not be treated as authoritative without validation.
- Use least-privileged roles and delegated permissions.
- Prefer a non-production tenant for demonstrations and initial testing.

## Community Use

This guide is intended as a practical community starting point and is not a replacement for official Microsoft documentation. Contributions, corrections, additional prompts, and lessons learned are welcome.
