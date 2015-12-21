<properties
	pageTitle="Desenvolva ou crie uma API hospedada no ambiente de serviço de aplicativo no PowerApps Enterprise | Microsoft Azure"
	description="Saiba como registrar uma API personalizada hospedada no ambiente de serviço de aplicativo no portal do Azure"
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

# Registrar uma API hospedada no seu ambiente de serviço de aplicativo
O PowerApps oferece suporte para as APIs hospedadas de registro existentes em qualquer parte na nuvem ou local, o que é realmente eficaz. Em alguns cenários, talvez você queira desenvolver ou criar algumas novas APIs. Por exemplo, talvez você queira:

- Implemente algumas novas funcionalidades para a sua organização usar.
- Desenvolva sobre a funcionalidade ou dados existentes para proporcionar uma melhor experiência para os usuários criarem seus aplicativos.

Quando você hospeda suas APIs em seu ambiente de serviço de aplicativo, aproveita todos os recursos existentes do [ambiente de serviço de aplicativo](../app-service-app-service-environment-intro.md) e também pode obter uma melhor experiência de integração.

Para usar essas APIs em seus aplicativos, você deverá “registrar” as APIs no portal do Azure. As opções a seguir estão disponíveis:

- Registre uma [API gerenciada pela Microsoft ou gerenciada por TI](powerapps-register-from-available-apis.md) pré-criada.
- Registre um aplicativo Web, um aplicativo de API e um aplicativo móvel hospedado no seu Ambiente do Serviço de Aplicativo (neste tópico).
- Registre uma de suas próprias APIs do Swagger usando uma [definição da API do Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

Este artigo mostra como **registrar um aplicativo Web, um aplicativo de API e um aplicativo móvel hospedados no seu ambiente de serviço de aplicativo**.

#### Pré-requisitos para começar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Crie um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md).


## Desenvolver e implantar uma API em seu ambiente de serviço de aplicativo

Desenvolver uma API no ambiente de serviço de aplicativo é simples. Você escolhe sua linguagem de programação favorita para compilar uma API da web e, em seguida, usa o [Swagger 2.0](http://swagger.io) para descrever a definição da API. Alguns exemplos incluem:

- [Compilar e implantar um .NET no Serviço de Aplicativo do Azure](../app-service-api-dotnet-get-started.md)
- [Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure](../app-service-api-java-api-app.md)
- [Criar e implantar um aplicativo de API do Node.js no serviço de aplicativo do Azure](../app-service-api-nodejs-api-app.md)

Você também tem opções para implantar sua API da web em um ambiente de serviço de aplicativo, incluindo a implantação do Visual Studio e a implantação contínua por meio de um sistema de controle do código-fonte. [Implantar um aplicativo Web no Serviço de Aplicativo do Azure](../web-sites-deploy.md) é um bom recurso.

## Registrar sua API personalizada no ambiente de serviço de aplicativo

Depois que a API for implantada em seu ambiente de serviço de aplicativo, use as seguintes etapas para registrar:

1. No [portal do Azure](https://portal.azure.com/), selecione **PowerApps** e, em seguida, selecione **Gerenciar APIs**: ![][11]
2. Na seção Gerenciar APIs, selecione **Adicionar**: ![][12]  
3. Em **Adicionar API**, insira as propriedades da API:  

	- Em **Nome**, insira um nome para sua API. Observe que o nome inserido é incluído na URL de tempo de execução da API. Verifique o nome significativo e exclusivo em sua organização.	
	- Em **Fonte**, selecione **Importar das APIs hospedadas no Ambiente de Serviço de Aplicativo**: ![][13]
4. Em **API hospedada no Ambiente de Serviço de Aplicativo**, selecione a API que você deseja importar. Esta lista mostra cada aplicativo Web, o aplicativo de API e o aplicativo móvel em seu ambiente de serviço de aplicativo que tem sua propriedade **apiDefinition.url** configurada. Para importar a API, ela usa a definição de API do Swagger 2.0 exposta usando essa propriedade. Verifique se essa URL é publicamente acessível ao registrar a API: ![][14]
5. Selecione **Adicionar** para concluir estas etapas.

> [AZURE.TIP]Ao registrar uma API, você está registrando a API para seu ambiente de serviço de aplicativo. Uma vez no ambiente de serviço de aplicativo, ela poderá ser usada por outros aplicativos no mesmo ambiente de serviço de aplicativo.

## Resumo e próximas etapas
Neste tópico, você viu como registrar APIs hospedadas em seu ambiente de serviço de aplicativo. Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps:

- [Configurar as propriedades da API](powerapps-configure-apis.md)
- [Dar aos usuários acesso às APIs](powerapps-manage-api-connection-user-access.md)
- [Começar a criar seus aplicativos no PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1210_2015-->