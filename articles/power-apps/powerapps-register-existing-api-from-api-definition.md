<properties
	pageTitle="Criar a definição da API do Swagger 2.0 de uma API do PowerApps Enterprise | Microsoft Azure"
	description="Saiba como registrar uma API de uma definição da API do Swagger 2.0 criada de uma API existente"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="12/09/2015"
   ms.author="guayan"/>

# Registrar uma API da definição da API do Swagger 2.0  
Muitas organizações já têm algumas APIs que os usuários podem usar e consumir nos aplicativos. Para usar essas APIs em seus aplicativos, você deverá “registrar” as APIs no portal do Azure. As opções a seguir estão disponíveis:

- Registre uma [API gerenciada pela Microsoft ou gerenciada por TI](powerapps-register-from-available-apis.md) pré-criada.
- Registre um aplicativo Web, um aplicativo de API e um aplicativo móvel hospedado no [seu Ambiente do Serviço de Aplicativo](powerapps-register-api-hosted-in-app-service.md).
- Registre uma de suas próprias APIs do Swagger usando uma definição da API do Swagger 2.0 (neste tópico).

Este artigo mostra como **registrar uma de suas próprias APIs usando a definição da API do Swagger 2.0** criada a partir de uma API existente.

#### Pré-requisitos para começar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Crie um [ambiente do serviço de aplicativo](powerapps-get-started-azure-portal.md)

## Registrar uma API existente usando a definição da API do Swagger 2.0

É muito fácil registrar essas APIs existentes. As etapas são as seguintes:

1. Crie a definição da API do [Swagger 2.0](http://swagger.io) para sua API existente. O PowerApps usa o Swagger 2.0 como o formato de definição da API. Você pode usar as ferramentas mencionadas no [site do Swagger 2.0](http://swagger.io) para ajudá-lo a criar facilmente uma definição da API do Swagger 2.0. Elementos a serem observados:  

	- A propriedade ``host`` deve apontar para o ponto de extremidade real da API existente. Não use esquemas ou subcaminhos. Por exemplo, insira ``api.contoso.com``. <br/><br/>
	- A propriedade ``basePath`` deve relacionar os subcaminhos do ponto de extremidade da API existente, se houver algum. Comece com uma barra invertida ``/``. Por exemplo, insira ``/purchaseorderapi``.

2. Verifique se a API existente pode ser acessada pelo ambiente do serviço de aplicativo de forma segura: <br/><br/> a) Se você estiver familiarizado com a criação da API acessível por meio da Internet, poderá configurar a autenticação básica de HTTP entre o ambiente de serviço de aplicativo e a API existente. Entre [aqui](powerapps-configure-apis.md) para saber como. <br/><br/> b) Se você quiser manter a API na rede de sua organização, poderá configurar uma rede virtual no ambiente de serviço de aplicativo para acessar a rede de sua organização com segurança. Entre [aqui](../app-service-app-service-environment-intro.md) para saber como.

3. No [portal do Azure](https://portal.azure.com/), selecione **PowerApps** e selecione **Gerenciar APIs**: ![][11]
4. Na seção Gerenciar APIs, selecione **Adicionar**: ![][12]
5. Em **Adicionar API**, insira as propriedades da API:  

	- Em **Nome**, insira um nome para sua API. Observe que o nome inserido é incluído na URL de tempo de execução da API. Verifique o nome significativo e exclusivo em sua organização.
	- Em **Fonte**, selecione **Importar do Swagger 2.0**.

6. Em **Definição da API (Swagger 2.0)**, carregue o arquivo de definição da API do Swagger 2.0: ![][13]
7. Selecione **ADICIONAR** para concluir estas etapas.

> [AZURE.TIP]Ao registrar uma API, você estará registrando a API para seu ambiente do serviço de aplicativo. Uma vez no ambiente de serviço de aplicativo, ela poderá ser usada por outros aplicativos no mesmo ambiente do serviço de aplicativo.

## Resumo e próximas etapas

Neste tópico, você viu como registrar uma API a partir da definição da API do Swagger 2.0. Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps:

- [Configurar as propriedades da API](powerapps-configure-apis.md)
- [Dar aos usuários acesso às APIs](powerapps-manage-api-connection-user-access.md)
- [Começar a criar seus aplicativos no PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1210_2015-->