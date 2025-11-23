# SAML 2.0 Identity Federation

- On AWS, only AWS credentials can be used to access AWS resources, so an exchange has to take place
- Federation lets user outside of AWS to assume a temporary role for access AWS resources
- These users assume identity provider access role
- SAML 2.0 - Security Assertion Markup Language
- Most large identity providers (IdP) use the SAML 2.0 open standard
- SAML 2.0 allows to **indirectly** use on-premise identities with AWS (console and CLI)
- SAML 2.0 based identity federation is used when we have an enterprise based identity provider which is SAML 2.0 compatible
- SAML 2.0 based federation is ideal when we have an existing identity management team managing access to other services including AWS
- If we are looking to maintain a single source of truth and/or we have more than 5000 users, SAML 2.0 based federation is recommended to be used
- Federation is using IAM Roles and AWS Temporary Credentials (SAML 2.0 credentials generally have a 12 hours validity on AWS)

## SAML 2.0 Identity Federation Authentication Process - API Access

![SAML 2.0 Federation API](images/SAML2.0FederationAPI.png)

## SAML 2.0 Identity Federation Authentication Process - AWS Console Access

![SAML 2.0 Federation Console](images/SAML2.0FederationConsole.png)

## SAML 2.0 Federation

- We need to setup trust between AWS IAM and SAML (both ways)
- SAML 2.0 enabled web based, cross domain SSO
- Uses the STS API: `AssumeRoleWithSAML`
- It is the old way of doing federation, recommended way by AWS is to use **Amazon Single Sign On (SSO)**
