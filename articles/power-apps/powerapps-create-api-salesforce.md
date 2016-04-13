<properties
	pageTitle="Adicione a API do Salesforce ao PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do Salesforce no ambiente de serviço de aplicativo da sua organização"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Criar uma nova API do Salesforce no PowerApps Enterprise

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

Adicione a API do Salesforce ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@*SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa.
 
2. Selecione **Procurar** na barra de tarefas: ![][14]

3. Na lista, você pode rolar a tela para localizar PowerApps ou digite *powerapps*: ![][15]

4. Em **PowerApps**, selecione **Gerenciar APIs**: ![Navegue até as APIs registradas][1]

5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API: ![Adicionar API][2]

6. Insira um **nome** descritivo para sua API.
	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **Salesforce**: ![selecione a api da Salesforce][3]

8. Selecione **Configurações - Definir as configurações necessárias**: ![defina as configurações do API do dropbox][7]

9. Digite a *Chave do Aplicativo* e o *Segredo do Aplicativo* do seu aplicativo do Salesforce. Se não tiver um, consulte a seção "Registrar um aplicativo do Salesforce para uso com o PowerApps" neste tópico para criar a chave e os valores secretos necessários.

	> [AZURE.IMPORTANT] Salve a **URL de redirecionamento**. Talvez esse valor seja necessário neste tópico posteriormente.

10. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do Salesforce será adicionada ao seu ambiente do serviço de aplicativo.


## Opcional: registre um aplicativo do Salesforce para uso com PowerApps

Se não tiver um aplicativo do Salesforce existente com a chave e valores do segredo, então use as seguintes etapas para criar o aplicativo e obter os valores necessários.

1. Abra a [página inicial do desenvolvedor Salesforce][5] e entre com sua conta do Salesforce. 

2. Na página inicial, selecione o perfil e selecione **Instalação**: ![Salesforce página inicial][6]

3. Selecione **Criar** e selecione **Aplicativos**. Na página **Aplicativos**, selecione **Novo** em **Aplicativos Conectados**: ![Salesforce criar aplicativo][7]

4. Em **Novo Aplicativo Conectado**:

	1. Insira o valor de **Nome do Aplicativo Conectado**.  
	2. Insira o valor de **Nome da API**.  
	3. Insira o valor de **Email de Contato**.  
	4. Em _API (habilitar configurações de OAuth)_, selecione **Ativar configurações de OAuth** e defina a **URL de Retorno de Chamada** para a URL de redirecionamento recebida ao adicionar a nova API do Salesforce no Portal do Azure (neste tópico).  

5. Em _Escopos de OAuth selecionados_, adicione os seguintes escopos para os **Escopos de OAuth selecionados**:

	- Acessar e gerenciar os dados de Chatter (chatter\_api)
	- Acessar e gerenciar seus dados (api)
	- Permitir o acesso ao seu identificador exclusivo (openid)
	- Executar solicitações no seu nome a qualquer momento (refresh\_token, offline\_access)

6. **Salve** suas alterações: ![Salesforce novo aplicativo][8]

É criado um novo aplicativo do Salesforce. Você pode usar esse aplicativo em sua configuração da API do Salesforce no portal do Azure.

## Consulte as APIs REST

Referência da [API REST do Salesforce](../connectors/connectors-create-api-salesforce.md).

## Resumo e próximas etapas
Neste tópico, foi adicionada a API do Salesforce ao seu PowersApps Enterprise. Em seguida, disponibilize aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!-----------HONumber=AcomDC_0330_2016--->