<properties
   pageTitle="Usar a declaração do cliente para obter tokens de acesso do Azure AD | Microsoft Azure"
   description="Como usar a declaração do cliente para obter tokens de acesso do Azure AD."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Utilização da declaração do cliente para obter tokens de acesso do Azure AD

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

## Segundo plano

Ao usar o fluxo de código de autorização ou o fluxo híbrido no OpenID Connect, o cliente troca um código de autorização por um token de acesso. Durante essa etapa, o cliente deve se autenticar no servidor.

![Segredo do cliente](media/guidance-multitenant-identity/client-secret.png)

Uma maneira de autenticar o cliente é usar um segredo do cliente. Este é o modo como o aplicativo [Tailspin Surveys][Surveys] é configurado por padrão.

Veja um exemplo de solicitação do cliente para o IDP, solicitando um token de acesso. Observe o parâmetro `client_secret`.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

O segredo é apenas uma cadeia de caracteres, portanto, você precisa tomar cuidado para não divulgar inadvertidamente o valor. A prática recomendada é manter o segredo do cliente fora do controle de origens. Ao implantar no Azure, armazene o segredo em uma [configuração de aplicativo][configure-web-app].

No entanto, qualquer pessoa com acesso à assinatura do Azure pode exibir as configurações do aplicativo. Além disso, há sempre a tentação de inserir segredos no controle de origem (por exemplo, em scripts de implantação), compartilhá-los por email e assim por diante.

Para obter segurança adicional, você pode usar a _declaração do cliente_ em vez de um segredo do cliente. Com a declaração do cliente, o cliente usa um certificado X.509 para comprovar que a solicitação de token veio do cliente. O certificado do cliente é instalado no servidor Web. Em geral, é mais fácil restringir o acesso ao certificado do que garantir que ninguém revele inadvertidamente um segredo do cliente.

Veja uma solicitação de token usando a declaração do cliente:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Observe que o parâmetro `client_secret` não é mais usado. Em vez disso, o parâmetro `client_assertion` contém um token JWT que foi assinado usando o certificado do cliente. O parâmetro `client_assertion_type` especifica o tipo de declaração &mdash; neste caso, token JWT. O servidor valida o token JWT. Se o token JWT for inválido, a solicitação de token retornará um erro.

> [AZURE.NOTE] Os certificados X.509 não são a única forma de declaração de cliente. Vamos nos concentrar na declaração aqui pois ela tem suporte no Azure AD.

## Usar declaração do cliente no aplicativo Surveys

Esta seção mostra como configurar o aplicativo Tailspin Surveys para usar a declaração do cliente. Nestas etapas, você gerará um certificado autoassinado que é adequado para o desenvolvimento, mas não para uso em produção.

1. Execute o script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] da seguinte maneira:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Para o parâmetro `Subject`, digite qualquer nome, como "surveysapp". O script gera um certificado autoassinado e o armazena no repositório de certificados "Usuário Atual/Pessoal".

2. A saída do script é um fragmento JSON. Adicione isso ao manifesto do aplicativo Web da seguinte maneira:

    1. Faça logon no [portal de gerenciamento do Azure][azure-management-portal] e navegue até o diretório do Azure AD.

    2. Clique em **Aplicativos**.

    3. Selecione o aplicativo Surveys.

    4.	Clique em **Gerenciar Manifesto** e selecione **Baixar Manifesto**.

    5.	Abra o arquivo de manifesto JSON em um editor de texto. Cole a saída do script na propriedade `keyCredentials`. O arquivo deve ser semelhante ao seguinte:

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	Salve as suas alterações ao arquivo JSON.

    7.	Volte para o portal. Clique em **Gerenciar Manifesto** > **Carregar Manifesto** e carregue o arquivo JSON.

3. Execute o comando a seguir para obter a impressão digital do certificado.

    ```
    certutil -store -user my [subject]
    ```

    onde `[subject]` é o valor especificado para o Assunto no script do PowerShell. A impressão digital é listada em "Cert Hash(sha1)". Remova os espaços entre os números hexadecimais.

4. Atualize seus segredos do aplicativo. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto Tailspin.Surveys.Web e escolha **Gerenciar Segredos do Usuário**. Adicione uma entrada para "Assymetric" em "AzureAd", conforme mostrado abaixo:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    Você deve definir `ValidationRequired` como false, pois o certificado não foi assinado por uma autoridade de certificação raiz. Em produção, use um certificado assinado por uma autoridade de certificação e defina `ValidationRequired` como true.

    Exclua também a entrada de `ClientSecret`, pois ela não é necessária com a declaração do cliente.

5. No Startup.cs, localize o código que registra o `ICredentialService`. Remova a linha que usa `CertificateCredentialService` e informe a linha que usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

No tempo de execução, o aplicativo Web lê o certificado do repositório de certificados. O certificado deve ser instalado no mesmo computador que o aplicativo Web.

## Recursos adicionais

- [Usar certificados em aplicativos de sites do Azure][using-certs-in-websites]
- [RFC 7521][RFC7521]. Define o mecanismo geral para enviar uma declaração do cliente.
- [RFC 7523][RFC7523]. Define como usar tokens JWT em declarações do cliente.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[parte de uma série]: guidance-multitenant-identity.md
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->