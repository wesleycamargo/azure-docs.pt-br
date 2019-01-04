---
title: Perguntas frequentes sobre o Azure Monitor para VMs (Versão prévia) | Microsoft Docs
description: Azure Monitor para VMs, uma solução do Azure que combina monitoramento da integridade e de desempenho do sistema operacional da VM do Azure. Descobre automaticamente os componentes de aplicativos e as dependências com outros recursos e mapeia a comunicação entre eles. Este artigo apresenta as respostas das perguntas frequentes.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184382"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Azure Monitor para VMs (versão prévia) Perguntas Frequentes
Este artigo responde perguntas frequentes sobre o Azure Monitor para VMs. Caso tenha outras dúvidas sobre a solução, acesse o [fórum de discussão do Azure](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Posso implantar VMs a um workspace existente?
Se as máquinas virtuais já estiverem conectadas a um workspace do Log Analytics, você poderá continuar usando esse workspace durante a integração com o Azure Monitor para VMs. O espaço de trabalho deve existir em uma das regiões com suporte listados na seção "Pré-requisitos" de [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md#prerequisites).

Durante a implantação, podemos configurar contadores de desempenho para o workspace. Essa ação faz com que as VMs que relatam dados ao workspace comecem a coletar as informações de exibição e análise no Azure Monitor para VMs. Como resultado, você verá os dados de desempenho de todas as VMs conectadas ao workspace selecionado. Os recursos Integridade e Mapa estão habilitados somente para as VMs especificadas para implantação.

Para obter mais informações sobre o desempenho do quais contadores estão habilitados, consulte [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Posso implantar VMs em workspace? 
Se, atualmente, as VMs não estiverem conectadas a um workspace existente do Log Analytics, você precisará criar um workspace para armazenar os dados. Você pode criar um automaticamente configurando uma única VM para o Azure Monitor para VMs no portal do Azure.

Se você optar por usar o método baseado em script, consulte [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que fazer se minha VM já estiver subordinada a um workspace existente?
Caso você já esteja coletando dados de suas máquinas virtuais, talvez você já tenha configurado uma delas para relatar os dados para um workspace existente do Log Analytics. Desde que esse workspace esteja em uma de nossas regiões compatíveis, você poderá habilitar o Azure Monitor para VMs nesse workspace pré-existente. Estamos trabalhando ativamente para dar suporte a outras regiões.

>[!NOTE]
>Configuramos contadores de desempenho para o workspace que afeta todas as VMs subordinadas ao workspace, caso você tenha optado ou não por implantá-las ao Azure Monitor para VMs. Para obter mais informações sobre como os contadores de desempenho são configurados para o espaço de trabalho, consulte a seção "Configuração de contadores de desempenho" de [fontes de dados de desempenho Windows e Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Para obter informações sobre os contadores configurados para o Azure Monitor para VMs, confira [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Por que minha implantação de VM falhou?
Ao implantar uma VM do Azure do portal do Azure, os seguintes eventos ocorrem:

* Um workspace padrão do Log Analytics é criado, caso essa opção tenha sido selecionada.
* Os contadores de desempenho são configurados para o workspace selecionado. Se essa etapa falhar, alguns dos quadros e tabelas de desempenho não exibem os dados para a VM que você implantou. Você pode corrigir esse problema, executando o script do PowerShell que está documentado na seção "Habilitar com o PowerShell" de [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md#enable-with-powershell).
* O agente do Log Analytics será instalado nas VMs do Azure usando uma extensão de VM, se isso for necessário. 
* O Dependency Agent do Mapa do Azure Monitor para VMs será instalado nas VMs do Azure usando com uma extensão, se isso for necessário. 
* Os componentes do Azure Monitor que dão suporte ao recurso Integridade são configurados, se necessário, e a VM é configurada para relatar dados de integridade.

Durante a implantação, podemos verificar o status para cada uma das etapas anteriores e retornar um status de notificação para você no portal. A configuração do workspace e a instalação do agente normalmente levam de 5 a 10 minutos. A exibição dos dados de monitoramento e integridade no portal do Azure leva 5 a 10 minutos adicionais. 

Se você tiver iniciado a implantação e vir mensagens indicando que a VM precisa ser integrada, permita uma margem de até 30 minutos para que a VM conclua o processo. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo alguns dados ou nenhum dado nos gráficos de desempenho de minha VM
Se os dados de desempenho não forem exibidos na tabela de disco ou os gráficos de desempenho, seus contadores de desempenho não podem ser configurados no workspace. Para corrigir esse problema, executando o script do PowerShell que está documentado na seção "Habilitar com o PowerShell" de [Implantar o Azure Monitor para VMs (versão prévia)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Qual a diferença entre o recurso Mapa do Azure Monitor para VMs e o Mapa do Serviço?
O recurso Mapa do Azure Monitor para VMs baseia-se no Mapa do Serviço, mas tem as seguintes diferenças:

* Você pode acessar a exibição do mapa do painel de VM e do Azure Monitor para VMs no Azure Monitor.
* As conexões no Mapa agora são clicáveis e mostram uma exibição dos dados de métrica de conexão no painel lateral.
* Há uma nova API que é usada para criar os mapas, a fim de dar um melhor suporte a mapas mais complexos.
* VMs monitoradas estão agora no nó de grupo do cliente e de gráfico de rosca exibe a proporção de máquinas virtuais monitoradas e não monitoradas. Você também pode filtrar a lista de computadores quando o grupo é expandido.
* Máquinas virtuais monitoradas agora estão em nós de grupo da porta do servidor e o gráfico de rosca exibe a proporção de computadores monitorados para não monitorados. Você também pode filtrar a lista de computadores quando o grupo é expandido.
* O tipo de mapa foi atualizado para ser mais consistente com o Mapa de Aplicativo do Azure Application Insights.
* Os painéis laterais foram atualizados, mas ainda não há o conjunto completo de integrações que tinham suporte no Mapa do Serviço: Gerenciamento de Atualizações, Controle de Alterações, Segurança e Suporte Técnico de Serviço. 
* A opção para escolher grupos e máquinas de mapeamento foi atualizada. Ela agora dá suporte a Assinaturas, Grupos de Recursos, Conjuntos de Dimensionamento de Máquina Virtual do Azure e serviços de nuvem.
* Não é possível criar grupos de computadores do Mapa do Serviço no recurso Mapa do Azure Monitor para VMs. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Por que meus gráficos de desempenho mostram linhas pontilhadas?

Gráficos de desempenho exibem linhas pontilhadas em vez de linhas sólidas por alguns motivos:
* Pode haver uma lacuna na coleta de dados. 

* A configuração padrão para amostragem de dados se dá a cada 60 segundos. Se você escolher um intervalo de tempo específico para o gráfico e sua frequência de amostragem for menor que o tamanho do bucket usado no gráfico, será possível ver as linhas pontilhadas. Digamos que você escolheu uma frequência de amostragem de 10 minutos e cada bucket no gráfico é de 5 minutos. Neste caso, A escolha de um intervalo de tempo maior para a exibição deverá fazer com que as linhas do gráfico apareçam como linhas sólidas em vez de pontos.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Os grupos são compatíveis com o Azure Monitor para VMs?
Sim, depois de instalar o agente de Dependência, coletamos informações das VMs para exibir grupos com base em assinaturas, grupos de recursos, conjuntos de dimensionamento de máquinas virtuais e serviços de nuvem. Se você estiver usando o Mapa do Serviço e tiver criado grupos de máquinas, eles também serão exibidos. Os grupos de computadores também aparecerão no filtro de grupos se você os criou para o espaço de trabalho que está visualizando. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como fazer exibir os detalhes para o que está orientando a linha do 95º percentil nos gráficos de desempenho de agregação?
Por padrão, a lista é classificada para mostrar a você as VMs que têm o valor mais alto para o 95 º percentil para a métrica selecionada. Uma exceção é o gráfico de **Memória disponível**, que mostra as máquinas com o menor valor do quinto percentil. Selecione o gráfico para abrir a **Lista N Principais** será aberta com a métrica apropriada selecionada.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Como o recurso Mapa manipula os IPs duplicados entre várias redes virtuais e sub-redes?
Se você estiver duplicando intervalos de IP com VMs ou conjuntos de dimensionamento de máquinas virtuais do Azure em sub-redes e redes virtuais, isso poderá fazer com que o recurso do Azure Monitor para VMs exiba informações incorretas. Estamos cientes desse problema e estamos investigando as opções para melhorar a experiência.

## <a name="does-the-map-feature-support-ipv6"></a>O recurso Mapa dá suporte ao IPv6?
Atualmente, o recurso Mapa dá suporte apenas ao IPv4 e estamos investigando suporte o suporte para IPv6. Também damos suporte ao IPv4 por túnel dentro do IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>Ao carregar um mapa para um grupo de recursos ou outro grupo grande, por que mapa fica difícil de ser exibido?
Embora melhoramos o recurso de mapa para lidar com as configurações grandes e complexas, percebemos que um mapa pode ter muitos nós, conexões e nós de trabalho como um cluster. Temos o compromisso de continuar aprimorando o suporte para aumentar a escalabilidade.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Por que o gráfico de rede na guia Desempenho tem uma aparência diferente do gráfico de rede na página Visão Geral da VM do Azure?

A página de visão geral de uma VM do Azure exibe gráficos com base na medida do host da atividade na VM convidada. Para o gráfico de rede na Visão Geral da VM do Azure, exibe apenas o tráfego de rede que será cobrado. Essa exibição não inclui o tráfego entre redes virtuais. Os dados e os gráficos mostrados para o Azure Monitor para VMs se baseiam nos dados da VM convidada e o gráfico de rede exibe todos os TCP/IP de tráfego que tem de entrada e saída para essa VM, incluindo o tráfego entre redes virtuais.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Quais são as limitações do plano gratuito do Log Analytics?
Se você configurou o Azure Monitor com um espaço de trabalho do Log Analytics usando o tipo de preço *Gratuito*, o recurso Mapa do Azure Monitor para VMs dará suporte a apenas cinco máquinas ao workspace. 

Por exemplo, digamos que você tenha cinco VMs conectadas a um workspace gratuito. Se você desconectar uma máquina virtual e, em seguida, conecta mais tarde uma nova, a nova VM não é monitorada e refletida na página Mapa. Nesse cenário, quando você abre a nova VM, será solicitado a usar a opção **Experimentar Agora** e selecionar **Insights (visualização)** no painel à esquerda, mesmo depois que tiver sido instalado na VM. No entanto, você não será solicitado com opções, como você normalmente seria se a VM não foi implantada no Azure Monitor para VMs. 

## <a name="next-steps"></a>Próximas etapas
Para entender os requisitos e os métodos para habilitar o monitoramento das máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-onboard.md) (versão prévia).
