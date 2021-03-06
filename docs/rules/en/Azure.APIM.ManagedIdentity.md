---
severity: Important
category: Security configuration
resource: API Management
online version: https://github.com/Microsoft/PSRule.Rules.Azure/blob/master/docs/rules/en/Azure.APIM.ManagedIdentity.md
---

# API Management uses a managed identity

## SYNOPSIS

Use managed identities to access Azure resources.

## DESCRIPTION

API Management must authenticate to access Azure resources such as Key Vault.
Use Key Vault to store certificates and secrets used within API Management.

## RECOMMENDATION

Consider configuring a managed identity for each API Management instance.
Also consider using managed identities to authenticate to related Azure services.

## LINKS

- [Use managed identities in Azure API Management](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-use-managed-service-identity)
- [Authenticate with managed identity](https://docs.microsoft.com/en-us/azure/api-management/api-management-authentication-policies#ManagedIdentity)
- [Azure template reference](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-12-01/service#ApiManagementServiceIdentity)
