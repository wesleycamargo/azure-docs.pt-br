<properties
	pageTitle="Configure uma API para se conectar ao sistema de back-end em um domínio do Active Directory do Azure em PowerApps | Microsoft Azure"
	description="Configure uma API para se conectar ao sistema de back-end do AAD protegido em PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Configure uma API para se conectar a um recurso de back-end em um domínio do Active Directory do Azure
Conforme mais usuários criam domínios no Active Directory do Azure (AAD), os recursos de back-end também estão sendo adicionados a esses domínios do AAD. Você pode criar e configurar as APIs para se conectar a esses recursos de back-end.

#### Pré-requisitos para iniciar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Crie um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md).
- Instale a visualização do [Azure PowerShell][11] 1.0 ou superior.
- Registre uma API em seu [ambiente de serviço de aplicativo](powerapps-register-api-hosted-in-app-service.md).

## Etapa 1: crie um aplicativo do Active Directory e atribua permissões a ele

Para acessar o sistema de back-end em um domínio do AAD, crie um aplicativo do AAD e dê a ele as permissões apropriadas para seu back-end existente (que também é um aplicativo do AAD). Etapas:

1. No [Portal clássico do Azure][13], acesse o Active Directory do Azure, abra seu locatário (ou diretório) e selecione a guia **Aplicativos**: ![][14]
2. Selecione o botão **Adicionar** na parte inferior. Em seguida:  

	a) Escolha **Adicionar um aplicativo que minha organização está desenvolvendo**. b) Digite um nome para o aplicativo e selecione **Aplicativo Web e/ou API da web**. c) Em **URL de logon** e **URI da ID do aplicativo**, insira URLs exclusivas na AAD e nas URLs que façam sentido para sua organização. Por exemplo, você pode inserir http://powerappssignon.contoso.com ou http://powerappsappid.contoso.com. É recomendável usar uma URL no domínio do AAD da sua organização. As URLs são usadas como identificadores e não há nenhum requisito para que elas possam existir. Ninguém vai procurar as URLs que você inserir. Você pode inserir HTTP ou HTTPS.

3. Na página do aplicativo do AAD criada recentemente, vá para a guia **Configurar**: ![][15]
4. Na seção **chaves**, use a lista suspensa para selecionar uma duração. Observe que a chave exibe depois que você selecionar **Salvar**: ![][16]
5. Em **Logon único**, adicione ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` como uma **URL de resposta**.
6. Em **permissões para outros aplicativos**:  

	1. Selecione **Adicionar aplicativo**. Na janela pop-up, escolha o aplicativo de AAD, protegendo seu back-end existente: ![][17]  

	2. Use a lista suspensa para adicionar as permissões: ![][18]

7. Selecione **Salvar** na parte inferior.
8. Copie a **ID do cliente**, a **chave** e armazene-os. A chave não é mostrada novamente depois que você fechar o portal do Azure. 

Consulte a seção [Como integrar aplicativos com o Active Directory do Azure](../active-directory/active-directory-integrating-applications.md) para saber mais sobre aplicativos do AAD.

## Etapa 2: configurar sua API usando o Azure PowerShell

Neste ponto, não existe nenhum suporte de portal do Azure para inicializar a configuração necessária para sua API. Para configurar a API no portal do Azure, use o seguinte script do PowerShell do Azure:

> [AZURE.TIP] Para aprender a instalar, configurar e executar o Azure PowerShell, consulte a seção [Como instalar e configurar o Azure PowerShell][11]. O script a seguir funciona com a visualização do Azure PowerShell 1.0 ou superior.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Observe** que o nome de parâmetro de conexão do **token** é importante. Você pode escolher seu próprio nome, desde que o texto seja concatenado. Você usará esse nome posteriormente no seu código de back-end ou na política de API.

Em seguida, acesse o [portal do Azure][19] e, em seguida, a folha de configurações **Gerais** da sua API. Você verá as opções de configuração adicionais: ![][21]


## Experimentar

Abra um aplicativo em PowerApps. Sua nova API está relacionada em **Conexões disponíveis**. Quando você seleciona a opção **Conectar**, ela exibe uma janela de logon do AAD. Insira os detalhes da conta do AAD da sua organização e sua conexão será criada.

Agora, quando uma chamada de tempo de execução for feita em seu aplicativo para a API usando essa conexão, o back-end receberá o token do usuário do AAD no cabeçalho de HTTP **x-ms-apim-tokens** no seguinte formato de [codificação Base64][20]\:

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Observe** que o **token** de nome da propriedade corresponde ao nome do parâmetro de conexão que você usa ao definir a configuração.

Seu código de back-end pode, então, obter o token do AAD da propriedade **AccessToken** e usá-lo, se necessário. O ambiente de serviço de aplicativo atualiza o token automaticamente.

## Configurar política de API

Como alternativa, você também pode usar a política de API para definir o token do AAD para o cabeçalho de **autorização** de HTTP padrão. Dessa forma, se seu código de back-end precisa usar o token do AAD, você pode obtê-lo em um modo padrão em vez de examinar um cabeçalho HTTP personalizado e executar a decodificação de Base64. Para fazer isso, acesse o portal do Azure, em seguida a folha **Política** de sua API e defina a política a seguir:

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Ao examinar essa política, ela basicamente permite que você referencie os valores do cabeçalho **x-ms-apim-tokens** como um JObject decodificado usando uma variável **tokens**. Então, você pode usar a política **set-header** para obter token do AAD real e defini-lo no cabeçalho **Autorização**. Essa é a mesma política usada pelo [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/). Para saber mais, consulte as [políticas no Gerenciamento de API do Azure](../api-management/api-management-howto-policies.md).

**Observe** que o **token** de nome da propriedade corresponde ao nome do parâmetro de conexão que você usou ao definir a configuração.

## Resumo e próximas etapas

Neste tópico, você viu como configurar uma API para se conectar (e autenticar) a um recurso de back-end em um domínio do Active Directory do Azure. Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps.

- [Desenvolva uma API para o PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_0309_2016-->