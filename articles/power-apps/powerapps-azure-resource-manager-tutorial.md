<properties
	pageTitle="Tutorial: Criar uma API personalizada usando o Azure Resource Manager no PowerApps e nos Fluxos Lógicos | Microsoft Azure"
	description="Tutorial do Azure Resource Manager para criar uma API personalizada no PowerApps e nos Fluxos Lógicos"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"s
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# Tutorial: Criar uma API Web personalizada protegida por ARM para PowerApps e Fluxos Lógicos 

Este tutorial orienta você pelas etapas necessárias para registrar um arquivo do Swagger que descreve a [API do ARM][6] e conecta-se à API personalizada no PowerApps.

## Para começar, você precisa do seguinte:

- Uma assinatura do Azure
- Uma conta do PowerApps

## Habilitar a autenticação no Azure Active Directory

Primeiro, precisamos criar um aplicativo do Azure Active Directory (AAD) que executa a autenticação ao chamar o ponto de extremidade de API ARM.

1. Para criar um aplicativo AAD, entre na [Assinatura do Azure][7] e vá para **Active Directory**:
![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "AAD do Azure")  

2. Nessa página, escolha o diretório no qual você deseja criar seu aplicativo AAD. Selecione o diretório, vá para a guia **Aplicativos** e selecione **Adicionar**:
![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Aplicativo do Azure")

3. Insira um nome para seu aplicativo, selecione **aplicativo Web e/ou API Web** e selecione **Avançar**:
![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "Novo Aplicativo")

4. Em **URL de Logon**, insira: **http://login.windows.net*. Em **URI da ID do Aplicativo**, insira qualquer URI exclusivo. Em seguida, selecione **Concluir**:
![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "Novo Segundo Aplicativo")

5. Depois de criar o aplicativo AAD, vá para a guia **Configurar**. Nessa guia, podemos configurar as permissões no aplicativo.

6. Em **permissões para outros aplicativos**, selecione **Adicionar aplicativo** e digite as seguintes permissões conforme mostrado:
![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "Permissões")

	Certifique-se de delegar as permissões necessárias para seu aplicativo:
![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "Delegar permissões")

7. Em **keys**, selecione qualquer duração. **Copie e salve a chave em um local seguro**. Precisaremos disso mais tarde. Anote também a __ID do Cliente__:
![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "Configurar as chaves")

8. Em **logon único**, insira a seguinte URL em __URL de resposta__: https://msmanaged-na.consent.azure-apim.net/redirect:
![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "URL de Redirecionamento")

9. **Salve** suas alterações. **Copie e salve a chave em um local seguro**.

## Adicione a conexão ao PowerApps ou aos Fluxos Lógicos

Agora que o aplicativo AAD está configurado, vamos adicionar a API personalizada.

1. Abra o [portal da Web do PowerApps][1], vá para a guia **Conexões** e selecione __Adicionar uma conexão__ no canto superior direito:
![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "Criar API Personalizada")  

2. Selecione __Adicionar uma API Personalizada__:
![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "Criar API Personalizada")

3. Carregue o arquivo Swagger ARM, que está disponível [para baixar][8]\:
![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "Criar API Personalizada")

4. Na próxima tela, como foi detectado que o arquivo Swagger usa a autenticação do AAD, precisamos inserir a ID do cliente do AAD, o segredo do cliente (a **chave** armazenada em um local seguro) e outras configurações:
![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "Configurações OAuth")

5. Se tudo estiver configurado corretamente, você pode usar a API personalizada ARM criando uma conexão e fazendo referência a ela ao criar o PowerApp ou o Fluxo de Lógica:
![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "CustomAPI criada")

Da mesma forma, você pode acessar todos os dados que são expostos por meio de APIs RESTful e autenticadas usando OAuth2 do AAD.

Para obter uma experiência mais detalhada sobre como criar PowerApps e Fluxos Lógicos, veja o seguinte:

- [Connect to Office 365, Twitter, and Microsoft Translator (Conectar-se ao Office 365, Twitter e Microsoft Translator)][5]
- [Show data from Office 365 (Mostrar dados do Office 365)][4]
- [Create an app from data (Criar um aplicativo com base em dados)][3]
- [Get started with logic flows (Introdução aos fluxos lógicos)][2]

Em caso de dúvidas ou comentários sobre as APIs personalizadas, envie um email para [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).


<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0413_2016-->