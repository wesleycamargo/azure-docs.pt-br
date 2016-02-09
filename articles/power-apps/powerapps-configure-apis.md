<properties
	pageTitle="Altere ou atualize as propriedades da API do PowerApps no portal do Azure | Microsoft Azure"
	description="Adicione um ícone personalizado, atualize a política de XML ou atualize a definição de Swagger de sua API do PowerApps"
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

# Atualizar uma API existente e suas propriedades

A API que você registra no ambiente de serviço de aplicativo é essencialmente um proxy para o serviço de back-end. Depois de criar a API, talvez você queira alterar suas propriedades. Por exemplo, talvez você queira:

- Adicionar um ícone personalizado em sua API.
- Alterar a forma como você protege o back-end usado pela API. 
- Atualizar o nome de exibição de sua API para um nome mais amigável.


#### Pré-requisitos para iniciar

- Inscreva-se no [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Crie um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md).
- Registre uma [API](powerapps-register-from-available-apis) em seu ambiente.

## Adicione um ícone personalizado ou um nome de exibição amigável de usuário

1. No [Portal do Azure](https://portal.azure.com), abra a folha para sua API.
2. Selecione **Todas as configurações**.
3. Em **Configurações**, selecione **Geral**: ![][11]

Na seção Geral, você pode alterar as seguintes configurações:

Configuração | Descrição
--- | ---
Nome de exibição | Esse nome é usado ao relacionar as *conexões disponíveis* em PowerApps. Por padrão, ele usa o nome do recurso da API, que pode não ser o melhor nome para seus usuários do PowerApps. Você pode inserir um nome de exibição amigável de usuário. Por exemplo, você pode chamá-lo de **Novos pedidos dos clientes** ou **Exibir o histórico de vendas**.  
URL do ícone | Você pode adicionar um ícone personalizado para sua API. O ícone é usado ao relacionar as *Conexões disponíveis* e *Minhas conexões* ao PowerApps. Por padrão, o ícone a seguir é usado: <br/><br/>![][12] <br/><br/>Ao usar um ícone personalizado:<br/><ul><li>A URL para o ícone deve ser acessível publicamente.</li><li>Ela pode ser um arquivo .png ou an .svg. Ao usar um arquivo png, o de 40 x 40 pixels é o preferencial.</li></ul>
Esquema de URL | Escolha qual esquema, ou esquemas, você deseja que sua API ofereça suporte. Você pode escolher **HTTP**, **HTTPS**, ou **HTTP e HTTPS**. Por padrão, HTTP e HTTPS são habilitados. <br/><br/>O ambiente de serviço de aplicativo configura automaticamente o esquema com base na configuração de back-end. Portanto, se não houver mais nada que precise ser configurado, você pode desenvolver ou alterar seu serviço de back-end. 
Autenticar com o serviço de back-end | Depois de registrar seu serviço de back-end no ambiente de serviço de aplicativo, é recomendável proteger o back-end para que os clientes possam requisitá-lo somente usando sua API. Com base em onde o back-end é implantado, as seguintes opções estão disponíveis:<br/><br/><ul><li><strong>Pode ser acessado somente por meio dessa API</strong>: essa opção só está disponível quando o back-end é implantado no ambiente de serviço de aplicativo. Quando selecionado, ele desabilita qualquer nome de host em seu back-end. Visto que o proxy da API também está em execução no mesmo ambiente de serviço de aplicativo, ele ainda pode acessar seu back-end.</li><li><strong>Autenticação básica de HTTP</strong>: essa opção está disponível independentemente de onde o back-end for implantado. Quando selecionado, você insere um nome de usuário e senha. Quando o proxy chama seu back-end, ele usa a autenticação básica de HTTP para passar o nome de usuário e a senha no cabeçalho de autorização de HTTP. Finalmente, seu serviço de back-end precisa confirmar (autenticar) o nome de usuário e a senha inserida.<br/><br/>Para saber mais sobre como implementar a autenticação básica de HTTP no ASP.NET Web API 2, consulte a seção [Filtros de autenticação na API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/authentication-filters).</li></ul>


## Atualizar o Swagger de sua API

1. No [Portal do Azure](https://portal.azure.com), abra a folha para sua API.
2. Selecione **Todas as configurações**.
3. Em **Configurações**, selecione **definição de API**: ![][13]

**Swagger 2.0** é o formato de definição de API com suporte. A definição de API atual está no editor de JSON inserido. Você pode editar em linha ou carregar um novo arquivo JSON. Depois que você **Salvar** as alterações, todos os erros serão mostrados nessa folha, incluindo quaisquer erros com a definição de API.

- Para saber mais sobre o Swagger 2.0, consulte o [site oficial do Swagger](http://swagger.io).
- Para saber mais sobre como obter o Swagger 2.0 durante o desenvolvimento de sua API, consulte:  
	- [Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure](../app-service-dotnet-create-api-app.md)
	- [Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure](../app-service-api-java-api-app.md)
	- [Criar e implantar um aplicativo de API do Node.js no serviço de aplicativo do Azure](../app-service-api-nodejs-api-app.md)
	- [Personalizar as definições da API gerada por Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md)
- Para saber mais sobre as práticas recomendadas de uso do Swagger 2.0 para PowerApps, consulte a seção [Desenvolver uma API para PowerApps](powerapps-develop-api.md).

## Atualizar a política XML de sua API

1. No [Portal do Azure](https://portal.azure.com), abra a folha para sua API.
2. Selecione **Todas as configurações**.
3. Em **Configurações**, selecione **Política**: ![][14]

Essa política é a mesma política com suporte do [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/). A política atual está no editor de XML inserido. Você pode editar em linha ou carregar um novo arquivo XML. Depois de **Salvar** suas alterações, todos os erros serão mostrados nessa folha, incluindo todos os problemas com a política de API.

As [Políticas no gerenciamento de API](../api-management-howto-policies.md) são um bom recurso para saber mais sobre como configurar e entender as políticas.


## Resumo e próximas etapas
Depois de criar sua API, você pode usar as etapas deste tópico para alterar suas configurações e até mesmo personalizar algumas delas.

Estes são alguns tópicos e recursos relacionados para saber mais sobre o PowerApps.

- [Configure uma API para se conectar ao back-end protegido de AAD](powerapps-configure-apis-aad.md)
- [Desenvolva uma API para o PowerApps](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_0128_2016-->