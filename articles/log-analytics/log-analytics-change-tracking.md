---
title: "Controlar alterações com o Azure Log Analytics | Microsoft Docs"
description: "A solução Controle de Alterações no Log Analytics ajuda a identificar alterações no software e nos Serviços do Windows que ocorrem no ambiente."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: becb179da6bc6b6df629a07d3ddb5d50edbaa577
ms.lasthandoff: 03/14/2017


---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar alterações de software no ambiente com a solução Controle de Alterações

Este artigo ajuda você a usar a solução de Controle de Alterações em Log Analytics para identificar facilmente as alterações em seu ambiente. A solução rastreia as alterações no software Windows e Linux, nos arquivos Windows, nos serviços Windows e nos daemons do Linux. Identificar as alterações de configuração pode ajudá-lo a detectar problemas operacionais.

Instale a solução para atualizar o tipo de agente já instalado. Alterações em software e serviços do Windows instalados e daemons do Linux em servidores monitorados são lidas e, em seguida, os dados são enviados para o serviço do Log Analytics na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

* Você deve ter um agente do [Windows](log-analytics-windows-agents.md), do [Operations Manager](log-analytics-om-agents.md) ou do [Linux](log-analytics-linux-agents.md) em cada computador no qual deseja controlar as alterações.
* Adicione a solução de Controle de Alterações do seu espaço de trabalho do OMS do [marketplace do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  Não é necessária nenhuma configuração.

### <a name="configure-windows-files-to-track"></a>Configurar os arquivos do Windows para controlar
Use as etapas a seguir para configurar os arquivos para controle em computadores Windows.

1. No portal do OMS, clique em **Configurações** (símbolo de engrenagem).
2. Na página **Configurações**, clique em **Dados** e, em seguida, clique em **Rastreamento de Arquivos do Windows**.
3. Em Controle de Alterações do Arquivo do Windows, digite o caminho completo, incluindo o nome do arquivo do arquivo que você deseja rastrear e, em seguida, clique no símbolo **Adicionar**. Por exemplo: C:\Program Files (x86)\Internet Explorer\iexplore.exe ou C:\Windows\System32\drivers\etc\hosts.
4. Clique em **Salvar**.  
   ![Controle de Alterações de Arquivo do Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="limitations"></a>Limitações
A solução de Controle de Alterações atualmente não suporta o seguinte:

* pastas (diretórios)
* recursão
* caracteres curinga
* variáveis de caminho
* sistemas de arquivos de rede

Outras limitações:

* Os valores e a coluna **Tamanho máximo de arquivo** não são utilizados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho da solução poderá ser prejudicado.
* Quando o tráfego de rede for alto, registros de alteração poderão levar até um máximo de seis horas para exibir.
* Se você modificar a configuração enquanto um computador for desligado, o computador poderá lançar as alterações de arquivo que pertenciam à configuração anterior.

## <a name="change-tracking-data-collection-details"></a>Detalhes de coleta de dados do Controle de Alterações
O Controle de Alterações coletará inventário de software e metadados do serviço Windows usando os agentes que você tiver habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Controle de Alterações.

| plataforma | Agente direto | Agente SCOM | Agente do Linux | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows e Linux |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) |![Não](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Não](./media/log-analytics-change-tracking/oms-bullet-red.png) |![Sim](./media/log-analytics-change-tracking/oms-bullet-green.png) | 5 minutos para 50 minutos, dependendo do tipo de alteração. Consulte abaixo para obter mais informações. |


A tabela a seguir mostra a frequência da coleta de dados para os tipos de alterações.

| **change type** | **frequency** | **O** **agente** **envia as diferenças quando encontradas?** |
| --- | --- | --- |
| Registro do Windows | 50 minutos | não |
| Arquivo do Windows | 30 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Arquivo Linux | 15 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Serviços do Windows | 30 minutos | Sim, quando as alterações são encontradas a cada 30 minutos. A cada 24 horas um instantâneo é enviado, independentemente da mudança. Portanto, o instantâneo é enviado até mesmo quando não há nenhuma alteração. |
| Daemons Linux | 5 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |
| Software do Windows | 30 minutos | Sim, quando as alterações são encontradas a cada 30 minutos. A cada 24 horas um instantâneo é enviado, independentemente da mudança. Portanto, o instantâneo é enviado até mesmo quando não há nenhuma alteração. |
| Software Linux | 5 minutos | Sim. Se não houver nenhuma alteração em até 24 horas, um instantâneo é enviado. |


## <a name="use-change-tracking"></a>Use o Controle de Alterações
Após instalar a solução, você pode exibir o resumo das alterações nos servidores monitorados usando o bloco **Controle de Alterações** na página **Visão geral** do OMS.

![imagem de bloco Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-tile.png)

Você pode exibir as alterações à sua infraestrutura e então analisar em detalhes para as seguintes categorias:

* Alterações por tipo de configuração para serviços de software e do Windows
* Alterações de software para aplicativos e atualizações para servidores individuais
* Número total de alterações de software para cada aplicativo
* Pacotes do Linux
* Alterações no serviço Windows para servidores individuais
* Alterações de daemon do Linux

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![imagem do painel Controle de Alterações](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para exibir as alterações para qualquer tipo de alteração
1. Na página **Visão Geral**, clique no bloco **Controle de Alterações**.
2. No painel **Controle de Alterações**, examine as informações resumidas em uma das folhas de tipo de alteração e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página de **pesquisa de log**.
3. Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados do controle de alterações.

