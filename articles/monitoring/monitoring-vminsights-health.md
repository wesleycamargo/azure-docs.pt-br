---
title: Monitor virtual machine health with Azure Monitor for VMs| Microsoft Docs
description: Este artigo descreve como você entende a integridade da máquina virtual e do sistema operacional subjacente com o Monitor do Azure para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: c8a8598640e31f59476b5b3351fdb2eab7b66a6c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952912"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms"></a>Entenda a integridade de suas máquinas virtuais do Azure com o Monitor do Azure para VMs
O Azure inclui vários serviços que executam individualmente uma função ou tarefa específica no espaço de monitoramento, mas não oferece uma perspectiva de integridade detalhada do sistema operacional hospedado nas máquinas virtuais do Azure.  Embora você possa monitorar diferentes condições usando o Log Analytics ou o Azure Monitor, elas não foram projetadas para modelar e representar a integridade dos principais componentes ou a integridade geral da máquina virtual.  Com o recurso de integridade do Azure Monitor for VMs, ele monitora proativamente a disponibilidade e o desempenho do sistema operacional convidado Windows ou Linux com um modelo que representa os principais componentes e seus relacionamentos, critérios que determinam como avaliar a integridade desses componentes e alertam quando um condição insalubre é detectada.  

Exibir o estado geral de integridade da VM do Azure e do sistema operacional subjacente pode ser observado de duas perspectivas com a integridade do Monitor do Azure para VMs, diretamente da máquina virtual ou em todas as VMs em um grupo de recursos do Monitor do Azure.

Este artigo ajudará você a entender como avaliar, investigar e resolver rapidamente os problemas de integridade detectados.

Para obter informações sobre como configurar o Monitor do Azure para VMs, consulte [Ativar o Monitor do Azure para VMs](monitoring-vminsights-onboard.md).

## <a name="monitoring-configuration-details"></a>Detalhes de configuração de monitoramento
Esta seção descreve os critérios de integridade padrão definidos para monitorar as máquinas virtuais do Windows e Linux do Azure.

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
- Tempo ocioso percentual do disco físico
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
- Tempo percentual de DPC do processador
- Porcentagem do Processador
- Percentual Total do Processador
- Tempo total de DPC
- Sistema Operacional Megabytes Disponíveis de Memória

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Entre no [Portal do Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introdução à experiência de saúde
Antes de começar a usar o recurso Funcionamento para uma única máquina virtual ou um grupo de VMs, é importante fornecer uma breve introdução para que você entenda como as informações são apresentadas e o que as visualizações representam.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Visualize a integridade diretamente de uma máquina virtual 
Para exibir a integridade de uma VM do Azure, selecione **Insights (visualização)** no painel esquerdo da máquina virtual. Na página de informações da VM, **Integridade** está aberto por padrão e mostra a visualização de integridade da VM.  

![Visão geral de integridade do Monitor do Azure para VMs de uma máquina virtual do Azure selecionada](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Na guia **Integridade**, na seção **Integridade Convidada da VM**, a tabela mostra o estado atual da integridade de sua máquina virtual e o número total de alertas de Integridade da VM gerados por um componente não íntegro. Consulte a [alertas e o gerenciamento de alertas](#alerting-and-alert-management) para obter mais detalhes.  

Os estados de integridade definidos para uma VM são: 

* **Integridade** - nenhum problema detectado para a VM e está funcionando conforme necessário.  
* **Crítico** - um ou mais problemas críticos são detectados, que precisam ser resolvidos para restaurar a funcionalidade normal conforme o esperado. 
* **Aviso** - um ou mais problemas são detectados, que precisam ser resolvidos ou a condição de integridade pode se tornar crítica.  
* **Desconhecido** - se o serviço não puder fazer uma conexão com a VM, o status será alterado para um estado desconhecido.  

A seleção de **Visualizar diagnósticos de integridade** abre uma página mostrando todos os componentes da VM, critérios de integridade associados, alterações de estado e outros problemas significativos encontrados pelos componentes de monitoramento relacionados à VM. Consulte o [Diagnóstico de integridade](#health-diagnostics) para obter mais detalhes. 

Na seção **Integridade do componente**, a tabela mostra um status cumulativo de integridade das categorias de desempenho principais monitoradas por critérios de integridade para essas áreas, especificamente **CPU**, **Memória**, **Disco** e **Rede**.  A seleção de qualquer um dos componentes abre uma página listando todos os aspectos de monitoramento de critério de integridade individuais desse componente e o respectivo estado de integridade de cada um.  

Ao acessar o Health a partir de uma VM do Azure executando o sistema operacional Windows, o estado de integridade dos 5 principais serviços principais do Windows é mostrado na seção **Integridade dos serviços principais**.  A seleção de qualquer um dos serviços abre uma página listando os critérios de integridade que monitoram esse componente e seu estado de integridade.  Clicar no nome dos critérios de integridade abrirá o painel de propriedades e, a partir daqui, você poderá revisar os detalhes de configuração, inclusive se os critérios de integridade tiverem um alerta do Monitor do Azure correspondente definido. Para saber mais sobre isso, consulte [Diagnóstico de Integridade e trabalhando com critérios de integridade](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Perspectiva de máquina de virtual de agregação
Para exibir a coleta de integridade de todas as suas máquinas virtuais em um grupo de recursos, na lista de navegação do portal, selecione **Monitor do Azure** e, em seguida, selecione **Máquinas Virtuais (visualização)**.  

![Exibição do Azure Monitor de monitoramento de Insights de VM](./media/monitoring-vminsights-health/vminsights-aggregate-health.png)

Nas listas suspensas **Assinatura** e **Grupo de recursos**, selecione a apropriada que inclua as VMs de destino integradas para visualizar seu estado de integridade. 

Na guia **Integridade**, você pode aprender o seguinte:

* Quantas VMs estão em um estado crítico ou não íntegro, em comparação com quantas estão em bom estado ou não estão enviando dados (referido como um estado desconhecido)?
* Quais VMs por sistema operacional (SO) estão relatando um estado não íntegro e quantas?
* Quantas VMs não são íntegras devido a um problema detectado com um processador, disco, memória ou adaptador de rede, categorizado por estado de integridade?  
* Quantas VMs não são íntegras devido a um problema detectado em um serviço central do sistema operacional, categorizado por estado de integridade?

Aqui você pode identificar rapidamente os principais problemas críticos detectados pelos critérios de integridade, monitorando proativamente a VM e analisando os detalhes do alerta de integridade da VM e o artigo de conhecimento associado com a intenção de auxiliar no diagnóstico e na correção do problema.  Selecione qualquer uma das gravidades para abrir a página [Todos os Alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md#all-alerts-page) filtrada por tal gravidade.

A lista da **distribuição da VM por sistema operacional** mostra as VMs listadas pela edição do Windows ou pela distribuição do Linux, junto com sua versão. Em cada categoria de sistema operacional, as VMs são divididas ainda mais com base na integridade da VM. 

![Perspectiva de distribuição de máquinas virtuais do VM Insights](./media/monitoring-vminsights-health/vminsights-vmdistribution-by-os.png)

Os estados de integridade definidos para uma VM são: 

* **Integridade** - nenhum problema detectado para a VM e está funcionando conforme necessário.  
* **Crítico** - um ou mais problemas críticos são detectados, que precisam ser resolvidos para restaurar a funcionalidade normal conforme o esperado. 
* **Aviso** - um ou mais problemas são detectados, que precisam ser resolvidos ou a condição de integridade pode se tornar crítica.  
* **Desconhecido** - se o serviço não puder fazer uma conexão com a VM, o status será alterado para um estado desconhecido.  

Você pode clicar em qualquer item da coluna - **VM count**, **Critical**, **Warning**, **Integridade** ou **Unknown** para perfurar - na página **Máquinas Virtuais**, veja uma lista de resultados filtrados que correspondem à coluna selecionada. Por exemplo, se quisermos revisar todas as VMs que estão executando **Red Hat Enterprise Linux versão 7.5**, clique no valor **VM count** para esse sistema operacional e ele abrirá a página a seguir, listando o virtual máquinas que correspondem a esse filtro e seu estado de integridade atualmente conhecido.  

![Exemplo de acumulação de VMs do Red Hat Linux](./media/monitoring-vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Sobre o **máquinas virtuais** página, se você selecionar o nome de uma VM sob a coluna **nome da VM**, você será direcionado para a página de instância VM com mais detalhes sobre os alertas e problemas de critérios de integridade identificado que são que afetam a VM selecionada.  A partir daqui, você pode filtrar os detalhes do estado de integridade clicando em **estado de integridade** ícone no canto superior esquerdo da página para ver quais componentes estão íntegros, ou você pode exibir alertas de integridade da VM geradas por um componente não íntegro categorizadas por gravidade do alerta.    

Na visualização de lista de VMs, clicar no nome de uma VM abre a página **Integridade** para essa VM selecionada, da mesma forma como se você selecionasse **Insights (visualização)** diretamente da VM.

![Insights VM de uma máquina virtual do Azure selecionada](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

Aqui, ele mostra um **Status de integridade** para a máquina virtual e **Alertas**, categorizados por gravidade, que representam os alertas de integridade da VM gerados quando o estado de integridade muda de integridade para insalubre para um critério de integridade.  Selecionar **VMs na condição crítica** abrirá uma página com uma lista de uma ou mais VMs que estão em um estado de integridade crítico.  Clicar no status de integridade de uma das VMs na lista mostrará a visualização **Diagnóstico de integridade** da VM.  Aqui você pode descobrir quais critérios de integridade estão refletindo um problema de estado de integridade. Quando a página **Diagnóstico de integridade** é aberta, ela mostra todos os componentes da VM e seus critérios de integridade associados ao estado de integridade atual.  Consulte a seção [Diagnóstico de integridade](#health-diagnostics) para obter mais detalhes.  

Selecionar **Visualizar todos os critérios de integridade** abre uma página mostrando uma lista de todos os critérios de integridade disponíveis com esse recurso.  As informações podem ser filtradas com base nas seguintes opções:

* **Tipo** - Existem três tipos de critérios de integridade para avaliar condições e acumular o estado geral de integridade da VM monitorada.  
    a. **Unidade** – medem alguns aspectos da máquina virtual. Esse tipo de critério de integridade pode estar verificando um contador de desempenho para determinar o desempenho do componente, executando um script para executar uma transação sintética ou observando um evento que indica um erro.  Por padrão, o filtro está definido para a unidade.  
    b. **Dependência** - Fornece acúmulo de integridade entre diferentes entidades. Esse critério de integridade permite que a integridade de uma entidade dependa da integridade de outro tipo de entidade da qual depende para uma operação bem-sucedida.  
    c. **Agregado** - Fornece um estado de integridade combinado de critérios de integridade semelhantes. O critério de integridade de unidade e dependência geralmente será configurado em um critério de integridade agregado. Além de fornecer uma melhor organização geral dos diversos critérios de integridade direcionados a uma entidade, o critério de integridade agregado fornece um estado de saúde exclusivo para categorias distintas das entidades.

* **Categoria** - Tipo de critérios de integridade usados para agrupar critérios de tipo semelhante para fins de relatório.  Eles estão **disponibilidade** ou **desempenho**.

Você pode fazer uma busca para ver quais instâncias estão íntegras, clicando em um valor sob a coluna **componente não íntegro**.  Na página, uma tabela lista os componentes que estão em um estado de integridade crítico.    

## <a name="health-diagnostics"></a>Diagnóstico de integridade
A página **Diagnóstico de integridade** permite que você visualize todos os componentes da VM, critérios de integridade associados, alterações de estado e outros problemas significativos encontrados pelo monitoramento de objetos relacionados à VM. 

![Exemplo de página de diagnóstico de integridade para uma VM](./media/monitoring-vminsights-health/health-diagnostics-page-01.png)

Você pode iniciar o diagnóstico de integridade das seguintes maneiras.

* Por estado de integridade cumulativo de todas as VMs da perspectiva de VM agregada no Monitor do Azure.  Na página **integridade**, clique no ícone para **estado crítico**, **Aviso**, **integridade** ou **Desconhecido** na seção **Integridade da VM Convidada** e vá até a página que lista todas as VMs que correspondem à categoria filtrada.  Clicar no valor na coluna **Estado de integridade** abrirá o Diagnóstico de integridade com escopo para essa VM específica.      

* Pelo sistema operacional da agregação perspectiva VM no Azure Monitor. Em **distribuição da VM**, a seleção de qualquer um dos valores da coluna abrirá a página **Máquinas Virtuais** e retornará uma lista na tabela correspondente à categoria filtrada.  Clicar no valor da coluna **Estado de integridade** abre o Diagnóstico de integridade da VM selecionada.    
 
* A VM no Azure Monitor para VMs convidada **integridade** guia, selecionando **exibir diagnóstico de integridade** 

O diagnóstico de saúde organiza informações de saúde nas seguintes categorias: 

* Disponibilidade
* Desempenho
 
Todos os critérios de integridade definidos para um destino selecionado são exibidos na categoria apropriada. 

O estado de saúde para critérios de saúde é definido por um dos três estados - *Crítico*, *Aviso* e *integridade*. Há outro estado *Desconhecido*, que não está associado ao estado de integridade, mas representa seu status de monitoramento conhecido pelo recurso.  

A tabela a seguir fornece detalhes sobre os estados de integridade representados em Diagnósticos de integridade.

|ícone |Estado de integridade |Significado |
|-----|-------------|------------|
| |Healthy |O estado de integridade é saudável se estiver dentro das condições de saúde definidas. No caso de um monitor de rollup pai, a saúde é acumulada e reflete o melhor caso ou o pior estado da criança.|
| |Crítico |O estado de integridade é crítico se não estiver dentro da condição de saúde definida. No caso de um monitor de rollup pai, a saúde é acumulada e reflete o melhor caso ou o pior estado da criança.|
| |Aviso |O estado de integridade está avisando se está entre dois limites para a condição de integridade definida, em que um indica um estado *Aviso* e o outro indica um estado *Crítico*. No caso de um rollup pai monitor, se um ou mais filhos estão em um estado de aviso, em seguida, pai refletirá estado *aviso*. Se houver um filho que esteja em um *Crítico* e outro filho em um estado *Aviso*, o pacote pai mostrará um estado de integridade de *Crítico*.|
| |Desconhecido |O estado de integridade está em um estado *Desconhecido* quando o estado de integridade não pode ser calculado por vários motivos, como não conseguir coletar dados, serviço não inicializado, etc.| 

A página de diagnósticos de saúde tem três seções principais:

* Modelo de componente 
* Critérios de integridade
* Alterações de estado 

![Seções da página de diagnóstico de integridade](./media/monitoring-vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente
A coluna mais à esquerda em uma página de diagnósticos de integridade é o modelo de componente. Todos os componentes e suas instâncias descobertas, associados à VM, são exibidos nessa coluna. 

No exemplo a seguir, os componentes descobertos são disco, disco lógico, processador, memória e sistema operacional. Várias instâncias desses componentes são descobertas e exibidas nessa coluna, com duas instâncias do disco lógico **/**, **/ boot** e **/mnt/resource**, uma instância do adaptador de rede **eth0**, duas instâncias do disco **sda** e **sdb**, duas instâncias do processador **0 e 1** e um **Servidor Red Hat Enterprise Linux versão 7.4 (Maipo) (Sistema Operacional)**. 

![Exemplo de modelo de componente apresentado em Diagnósticos de integridade](./media/monitoring-vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de integridade
A coluna central na página Diagnósticos de integridade é a coluna **Critérios de integridade**. O modelo de integridade definido para a VM é exibido em uma árvore hierárquica. O modelo de integridade de uma VM consiste em critérios de unidade, dependência e saúde agregada.  

![Exemplos de critérios de integridade apresentados em Diagnósticos de saúde](./media/monitoring-vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de integridade mede a integridade da instância monitorada com alguns critérios, que podem ser um valor limite ou um estado de uma entidade, etc. Um critério de integridade tem dois ou três estados de integridade, conforme descrito na seção acima. Em qualquer ponto, o critério de integridade pode estar em apenas um dos seus estados potenciais. 

A integridade geral de um alvo é determinada a partir da integridade de cada um dos critérios de integridade definidos no modelo de integridade. Esta será uma combinação de critérios de integridade direcionados diretamente para a meta, critérios de integridade direcionados a componentes que rumam para o alvo através de um critério de integridade de dependência. Essa hierarquia é ilustrada na seção **critérios de integridade** da página Diagnósticos de integridade. A política de como a integridade é acumulada faz parte da configuração dos critérios de integridade de agregação e dependência. Você pode encontrar uma lista do conjunto padrão de critérios de integridade em execução como parte desse recurso na seção [Monitorando detalhes da configuração](#monitoring-configuration-details).  

No exemplo a seguir, o critério de integridade agregado **Principal Pacote de Serviços do Windows** para uma VM baseada em Windows avalia a integridade dos Serviços Windows mais importantes com base em critérios de integridade de serviço individuais. O status de cada serviço, como DNS, DHCP, etc., é avaliado e a integridade é acumulada no critério de integridade de rollup correspondente (conforme mostrado abaixo).  

![Exemplo de rollup de integridade](./media/monitoring-vminsights-health/health-diagnostics-windows-svc-rollup.png)

A integridade do **Core Windows Services Rollup** rola para a integridade da **disponibilidade do sistema operacional**, que eventualmente é acumulada na **Disponibilidade** da VM. 

Critérios de integridade **O tipo de unidade** pode ter sua configuração modificada clicando no link de elipse no canto direito e selecionando **Mostrar Detalhes** para abrir o painel de configuração. 

![Configurando um exemplo de critérios de integridade](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-03.png)

No painel de configuração dos critérios de integridade selecionados, neste exemplo, o  **Espaço Livre em Disco%** pode ser configurado com um valor numérico diferente para seu limite, pois é um monitor de dois estados, o que significa que ele só muda de saudável a crítica.  Outro critério de integridade pode ser três estados, em que você pode configurar um valor para o limite de estado crítico e de aviso.  

>[!NOTE]
>A aplicação da alteração da configuração de critérios de integridade a uma instância é aplicada a todas as instâncias monitoradas.  Por exemplo, se você selecionar **/mnt/resource** e modificar o limite **Logical Disk% Free Space**, ele não se aplicará apenas a essa instância, mas todas as outras instâncias de disco lógico descobertas e monitoradas na VM.
>

![Configurando um critério de integridade de um exemplo de monitor de unidade](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-04.png)


### <a name="state-changes"></a>Alterações de estado
A coluna mais à direita na página Diagnósticos de Integridade é **Mudanças de Estado**. Ele lista todas as alterações de estado associadas aos critérios de integridade selecionados na seção **Critérios de integridade** ou a alteração de estado da VM se uma VM foi selecionada no **Modelo de Componente** ou **critérios de integridade** coluna da tabela. 

![Exemplo de alterações de estado apresentadas no Diagnóstico de integridade](./media/monitoring-vminsights-health/health-diagnostics-page-statechanges.png)

Esta seção consiste no estado dos critérios de integridade e na hora associada classificada pelo estado mais recente na parte superior.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associação de colunas de modelo de componente, critérios de integridade e alteração de estado 
As três colunas são interconectadas uns com os outros. Quando um usuário seleciona uma instância descoberta no Modelo de Componente, a seção **Critérios de integridade** é filtrada para essa visão de componente e, correspondentemente, a **Alteração de Estado** é atualizada com base nos critérios de integridade selecionados. 

![Exemplo de seleção de instância monitorada e resultados](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-02.png)

No exemplo acima, quando se seleciona **/mnt (Disco Lógico)**, a árvore de Critérios de Funcionamento é filtrada para **/mnt (Disco Lógico)**. As guias **Disponibilidade** e **Desempenho** também são filtradas de acordo. A coluna **State Change** mostra a mudança de estado com base na disponibilidade de **/mnt (Disco Lógico)**. 

Para ver o estado de integridade atualizado, você pode atualizar a página de Diagnósticos de Integridade clicando no link **Atualizar**.  Se houver uma atualização no estado de integridade do critério de integridade com base no intervalo de pesquisa predefinido, essa tarefa permitirá que você evite esperar e reflita o estado de integridade mais recente.  O **Estado de Critérios de Integridade** é um filtro que permite que você alcance os resultados com base no estado de integridade selecionado - Saudável, Aviso, Crítico, Desconhecido e todos.  A hora **Última Atualização** no canto superior direito representa a última vez em que a página Diagnósticos de integridade foi atualizada.  

## <a name="alerting-and-alert-management"></a>Gerenciamento de alerta e alerta 
O recurso Azure Monitor for VMs Health se integra ao [Alertas do Azure](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) e gera um alerta quando os critérios de integridade predefinidos mudam de um estado íntegro para um estado não íntegro quando a condição é detectada. Os alertas são categorizados por gravidade - Sev 0 a 4, com Sev 0 representando o nível de severidade mais alto.  

O número total de alertas de integridade da VM categorizados por gravidade está disponível no painel **Integridade** na seção **Alertas**. Quando você seleciona o número total de alertas ou o número correspondente a um nível de gravidade, a página **Alertas** é aberta e lista todos os alertas correspondentes à sua seleção.  Por exemplo, se você tiver selecionado a linha correspondente à **nível de gravidade 1**, em seguida, você vê a seguinte exibição:

![Exemplo de todos os alertas de severidade de nível 1](./media/monitoring-vminsights-health/vminsights-sev1-alerts-01.png)

É possível filtrar essa exibição, selecionando valores nos menus suspensos na parte superior da página.

|Coluna |DESCRIÇÃO | 
|-------|------------| 
|Assinatura |Selecione uma assinatura do Azure. Apenas alertas na assinatura selecionada são incluídos na exibição. | 
|Grupo de recursos |Selecione um único grupo de recursos. Somente alertas com destinos no grupo de recursos selecionado são incluídos na exibição. | 
|Tipo de recurso |Selecione um ou mais tipos de recurso. Somente alertas com destinos do tipo selecionado são incluídos na exibição. Essa coluna somente estará disponível depois que um grupo de recursos for especificado. | 
|Recurso |Selecione um recurso. Apenas alertas com esse recurso como um destino são incluídos na exibição. Essa coluna somente estará disponível depois que um tipo de recurso for especificado. | 
|Severity |escolha uma gravidade de alerta ou selecione *Tudo* para incluir alertas de todas as gravidades. | 
|Monitorar condição |Selecione uma condição de monitor para filtrar alertas se eles foram *Disparados* pelo sistema ou *Resolvidos* pelo sistema se a condição não estiver mais ativa. Ou selecione *todos* para incluir alertas de todas as condições. | 
|Estado de alerta |Selecione um estado de alerta, *Novo*, *Confirme*, *Fechado* ou selecione *Todos* para incluir alertas de todos os estados. | 
|Monitorar serviço |Selecione um serviço ou selecione *Todos* para incluir todos os serviços. Apenas alertas do Infrastructure Insights são suportados para esse recurso. | 
|Intervalo de tempo| Apenas alertas acionados dentro da janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. | 

A página **Detalhes do alerta** é exibida quando você seleciona um alerta, fornecendo detalhes do alerta e permitindo que você altere seu estado. Para saber mais sobre como trabalhar com regras de alerta e gerenciar alertas, consulte [Criar, exibir e gerenciar alertas usando o Monitor do Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

![Painel de detalhes do alerta para um alerta selecionado](./media/monitoring-vminsights-health/alert-details-pane-01.png)

O estado de alerta também pode ser alterado para um ou vários alertas, selecionando-os e selecionando **Alterar estado** na página **Todos os alertas**, no canto superior esquerdo. No **alterar estado de alerta** painel que você selecione um dos estados, adicione uma descrição da alteração na **comentário** campo e, em seguida, clique em **Ok** para confirmar as alterações. Enquanto as informações são verificadas e as alterações são aplicadas, você pode acompanhar o progresso em **notificações** no menu.  

## <a name="next-steps"></a>Próximas etapas
Para identificar gargalos e a utilização geral com o desempenho de máquinas virtuais, consulte [exibição de desempenho da VM do Azure](monitoring-vminsights-performance.md), ou para exibir dependências de aplicativos descobertos, consulte [modo de exibição do Azure Monitor para VMs mapa](monitoring-vminsights-maps.md). 