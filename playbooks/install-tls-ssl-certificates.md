# How to install TSL/SSL Certificate in Azure app services

App Service provides a highly scalable, self-patching web hosting service. The certificates are currently called _Transport Layer Security_ (TLS) certificates, previously known as _Secure Sockets Layer_ (SSL) certificates. 
These certificates are private or public and can make internet connections more secure. They encrypt the data sent between browsers, websites visited, and the website server.
The table below lists the options to add certificates in App Service.

| Option | Description |
| ------ | ------ |
| Create a free App Service managed certificate | A private certificate that's free of charge and easy to use to improve security for a custom domain in App Service. |
| Import an App Service certificate | Azure manages the private certificate. It combines automated certificate management and renewal and export options in a more flexible way. |
| Import a certificate from Azure Key Vault | Useful when using Key Vault to manage PKCS12 certificates. |
| Upload a private certificate | Upload your own certificate if you already have a private certificate from a non-Microsoft provider. |
| Upload a public certificate | Public certificates aren't used to secure custom domains, but you can load them into the code if you need to access remote resources. |

## Prerequisites
- Create an App Service app. The app's plan must be in the Basic, Standard, Premium, or Isolated tier.
- For a private certificate, make sure that it satisfies all requirements from App Service.
- For free certificates, follow the steps below:
  - Map the domain where the certificate shall live in to App Service.
  - For a root domain (like contoso.com), make sure that the app doesn't have any IP restrictions configured. 
    Both certificate creation and its periodic renewal for a root domain depend on the app being reachable from the internet.

## Private certificate requirements
The free App Service managed certificate and the App Service certificate already satisfy the requirements of App Service. 
If you choose to upload or import a private certificate to App Service, the certificate must meet the following requirements:
- Be exported as a password-protected PFX file.
- Contain all intermediate certificates and the root certificate in the certificate chain.

To help secure a custom domain in a TLS binding, the certificate must meet these extra requirements:
- Contain an extended key usage for server authentication (OID = 1.3.6.1.5.5.7.3.1).
- Be signed by a trusted certificate authority.
