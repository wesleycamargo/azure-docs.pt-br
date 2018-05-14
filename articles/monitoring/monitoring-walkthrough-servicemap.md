---
title: Demonstração individualizada da solução Mapa do Serviço no Azure | Microsoft Docs
description: O Mapa do Serviço é uma solução no Azure que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux, e mapeia a comunicação entre os serviços. Esta é uma demonstração individualizada que orienta no uso do Mapa do Serviço para identificar e diagnosticar um problema simulado em um aplicativo Web.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>Demonstração individualizada - Mapa do Serviço
Esta é uma demonstração individualizada que orienta no uso da [solução Mapa do Serviço](monitoring-service-map.md) no Azure para identificar e diagnosticar um problema simulado em um aplicativo Web. O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Isso também consolida os dados coletados por outros serviços e soluções a fim de ajudar a analisar o desempenho e identificar os problemas. Você também usa as [pesquisas de log no Log Analytics](../log-analytics/log-analytics-log-searches.md) para fazer uma busca detalhada dos dados coletados para identificar a causa do problema.


## <a name="scenario-description"></a>Descrição do cenário
Você acabou de receber uma notificação de que o aplicativo do Portal do Cliente ACME está tendo problemas de desempenho. As únicas informações que você tem é que esses problemas iniciaram aproximadamente às 4:00 PST da manhã de hoje. Você não está totalmente certo sobre todos os componentes dos quais o portal depende, exceto um conjunto de servidores Web. 

## <a name="components-and-features-used"></a>Componentes e recursos usados
- [Solução do Mapa de Serviço](monitoring-service-map.md)
- [Pesquisas de log do Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>Passo a passo

### <a name="1-connect-to-the-oms-experience-center"></a>1. Conecte-se à Central de Experiência OMS
Este passo a passo usa a [Central de Experiência do Operations Management Suite](https://experience.mms.microsoft.com/) que fornece um ambiente do Log Analytics completo com dados de exemplo. Comece seguindo esse link, forneça suas informações, em seguida, selecione o cenário **Insight e Analytics**.


### <a name="2-start-service-map"></a>2. Iniciar o Mapa de Serviço
Inicie a solução do Mapa de Serviço clicando no bloco **Mapa de Serviço**.

![Bloco Mapa de Serviço](media/monitoring-walkthrough-servicemap/tile.png)

O console do Mapa de Serviço é exibido. No painel esquerdo, está uma lista de computadores em seu ambiente com o agente do Mapa de Serviço instalado. Você seleciona o computador que deseja exibir na lista.

![Lista de computadores](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Exibir computador
Você sabe que os servidores Web são chamados de AcmeWFE001 e AcmeWFE002, portanto, este parece ser um lugar razoável para começar. Clique em **AcmeWFE001**. Isso exibe o mapa para AcmeWFE001 e todas as suas dependências. Você pode ver quais processos estão em execução no computador selecionado e com quais serviços externos eles se comunicam.

![Servidor Web](media/monitoring-walkthrough-servicemap/web-server.png)

Você está preocupado com o desempenho do aplicativo Web, então, clique no processo **AcmeAppPool (Pool de Aplicativos do IIS)**. Isto exibe os detalhes do processo e destaca suas dependências. 

![Pool de Aplicativos](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Alterar janela do tempo

Você soube que o problema começou às 4:00 da manhã, portanto, veremos o que estava acontecendo no momento. Clique em **Intervalo de Tempo** e altere a hora para 4:00 AM PST (mantenha a data atual e ajuste seu fuso horário local) com uma duração de 20 minutos.

![Seletor de Tempo](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Exibir alerta

Você agora vê que a dependência **acmetomcat** tem um alerta exibido, portanto, esse é o problema em potencial. Clique no ícone de alerta em **acmetomcat** para mostrar seus detalhes. Você pode ver que a utilização da CPU está crítica e pode expandi-la para obter mais detalhes. Provavelmente é isso que está causando o desempenho lento. 

![Alerta](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Exibir desempenho

Vamos examinar o **acmetomcat** com mais atenção. Clique na parte direita superior do **acmetomcat** e selecione **Carregar Mapa do Servidor** para mostrar os detalhes e as dependências do computador. Você pode examinar um pouco mais esses contadores de desempenho para verificar sua suspeita. Selecione a guia **Desempenho** para exibir os [contadores de desempenho coletados pelo Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) no intervalo de tempo. Você pode ver que está tendo picos periódicos no processador e na memória.

![Desempenho](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Exibir controle de alterações
Vejamos se conseguimos descobrir o que pode ter causado essa utilização elevada. Clique na guia **Resumo**. Isso fornece as informações que o Log Analytics coletou do computador, como conexões com falha, alertas críticos e alterações do software. As seções com informações recentes relevantes já devem estar expandidas e você poderá expandir outras seções para inspecionar as informações que elas contêm.


Se o **Controle de Alterações** ainda não estiver aberto, expanda-o. Isso mostra as informações coletadas pela [solução do Controle de Alterações](../log-analytics/log-analytics-change-tracking.md). Parece que houve uma alteração do software feita durante a janela do tempo. Clique em **Software** para obter detalhes. Um processo de backup foi adicionado à máquina logo após as 4:00 manhã e isso parece ser o responsável pelo consumo excessivo de recursos.

![Controle de alterações](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Exibir detalhes na Pesquisa de Logs
Você pode verificar mais examinando as informações detalhadas do desempenho coletadas no espaço de trabalho do Log Analytics. Clique novamente na guia **Alertas** e, em seguida, clique em um dos alertas de **CPU Alta**. Clique em **Mostrar na Pesquisa de Log**. Isso abre a janela Pesquisa de Logs, na qual você pode executar as [pesquisas de log](../log-analytics/log-analytics-log-searches.md) em todos os dados armazenados no espaço de trabalho. O Mapa do Serviço já preencheu uma consulta para recuperar o alerta no qual estamos interessados. 

![Pesquisa de log](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Abrir pesquisa salva
Vejamos se podemos obter mais detalhes sobre a coleta do desempenho que gerou esse alerta e verificar sua suspeita de que os problemas são causados por esse processo de backup. Altere o intervalo de tempo para **6 horas**. Em seguida, clique em **Favoritos** e role até as pesquisas salvas para o **Mapa de Serviço**. Você criou essas consultas especificamente para essa análise. Clique em **5 Primeiros Processos por CPU para acmetomcat**.

![Pesquisa salva](./media/monitoring-walkthrough-servicemap/saved-search.png)


Esta consulta retorna uma lista dos cinco primeiros processos que consomem o processador no **acmetomcat**. Você pode inspecionar a consulta para obter uma introdução à linguagem de consulta usada para as pesquisas de log. Se você estiver interessado nos processos em outros computadores, poderá modificar a consulta para recuperar essas informações.

Neste caso, você pode ver que o processo de backup está consumindo consistentemente cerca de 60% da CPU do servidor de aplicativo. É óbvio que esse novo processo é responsável por seu problema de desempenho. Obviamente, a solução seria remover o novo software de backup do servidor de aplicativo. Na verdade, você poderia utilizar a DSC (Configuração de Estado Desejado) gerenciada pela Automação do Azure para definir as políticas que garantem que esse processo nunca seja executado em sistemas críticos.


## <a name="summary-points"></a>Pontos do resumo
- O[Mapa de Serviço](monitoring-service-map.md) fornece uma exibição de todo o aplicativo, mesmo que você não conheça todos os servidores e dependências.
- O Mapa do Serviço expõe os dados coletados por outras soluções de gerenciamento para ajudar a identificar problemas com seu aplicativo e a infraestrutura subjacente.
- As [Pesquisas de Log](../log-analytics/log-analytics-log-searches.md) permitem fazer uma busca detalhada nos dados específicos coletados no espaço de trabalho do Log Analytics.  

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [Mapa de Serviço](monitoring-service-map.md).
- [Implante e configure o](monitoring-service-map-configure.md) Mapa de Serviço.
- Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md) que é necessário para o Mapa do Serviço e armazena os dados operacionais guardados por agentes.