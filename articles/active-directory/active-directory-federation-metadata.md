<properties
   pageTitle="Metadados de federação"
   description="Uma descrição de pontos de extremidade de metadados e o conteúdo no documento de metadados que serviços que aceitam tokens do AD do Azure devem usar."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# Metadados de federação
O AD do Azure (Active Directory do Azure) publica um documento de metadados de federação para serviços que são configurados para aceitar os tokens de segurança que o Active Directory do Azure emite. O formato de documento de metadados de federação está descrito no [Web Services Federation Language (WS-Federation) Versão 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que estende [Metadados para o SAML (Security Assertion Markup Language) OASIS V 2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

Este tópico lista os pontos de extremidade de metadados e explica o conteúdo no documento de metadados que os serviços que aceitam tokens do AD do Azure precisam usar.

##Pontos de extremidade de metadados específicos de locatário e independentes de locatário

O AD do Azure publica pontos de extremidade específicos de locatário e independentes de locatário. Pontos de extremidade específicos de locatário destinam-se a um locatário específico. Os metadados de federação específicos de locatário incluem informações sobre o locatário, incluindo informações de emissor e o ponto de extremidade específico de locatário. Os aplicativos que restringem o acesso a um único locatário usam pontos de extremidade específicos de locatário.

Os pontos de extremidade independentes de locatário fornecem informações que são comuns para todos os locatários do AD do Azure. Essas informações se aplicam aos locatários hospedados em *login.windows.net* e são compartilhadas pelos locatários. Os pontos de extremidade independentes de locatário são recomendados para aplicativos com vários locatários, pois eles não estão associados a qualquer locatário específico.

## Pontos de extremidade de metadados de federação

O AD do Azure publica metadados de Federação em qu *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*, eme o valor de <TenantDomainName> pode ser "common" ou um valor específico de locatário. Os pontos de extremidade são endereçáveis. Portanto, você pode ir para o site para exibir os metadados de federação de um locatário.

Para **pontos de extremidade específicos de locatário**, o <TenantDomainName> pode ser um dos seguintes tipos:

- Um nome de domínio registrado de um locatário do AD do Azure, como: contoso.onmicrosoft.com.

- A id de locatário imutável do domínio, como 72f988bf 86f1 41af 91ab 2d7cd011db45.

Para **pontos de extremidade independentes de locatário**, o <TenantDomainName> é **common**. Esse nome indica que apenas os elementos de Metadados de Federação que são comuns a todos os locatários do AD do Azure hospedados em login.windows.net.

Por exemplo, um ponto de extremidade específico de locatário pode ser *https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*. O ponto de extremidade independente de locatário é *https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*.

## Conteúdo de metadados de federação

A seção a seguir fornece as informações necessárias para serviços que consomem os tokens emitidos pelo AD do Azure.

### EntityID

O elemento **EntityDescriptor** contém um atributo **EntityID**. O valor do atributo **EntityID** representa o emissor, ou seja, o STS (serviço de token de segurança) que emitiu o token. É importante validar o emissor. Portanto, você pode confirmar qual locatário emitiu um token.

Os metadados a seguir mostram um elemento de exemplo específico de locatário **EntityDescriptor** com um elemento **EntityID**.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

O **EntityID** no ponto de extremidade independente de locatário fornece um modelo que pode ser usado para gerar um valor específico de locatário **EntityID**. Substitua o literal "{tenant}" no ponto de extremidade independente de locatário pelo valor da declaração **TenantID** de um token. O valor resultante será igual ao emissor do token. Essa estratégia permite que um aplicativo de vários locatários valide o emissor para determinado locatário.

Os metadados a seguir mostram um exemplo de elemento independente de locatário **EntityID**. Nesse elemento, {tenant} é um literal, não um espaço reservado.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### Certificado de assinatura de tokens
Quando um serviço recebe um token emitido por um locatário do AD do Azure, a assinatura do token deve ser validada com uma chave de assinatura publicada no documento de metadados de federação.

Os metadados de federação incluem a parte pública dos certificados que os locatários usam para autenticação de tokens. Os bytes brutos do certificado aparecem no elemento **KeyDescriptor**. O certificado de assinatura de token é válido para a assinatura somente quando o valor do atributo **use** é **signing**.

Um documento de metadados de federação publicado pelo AD do Azure pode ter várias chaves de assinatura, como quando o AD do Azure está se preparando para atualizar o certificado de autenticação. Quando um documento de metadados de federação inclui mais de um certificado, um serviço que valida os tokens deve dar suporte a todos os certificados no documento.

Os metadados a seguir mostram um elemento de exemplo **KeyDescriptor** com uma chave de assinatura.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

O elemento **KeyDescriptor** aparece em dois lugares no documento de metadados de federação: na seção específica do WS-Federation e na seção específica de SAML. Os certificados publicados em ambas as seções serão os mesmos.

Na seção específica de WS-Federation, um leitor de metadados de WS-Federation leria os certificados de um elemento **RoleDescriptor** com o tipo **SecurityTokenServiceType**.

Os metadados a seguir mostram um elemento de exemplo **RoleDescriptor**.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

Na seção específica de SAML, um leitor de metadados de WS-Federation leria os certificados de um elemento **IDPSSODescriptor**.

Os metadados a seguir mostram um elemento de exemplo **IDPSSODescriptor**.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

Não há diferenças no formato de certificados específicos de locatário e independentes de locatário.

### URL de ponto de extremidade de WS-Federation
Os metadados de federação incluem a URL que usa o AD do Azure para logon único e saída única no protocolo WS-Federation. Esse ponto de extremidade é exibido no elemento **PassiveRequestorEndpoint**.

Os metadados a seguir mostram um elemento de exemplo **PassiveRequestorEndpoint** para um ponto de extremidade específico de locatário.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

Para o ponto de extremidade independente de locatário, a URL de WS-Federation aparece no ponto de extremidade WS-Federation, conforme mostrado no exemplo a seguir.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### URL de ponto de extremidade do protocolo SAML

Os metadados de Federação incluem a URL que o AD do Azure usa para logon único e saída única no protocolo SAML 2.0. Esses pontos de extremidade aparecem no elemento **IDPSSODescriptor**.

As URLs de entrada e saída aparecem nos elementos **SingleSignOnService** e **SingleLogoutService**. Os metadados a seguir mostram um **PassiveResistorEndpoint** de exemplo para um ponto de extremidade específico de locatário.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

Da mesma forma, os pontos de extremidade para os pontos de extremidade de protocolo SAML 2.0 comuns são publicados nos metadados de federação independentes de locatário, conforme mostrado no exemplo a seguir.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## Consulte também
[Protocolos de autenticação do Active Directory do Azure](active-directory-authentication-protocols.md)

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

<!---HONumber=62-->