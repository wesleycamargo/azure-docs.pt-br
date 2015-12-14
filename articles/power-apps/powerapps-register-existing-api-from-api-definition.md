<properties
	pageTitle="Criar definição de API do Swagger 2.0 de uma API do PowerApps Enterprise | Microsoft Azure"
	description="Saiba como registrar uma API de uma definição de API do Swagger 2.0 criada de uma API existente"
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
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Registre uma API da definição de API do Swagger 2.0  
Muitas organizações já têm algumas APIs existentes que os usuários podem usar e consumir dentro de seus aplicativos. Para usar essas APIs em seus aplicativos, você deve “registrar” as APIs no portal do Azure. As opções a seguir estão disponíveis:

- Registre uma [API gerenciada pela Microsoft ou por TI](powerapps-register-from-available-apis.md)
- Registre uma API hospedada em [seu ambiente de serviço de aplicativo](powerapps-register-api-hosted-in-app-service.md)
- Registre-se usando uma definição de API do Swagger 2.0

Este artigo mostra como **registrar uma definição de API do Swagger 2.0** criada de uma API existente.

#### Pré-requisitos para iniciar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Crie um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md)

## Registre uma API existente usando a definição de API do Swagger 2.0

É muito fácil registrar essas APIs existentes. As etapas são as seguintes:

1. Crie a definição de API do [Swagger 2.0](http://swagger.io) para sua API existente. O PowerApps usa o Swagger 2.0 como o formato de definição de API. Você pode usar as ferramentas mencionadas no [site do Swagger 2.0](http://swagger.io) para ajudá-lo a criar facilmente uma definição da API do Swagger 2.0. Elementos a serem observados:  

	- A ``host`` propriedade deve apontar para o ponto de extremidade real de sua API existente. Não use nenhum esquema ou subcaminho. Por exemplo, insira ``api.contoso.com``. <br/><br/>
	- A ``basePath`` propriedade deve relacionar os caminhos de seu ponto de extremidade de API existente, se houver algum. Iniciar com uma barra invertida ``/``. Por exemplo, insira ``/purchaseorderapi``.

2. Verifique se sua API existente é acessível pelo seu ambiente de serviço de aplicativo de forma segura: <br/><br/> a) Se você estiver familiarizado com a criação de sua API acessível por meio da Internet, você pode configurar a autenticação básica de HTTP entre o ambiente de serviço de aplicativo e da sua API existente. Acesse [aqui](powerapps-configure-apis.md) para ver como. <br/><br/> b) Se você quiser manter sua API na rede da sua organização, você pode configurar uma rede virtual no ambiente de serviço de aplicativo para acessar a rede da sua organização com segurança. Acesse [aqui](../app-service-app-service-environment-intro.md) para ver como.

3. No [portal do Azure](https://portal.azure.com/), selecione **PowerApps** e, em seguida, selecione **Gerenciar APIs**: ![][11]
4. Na seção Gerenciar APIs, selecione **Adicionar**: ![][12]
5. Em **Adicionar API**, insira as propriedades da API:  

	- Em **Nome**, insira um nome para sua API. Observe que o nome inserido está incluído na URL de tempo de execução da API. Verifique o nome significativo e exclusivo em sua organização.
	- Em **Fonte**, selecione **Importar do Swagger 2.0**.

6. Em **definição de API (Swagger 2.0)**, faça upload do arquivo de definição da API do Swagger 2.0: ![][13]
7. Selecione **Adicionar** para concluir estas etapas.

> [AZURE.TIP]Ao registrar uma API, você está registrando a API para seu ambiente de serviço de aplicativo. Uma vez no ambiente de serviço de aplicativo, ela poderá ser usada por outros aplicativos no mesmo ambiente de serviço de aplicativo.

## Resumo e próximas etapas

Neste tópico, você viu como registrar uma API da definição de API do Swagger 2.0. Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps:

- [Configurar APIs](powerapps-configure-apis.md)
- [Adicionar uma nova API](powerapps-register-from-available-apis.md)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1203_2015-->