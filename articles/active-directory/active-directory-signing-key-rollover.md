<properties
	pageTitle="Substituição da chave de assinatura no Azure AD | Microsoft Azure"
	description="Este artigo descreve as práticas recomendadas de substituição de chave de assinatura para o Azure Active Directory"
	services="active-directory"
	documentationCenter=".net"
	authors="gsacavdm"
	manager="krassk"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="gsacavdm"/>

# Substituição de chave de assinatura no Azure Active Directory

Este tópico aborda o que você precisa saber sobre as chaves públicas que são usadas no Azure AD (Azure Active Directory) para assinar tokens de segurança. É importante observar que essas chaves são substituídas em intervalos periódicos e, em caso de emergência, podem ser substituídas imediatamente. Todos os aplicativos que usam o Azure AD devem ser capazes de manipular programaticamente o processo de substituição de chave. Continue lendo para entender como funcionam as chaves, como avaliar o impacto de substituição no seu aplicativo e como atualizar seu aplicativo para tratar a substituição de chave, se necessário.

> [AZURE.IMPORTANT] A próxima substituição de chave de assinatura ocorrerá no dia 15 de agosto de 2016 e *não* afetará os Aplicativos da Galeria ou o aplicativo em locatários B2C.

## Visão geral das chaves de assinatura no Azure AD

O Azure AD usa criptografia de chave pública criada nos padrões da indústria para estabelecer a confiança entre ela e os aplicativos que a utilizam. Em termos práticos, isso funciona da seguinte maneira: o Azure AD usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando um usuário entra em um aplicativo que usa o Azure AD para autenticação, este cria um token de segurança que contém informações sobre o usuário. Esse token é assinado pelo Azure AD usando sua chave privada antes de ser enviado para o aplicativo. Para verificar se o token é válido e se realmente se origina do Azure AD, o aplicativo deverá validar a assinatura do token usando a chave pública exposta pelo Azure AD que está contida no [documento de descoberta do OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) do locatário ou o [documento de metadados de federação do locatário SAML/WS-Fed](active-directory-federation-metadata.md).

Para fins de segurança, a chave de assinatura do Azure AD é substituída periodicamente e, em caso de emergência, pode ser substituída imediatamente. Um aplicativo que se integra ao Azure AD deve estar preparado para lidar com um evento de substituição de chave, independentemente da frequência em que pode ocorrer. Se não tiver, e o aplicativo tentar usar uma chave expirada para verificar a assinatura em um token, a solicitação falhará.

Sempre há mais de uma chave pública válida disponível no documento de descoberta do OpenID Connect e no documento de metadados de federação. O aplicativo deve estar preparado para usar qualquer uma das chaves especificadas no documento, já que uma das chaves pode ser substituída em breve, a outra pode ser a sua substituta e assim por diante.

## Como avaliar se o aplicativo será afetado e o que fazer com ele

Como o seu aplicativo lida com a substituição de chave depende de variáveis como o tipo de aplicativo ou que protocolo de identidade e biblioteca foi usado. As seções a seguir avaliam se os tipos mais comuns de aplicativos são afetados pela substituição de chave e fornecem diretrizes sobre como atualizar o aplicativo para dar suporte à substituição automática ou à atualização manual da chave.

* [Aplicativos/APIs Web usando .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplicativos/APIs Web usando .NET Core OpenID Connect ou o middleware JwtBearerAuthentication](#owincore)
* [Aplicativos/APIs Web usando o módulo passport-azure-ad do Node.js](#passport)
* [Aplicativos/APIs Web criados com o Visual Studio 2015](#vs2015)
* [Aplicativos Web criados com o Visual Studio 2013](#vs2013)
* [APIs Web criadas com o Visual Studio 2013](#vs2013_webapi)
* [Aplicativos Web criados com o Visual Studio 2012](#vs2012)
* [Aplicativos Web criados com o Visual Studio 2010, 2008 ou usando o Windows Identity Foundation](#vs2010)
* [Aplicativos/APIs Web usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte](#other)

### <a name="owin"></a> Aplicativos/APIs Web usando .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication

Se seu aplicativo estiver usando o .NET OWIN OpenID Connect, WS-Fed ou o middleware WindowsAzureActiveDirectoryBearerAuthentication, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um desses ao procurar qualquer um dos trechos no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
	 // ...
 	});
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
	 new WindowsAzureActiveDirectoryBearerAuthenticationOptions
	 {
	 // ...
	 });
```

### <a name="owincore"></a> Aplicativos/APIs Web usando .NET Core OWIN OpenID Connect ou o middleware JwtBearerAuthentication

Se seu aplicativo estiver usando o .NET Core OWIN OpenID Connect ou o middleware JwtBearerAuthentication, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um desses ao procurar qualquer um dos trechos no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
	 // ...
 	});
```

### <a name="passport"></a> Aplicativos/APIs Web usando o módulo passport-ad do Node.js

Se seu aplicativo estiver usando o módulo passport-ad do Node.js, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo tem passport-ad pesquisando o trecho a seguir no app.js do seu aplicativo

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
	//...
));
```

### <a name="vs2015"></a> Aplicativos/APIs Web criados com o Visual Studio 2015

Se seu aplicativo tiver sido criado usando um modelo de aplicativo Web no Visual Studio 2015 e se você tiver selecionado **Contas Corporativas ou de Estudante** no menu **Alterar Autenticação**, ele já terá a lógica necessária para tratar da substituição de chave automaticamente. Essa lógica, incorporada ao middleware OWIN OpenID Connect, recupera e armazena em cache as chaves de documento de descoberta OpenID Connect e as atualiza periodicamente.

Se você tiver adicionado a autenticação à sua solução manualmente, talvez seu aplicativo não tenha a lógica de substituição de chave necessária. Você precisará escrevê-la por conta própria ou seguir as etapas em [Aplicativos/APIs Web usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte](#other).

### <a name="vs2013"></a> Aplicativos Web criados com o Visual Studio 2013

Se seu aplicativo foi criado usando um modelo de aplicativo Web no Visual Studio 2013 e você selecionou **Contas Organizacionais** no menu **Alterar Autenticação**, ele já tem a lógica necessária para tratar da substituição de chave automaticamente. Essa lógica armazena o identificador exclusivo da sua organização e as informações de chave de autenticação em duas tabelas de banco de dados associadas ao projeto. Você pode encontrar a cadeia de conexão para o banco de dados no arquivo Web.config do projeto.

Se você tiver adicionado a autenticação à sua solução manualmente, talvez seu aplicativo não tenha a lógica de substituição de chave necessária. Você precisará escrevê-la por conta própria ou seguir as etapas em [Aplicativos/APIs Web usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte](#other).

As etapas a seguir o ajudarão a verificar se a lógica está funcionando corretamente em seu aplicativo.

1. No Visual Studio 2013, abra a solução e clique na guia **Gerenciador de Servidores** na janela à direita.
2. Expanda **Conexões de Dados**, **DefaultConnection** e **Tabelas**. Localize a tabela **IssuingAuthorityKeys**, clique com o botão direito do mouse e clique em **Mostrar Dados da Tabela**.
3. Na tabela **IssuingAuthorityKeys**, haverá pelo menos uma linha, que corresponde ao valor de impressão digital da chave. Exclua todas as linhas na tabela.
4. Clique com botão direito do mouse na tabela **Locatários** e clique em **Mostrar Dados da Tabela**.
5. Na tabela **Locatários**, haverá pelo menos uma linha, que corresponde a um identificador de locatário de diretório exclusivo. Exclua todas as linhas na tabela. Se você não excluir as linhas em ambas as tabelas **Locatários** e **IssuingAuthorityKeys**, obterá um erro no tempo de execução.
6. Compile e execute o aplicativo. Depois de entrar na sua conta, você poderá interromper o aplicativo.
7. Volte para o **Gerenciador de Servidores** e examine os valores das tabelas **IssuingAuthorityKeys** e **Locatários**. Você notará que eles foram repopulados automaticamente com as informações apropriadas do documento de metadados federados.

### <a name="vs2013"></a> APIs Web criadas usando o Visual Studio 2013

Se você criou um aplicativo de API Web usando um modelo de aplicativo de API Web no Visual Studio 2013 e selecionou **Contas Organizacionais** no menu **Alterar Autenticação**, já tem a lógica necessária no seu aplicativo. Se você configurou a autenticação manualmente, siga as instruções abaixo para saber como configurar sua API Web para atualizar as informações de chave automaticamente.

O trecho de código a seguir demonstra como obter as últimas chaves de documento de metadados federados e usar o [Manipulador de token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O trecho de código pressupõe que você usará seu próprio mecanismo de cache para persistir a chave e validar futuros tokens do Azure AD, seja em um banco de dados, em um arquivo de configuração ou em outro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a> Aplicativos Web criados com o Visual Studio 2012

Se seu aplicativo foi criado no Visual Studio 2012, você provavelmente usou a Ferramenta de Identidade e Acesso para configurar seu aplicativo. Também é provável que você esteja usando o [VINR (registro de nome de emissor validador)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável por manter informações sobre os provedores de identidade confiável (Azure AD) e as chaves usadas para validar tokens emitidos por eles. O VINR também facilita a atualização automática das informações fundamentais armazenadas em um arquivo Web.config baixando o documento de metadados federados mais recente associado ao seu diretório, verificando se a configuração está desatualizada em relação ao documento mais recente e atualizando o aplicativo para usar a nova chave conforme necessário.

Se você criou seu aplicativo usando um dos exemplos de código ou documentação passo a passo fornecidos pela Microsoft, a lógica de substituição de chave já estará incluída no seu projeto. Você notará que o código a seguir já existe em seu projeto. Se seu aplicativo não tiver essa lógica, siga as etapas abaixo para adicioná-la e verificar se ela está funcionando corretamente.

1. No **Gerenciador de Soluções**, adicione uma referência ao assembly **System.IdentityModel** para o projeto apropriado.
2. Abra o arquivo **Global.asax.cs** e adicione o seguinte com o uso de políticas:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Adicione o seguinte método ao arquivo **Global.asax.cs**:
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Invoque o método **RefreshValidationSettings()** no método **Application\_Start()** em **Global.asax.cs** conforme mostrado abaixo:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Depois que você tiver seguido as etapas, o Web.config do seu aplicativo será atualizado com as últimas informações do documento de metadados federados, incluindo as últimas chaves. Essa atualização ocorrerá sempre que o pool de aplicativos for reciclado no IIS. Por padrão, o IIS é definido para reciclar aplicativos a cada 29 horas.

Siga as etapas abaixo para verificar se a lógica de substituição de chave está funcionando.

1. Após ter verificado que seu aplicativo está usando o código acima, abra o arquivo **Web.config** e navegue até o bloco **<issuerNameRegistry>**, procurando especificamente as poucas linhas abaixo:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. Na configuração **<add thumbprint=””>**, altere o valor de impressão digital, substituindo um caractere por um diferente. Salve o arquivo **Web.config**.

3. Crie o aplicativo e o execute. Se você consegue completar o processo de logon, seu aplicativo está atualizando a chave com êxito baixando as informações necessárias do documento de metadados federados do diretório. Se você estiver tendo problemas ao se conectar, verifique se as alterações em seu aplicativo estão corretas lendo o tópico [Adicionando logon ao seu aplicativo Web usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) ou baixando e inspecionando o seguinte exemplo de código: [Aplicativo de nuvem multilocatário do Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a> Aplicativos da Web criados com o Visual Studio 2008 ou 2010 e a versão 1.0 do Windows Identity Foundation (WIF) para .NET 3.5

Se você criou um aplicativo no WIF v 1.0, nenhum mecanismo foi fornecido para atualizar automaticamente a configuração do seu aplicativo e usar uma nova chave. A maneira mais fácil de atualizar a chave é usar a ferramenta FedUtil incluída no SDK do WIF, que pode recuperar o documento de metadados mais recente e atualizar sua configuração. Essas instruções estão incluídas abaixo. Como alternativa, você pode fazer o seguinte:

- Siga as instruções na seção Recuperar manualmente a chave mais recente e atualizar seu aplicativo e codifique a lógica para executar as etapas programaticamente.
- Atualize seu aplicativo para .NET 4.5, que inclui a versão mais recente do WIF localizado no namespace System. Você pode usar o [VINR (registro de nome de emissor validador)](https://msdn.microsoft.com/library/dn205067.aspx) para executar as atualizações automáticas da configuração do aplicativo.


1. Verifique se você tem o SDK do WIF v 1.0 instalado na máquina de desenvolvimento para o Visual Studio 2008 ou 2010. Você pode [baixá-lo aqui](https://www.microsoft.com/pt-BR/download/details.aspx?id=4451) se ainda não estiver instalado.
2. No Visual Studio, abra a solução, clique com o botão direito do mouse no projeto aplicável e selecione **Atualizar metadados federados**. Se essa opção não estiver disponível, o FedUtil e/ou o WIF v 1.0 SDK não foram instalados.
3. No prompt, selecione **Atualizar** para começar a atualizar os metadados federados. Se você tiver acesso ao ambiente de servidor onde o aplicativo está hospedado, você pode opcionalmente usar o [Agendador de atualização automática de metadados](https://msdn.microsoft.com/library/ee517272.aspx) do FedUtil.
4. Clique em **Concluir** para concluir o processo de atualização.

### <a name="other"></a> Aplicativos/APIs Web usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte.

Se você estiver usando alguma outra biblioteca ou se tiver implementado manualmente qualquer um dos protocolos com suporte, precisará examinar a biblioteca ou a implementação para garantir que a chave esteja sendo recuperada do documento de descoberta OpenID Connect ou do documento de metadados de federação. Uma maneira de verificar isso é fazer uma pesquisa no seu código ou no código da biblioteca de quaisquer chamadas para o documento de descoberta OpenID ou para o documento de metadados de federação.

Se a chave estiver sendo armazenada em algum lugar ou codificadas em seu aplicativo, você poderá recuperar manualmente a chave e atualizá-la adequadamente. **É altamente incentivado que você aprimore seu aplicativo para dar suporte à substituição automática** usando qualquer um dos tópicos de abordagens neste artigo para evitar transtornos futuros e sobrecarga caso o Azure AD aumente a cadência de sua substituição ou tenha uma substituição fora de banda de emergência.

Para recuperar manualmente a chave mais recente do documento de descoberta OpenID:

1. No seu navegador da Web, acesse `https://login.microsoftonline.com/your_directory_name/.well-known/openid-configuration`. Você verá o conteúdo do documento de descoberta OpenID Connect. Para saber mais sobre este documento, veja a [especificação de documento de descoberta OpenID](http://openid.net/specs/openid-connect-discovery-1_0.html).
2. Copiar o link no valor de jwks\_uri
3. Abra uma nova guia em seu navegador e vá para a URL que você acabou de copiar. Você verá o conteúdo do documento Conjunto de Chaves da Web JSON.
4. Para atualizar um aplicativo e permitir que ele use uma nova chave, encontre os elementos **x5c** e copie o valor de cada um deles. Por exemplo:
```
keys: [
	{
		kty: "RSA",
		use: "sig",
		kid: "MnC_VZcATfM5pOYiJHMba9goEKY",
		x5t: "MnC_VZcATfM5pOYiJHMba9goEKY",
		n: "vIqz-4-ER_vNW...ixLUQ",
		e: "AQAB",
		x5c: [
			"MIIC4jCCAcqgAw...dhXsIIKvJQ=="
		]
	},
```
5. Depois de ter copiado o valor do elemento **<X509Certificate>**, abra um editor de texto sem formatação e cole-o. Não deixe de remover possíveis espaços em branco à direita e salve o arquivo com uma extensão **.cer**.

Para recuperar manualmente a chave mais recente do documento de metadados de federação:

1. No seu navegador da Web, acesse `https://login.microsoftonline.com/your_directory_name/federationmetadata/2007-06/federationmetadata.xml`. Você verá o conteúdo do documento XML de Metadados Federados. Para saber mais sobre este documento, confira o tópico [Metadados federados](active-directory-federation-metadata.md).
2. Para atualizar um aplicativo e permitir que ele use uma nova chave, encontre cada bloco **<RoleDescriptor>** e copie o valor do elemento **<X509Certificate>** de cada bloco. Por exemplo:
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
3. Depois de ter copiado o valor do elemento **<X509Certificate>**, abra um editor de texto sem formatação e cole-o. Não deixe de remover possíveis espaços em branco à direita e salve o arquivo com uma extensão **.cer**.

Você acabou de criar o certificado X509 que é usado como a chave pública do Azure AD. Usando os detalhes do certificado, como sua impressão digital e data de validade, você pode verificar manual ou programaticamente se o certificado e a impressão digital usados atualmente pelo aplicativo são válidos.

<!---HONumber=AcomDC_0629_2016-->