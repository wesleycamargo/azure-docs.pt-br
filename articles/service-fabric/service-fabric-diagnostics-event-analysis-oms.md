---
title: "Análise de Eventos do Service Fabric do Azure com o OMS | Microsoft Docs"
description: "Saiba mais sobre visualização e análise de eventos utilizando o OMS para o monitoramento e diagnóstico de clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Análise de eventos e visualização com OMS

O OMS (Operations Management Suite) é uma coleção de serviços de gerenciamento que ajudam no monitoramento e diagnóstico para aplicativos e serviços hospedados na nuvem. Para obter uma visão mais detalhada do OMS e o que ele oferece, leia [Novidades do OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics e o espaço de trabalho de OMS

O Log Analytics coleta dados de recursos gerenciados, incluindo uma tabela de armazenamento do Azure ou um agente, e os mantém em um repositório central. Os dados podem ser usado para análise, alertas e visualização ou para mais exportação. O Log Analytics dá suporte a eventos, dados de desempenho ou outros dados personalizados.

Quando o OMS for configurado, você terá acesso a um *espaço de trabalho de OMS* específico, de onde os dados poderão ser consultados ou visualizados em painéis.

Depois que dados são recebidos pelo Log Analytics, o OMS tem várias *Soluções de Gerenciamento*, que são soluções pré-empacotados para monitorar dados de entrada, personalizados para vários cenários. Isso inclui uma solução de *Análise do Service Fabric* e uma solução de *Contêineres*, que são as duas mais relevantes para diagnóstico e monitoramento ao usar clusters do Service Fabric. Também há várias outras que vale a pena explorar, e o OMS também permite a criação de soluções personalizadas, sobre as quais você pode ler mais [aqui](../operations-management-suite/operations-management-suite-solutions.md). Cada solução que você optar por usar para um cluster pode ser configurada no mesmo espaço de trabalho de OMS, juntamente com o Log Analytics. Os espaços de trabalho permitem painéis personalizados e visualização de dados e modificações nos dados que você deseja coletar, processar e analisar.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configuração de um espaço de trabalho de OMS com a Solução de Análise do Service Fabric
É recomendável que você inclua a Solução do Service Fabric no espaço de trabalho do OMS - ela inclui um painel que mostra os vários canais de log de entrada no nível de plataforma e de aplicativo e fornece a capacidade de consultar logs específicos do Service Fabric. Aqui está a aparência de uma solução do Service Fabric relativamente simples, com um único aplicativo implantado no cluster:

![Solução de OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Há duas maneiras de provisionar e configurar um espaço de trabalho do OMS, por meio de um modelo do Resource Manager ou diretamente no Azure Marketplace. Use o primeiro item quando você estiver implantando um cluster e o segundo se você já tiver um cluster implantado com o diagnóstico habilitado.

### <a name="deploying-oms-using-a-resource-management-template"></a>Implantação do OMS usando um modelo de Gerenciamento de Recursos

Ao implantar um cluster usando um modelo do Resource Manager, o modelo também pode criar um novo espaço de trabalho de OMS, adicionar a Solução do Service Fabric a ele e configurá-lo para ler dados das tabelas de armazenamento apropriadas.

>[!NOTE]
>Para que isso funcione, o diagnóstico precisa ser habilitado para que as tabelas de armazenamento do Azure existam para o OMS/Log Analytics ler informações nelas.

[Aqui](https://azure.microsoft.com/resources/templates/service-fabric-oms/) está um modelo de exemplo que você pode usar e modificar conforme a necessidade, que executa as ações acima. Caso você deseje mais opções, há mais alguns modelos que oferecem opções diferentes, dependendo da parte do processo em que você configura um espaço de trabalho do OMS. Eles podem ser encontrados em [Modelos do Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Implantação com o OMS por meio do Azure Marketplace

Se você preferir adicionar um espaço de trabalho de OMS depois de implantar um cluster, acesse o Azure Marketplace e procure *"Análise do Service Fabric"*.

![Análise do OMS SF no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* Clique em **Criar**
* Na janela de criação de Análise do Service Fabric, clique em **Selecionar um espaço de trabalho** para o campo *Espaço de trabalho do OMS* e, em seguida, **Criar um novo espaço de trabalho**. Preencha as entradas necessárias. O único requisito é que a assinatura para o cluster do Service Fabric e o espaço de trabalho de OMS devem ser iguais. Depois que as entradas forem validadas, o espaço de trabalho do OMS começará a ser implantado. Isso deve levar apenas alguns minutos.
* Quando terminar, clique em **Criar** novamente na parte inferior da janela de criação de Análise do Service Fabric. Verifique se o novo espaço de trabalho será exibido em *Espaço de trabalho do OMS*. Isso adicionará a solução ao espaço de trabalho que você acabou de criar.


Embora isso adicione a solução ao espaço de trabalho, o espaço de trabalho ainda precisa estar conectado aos dados de diagnóstico provenientes do seu cluster. Navegue até o grupo de recursos onde você criou a solução de Análise do Service Fabric. Você deve ver *ServiceFabric (\<nameOfOMSWorkspace\>)*.

* Clique na solução para navegar até a página de visão geral, de onde você pode alterar as configurações de solução, configurações de espaço de trabalho e navegue até o portal do OMS.
* No menu de navegação à esquerda, clique em **Logs das contas de armazenamento**, em *Fontes de dados de espaço de trabalho*.

    ![Solução de Análise do Service Fabric no Portal](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* Na página *Logs das contas de armazenamento*, clique em **Adicionar** na parte superior para adicionar os logs do cluster ao espaço de trabalho.
* Clique em **Conta de armazenamento** para adicionar a conta apropriada criada no cluster. Se você usou o nome padrão, a conta de armazenamento será denominada *sfdg\<resourceGroupName\>*. Você também pode confirmar isso verificando o modelo do Azure Resource Manager usado para implantar o cluster, verificando o valor usado para o `applicationDiagnosticsStorageAccountName`. Se o nome da conta não aparecer, talvez você precise rolar para baixo e clicar em **Carregar mais**. Clique no nome da conta de armazenamento adequada quando ela for exibida para selecioná-la.
* Em seguida, você precisará especificar o *Tipo de dados*, que deve ser **Eventos do Fabric Service**.
* A *Fonte* deve ser definida automaticamente para *WADServiceFabric\*EventTable*.
* Clique em **OK** para conectar o espaço de trabalho aos seus logs do cluster.

    ![Adicionar logs de conta de armazenamento ao OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* A conta agora deve aparecer como parte dos seus *Logs de conta de armazenamento* nas fontes de dados do seu espaço de trabalho.

Com isso você acaba de adicionar a solução de Análise do Service Fabric em um espaço de trabalho do Log Analytics do OMS que agora está conectado corretamente à plataforma do cluster e à tabela de log do aplicativo. Você pode adicionar outras fontes ao espaço de trabalho dessa mesma forma.

## <a name="using-the-oms-agent"></a>Uso do Agente do OMS

É recomendado usar EventFlow e WAD como soluções de agregação, pois permitem uma abordagem mais modular para diagnósticos e monitoramento. Por exemplo, se você quiser alterar as saídas de EventFlow, não será preciso alterar a instrumentação real, apenas fazer uma modificação simples no arquivo de configuração. No entanto, se você decidir investir no uso do OMS e quiser continuar a usá-lo para análise de eventos (não precisa ser a única plataforma que você usa, mas será pelo menos uma das plataformas), recomendamos que você explore a configuração do [agente OMS](../log-analytics/log-analytics-windows-agents.md). Você também deve usar o agente do OMS ao implantar contêineres ao seu cluster, conforme discutido abaixo.

O processo para fazer isso é relativamente fácil, pois você só precisa adicionar o agente como uma extensão de conjunto de dimensionamento de máquinas virtuais para o modelo do Resource Manager, garantindo que ele seja instalado em cada um dos nós. Um modelo do Resource Manager que implanta o espaço de trabalho do OMS com a solução do Service Fabric (como acima) e adiciona o agente para os nós pode ser encontrado para clusters que executam o [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

As vantagens são as seguintes:

* Dados avançados no lado de métricas e contadores de desempenho
* Métricas fáceis de configurar coletadas do cluster e sem a necessidade de atualizar a configuração do cluster. As alterações nas configurações do agente podem ser feitas no portal do OMS e o agente é reiniciado automaticamente para corresponder à configuração necessária. Para configurar o agente do OMS para selecionar contadores de desempenho específicos, acesse o espaço de trabalho **Página Inicial > Configurações > Dados > Contadores de Desempenho do Windows** e selecione os dados que você deseja coletar
* Os dados são mostrados mais rápido do que se tiverem que ser armazenados antes de serem selecionados pelo OMS/Log Analytics
* A monitoração de contêineres é muito mais fácil, pois é possível selecionar logs de docker (stdout, stderr) e estatísticas (métricas de desempenho nos níveis de contêiner e nó)

A principal consideração é que, como ele é um agente que será implantado no cluster juntamente com todos os seus aplicativos, pode haver algum impacto sobre o desempenho dos aplicativos no cluster.

## <a name="monitoring-containers"></a>Monitoramento de contêineres

Ao implantar contêineres em um cluster do Service Fabric, é recomendável que o cluster tenha sido configurado com o agente do OMS e que a solução de contêineres tenha sido adicionada ao espaço de trabalho de OMS para habilitar o monitoramento e o diagnóstico. Aqui está a aparência de solução de contêineres em um espaço de trabalho:

![Painel do OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

O agente habilita a coleta de vários logs de contêineres específicos que podem ser consultados no OMS ou usado para indicadores de desempenho visualizados. Os tipos de log coletados são:

* ContainerInventory: mostra informações sobre imagens, nome e localização do contêiner
* ContainerImageInventory: informações sobre imagens implantadas, inclusive IDs ou tamanhos
* ContainerLog: logs de erros específicos, logs de docker (stdout etc.) e outras entradas
* ContainerServiceLog: comandos de daemon do docker que foram executados
* Perf: contadores de desempenho incluindo o CPU, memória, tráfego de rede, E/S do disco e métricas personalizadas das máquinas de host do contêiner

Este artigo aborda as etapas necessárias para configurar o monitoramento de contêiner para seu cluster. Para saber mais sobre a solução de Contêineres do OMS, consulte sua [documentação](../log-analytics/log-analytics-containers.md).

Para configurar a solução de Contêiner no espaço de trabalho, verifique se o agente do OMS foi implantado nos nós do cluster, seguindo as etapas mencionadas acima. Quando o cluster estiver pronto, implante um contêiner nele. Tenha em mente que na primeira vez que uma imagem de contêiner é implantada em um cluster, ela leva vários minutos para baixar a imagem, dependendo de seu tamanho.

No Azure Marketplace, pesquise *Solução de monitoramento de contêiner* e crie o resultado da **Solução de monitoramento de contêiner** que deverá surgir, na categoria Monitoramento + Gerenciamento.

![Adicionando a solução de Contêineres](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

Na etapa de criação, é solicitado um espaço de trabalho do OMS. Selecione o que foi criado com a implantação acima. Esta etapa adiciona uma solução de Contêineres dentro de seu espaço de trabalho do OMS e é detectada automaticamente pelo agente do OMS implantado pelo modelo. O agente começará a coletar dados sobre os processos de contêineres no cluster e, em menos de cerca de 15 minutos, você deverá ver a solução se iluminar com os dados como a imagem do painel acima.


## <a name="next-steps"></a>Próximas etapas

Explore as seguintes ferramentas de OMS e opções para personalizar um espaço de trabalho conforme suas necessidades:

* Para clusters locais, o OMS oferece um Gateway (Proxy de Encaminhamento HTTP) que pode ser usado para enviar dados ao OMS. Leia mais sobre isso em [Conectar computadores sem acesso à Internet ao OMS usando o Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configure o OMS para configurar [alertas automatizados](../log-analytics/log-analytics-alerts.md) para auxiliar na detecção e diagnóstico
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics