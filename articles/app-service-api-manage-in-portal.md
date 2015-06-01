<properties 
	pageTitle="Gerenciar um aplicativo de API" 
	description="Saiba como gerenciar aplicativos de API, usando o portal de visualização do Azure e o Gerenciador de Servidores do Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="tdykstra"/>

# Gerenciar um aplicativo de API

Este artigo mostra como usar o portal de visualização do Azure para executar tarefas de gerenciamento de aplicativo de API tais como configurar a autenticação e configurar o dimensionamento automático. O artigo também mostra como realizar algumas tarefas de gerenciamento na janela do Gerenciador de servidores no Visual Studio.

## Noções básicas sobre arquitetura de aplicativo de API

No serviço de aplicativo do Azure, um aplicativo de API é um [aplicativo Web](app-service-web-overview.md) que tem recursos adicionais para a hospedagem de serviços da Web. No portal de visualização do Azure, há uma folha **Aplicativo de API** para gerenciar os recursos específicos da API e uma folha **Host de aplicativo de API** para gerenciar o aplicativo Web subjacente.

Cada grupo de recursos que contém pelo menos um aplicativo de API também inclui um *gateway*. O gateway atua como um proxy, gerenciando a autenticação e outras funções administrativas para todos os aplicativos de API em um grupo de recursos. Como um aplicativo de API, um gateway é um aplicativo Web com funcionalidade adicional, portanto, há também duas folhas portal para gerenciamento do gateway: uma folha **Gateway** para funções específicas do gateway e uma folha **Host do Gateway** para gerenciar o aplicativo Web subjacente.

### Tarefas de folha de aplicativo de API 

Você usa a folha **aplicativo API** para as seguintes tarefas:

- Configurar o nível de acesso - Clique em **Configurações > Configurações do aplicativo**. O valor padrão é interno, o que significa que somente aplicativos de API no mesmo grupo de recursos têm permissão para chamar o aplicativo de API. Para obter mais informações, consulte [Proteger um aplicativo API](app-service-api-dotnet-add-authentication.md).   
- Configurar a política de atualização - Clique em **Configurações > Configurações do aplicativo**. O valor padrão é **Ativado**. Isso significa que, quando uma nova versão do aplicativo de API é publicada no mercado, seu aplicativo de API será automaticamente atualizado para a nova versão se a alteração não for interruptiva.  
- Configurar a autenticação para chamadas de saída do aplicativo de API - clique em **Configurações > Autenticação**. Se o aplicativo de API faz chamadas para um serviço externo que requer autenticação, os valores de configuração necessários são inseridos aqui. Por exemplo, um conector Dropbox requer uma ID de cliente e um segredo do cliente para acessar o serviço de Dropbox.
- Configurar [RBAC](role-based-access-control-configure.md) - clique em **Configurações > Usuários**. Acesso de usuário que você configura aqui determina apenas quem pode acessar os recursos específicos do aplicativo de API. Para configurar o RBAC para os recursos de aplicativo Web, use a folha **Host de aplicativo de API**. Normalmente, você desejaria manter as configurações de RBAC para o aplicativo de API e para o host do aplicativo de API em sincronia. Se você fornecer a outra pessoa acesso ao aplicativo de API, mas não ao host do aplicativo de API, essa pessoa não poderá usar os recursos na folha **Aplicativo de API** que realmente pertencem ao host do aplicativo de API. A relação entre a folha **Aplicativo de API** e a folha **Host de aplicativo de API** é explicada abaixo.
- Visualizar definição de API - clique em **Definição de API** na seção **Resumo** para ver uma lista dos métodos expostos pelo **aplicativo de API**.

### Recursos comuns das folhas Aplicativo de API e Host do aplicativo de API 

A folha **Aplicativo de API** permite que você execute várias tarefas relativas ao aplicativo Web subjacente. Por exemplo, ela oferece botões para parar, iniciar e reiniciar o aplicativo Web que hospeda o aplicativo de API. No entanto, essas tarefas também podem ser feitas usando a folha **Host de aplicativo de API**. É por isso que as duas folhas compartilham grande parte da mesma interface de usuário. Os botões **Parar**, **Iniciar** e **Reiniciar** da folha **Aplicativo de API** têm o mesmo efeito que os botões **Parar**, **Iniciar** e **Reiniciar** da folha **Host de aplicativo de API**. Do mesmo modo, as informações de monitoramento fornecidas na folha **Aplicativo de API** são as mesmas exibidas na folha **Host de aplicativo de API**.

As únicas funções fornecidas na folha **Aplicativo de API** que não são duplicatas da folha **Host de aplicativo de API** estão listadas na seção anterior.

### Tarefas da folha Host de aplicativo de API

Você usa a folha **Host de aplicativo de API** para todas as tarefas que você realizaria para qualquer aplicativo da Web. Para obter mais informações, consulte [Gerenciar aplicativos Web no portal](web-sites-manage.md).

### Tarefas da folha Gateway

Você usa a folha **Gateway** para as seguintes tarefas:

- Configuração de provedor de autenticação para chamadas de entrada para aplicativos de API - clique em **Configurações > Identidade**. Se o gateway precisa autenticar usuários antes de permitir que eles chamem aplicativos de API no grupo de recursos, os valores de configuração necessários são inseridos aqui. Para obter mais informações, consulte [Configurar e testar um conector SaaS no serviço de aplicativo do Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurar [RBAC](role-based-access-control-configure.md) - clique em **Configurações > Usuários**. Os mesmos comentários acima explicando a relação entre a configuração de RBAC inserida nas folhas Aplicativo de API e Host de aplicativo de API aplica-se às folhas Gateway e Host de gateway.

### Tarefas da folha Host de gateway

Você usa a folha **Host de gateway** para todas as tarefas que você realizaria para qualquer aplicativo da Web. Para obter mais informações, consulte [Gerenciar aplicativos Web no portal](web-sites-manage.md).

## Navegar até a folha Aplicativo de API 

Uma maneira de chegar à folha **Aplicativo de API** é por meio do recurso de navegação do portal de visualização do Azure. Na home page do portal, clique em **Procurar > Aplicativos de API** para ver todos os aplicativos de API que você pode gerenciar.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

Quando você clica em uma linha na folha **Aplicativos de API**, o portal exibe a folha **Aplicativo de API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

## Navegar até a folha Host de aplicativo de API

Para chegar à folha **Host de aplicativo de API**, clique em **Host de aplicativo de API** na folha **Aplicativo de API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

## Navegar até a folha Gateway

Para chegar à folha **Gateway**, clique no link **Gateway** na folha **Aplicativo de API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

## Navegar até a folha Host de Gateway

Para chegar à folha **Host de Gateway**, clique no link **Host de Gateway** na folha **Gateway**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Acessar aplicativos de API no Gerenciador de Servidores no Visual Studio

No Gerenciador de Servidores no Visual Studio, você pode iniciar uma sessão de depuração remota, exibir logs de streaming e clicar em uma entrada de menu que abra a folha Aplicativo de API no portal.

Para chegar a um aplicativo de API no Gerenciador de servidores, clique em **Azure > Serviço de aplicativo > [o nome do grupo de recursos] > [o nome do aplicativo API]**, conforme mostrado na ilustração.

![](./media/app-service-api-manage-in-portal/se.png)

## Resumo

Este artigo mostra como usar o portal de visualização do Azure para executar tarefas de gerenciamento de aplicativo de API. Para obter mais informações, consulte [O que são aplicativos de API?](app-service-api-apps-why-best-platform.md)


<!--HONumber=52-->
