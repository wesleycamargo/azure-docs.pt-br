---
title: Solução de Controle de Alterações no Log Analytics | Microsoft Docs
description: Você pode usar a solução de Controle de Alterações de Configuração no Log Analytics para ajudar a identificar facilmente as alterações no software e nos Serviços do Windows que ocorrem em seu ambiente. Identificar essas alterações de configuração poderá ajudá-lo a detectar problemas operacionais.
services: operations-management-suite
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="change-tracking-solution-in-log-analytics"></a>Solução de Controle de Alterações no Log Analytics
Você pode usar a solução de Controle de Alterações de Configuração no Log Analytics para ajudar a identificar facilmente as alterações no software e nos Serviços do Windows, bem como as alterações no daemon do Linux que ocorrem em seu ambiente. Identificar essas alterações de configuração poderá ajudar a detectar problemas operacionais.

Instale a solução para atualizar o tipo de agente já instalado. Alterações a software e serviços do Windows instalados nos servidores monitorados são lidas e, em seguida, os dados são enviados para o serviço do Log Analytics na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Quando as alterações são encontradas, os servidores com as alterações são mostrados no painel Controle de Alterações. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

* O Operations Manager é necessário para a solução de Controle de Alterações.
* Você deve ter um agente do Windows ou do Operations Manager em cada computador no qual deseja controlar as alterações.
* Adicione a solução de Controle de Alterações ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md).  Não é necessária nenhuma configuração.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações
O Controle de Alterações coletará inventário de software e metadados do serviço Windows usando os agentes que você tiver habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Controle de Alterações.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Não](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Não](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |por hora |

## <a name="use-change-tracking"></a>Use o Controle de Alterações
Após instalar a solução, você pode exibir o resumo das alterações nos servidores monitorados usando o bloco **Controle de Alterações** na página **Visão geral** do OMS.

![imagem de bloco Controle de Alterações](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Você pode exibir as alterações à sua infraestrutura e então analisar em detalhes para as seguintes categorias:

* Alterações por tipo de configuração para serviços de software e do Windows
* Alterações de software para aplicativos e atualizações para servidores individuais
* Número total de alterações de software para cada aplicativo
* Alterações no serviço Windows para servidores individuais

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/oms-changetracking01.png)

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para exibir as alterações para qualquer tipo de alteração
1. Na página **Visão Geral**, clique no bloco **Controle de Alterações**.
2. No painel **Controle de Alterações**, examine as informações resumidas em uma das folhas de tipo de alteração e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página de **pesquisa de log**.
3. Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados do controle de alterações.

<!--HONumber=Oct16_HO2-->


