<properties 
	pageTitle="Gerenciar aplicativos de API" 
	description="Saiba como gerenciar aplicativos de API do Serviço de Aplicativo do Azure usando o portal do Azure e o Gerenciador de Servidores do Visual Studio." 
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
	ms.date="09/08/2015" 
	ms.author="tdykstra"/>

# Gerenciar aplicativos de API no Serviço de Aplicativo do Azure

Este artigo mostra como usar o [Portal de Visualização do Azure](https://portal.azure.com/) para executar tarefas de monitoramento e gerenciamento de aplicativos de API. Aqui estão algumas das tarefas que você pode realizar:

- Configurar autenticação 
- Habilitar dimensionamento automático
- Exibir logs
- Ver quantas solicitações são feitas e ver a quantidade de dados sendo usados por um aplicativo de API
- Realizar backup de um Aplicativo de API e criar alertas
- Configurar a segurança de acesso baseado em função

O artigo também mostra como realizar algumas tarefas de gerenciamento na janela do Gerenciador de servidores no Visual Studio.

## As folhas do gateway e aplicativo API no Portal de Visualização do Azure

No Serviço de Aplicativo do Azure, um aplicativo de API é um [aplicativo Web](../app-service-web/app-service-web-overview.md) que tem recursos adicionais para a hospedagem de serviços Web. No Portal do Azure, há uma folha **Aplicativo de API** para gerenciar os recursos específicos da API e uma folha **Host de aplicativo de API** para gerenciar o aplicativo Web subjacente.

Cada grupo de recursos que contém pelo menos um aplicativo de API também inclui um *gateway*. O gateway atua como um proxy, gerenciando a autenticação e outras funções administrativas para todos os aplicativos de API em um grupo de recursos. Como um aplicativo de API, um gateway é um aplicativo Web com funcionalidade adicional, portanto, há também duas folhas portal para gerenciamento do gateway: uma folha **Gateway** para funções específicas do gateway e uma folha **Host do Gateway** para gerenciar o aplicativo Web subjacente.

### Tarefas que você pode fazer apenas na folha Aplicativo de API

Você usa a folha **aplicativo API** para as seguintes tarefas:

- Configurar o nível de acesso - Clique em **Configurações > Configurações do aplicativo**. O valor padrão é interno, o que significa que somente aplicativos de API no mesmo grupo de recursos têm permissão para chamar o aplicativo de API. Para obter mais informações, consulte [Proteger um aplicativo API](app-service-api-dotnet-add-authentication.md).   
- Configurar a política de atualização - Clique em **Configurações > Configurações do aplicativo**. O valor padrão é **Ativado**. Isso significa que, quando uma nova versão do aplicativo de API é publicada no mercado, seu aplicativo de API será automaticamente atualizado para a nova versão se a alteração não for interruptiva.  
- Configurar a autenticação para chamadas de saída do aplicativo de API - clique em **Configurações > Autenticação**. Se o aplicativo de API faz chamadas para um serviço externo que requer autenticação, os valores de configuração necessários são inseridos aqui. Por exemplo, um conector Dropbox requer uma ID de cliente e um segredo do cliente para acessar o serviço de Dropbox.
- Configurar [RBAC](../role-based-access-control-configure.md) - clique em **Configurações > Usuários**. Acesso de usuário que você configura aqui determina apenas quem pode acessar os recursos específicos do aplicativo de API. Para configurar o RBAC para os recursos de aplicativo Web, use a folha **Host de aplicativo de API**. Normalmente, você desejaria manter as configurações de RBAC para o aplicativo de API e para o host do aplicativo de API em sincronia. Se você fornecer a outra pessoa acesso ao aplicativo de API, mas não ao host do aplicativo de API, essa pessoa não poderá usar os recursos na folha **Aplicativo de API** que realmente pertencem ao host do aplicativo de API.
- Visualizar definição de API - clique em **Definição de API** na seção **Resumo** para ver uma lista dos métodos expostos pelo aplicativo de API.
- [Instalar o Gerenciador de Conexão Híbrida](../app-service-logic/app-service-logic-hybrid-connection-manager.md). O Gerenciador de Conexão Híbrida lhe oferece a capacidade de conectar-se a um sistema local, como SQL Server ou SAP. Essa conectividade híbrida usa o Barramento de Serviço do Azure para conectar e controlar a segurança entre os recursos do Azure e seus recursos locais.

### Tarefas que podem ser realizadas tanto na folha de aplicativo de API quanto na folha de host de aplicativo de API 

A folha **Aplicativo de API** permite que você execute várias tarefas relativas ao aplicativo Web subjacente. Por exemplo, você pode realizar as tarefas a seguir:

* Parar, iniciar e reiniciar o aplicativo Web que hospeda o aplicativo de API.  
- Selecione **Solicitações e erros** para adicionar diferentes métricas de desempenho, incluindo códigos de erro HTTP comumente conhecidos, como códigos de status HTTP 200, 400 ou 500.
- Consulte os tempos de resposta, consulte quantas solicitações são feitas ao aplicativo de API e veja a quantidade de dados em entrada e a quantidade de dados em saída. 
- Crie alertas via email caso uma métrica exceda um limite de sua escolha. 
- Veja a quantidade de **CPU** usada pelo Aplicativo de API, examine a **cota de uso** atual em MB e consulte a utilização máxima de dados com base na faixa de custo.
- Consulte **Despesa estimada** para determinar os custos potenciais da execução do Aplicativo de API.
- Exiba os logs de aplicativos e outros logs do IIS, incluindo logs de servidor Web e logs FREB.
- Selecione **Processos** para abrir o Gerenciador de Processos. Isso mostra as instâncias da Web e suas propriedades, incluindo o uso de memória e contagem de threads.

No entanto, essas tarefas também podem ser realizadas usando a folha **Host de aplicativo de API**. É por isso que as duas folhas compartilham grande parte da mesma interface de usuário. Por exemplo, os botões **Parar**, **Iniciar** e **Reiniciar** da folha **Aplicativo de API** têm o mesmo efeito que os botões **Parar**, **Iniciar** e **Reiniciar** da folha **Host de aplicativo de API**. Do mesmo modo, as informações de monitoramento fornecidas na folha **Aplicativo de API** são as mesmas exibidas na folha **Host de aplicativo de API**.

As únicas funções fornecidas na folha **Aplicativo de API** que não são duplicatas da folha **Host de aplicativo de API** estão listadas na seção anterior.

### Tarefas que você pode realizar apenas na folha Host de Aplicativo de API

Você usa a folha **Host de Aplicativo de API** para todas as tarefas que você realizaria para qualquer aplicativo Web.

### Tarefas que você pode realizar apenas na folha gateway

Você usa a folha **Gateway** para as seguintes tarefas:

- Configuração de provedor de autenticação para chamadas de entrada para aplicativos de API - clique em **Configurações > Identidade**. Se o gateway precisa autenticar usuários antes de permitir que eles chamem aplicativos de API no grupo de recursos, os valores de configuração necessários são inseridos aqui. Para obter mais informações, consulte [Configurar e testar um conector SaaS no serviço de aplicativo do Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurar [RBAC](../role-based-access-control-configure.md) - clique em **Configurações > Usuários**. Acesso de usuário que você configura aqui determina apenas quem pode acessar os recursos específicos do gateway, mas não aqueles recursos compartilhados com todos os aplicativos Web.

### Tarefas que podem ser realizadas tanto na folha do gateway quanto na folha de host do gateway 

As folhas gateway e host do gateway compartilham a mesma IU presente nas folhas aplicativo de API e host de aplicativo de API.

### Tarefas que você pode realizar somente na folha host do gateway

Você usa a folha **Host de gateway** para todas as tarefas que você realizaria para qualquer aplicativo da Web.

## <a id="navigate"></a>Como navegar até as folhas de aplicativo de API e de gateway 

Uma maneira de chegar à folha **Aplicativo de API** é por meio do recurso de navegação do Portal do Azure. Na home page do portal, clique em **Procurar > Aplicativos de API** para ver todos os aplicativos de API que você pode gerenciar.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### Navegar até a folha Aplicativo de API

Quando você clica em uma linha na folha **Aplicativos de API**, o portal exibe a folha **Aplicativo de API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### Navegar até a folha Host de aplicativo de API

Para chegar à folha **Host de aplicativo de API**, clique em **Host de aplicativo de API** na folha **Aplicativo de API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### Navegar até a folha Gateway

Para chegar à folha **Gateway**, clique no link **Gateway** na folha **Aplicativo de API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### Navegar até a folha Host de Gateway

Para chegar à folha **Host de Gateway**, clique no link **Host de Gateway** na folha **Gateway**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Acessar aplicativos de API no Gerenciador de Servidores no Visual Studio

No **Gerenciador de Servidores** no Visual Studio, você pode iniciar uma sessão de depuração remota, exibir logs de streaming e clicar em uma entrada de menu que abra a folha Aplicativo de API no portal.

Para chegar a um aplicativo de API no Gerenciador de Servidores, clique em **Azure > Serviço de Aplicativo > [o nome do grupo de recursos] > [o nome do aplicativo API]**, conforme mostrado na ilustração.

![](./media/app-service-api-manage-in-portal/se.png)

## Próximas etapas

Este artigo mostra como usar o portal do Azure para executar tarefas de gerenciamento de aplicativos de API. Para aplicativos de API instalados a partir da galeria de aplicativos de API, confira também [Gerenciar e monitorar aplicativos de API e conectores internos](../app-service-logic/app-service-logic-monitor-your-connectors.md).

Para obter informações sobre como gerenciar aplicativos de API usando a linha de comando, consulte os artigos na seção **Automatizar** do menu que aparece no lado esquerdo do artigo (em janelas de navegador largas) ou na parte superior do artigo (em janelas de navegador estreitas).

<!---HONumber=Oct15_HO3-->