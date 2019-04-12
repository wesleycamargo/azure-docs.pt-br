---
title: Monitore a integridade da máquina virtual com o Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo descreve como você entende a integridade da máquina virtual e do sistema operacional subjacente com o Monitor do Azure para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: b1118a3add665de403e4e0f8fd8883ce0094d9dd
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490026"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Entender a integridade de suas máquinas virtuais do Azure

O Azure inclui vários serviços que individualmente executam uma função ou tarefa específica no espaço de monitoramento, mas ainda não forneceram uma perspectiva aprofundada de integridade do sistema operacional hospedado em máquinas virtuais do Azure. Enquanto você pode monitorar condições diferentes usando o Azure Monitor, ele não foi projetado para modelar e representam a integridade geral da máquina virtual ou a integridade dos componentes principais. Com o recurso de integridade do Azure Monitor for VMs, ele monitora proativamente a disponibilidade e o desempenho do sistema operacional convidado Windows ou Linux com um modelo que representa os principais componentes e seus relacionamentos, critérios que determinam como avaliar a integridade desses componentes e alertam quando um condição insalubre é detectada.  

Exibir o estado geral de integridade da VM do Azure e do sistema operacional subjacente pode ser observado de duas perspectivas com a integridade do Monitor do Azure para VMs, diretamente da máquina virtual ou em todas as VMs em um grupo de recursos do Monitor do Azure.

Este artigo ajudará você a entender como avaliar, investigar e resolver rapidamente os problemas de integridade detectados.

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](vminsights-onboard.md).

## <a name="monitoring-configuration-details"></a>Detalhes de configuração de monitoramento

Esta seção descreve os critérios de integridade padrão definidos para monitorar as máquinas virtuais do Windows e Linux do Azure. Todos os critérios de integridade são pré-configurados para o alerta quando a condição não íntegra é atendida. 

### <a name="windows-vms"></a>VMs Windows

- Megabytes Disponíveis de Memória 
- Média de Segundos de Disco por Gravação (Disco Lógico)
- Média de Segundos de Disco por Gravação (Disco)
- Média de Segundos de Disco por Gravação
- Média de segundos de disco lógico por transferência
- Média de segundos de disco por leitura
- Média de segundos de disco por transferência
- Comprimento atual da fila de disco (disco lógico)
- Comprimento atual da fila de disco (disco)
- Tempo ocioso percentual do disco
- Erro ou corrupção do sistema de arquivos
- Espaço livre em disco lógico (%) baixo
- Espaço livre em disco lógico (MB) baixo
- Porcentagem de Tempo Ocioso do Disco Lógico
- Páginas de memória por segundo
- Porcentagem de largura de banda usada
- Porcentagem de largura de banda total utilizada
- Porcentagem de largura de banda usada
- Porcentagem de memória confirmada em uso
- Tempo ocioso percentual do disco
- Integridade do serviço de cliente DHCP
- Integridade do serviço de cliente DNS
- Integridade do serviço RPC
- Integridade do serviço de servidor
- Porcentagem de utilização de CPU total
- Integridade de serviço de Log de eventos do Windows
- Integridade de serviço de Firewall do Windows
- Integridade de serviço de gerenciamento remoto do Windows

### <a name="linux-vms"></a>VMs Linux
- Disk Avg. de segundos/Transferência do Disco 
- Disk Avg. de segundos/Leitura do Disco 
- Disk Avg. de segundos/Gravação do Disco 
- Integridade do disco
- Espaço livre em disco lógico
- % de espaço livre no Disco lógico
- % de Inodes livres do disco lógico
- Saúde do adaptador de rede
- Percentual Total do Processador
- Sistema Operacional Megabytes Disponíveis de Memória

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introdução à experiência de saúde

Antes de começar a usar o recurso Funcionamento para uma única máquina virtual ou um grupo de VMs, é importante fornecer uma breve introdução para que você entenda como as informações são apresentadas e o que as visualizações representam.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Visualize a integridade diretamente de uma máquina virtual 

Para exibir a integridade de uma VM do Azure, selecione **Insights (visualização)** no painel esquerdo da máquina virtual. Na página de informações da VM, **Integridade** está aberto por padrão e mostra a visualização de integridade da VM.  

![Visão geral de integridade do Monitor do Azure para VMs de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

Na guia **Integridade**, na seção **Integridade Convidada da VM**, a tabela mostra o estado atual da integridade de sua máquina virtual e o número total de alertas de Integridade da VM gerados por um componente não íntegro. Para obter mais informações, consulte [alertas](#alerts) para obter mais detalhes sobre o alerta ocorrer.  

Os estados de integridade definidos para uma VM estão descritos na seguinte tabela: 

|ícone |Estado de integridade |Significado |
|-----|-------------|------------|
| |Healthy |O estado de integridade é íntegro se está dentro das condições de integridade definidas, que indicam ausência de problemas detectados na VM e que ela funciona conforme necessário. Com um monitor de acumulação pai, o Rollup de integridade e ele reflete o estado mais favorável, ou pior do filho.|
| |Crítico |O estado de integridade é crítico se ele não está dentro da condição de integridade definida, o que indica que um ou mais problemas críticos foram detectados e precisam ser resolvidos para restaurar a funcionalidade normal. Com um monitor de acumulação pai, o Rollup de integridade e ele reflete o estado mais favorável, ou pior do filho.|
| |Aviso |O estado de integridade será de Aviso se estiver entre dois limites para a condição de integridade definida, em que um indica um estado de *Aviso* e o outro indica um estado *Crítico* (três limites de estado de integridade podem ser configurados), ou quando um problema não crítico é detectado, podendo vir a causar problemas críticos se não for resolvido. Com rollup pai monitor, se um ou mais filhos estão em um estado de aviso, em seguida, o pai refletirá *aviso* estado. Se houver um filho que esteja em um *Crítico* e outro filho em um estado *Aviso*, o pacote pai mostrará um estado de integridade de *Crítico*.|
| |Desconhecido |O estado de integridade está em um estado *Desconhecido* quando o estado de integridade não pode ser calculado por vários motivos, como não conseguir coletar dados, serviço não inicializado, etc. Esse estado de integridade não é configurável.| 

A seleção de **Exibir diagnósticos de integridade** abre uma página mostrando todos os componentes da VM, critérios de integridade associados, alterações de estado e outros problemas significativos encontrados pelos componentes de monitoramento relacionados à VM. Para saber mais, consulte [Diagnóstico de integridade](#health-diagnostics). 

Na seção **Integridade do componente**, a tabela mostra um status cumulativo de integridade das categorias de desempenho principais monitoradas por critérios de integridade para essas áreas, especificamente **CPU**, **Memória**, **Disco** e **Rede**.  A seleção de qualquer um dos componentes abre uma página listando todos os aspectos de monitoramento de critério de integridade individuais desse componente e o respectivo estado de integridade de cada um.  

Ao acessar a integridade de uma VM do Azure executando o sistema operacional Windows, o estado de integridade da parte superior cinco principais Windows são mostrados na seção **Core services integridade**.  A seleção de qualquer um dos serviços abre uma página listando os critérios de integridade que monitoram esse componente e seu estado de integridade.  Clicar no nome dos critérios de integridade abrirá o painel de propriedades e, a partir daqui, você poderá revisar os detalhes de configuração, inclusive se os critérios de integridade tiverem um alerta do Monitor do Azure correspondente definido. Para saber mais sobre isso, consulte [Diagnóstico de Integridade e trabalhando com critérios de integridade](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Perspectiva de máquina de virtual de agregação

Para exibir a coleta de integridade de todas as suas máquinas virtuais em um grupo de recursos, na lista de navegação do portal, selecione **Monitor do Azure** e, em seguida, selecione **Máquinas Virtuais (visualização)**.  

![Exibição do Azure Monitor de monitoramento de Insights de VM](./media/vminsights-health/vminsights-aggregate-health.png)

Nas listas suspensas **Assinatura** e **Grupo de Recursos**, selecione o grupo de recursos apropriado que inclui as VMs relacionadas ao grupo, para exibir seu estado de integridade relatado.  Sua seleção só se aplica ao recurso de Integridade e não é transferido para o Desempenho ou um Mapa.

Na guia **Integridade**, você pode aprender o seguinte:

* Quantas VMs estão em um estado crítico ou não íntegro, em comparação com quantas estão em bom estado ou não estão enviando dados (referido como um estado desconhecido)?
* Quais VMs por sistema operacional (SO) estão relatando um estado não íntegro e quantas?
* Quantas VMs não são íntegras devido a um problema detectado com um processador, disco, memória ou adaptador de rede, categorizado por estado de integridade?  
* Quantas VMs não são íntegras devido a um problema detectado em um serviço central do sistema operacional, categorizado por estado de integridade?

Aqui você pode identificar rapidamente os principais problemas críticos detectados pelos critérios de integridade, monitorando proativamente a VM e analisando os detalhes do alerta de integridade da VM e o artigo de conhecimento associado com a intenção de auxiliar no diagnóstico e na correção do problema.  Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrada por tal gravidade.

A lista da **distribuição da VM por sistema operacional** mostra as VMs listadas pela edição do Windows ou pela distribuição do Linux, junto com sua versão. Em cada categoria de sistema operacional, as VMs são divididas ainda mais com base na integridade da VM. 

![Perspectiva de distribuição de máquinas virtuais do VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Você pode clicar em qualquer item da coluna - **VM count**, **Critical**, **Warning**, **Integridade** ou **Unknown** para perfurar - na página **Máquinas Virtuais**, veja uma lista de resultados filtrados que correspondem à coluna selecionada. Por exemplo, se quisermos revisar todas as VMs que estão executando **Red Hat Enterprise Linux versão 7.5**, clique no valor **VM count** para esse sistema operacional e ele abrirá a página a seguir, listando o virtual máquinas que correspondem a esse filtro e seu estado de integridade atualmente conhecido.  

![Exemplo de acumulação de VMs do Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Sobre o **máquinas virtuais** página, se você selecionar o nome de uma VM sob a coluna **nome da VM**, você será direcionado para a página de instância VM com mais detalhes sobre os alertas e problemas de critérios de integridade identificado que são que afetam a VM selecionada.  Aqui, você pode filtrar os detalhes do estado de integridade clicando no ícone **Estado de integridade** no canto superior esquerdo da página para ver quais componentes não estão íntegros, ou você pode exibir alertas de integridade da VM gerados por um componente não íntegro categorizados por gravidade do alerta.    

Na visualização de lista de VMs, clicar no nome de uma VM abre a página **Integridade** para essa VM selecionada, da mesma forma como se você selecionasse **Insights (visualização)** diretamente da VM.

![Insights VM de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

Aqui, ele mostra um **Status de integridade** para a máquina virtual e **Alertas**, categorizados por gravidade, que representam os alertas de integridade da VM gerados quando o estado de integridade muda de integridade para insalubre para um critério de integridade.  Selecionar **VMs na condição crítica** abrirá uma página com uma lista de uma ou mais VMs que estão em um estado de integridade crítico.  Clicar no status de integridade de uma das VMs na lista mostrará a visualização **Diagnóstico de integridade** da VM.  Aqui você pode descobrir quais critérios de integridade estão refletindo um problema de estado de integridade. Quando a página **Diagnóstico de integridade** é aberta, ela mostra todos os componentes da VM e seus critérios de integridade associados ao estado de integridade atual. Para obter mais informações, consulte [diagnóstico de integridade](#health-diagnostics).  

Selecionar **Visualizar todos os critérios de integridade** abre uma página mostrando uma lista de todos os critérios de integridade disponíveis com esse recurso.  As informações podem ser filtradas com base nas seguintes opções:

* **Tipo** - Existem três tipos de critérios de integridade para avaliar condições e acumular o estado geral de integridade da VM monitorada.  
     a. **Unidade** – medem alguns aspectos da máquina virtual. Esse tipo de critério de integridade pode estar verificando um contador de desempenho para determinar o desempenho do componente, executando um script para executar uma transação sintética ou observando um evento que indica um erro.  Por padrão, o filtro está definido para a unidade.  
    b. **Dependência** - Fornece acúmulo de integridade entre diferentes entidades. Esse critério de integridade permite que a integridade de uma entidade dependa da integridade de outro tipo de entidade da qual depende para uma operação bem-sucedida.  
    c. **Agregado** - Fornece um estado de integridade combinado de critérios de integridade semelhantes. O critério de integridade de unidade e dependência geralmente será configurado em um critério de integridade agregado. Além de fornecer uma melhor organização geral dos diversos critérios de integridade direcionados a uma entidade, o critério de integridade agregado fornece um estado de saúde exclusivo para categorias distintas das entidades.

* **Categoria** - Tipo de critérios de integridade usados para agrupar critérios de tipo semelhante para fins de relatório.  Eles estão **disponibilidade** ou **desempenho**.

Você pode fazer uma busca para ver quais instâncias estão íntegras, clicando em um valor sob a coluna **componente não íntegro**.  Na página, uma tabela lista os componentes que estão em um estado de integridade crítico.    

## <a name="health-diagnostics"></a>Diagnóstico de integridade

A página **Diagnóstico de integridade** permite que você visualize o modelo de integridade de uma VM, listando todos os componentes da VM em questão, os critérios de integridade associados, as alterações de estado e outros problemas significativos identificados por componentes monitorados relacionados à VM.

![Exemplo de página de diagnóstico de integridade para uma VM](./media/vminsights-health/health-diagnostics-page-01.png)

Você pode inicializar o Diagnóstico de Integridade das seguintes maneiras.

* Por estado de integridade cumulativo de todas as VMs da perspectiva de VM agregada no Monitor do Azure.  Na página **integridade**, clique no ícone para **estado crítico**, **Aviso**, **integridade** ou **Desconhecido** na seção **Integridade da VM Convidada** e vá até a página que lista todas as VMs que correspondem à categoria filtrada.  Clicar no valor na coluna **Estado de integridade** abrirá o Diagnóstico de integridade com escopo para essa VM específica.      

* Pelo sistema operacional da agregação perspectiva VM no Azure Monitor. Em **distribuição da VM**, a seleção de qualquer um dos valores da coluna abrirá a página **Máquinas Virtuais** e retornará uma lista na tabela correspondente à categoria filtrada.  Clicar no valor da coluna **Estado de Integridade** abre o Diagnóstico de Integridade da VM selecionada.    
 
* A VM no Azure Monitor para VMs convidada **integridade** guia, selecionando **exibir diagnóstico de integridade** 

O Diagnóstico de Integridade organiza informações de integridade nas seguintes categorias: 

* Disponibilidade
* Desempenho
 
Todos os critérios de integridade definidos para um componente específico, como o disco lógico, CPU, etc. podem ser exibidos sem filtragem nas categorias de dois (ou seja, um modo de exibição completo de todos os critérios) ou filtrar os resultados por qualquer categoria ao selecionar **disponibilidade**  ou **desempenho** opções na página. Além disso, a categoria dos critérios pode ser vista ao lado na **critérios de integridade** coluna. Se os critérios não corresponder a categoria selecionada, ela mostrará a mensagem **sem critérios de integridade disponíveis para a categoria selecionada** na **critérios de integridade** coluna.  

O estado de um critério de integridade é definido por um dos quatro estados – *Crítico*, *Aviso*, *Íntegro* e *Desconhecido*. As três primeiras são configuráveis, que significa que você pode modificar os valores de limite dos monitores diretamente no painel de critérios de integridade de configuração ou usando a API REST do Azure Monitor [monitor de operação de atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Desconhecido* não é configurável e está reservado para cenários específicos.  

A página de diagnósticos de saúde tem três seções principais:

* Modelo de componente 
* Critérios de integridade
* Alterações de estado 

![Seções da página de diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente

A coluna mais à esquerda em uma página de Diagnósticos de Integridade é o modelo de componente. Todos os componentes associados à VM são exibidos nessa coluna, juntamente com os respectivos estados de integridade. 

No exemplo a seguir, os componentes descobertos são disco, disco lógico, processador, memória e sistema operacional. Várias instâncias desses componentes são descobertas e exibidas nessa coluna. Por exemplo, a imagem abaixo mostra que a VM tem duas instâncias de discos lógicos, C: e D:, que estão em um estado íntegro.  

![Exemplo de modelo de componente apresentado em Diagnósticos de integridade](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de integridade

A coluna central na página Diagnósticos de Integridade é a coluna **Critérios de Integridade**. O modelo de integridade definido para a VM é exibido em uma árvore hierárquica. O modelo de integridade de uma VM consiste em critérios de integridade agregada e da unidade.  

![Exemplos de critérios de integridade apresentados em Diagnósticos de saúde](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de integridade mede a integridade da instância monitorada com alguns critérios, que podem ser um valor limite, um estado de uma entidade, etc. Um critério de integridade tem dois ou três estados de integridade configuráveis, conforme descrito anteriormente. Em qualquer ponto, o critério de integridade pode estar em apenas um dos seus estados potenciais. 

A integridade geral de um alvo é determinada pela integridade de cada um dos critérios de integridade definidos no modelo de integridade. É uma combinação de critérios de integridade direcionados diretamente para o destino, os critérios de integridade voltados para componentes acumulando para o destino por meio de um critério de integridade agregado. Essa hierarquia é ilustrada na seção **critérios de integridade** da página Diagnósticos de integridade. A política de rollup da integridade faz parte da configuração dos critérios de integridade de agregação (o padrão é definido para *Worst-of*). Você pode encontrar uma lista do conjunto padrão de critérios de integridade em execução como parte desse recurso na seção [detalhes de configuração de monitoramento](#monitoring-configuration-details), e você pode usar a API REST do Azure Monitor [monitorar instâncias - lista por recurso operação](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) para obter uma lista de todos os critérios de integridade e a configuração detalhada em execução em relação ao recurso de VM do Azure.  

O tipo de critério de integridade **Unidade** pode ter sua configuração modificada clicando no link de elipse no canto direito e selecionando **Mostrar Detalhes** para abrir o painel de configuração. 

![Configurando um exemplo de critérios de integridade](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração dos critérios de integridade selecionado, usando o exemplo **Média de Segundos de Disco por Gravação**, seu limite pode ser configurado com um valor numérico diferente. Ele é um monitor de dois estados, o que significa que o estado se altera somente de íntegro para aviso. Outro critério de integridade pode ser de três estados, em que você pode configurar um valor para o limite de estado de integridade crítico e um valor para o de aviso. Você também pode modificar o limite usando a API de REST do Azure Monitor [monitor de operação de atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>A aplicação de alterações de configuração de critérios de integridade a uma instância é aplicada a todas as instâncias monitoradas.  Por exemplo, se você escolher **Disco - 1 D:** e modificar o limite de **Média de Segundos de Disco por Gravação**, ele não se aplicará apenas a essa instância, mas a todas as outras instâncias de disco lógico descobertas e monitoradas na VM.
>

![Configurando um critério de integridade de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Caso queira saber mais sobre o indicador de integridade, artigos de conhecimento estão inclusos, ajudando você a identificar problemas, causas e resoluções. Clique no link **Exibir informações** na página e ela abre uma nova guia no navegador mostrando o artigo de conhecimento específico. A qualquer momento, é possível examinar todos os artigos de conhecimento dos critérios de integridade inclusos no Azure Monitor para o recurso de integridade de VMs [aqui](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Alterações de estado

A coluna mais à direita na página Diagnósticos de Integridade é **Mudanças de Estado**. Ele lista todas as alterações de estado associadas aos critérios de integridade selecionados na seção **Critérios de integridade** ou a alteração de estado da VM se uma VM foi selecionada no **Modelo de Componente** ou **critérios de integridade** coluna da tabela. 

![Exemplo de alterações de estado apresentadas no Diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Esta seção consiste no estado dos critérios de integridade e na hora associada classificada pelo estado mais recente na parte superior.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associação de modelo de componente, critérios de integridade e estado alterar colunas 

As três colunas são interconectadas uns com os outros. Quando você seleciona uma instância descoberta na seção **Modelo de Componente**, a seção **Critérios de Integridade** é filtrada para essa exibição de componente e, correspondentemente, a seção **Alteração de Estado** é atualizada com base nos critérios de integridade selecionados. 

![Exemplo de seleção de instância monitorada e resultados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

No exemplo acima, quando você escolhe **Disco - 1 D:**, a árvore de Critérios de Integridade é filtrada como **Disco - 1 D:**. A coluna **Alteração de Estado** mostra a mudança de estado com base na disponibilidade de **Disco - 1 D:**. 

Para ver o estado de integridade atualizado, você pode atualizar a página de Diagnósticos de Integridade clicando no link **Atualizar**.  Se houver uma atualização no estado de integridade do critério de integridade com base no intervalo de pesquisa predefinido, essa tarefa permitirá que você evite esperar e reflita o estado de integridade mais recente.  O **Estado de Critérios de Integridade** é um filtro que permite que você defina o escopo dos resultados com base no estado de integridade selecionado – *Íntegro*, *Aviso*, *Crítico*, *Desconhecido* e *Todos*.  A hora de **Última Atualização** no canto superior direito representa a última vez em que a página Diagnósticos de Integridade foi atualizada.  

## <a name="alerts"></a>Alertas

O recurso Azure Monitor for VMs Health se integra ao [Alertas do Azure](../../azure-monitor/platform/alerts-overview.md) e gera um alerta quando os critérios de integridade predefinidos mudam de um estado íntegro para um estado não íntegro quando a condição é detectada. Os alertas são categorizados por gravidade - Sev 0 a 4, com Sev 0 representando o nível de severidade mais alto.  

O número total de alertas de integridade da VM categorizados por gravidade está disponível no painel **Integridade** na seção **Alertas**. Quando você seleciona o número total de alertas ou o número correspondente a um nível de gravidade, a página **Alertas** é aberta e lista todos os alertas correspondentes à sua seleção.  Por exemplo, se você tiver selecionado a linha correspondente à **nível de gravidade 1**, em seguida, você vê a seguinte exibição:

![Exemplo de todos os alertas de severidade de nível 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Na página **Alertas**, o escopo não abrange apenas os alertas correspondentes à sua seleção, mas também são filtrados por **Tipo de recurso** para mostrar apenas os alertas de integridade gerados pelo recurso de máquina virtual.  Ela é refletida na lista de alertas, sob a coluna **recurso de destino**, onde ele mostra que a VM do Azure que o alerta foi gerado para quando a condição de não íntegro dos critérios de integridade específico foi atendida.  

Alertas de outros tipos de recursos ou serviços não devem ser incluídos nessa exibição, como alertas de log com base em consultas de log ou alertas de métrica que você normalmente seria exibir do padrão do Azure Monitor [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página. 

É possível filtrar essa exibição, selecionando valores nos menus suspensos na parte superior da página.

|Coluna |DESCRIÇÃO | 
|-------|------------| 
|Assinatura |Selecione uma assinatura do Azure. Apenas alertas na assinatura selecionada são incluídos na exibição. | 
|Grupo de recursos |Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. | 
|Tipo de recurso |Selecione um ou mais tipos de recurso. Por padrão, somente os alertas de destino **Máquinas virtuais** estão selecionados e incluídos nessa exibição. Essa coluna somente estará disponível depois que um grupo de recursos for especificado. | 
|Recurso |Selecione um recurso. Apenas alertas com esse recurso como um destino são incluídos na exibição. Essa coluna somente estará disponível depois que um tipo de recurso for especificado. | 
|Severity |escolha uma gravidade de alerta ou selecione *Tudo* para incluir alertas de todas as gravidades. | 
|Monitorar condição |Selecione uma condição de monitor para filtrar alertas se eles foram *Disparados* pelo sistema ou *Resolvidos* pelo sistema se a condição não estiver mais ativa. Ou selecione *todos* para incluir alertas de todas as condições. | 
|Estado de alerta |Selecione um estado de alerta, *Novo*, *Confirme*, *Fechado* ou selecione *Todos* para incluir alertas de todos os estados. | 
|Monitorar serviço |Selecione um serviço ou selecione *Todos* para incluir todos os serviços. Apenas alertas do *VM Insights* são compatíveis com esse recurso.| 
|Intervalo de tempo| Apenas alertas acionados dentro da janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. | 

A página **Detalhes do alerta** é exibida quando você seleciona um alerta, fornecendo detalhes do alerta e permitindo que você altere seu estado. Para saber mais sobre como gerenciar alertas, confira [Criar, exibir e gerenciar alertas usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>No momento, a criação de novos alertas com base em critérios de integridade ou a modificação das regras de alerta de integridade existentes no Azure Monitor por meio do portal não são operações compatíveis.  
>

![Painel de detalhes do alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

O estado de alerta também pode ser alterado para um ou vários alertas, selecionando-os e selecionando **Alterar estado** na página **Todos os alertas**, no canto superior esquerdo. No **alterar estado de alerta** painel que você selecione um dos estados, adicione uma descrição da alteração na **comentário** campo e, em seguida, clique em **Ok** para confirmar as alterações. Enquanto as informações são verificadas e as alterações são aplicadas, você pode acompanhar o progresso em **notificações** no menu.  

### <a name="configure-alerts"></a>Configurar alertas
Certos gerenciamento de alertas, tarefas não podem ser gerenciadas no portal do Azure e devem ser realizadas usando o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Especificamente:

- Habilitar ou desabilitar um alerta para os critérios de integridade 
- Configurar notificações para alertas de critérios de integridade 

A abordagem usada em cada exemplo está usando [ARMClient](https://github.com/projectkudu/armclient) em seu computador Windows. Se você não estiver familiarizado com esse método, consulte [usando o ARMClient](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Habilitar ou desabilitar a regra de alerta

Para habilitar ou desabilitar um alerta para um critério de integridade específico, a propriedade de critérios de integridade *alertGeneration* precisa ser modificado com um valor de **desabilitado** ou **habilitado**. Para identificar o *monitorId* de um critério de integridade específico, o exemplo a seguir mostrará como consultar esse valor para os critérios **lógico \ média de disco segundos por transferência**.

1. Em uma janela de terminal, digite **armclient.exe login**. Isso solicita que você entrar no Azure.

2. Digite o seguinte comando para recuperar todos os critérios de integridade ativos em uma máquina virtual específica e identificar o valor de *monitorId* propriedade. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    O exemplo a seguir mostra a saída desse comando. Anote o valor de *MonitorId*. Esse valor é necessário para a próxima etapa em que precisamos especificar a ID dos critérios de integridade e modificar sua propriedade para criar um alerta.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Digite o seguinte comando para modificar o *alertGeneration* propriedade.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Digite o comando GET usado na etapa 2 para verificar se o valor da propriedade é definido como **desabilitado**.  

#### <a name="associate-action-group-with-health-criteria"></a>Associar o grupo de ação com critérios de integridade

O Azure Monitor de integridade de VMs dá suporte a notificações de SMS e email quando os alertas são gerados quando os critérios de integridade se torna não íntegro. Para configurar notificações, você precisa observar o nome do grupo de ação que está configurado para enviar notificações por email ou SMS. 

>[!NOTE]
>Essa ação precisa ser executada em relação a cada VM monitorado que você deseja receber uma notificação para.

1. Em uma janela de terminal, digite **armclient.exe login**. Isso solicita que você entrar no Azure.

2. Digite o comando a seguir para associar um grupo de ações a regras de alerta.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Para verificar o valor da propriedade **actionGroupResourceIds** com êxito atualizado, digite o comando a seguir.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    O resultado deve ser assim:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Para identificar gargalos e a utilização geral com o desempenho de VMs, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md), ou para exibir dependências de aplicativos descobertos, consulte [Exibir o Azure Monitor para mapa de VMs](vminsights-maps.md). 
