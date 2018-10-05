---
title: Perguntas frequentes sobre o Azure Monitor para VMs | Microsoft Docs
description: O Azure Monitor para VMs é uma solução do Azure que combina integridade e monitoramento de desempenho do sistema operacional da VM do Azure, bem como a descoberta automática dos componentes de aplicativo e dependências com outros recursos e mapeia a comunicação entre eles. Este artigo responde a perguntas comuns.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2018
ms.author: magoedte
ms.openlocfilehash: 308a447ff99cd11ad6a28df0bdb515764b0f546b
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063448"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Monitor para VMs
Essas perguntas frequentes da Microsoft são uma lista de perguntas frequentes sobre o Azure Monitor para VMs no Microsoft Azure. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="can-i-onboard-to-an-existing-workspace"></a>É possível fazer a integração com um workspace existente?
Se as máquinas virtuais já estiverem conectadas a um workspace do Log Analytics, você poderá continuar usando esse workspace durante a integração com o Azure Monitor para VMs, desde que ele esteja em uma das regiões compatíveis listadas [aqui](monitoring-vminsights-onboard.md#prerequisites).

Durante a integração, configuramos contadores de desempenho para o workspace, o que fará com que todas as VMs que relatam os dados para o workspace iniciem a coleta dessas informações para exibição e análise no Azure Monitor para VMs.  Como resultado, você verá os dados de desempenho de todas as VMs conectadas ao workspace selecionado.  Os recursos Integridade e Mapa estão habilitados somente para as VMs especificadas para integração.

Para obter mais informações sobre quais contadores de desempenho estão habilitados, consulte nosso artigo sobre [integração](monitoring-vminsights-onboard.md).

## <a name="can-i-onboard-to-a-new-workspace"></a>É possível fazer a integração com um novo workspace? 
Se, atualmente, as VMs não estiverem conectadas a um workspace existente do Log Analytics, você precisará criar um workspace para armazenar os dados.  A criação de um workspace padrão será feita automaticamente se você configurar uma VM individual do Azure para o Azure Monitor para VMs por meio do portal do Azure.

Caso você opte por usar o método baseado em script, essas etapas serão abordadas no artigo sobre [integração](monitoring-vminsights-onboard.md). 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que fazer se minha VM já estiver subordinada a um workspace existente?
Caso você já esteja coletando dados de suas máquinas virtuais, talvez você já tenha configurado uma delas para relatar os dados para um workspace existente do Log Analytics.  Desde que esse workspace esteja em uma de nossas regiões compatíveis, você poderá habilitar o Azure Monitor para VMs nesse workspace pré-existente.  Se o workspace que você já está usando não estiver em uma de nossas regiões compatíveis, você não conseguirá fazer a integração com o Azure Monitor para VMs no momento.  Estamos trabalhando ativamente para dar suporte a outras regiões.

>[!NOTE]
>Configuramos contadores de desempenho para o workspace que afeta todas as VMs subordinadas ao workspace, caso você tenha optado ou não por integrá-las ao Azure Monitor para VMs. Para obter mais detalhes sobre como os contadores de desempenho são configurados para o workspace, consulte nossa [documentação](../log-analytics/log-analytics-data-sources-performance-counters.md). Para obter informações sobre os contadores configurados para o Azure Monitor para VMs, confira nossa [documentação sobre integração](monitoring-vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Por que minha VM não pôde ser integrada?
Ao fazer a integração de uma VM do Azure por meio do portal do Azure, ocorrem as seguintes etapas:

* Um workspace padrão do Log Analytics é criado, caso essa opção tenha sido selecionada.
* Os contadores de desempenho são configurados para o workspace selecionado. Se essa etapa falhar, você observará que algumas das tabelas e alguns dos gráficos de desempenho não mostrarão os dados da VM integrada. Corrija isso executando o script do PowerShell documentado [aqui](monitoring-vminsights-onboard.md#enable-with-powershell).
* O agente do Log Analytics será instalado nas VMs do Azure usando uma extensão de VM, se for determinado que isso é necessário.  
* O Dependency Agent do Mapa do Azure Monitor para VMs será instalado nas VMs do Azure usando uma extensão, se for determinado que isso é necessário.  
* Os componentes do Azure Monitor que dão suporte ao recurso Integridade são configurados, se necessário, e a VM é configurada para relatar dados de integridade.

Durante o processo de integração, verificamos o status de cada um dos itens acima para retornar um status de notificação a você no portal.  A configuração do workspace e a instalação do agente normalmente levam de 5 a 10 minutos.  A exibição dos dados de monitoramento e integridade no portal leva 5 a 10 minutos adicionais.  

Se você tiver iniciado a integração e vir mensagens indicando que a VM precisa ser integrada, permita uma margem de até 30 minutos para que a VM conclua o processo. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo alguns dados ou nenhum dado nos gráficos de desempenho de minha VM
Se você não vir dados de desempenho na tabela de disco ou em alguns dos gráficos de desempenho, os contadores de desempenho poderão não estar configurados no workspace. Para resolver isso, execute o [script do PowerShell](monitoring-vminsights-onboard.md#enable-with-powershell) a seguir.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Qual a diferença entre o recurso Mapa do Azure Monitor para VMs e o Mapa do Serviço?
O recurso Mapa do Azure Monitor para VMs baseia-se no Mapa do Serviço, mas tem as seguintes diferenças:

* A exibição de Mapa pode ser acessada por meio da folha da VM e do Azure Monitor para VMs no Azure Monitor.
* As conexões no Mapa agora são clicáveis e mostram uma exibição dos dados de métrica de conexão no painel lateral da conexão selecionada.
* Há uma nova API que é usada para criar os mapas, a fim de dar um melhor suporte a mapas mais complexos.
* As VMs monitoradas agora estão incluídas no nó do grupo de clientes e o gráfico de rosca mostra a proporção de máquinas virtuais monitoradas vs. não monitoradas no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* As máquinas virtuais monitoradas agora estão incluídas nos nós do grupo de portas do servidor e o gráfico de rosca mostra a proporção de computadores monitorados vs. não monitorados no grupo.  Ele também pode ser usado para filtrar a lista de computadores quando o grupo é expandido.
* O estilo de mapa foi atualizado para ser mais consistente com o Mapa do Aplicativo do Application Insights.
* Os painéis laterais foram atualizados, mas ainda não têm o conjunto completo de integrações que eram compatíveis com o Mapa do Serviço – Gerenciamento de Atualizações, Controle de Alterações, Segurança e Central de Serviços. 
* A opção para escolher grupos e computadores a serem mapeados foi atualizada e agora dá suporte a Assinaturas, Grupos de Recursos, conjuntos de dimensionamento de máquinas virtuais do Azure e Serviços de nuvem.
* Não é possível criar grupos de computadores do Mapa do Serviço no recurso Mapa do Azure Monitor para VMs.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que meus gráficos de desempenho mostram linhas pontilhadas?

Isso pode ocorrer por alguns motivos.  Nos casos em que há uma lacuna na coleta de dados, as linhas são mostradas como pontilhadas.  Se você tiver modificado a frequência de amostragem de dados dos contadores de desempenho habilitados (a configuração padrão é coletar dados a cada 60 segundos), você poderá ver linhas pontilhadas no gráfico caso tenha escolhido um intervalo de tempo restrito para o gráfico e a frequência de amostragem for menor que o tamanho do bucket usado no gráfico (por exemplo, a frequência de amostragem é de intervalos de 10 minutos e cada bucket no gráfico é de 5 minutos).  A escolha de um intervalo de tempo maior para a exibição deverá fazer com que as linhas do gráfico apareçam como linhas sólidas em vez de pontos, neste caso.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Os grupos são compatíveis com o Azure Monitor para VMs?
O recurso Desempenho dá suporte a grupos com base nos recursos realçados em um workspace específico, bem como o agrupamento com base em um conjunto de dimensionamento de máquinas virtuais do Azure e um Serviço de nuvem específicos.

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como fazer para ver os detalhes para o que está orientando a linha do 95º percentil nos gráficos de desempenho de agregação?
Por padrão, a lista é classificada para mostrar as VMs que têm o valor mais alto para o 95º percentil na métrica selecionada, exceto pelo gráfico de Memória disponível, que mostra os computadores com o valor mais baixo do 5º percentil.  Ao clicar no gráfico, a exibição **Lista N Principais** será aberta com a métrica apropriada selecionada.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como o recurso Mapa manipula os IPs duplicados entre diferentes VNETs e sub-redes?
Se você estiver duplicando intervalos de IP com VMs ou conjuntos de dimensionamento de máquinas virtuais do Azure em sub-redes e VNETs, isso poderá fazer com que o Mapa do Azure Monitor para VMs exiba informações incorretas. Esse é um problema conhecido e estamos investigando opções para melhorar essa experiência.

## <a name="does-map-feature-support-ipv6"></a>O recurso Mapa dá suporte ao IPv6?
Atualmente, o recurso Mapa dá suporte apenas ao IPv4. Estamos considerando o suporte para IPv6. Também damos suporte ao IPv4 por túnel dentro do IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Ao carregar um mapa para um Grupo de Recursos ou outro grupo grande, o mapa fica difícil de ser exibido
Embora tenhamos feito melhorias no Mapa para lidar com configurações grandes e complexas, percebemos que um mapa pode ter uma variedade de nós, conexões e um nó funcionando como um cluster.  Temos o compromisso de continuar aprimorando o suporte para aumentar a escalabilidade.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia Desempenho tem uma aparência diferente do gráfico de rede na página Visão Geral da VM do Azure?

A página de visão geral de uma VM do Azure exibe gráficos com base na medida do host da atividade na VM convidada.  Para o gráfico de rede na Visão Geral da VM do Azure, ela exibe apenas o tráfego de rede que será cobrado.  Isso não inclui o tráfego entre VNETs.  Os dados e os gráficos mostrados para o Azure Monitor para VMs se baseiam nos dados da VM convidada e o gráfico de rede exibe todo o tráfego de TCP/IP de entrada e saída nessa VM, incluindo entre VNETs.

## <a name="next-steps"></a>Próximas etapas
Examine [Integrar o Azure Monitor para VMs](monitoring-vminsights-onboard.md) para entender os requisitos e os métodos usados para habilitar o monitoramento das máquinas virtuais.
