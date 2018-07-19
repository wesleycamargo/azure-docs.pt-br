---
title: Solução Gerenciamento de Atualizações no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações para os computadores Windows e Linux.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 237f0d2b25230528c64bd47edd10ebae62750a0c
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345375"
---
# <a name="update-management-solution-in-azure"></a>Solução Gerenciamento de Atualizações no Azure

Você pode usar a solução de Gerenciamento de Atualizações na Automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux que são implantados no Azure, em ambientes locais ou em outros provedores de nuvem. Você pode avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar rapidamente o processo de instalação das atualizações necessárias para os servidores.

Você pode habilitar o Gerenciamento de Atualizações para máquinas virtuais diretamente da sua conta da Automação do Azure. Para saber como habilitar o Gerenciamento de Atualizações para máquinas virtuais de sua conta de automação, veja [Gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md). Você também pode habilitar o Gerenciamento de Atualizações para uma única máquina virtual do painel de máquina virtual no portal do Azure. Esse cenário está disponível para máquinas virtuais do [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management).

## <a name="solution-overview"></a>Visão geral da solução

Os computadores que são gerenciados pelo Gerenciamento de Atualizações usam as configurações a seguir para realizar implantações de atualização e avaliação:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados, indicando como a solução avalia e aplica atualizações de segurança a todos os computadores Linux e servidores Windows conectados em um espaço de trabalho:

![Fluxo do processo de Gerenciamento de Atualizações](media/automation-update-management/update-mgmt-updateworkflow.png)

Depois que um computador executa uma verificação de conformidade da atualização, o agente encaminha as informações em massa ao Log Analytics do Azure. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade de atualização será iniciada em 15 minutos se o MMA for reiniciado antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada 3 horas por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade é iniciada dentro de 15 minutos.

A solução relata o grau de atualização do computador com base na fonte com a qual você está configurado para realizar a sincronização. Se o computador do Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados poderão diferir do que é mostrado pelo Microsoft Updates. O mesmo se aplica a computadores Linux que estão configurados para relatar para um repositório local em vez de um repositório público.

> [!NOTE]
> Para relatar adequadamente o serviço, o Gerenciamento de Atualizações requer que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, consulte [Planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Você pode implantar e instalar atualizações de software em computadores que precisam de atualizações, criando uma implantação agendada. As atualizações classificadas como *Opcional* não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação. 

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, explicitamente especificando computadores ou selecionando um [grupo de computadores](../log-analytics/log-analytics-computer-groups.md) que se baseia em pesquisas de log de um conjunto específico de computadores. Você também pode especificar uma agenda para aprovar e designar um período de tempo durante o qual as atualizações podem ser instaladas. 

As atualizações são instaladas por runbooks na Automação do Azure. Você não consegue exibir esses runbooks e os runbooks não exigem nenhuma configuração. Quando uma implantação de atualizações é criada, a implantação de atualizações cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para executar a instalação de atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada primeiro para verificar se as atualizações ainda são necessárias. Para computadores cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |O .NET Framework 4.5 ou superior é necessário. ([Download do .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 ou posterior é necessário. ([Baixar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1é recomendado para maior confiabilidade.  ([Baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. A aplicação de patch baseada em classificação requer 'yum' para retornar dados de segurança que não têm o CentOS fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS e 16.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Client     | Os sistemas operacionais clientes (como Windows 7 e Windows 10) não têm suporte.        |
|Windows Server 2016 Nano Server     | Sem suporte.       |

### <a name="client-requirements"></a>Requisitos do cliente

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicarem-se com um servidor WSUS ou eles devem ter acesso ao Microsoft Update. Você pode usar o Gerenciamento de Atualizações com o System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [Integrar System Center Configuration Manager com Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../log-analytics/log-analytics-agent-windows.md) é obrigatório. O agente será instalado automaticamente se você estiver instalando uma máquina virtual do Azure.

#### <a name="linux"></a>Linux

Para Linux, a máquina deve ter acesso a um repositório de atualização. O repositório de atualização pode ser público ou privado. TLS 1.1 ou TLS 1.2 é necessário para interagir com o gerenciamento de atualizações. Um Agente do OMS (Operations Management Suite) para Linux configurado para gerar relatórios em vários espaços de trabalho do Log Analytics não tem suporte com esta solução.

Para obter informações sobre como instalar o agente do OMS para Linux e fazer o download da versão mais recente, consulte [Agente do Operations Management Suite para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o Agente do OMS para Windows, consulte [Operations Management Suite Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, consulte [Acesso baseado em Função - Gerenciamento de Atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos recursos a seguir. Os recursos são adicionados à sua conta de Automação. Eles são os agentes conectados diretamente ou de um grupo de gerenciamento conectados do Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker

Depois que você habilita essa solução, qualquer computador com Windows conectado diretamente a seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nessa solução.

Cada computador Windows que é gerenciado pela solução é listado no painel de **grupos de trabalho Hybrid** como um **grupo de trabalho híbrido do Sistema** para a conta de Automação. As soluções usam a convenção de nomenclatura *Hostname FQDN_GUID*. Não é possível direcionar esses grupos com runbooks em sua conta. Elas falham se você tentar. Esses grupos devem dar suporte somente à solução de gerenciamento.

Você pode adicionar os computadores com Windows a um grupo de Hybrid Runbook Worker em sua conta de Automação para dar suporte a runbooks de automação se você usar a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente depois que você adicionar a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](../log-analytics/log-analytics-om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser totalmente gerenciado pelo gerenciamento de atualizações, o agente precisa ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, consulte [como atualizar um agente do Operations Manager](/system-center/scom/deploy-upgrade-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirmar se computadores não Azure estão integrados

Para confirmar se os computadores conectados diretamente estão comunicando-se com o Log Analytics, depois de alguns minutos você poderá executar uma das pesquisa de logs a seguir.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Em um computador Windows, você pode examinar a seguinte informação para verificar a conectividade do agente com o Log Analytics:

1. No painel de controle, abra o **Microsoft Monitoring Agent**. Na guia **Log Analytics do Azure**, o agente exibe a seguinte mensagem: **O Microsoft Monitoring Agent se conectou com êxito ao Log Analytics**.
2. Abra o Log de Eventos do Windows. Navegue até **Logs de Aplicativos e Serviços\Operations Manager** e procure as IDs de Evento 3000 e 5002 do **Conector de Serviço** de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho do Log Analytics e está recebendo a configuração.

Se o agente não puder se comunicar com o Log Analytics e o agente estiver configurado para se comunicar com a Internet através de um servidor proxy ou firewall, verifique se o servidor proxy ou firewall está configurado corretamente. Para saber como verificar se o firewall ou o servidor proxy está configurado corretamente, consulte [Configuração de rede para agente do Windows](../log-analytics/log-analytics-agent-windows.md) ou [Configuração de rede para agente do Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os sistemas Linux estiverem configurados para comunicar-se com um proxy ou Gateway OMS e se você estiver integrando essa solução, atualize as permissões de *proxy.conf* para conceder a permissão de leitura de grupo omiuser no arquivo, usando os comandos a seguir:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux recém-adicionados mostram um status de **Atualizado** após ter sido realizada uma avaliação. Esse processo pode levar até seis horas.

Para confirmar se um grupo de gerenciamento do Operations Manager está se comunicando com o Log Analytics, confira [Validar a integração do Operations Manager com o Log Analytics](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A seguinte tabela descreve as fontes conectadas que têm suporte nessa solução:

| Fonte conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |Sim |A solução coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br/>Uma conexão direta do agente do Operations Manager ao Log Analytics não é necessária. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Uma verificação é executada duas vezes por dia para cada computador Windows gerenciado. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status foi alterado, uma verificação de conformidade é iniciada. 

Uma verificação é executada a cada três horas para cada computador Linux gerenciado.

Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

## <a name="viewing-update-assessments"></a>Exibir avaliação de atualização

Na sua conta de Automação, selecione **Gerenciamento de Atualizações** para exibir o status de dos computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada. No **COLUNA DE CONFORMIDADE**, você pode ver a última vez em que a máquina foi avaliada. Na coluna **PREPARAÇÃO DO AGENTE DE ATUALIZAÇÃO**, você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para ir para a documentação de solução problemas que pode ajudá-lo a conhecer as etapas necessárias para corrigir o problema.

Para executar uma pesquisa de logs sobre as informações do computador, atualização ou implantação, selecione o item na lista. O painel **Pesquisa de Logs** abre com uma consulta para o item selecionado:

![Exibição padrão do Gerenciamento de Atualizações](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalar as atualizações

Depois que as atualizações são avaliadas para todos os computadores com Windows e Linux em seu espaço de trabalho, você pode instalar as necessárias atualizações, criando uma *implantação de atualizações*. Uma implantação de atualizações é uma instalação agendada de atualizações necessárias para um ou mais computadores. Você especifica a data e hora para a implantação e um computador ou um grupo de computadores para incluir no escopo de uma implantação. Para saber mais sobre grupos de computadores, confira [Grupos de computadores na Análise de Log](../log-analytics/log-analytics-computer-groups.md).

 Quando você inclui grupos de computadores em sua implantação de atualização, a associação de grupo é avaliada apenas uma vez no momento da criação da agenda. As alterações subsequentes em um grupo não são refletidas. Para solucionar esse problema, exclua a implantação de atualização agendada e recrie-a.

> [!NOTE]
> Máquinas virtuais do Windows que são implantadas no Azure Marketplace por padrão são definidas para receber atualizações automáticas do Serviço Windows Update. Esse comportamento não é alterado quando você adiciona essa solução ou adiciona máquinas virtuais do Windows para seu espaço de trabalho. Se você não gerenciou atualizações ativamente usando essa solução, o comportamento padrão (para aplicar automaticamente as atualizações) é aplicado.

Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar o pacote, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

As máquinas virtuais que foram criadas a partir das imagens do Red Hat Enterprise Linux (RHEL) sob demanda que estão disponíveis no Azure Marketplace são registradas para acessar a [Infraestrutura de Atualização do Red Hat (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) que é implantada no Azure. Qualquer distribuição do Linux deve ser atualizada nos repositórios de distribuição de arquivo online seguindo os métodos de distribuição com suporte.

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e pode ser selecionada. Informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel de **Pesquisa de Log** mostra mais detalhes sobre as atualizações.

## <a name="view-update-deployments"></a>Exibir implantações de atualização

Selecione a guia **Atualizar Implantações** para exibir a lista das implantações com atualizações existentes. Selecione qualquer uma das implementações de atualização na tabela para abrir o painel **Atualizar Execução de Implantação** para essa implementação de atualização.

![Visão geral dos resultados da implantação de atualizações](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Criar ou editar uma implantação de atualização

Para criar uma nova implantação de atualização, selecione **Agendar implantação de atualização**. O painel **Nova implantação de atualizações** é aberto. Insira os valores para as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
| --- | --- |
|Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Selecione **Linux** ou **Windows**.|
|Computadores para atualizar |Selecione uma pesquisa salva ou escolha **Computador** no menu suspenso e selecione computadores individuais. |
|Classificações de origem|Selecione todas as classificações de atualização que você precisa. CentOS não oferece suporte para isso fora da caixa.|
|Atualizações para excluir|Insira as atualizações a serem excluídas. Para Windows, insira o artigo KB sem o prefixo **KB**. Para o Linux, insira o nome do pacote ou use um caractere curinga.  |
|Configurações de agendamento|Selecione o tempo para iniciar e selecione **Uma Vez** ou **Recorrente** para a recorrência.|| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos ou superior a 6 horas. |

## <a name="update-classifications"></a>Classificações de origem

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição de cada classificação.

### <a name="windows"></a>Windows

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico do produto relacionadas à segurança.        |
|Pacotes cumulativos de atualização     | Um conjunto cumulativo de hotfixes que são reunidos para facilitar a implantação.        |
|Feature packs     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definição     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está atualmente instalado.        |

### <a name="linux"></a>Linux

|classificação  |Descrição  |
|---------|---------|
|Atualizações críticas ou de segurança     | Atualizações para um problema específico ou um problema relacionado à segurança específico do produto.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas em atualizações de segurança ou de natureza.        |

Para o Linux, o Gerenciamento de Atualizações pode distinguir entre críticas e atualizações de segurança na nuvem ao exibir dados de avaliação devido a enriquecimento de dados na nuvem. Para aplicação de patch, o Gerenciamento de Atualizações se baseia em dados de classificação disponíveis no computador. Ao contrário de outras distribuições, CentOS não tem essas informações disponíveis fora da caixa. Se você tiver máquinas CentOS configuradas para retornar dados de segurança para o comando a seguir, o Gerenciamento de Atualizações poderá aplicar patch com base em classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há nenhum método com suporte para habilitar a disponibilidade de dados nativos de classificação em CentOS. Neste momento, somente o suporte de melhor esforço é fornecido aos clientes que podem ter isso habilitado por conta própria.

## <a name="ports"></a>Portas

Os endereços a seguir são necessários especificamente para gerenciamento de atualizações. A comunicação para esses endereços ocorre pela porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

Para obter mais informações sobre as portas que exige o Hybrid Runbook Worker, consulte [portas de função do Hybrid Worker](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, você pode baixar a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Este arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP, é postado semanalmente.

## <a name="search-logs"></a>Pesquisa da logs

Além dos detalhes fornecidos no portal do Azure, você pode fazer as pesquisas nos logs. Nas páginas de solução, selecione **Log Analytics**. O painel **Pesquisa de Log** é aberto.

Você também pode aprender a personalizar as consultas ou usá-las de diferentes clientes e mais visitando: [Documentação da API de pesquisa do Log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Consultas de exemplo

As seções a seguir fornecem consultas de log de exemplo para registros de atualizações que são coletados por essa solução:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas de Avaliação de VM única do Azure (Windows)

Substitua o valor VMUUID pelo VM GUID da máquina virtual que você está consultando. Você pode encontrar o VMUUID que deveria ser usado executando a seguinte consulta no Log Analytics: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação de VM única do Azure (Linux)

Para algumas distribuições de Linux, há uma incompatibilidade [endianness](https://en.wikipedia.org/wiki/Endianness) com o valor VMUUID que vem do Azure Resource Manager e o que é armazenado no Log Analytics. A consulta a seguir procura uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato big-endian e little-endian do GUID para retornar corretamente os resultados. Você pode encontrar o VMUUID que deveria ser usado executando a seguinte consulta no Log Analytics: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMs

##### <a name="computers-summary"></a>Resumo de computadores

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Lista de computadores

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="integrate-with-system-center-configuration-manager"></a>Integração com o System Center Configuration Manager

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade do Configuration Manager para ajudá-los a gerenciar as atualizações de software. Configuration Manager é parte de seu ciclo de gerenciamento de atualizações de software (SUM).

Para saber como integrar a solução de gerenciamento ao System Center Configuration Manager, veja [Integrar o System Center Configuration Manager ao Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Aplicar patch em computadores Linux

As seções a seguir explicam possíveis problemas com aplicação de patch do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações de nível de sistema operacional inesperadas

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de execuções do Gerenciamento de Atualizações, utilize o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64.

![Pacotes a serem excluídos do Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Patches críticos / de segurança não são aplicados

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Isso filtra as atualizações aplicadas às que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como o Gerenciamento de Atualizações realiza o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas no Gerenciamento de Atualizações como tendo impacto de segurança, embora o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo impacto de segurança nesse computador e as atualizações não são aplicadas.

No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

Implantar atualizações por classificação de atualizações não funciona em CentOS fora da caixa. Para SUSE, selecionar *somente* 'Outras atualizações', já que a classificação pode resultar em que algumas atualizações de segurança também sejam instaladas se as atualizações de segurança relacionados ao zypper (gerenciamento de pacotes) ou suas dependências forem necessárias primeiro. Essa é uma limitação do zypper. Em alguns casos, você pode requerer executar novamente a implantação da atualização para verificar o log de atualização.

## <a name="troubleshoot"></a>Solucionar problemas

Para saber como solucionar problemas no seu Gerenciamento de Atualizações, consulte [Solucionar problemas no Gerenciador de Atualizações](troubleshoot/update-management.md)

## <a name="next-steps"></a>Próximas etapas

Continue no tutorial para saber como gerenciar atualizações para as máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-update-management.md)

* Use pesquisas de log no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados das atualizações.
* [Crie alertas](../log-analytics/log-analytics-alerts.md) quando atualizações críticas forem detectadas como ausentes de um computador ou quando um computador tiver as atualizações automáticas desabilitadas.
