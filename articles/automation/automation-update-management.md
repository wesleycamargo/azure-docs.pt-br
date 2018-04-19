---
title: Solução Gerenciamento de Atualizações no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar essa solução para gerenciar atualizações para os computadores Windows e Linux.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c9a546f82d3300b37f861fff53421ebbf9fe3804
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="update-management-solution-in-azure"></a>Solução Gerenciamento de Atualizações no Azure

A solução Gerenciamento de Atualizações na Automação do Azure permite que você gerencie atualizações de segurança do sistema operacional para os computadores Windows e Linux implantados no Azure, em ambientes locais ou em outros provedores de nuvem. Você pode avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar rapidamente o processo de instalação das atualizações necessárias para os servidores.

Você pode habilitar o gerenciamento de atualização de máquinas virtuais diretamente da sua conta da [Automação do Azure](automation-offering-get-started.md).
Para saber como habilitar o gerenciamento de atualização de máquinas virtuais de sua conta de automação, veja [Gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md).

## <a name="solution-overview"></a>Visão geral da solução

Computadores gerenciados pelo gerenciamento de atualizações usam as configurações a seguir para realizar implantações de atualização e avaliação:

* Microsoft Monitoring Agent para Windows ou Linux
* DSC (PowerShell Desired State Configuration) para Linux
* Hybrid Runbook Worker de Automação
* Microsoft Update ou Windows Server Update Services para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados, indicando como a solução avalia e aplica atualizações de segurança a todos os computadores Linux e servidores Windows conectados em um espaço de trabalho.

![Fluxo do processo de gerenciamento de atualizações](media/automation-update-management/update-mgmt-updateworkflow.png)

Depois que um computador executa uma verificação de conformidade da atualização, o agente encaminha as informações em massa ao Log Analytics. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão. Além do agendamento da verificação, a verificação de conformidade de atualização será iniciada em 15 minutos se o MMA (Microsoft Monitoring Agent) for reiniciado antes da instalação da atualização e após a instalação da atualização. Com um computador Linux, a verificação de conformidade é executada a cada três horas por padrão, e uma verificação de conformidade é iniciada em 15 minutos, se o agente MMA é reiniciado.

A solução relata o grau de atualização do computador com base na fonte com a qual você está configurado para realizar a sincronização. Se o computador do Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizada pela última vez com o Microsoft Update, os resultados poderão diferir do que é mostrado pelo Microsoft Updates. O mesmo se aplica a computadores Linux que estão configurados para relatar para um repositório local em vez de um repositório público.

Você pode implantar e instalar atualizações de software em computadores que precisam de atualizações, criando uma implantação agendada. As atualizações classificadas como *Opcional* não são incluídas no escopo de implantação para computadores Windows, somente as atualizações necessárias. A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, explicitamente especificando computadores ou selecionando um [grupo de computadores](../log-analytics/log-analytics-computer-groups.md) que se baseia em pesquisas de log de determinado conjunto de computadores. Você também pode especificar uma agenda para aprovar e designar um período de tempo quando é permitido que as atualizações sejam instaladas. As atualizações são instaladas por runbooks na Automação do Azure. Você não consegue exibir esses runbooks e eles não exigem nenhuma configuração. Quando uma Implantação de Atualizações é criada, ela cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. Esse runbook mestre inicia um runbook filho em cada agente que executa a instalação de atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Uma verificação é executada primeiro para verificar se as atualizações ainda são necessárias e as instala. Para computadores cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de clientes com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte: 

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporte apenas para avaliações de atualização         |
|Windows Server 2008 R2 SP1 e superior     |.NET Framework 4.5 e WMF 5.0 ou posterior são necessários para Windows Server 2008 R2 SP1        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 12.04 LTS e posterior x86/x64       |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

### <a name="unsupported-client-types"></a>Tipos de clientes sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Client     | Não há suporte para sistemas operacionais de cliente (Windows 7, Windows 10 e etc.).        |
|Windows Server 2016 Nano Server     | Sem suporte       |

### <a name="client-requirements"></a>Requisitos do cliente

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para comunicarem-se com um servidor WSUS (Windows Server Update Services) ou terem acesso ao Microsoft Update. O Gerenciamento de Atualizações pode ser usado com o System Center Configuration Manager. Para saber mais sobre cenários de integração, veja [Integrar o System Center Configuration Manager ao Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../log-analytics/log-analytics-agent-windows.md) é obrigatório. Esse agente será instalado automaticamente se você estiver instalando uma VM do Azure.

#### <a name="linux"></a>Linux

Para o Linux, o computador deve ter acesso a um repositório de atualizações, que pode ser privado ou público. Um agente do OMS para Linux configurado para emitir relatórios para vários espaços de trabalho do Log Analytics não tem suporte nessa solução.

Para obter informações sobre como instalar o agente do OMS para Linux e fazer o download da versão mais recente, consulte [Agente do Operations Management Suite para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o Agente do OMS para Windows, confira [Operations Management Suite Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa ter permissões específicas. Para saber mais sobre essas permissões, visite [Acesso baseado em Função - Gerenciamento de atualizações](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Componentes da solução

Essa solução consiste nos seguintes recursos que são adicionados à sua conta de Automação e a agentes conectados diretamente ou ao grupo de gerenciamento conectado do Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker

Depois que você habilita essa solução, qualquer computador com Windows conectado diretamente a seu espaço de trabalho do Log Analytics é automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nessa solução. Para cada computador Windows gerenciado pela solução, ele é listado na página de grupos de trabalho Hybrid como um grupo de trabalho híbrido do Sistema para a conta de Automação do Azure, seguindo a convenção de nomenclatura *Hostname FQDN_GUID*. Não é possível direcionar esses grupos com runbooks em sua conta, caso contrário eles falham. Esses grupos devem dar suporte à solução de gerenciamento.

No entanto, você pode adicionar os computadores com Windows a um grupo de Hybrid Runbook Worker em sua conta de Automação para dar suporte a runbooks de automação enquanto você estiver usando a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gerenciamento

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente após a adição dessa solução. Não há nada para configurar ou gerenciar com esses pacotes de gerenciamento.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* MP de Implantação de Atualizações

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Confirmar se computadores não Azure estão integrados

Para confirmar se os computadores conectados diretamente estão comunicando-se com o Log Analytics, depois de alguns minutos você poderá executar a pesquisa de logs a seguir:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Em um computador Windows, você pode examinar o seguinte para verificar a conectividade do agente com o Log Analytics:

1. Abra o Microsoft Monitoring Agent no Painel de Controle e, na guia **Análise de Log do Azure**, o agente exibe uma mensagem dizendo: **O Microsoft Monitoring Agent se conectou com êxito ao Log Analytics**.   
2. Abra o Log de Eventos do Windows, navegue até **Logs de Aplicativos e Serviços\Operations Manager** e procure as IDs de Evento 3000 e 5002 do Conector de Serviço de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho do Log Analytics e está recebendo a configuração.

Se o agente puder se comunicar com o serviço Log Analytics e estiver configurado para se comunicar com a Internet através de um servidor proxy ou firewall, verifique se o servidor proxy ou firewall está configurado corretamente examinando [Configuração de rede para o agente do Windows](../log-analytics/log-analytics-agent-windows.md) ou [Configuração de rede para o agente do Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os sistemas Linux estiverem configurados para comunicar-se com um proxy ou Gateway OMS e se você estiver integrando essa solução, atualize as permissões de *proxy.conf* para conceder a permissão de leitura de grupo omiuser no arquivo, executando os comandos a seguir: `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux recém-adicionados mostrarão um status de **Atualizado** após ter sido realizada uma avaliação. Esse processo pode levar até seis horas.

Para confirmar se um grupo de gerenciamento do Operations Manager está se comunicando com o Log Analytics, confira [Validar a integração do Operations Manager com o Log Analytics](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Coleta de dados

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Com suporte | DESCRIÇÃO |
| --- | --- | --- |
| Agentes do Windows |sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e inicia a instalação de atualizações necessárias. |
| Agentes do Linux |sim |A solução coleta informações sobre atualizações do sistema de agentes Linux e inicia a instalação das atualizações necessárias em distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |sim |A solução coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectados.<br>Uma conexão direta do agente do Operations Manager ao Log Analytics não é necessária. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics. |

### <a name="collection-frequency"></a>Frequência de coleta

Para cada computador gerenciado do Windows, uma verificação é executada duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status for alterado e, nesse caso, é iniciada uma verificação de conformidade. Para cada computador Linux gerenciado, uma verificação é executada a cada três horas.

Pode demorar de 30 minutos a seis horas para o painel exibir dados atualizados em computadores gerenciados.

## <a name="viewing-update-assessments"></a>Exibição de avaliações de atualização

Clique no **Gerenciamento de Atualizações** na sua conta de automação para exibir o status de dos computadores.

Essa exibição fornece informações sobre os computadores, atualizações ausentes, implantações de atualização e implementações de atualização agendada.

É possível executar uma pesquisa de logs que retorna informações no computador, atualização ou implantação, selecionando o item na lista. Isso abre a página **Pesquisa de Logs** com uma consulta para o item selecionado.

## <a name="installing-updates"></a>Instalação de atualizações

Depois de avaliar atualizações para todos os computadores com Windows e Linux em seu espaço de trabalho, você pode ter as necessárias atualizações instaladas, criando uma *Implantação de Atualizações*. Uma Implantação de Atualizações é uma instalação agendada de atualizações necessárias para um ou mais computadores. Você especifica a data e hora para a implantação, além de um computador ou um grupo de computadores que devem ser incluídos no escopo de uma implantação. Para saber mais sobre grupos de computadores, confira [Grupos de computadores na Análise de Log](../log-analytics/log-analytics-computer-groups.md). Quando você inclui grupos de computadores em sua implantação de atualização, a associação de grupo é avaliada apenas uma vez no momento da criação da agenda. As alterações subsequentes em um grupo não são refletidas. Para solucionar esse problema, exclua a implantação de atualização agendada e recrie-a.

> [!NOTE]
> Máquinas virtuais do Windows implantadas no Azure Marketplace por padrão são definidas para receber atualizações automáticas do Serviço Windows Update. Esse comportamento não é alterado depois de adicionar essa solução ou máquinas virtuais do Windows ao seu espaço de trabalho. Se você não gerenciou atualizações ativamente com essa solução, o comportamento padrão (aplicar automaticamente as atualizações) é aplicado.

Para evitar atualizações aplicadas fora da janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para saber mais sobre como configurar isso, veja [o tópico Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

As máquinas virtuais criadas por meio das imagens do RHEL (Red Hat Enterprise Linux) sob demanda disponíveis no Azure Marketplace são registradas para acessar a [RHUI (Infraestrutura de Atualização do Red Hat)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) implantada no Azure. Qualquer distribuição do Linux deve ser atualizada nos repositórios de distribuição de arquivo online seguindo os métodos com suporte.

## <a name="viewing-missing-updates"></a>Exibir atualizações ausentes

Clique em **Atualizações ausentes** para exibir a lista de atualizações que estão faltando nos computadores. Cada atualização é listada e exibe informações em relação ao número de computadores que exigem a atualização, o sistema operacional e o link para obter mais informações. Cada atualização pode ser selecionada e a página **Pesquisa de Logs** é exibida e mostra mais detalhes sobre as atualizações.

## <a name="viewing-update-deployments"></a>Exibição de implantações de atualização

Clique na guia **Atualizar Implantações** para exibir a lista das Implantações com Atualizações existentes. Ao clicar em qualquer uma das implementações de atualização na tabela, é aberta a página **Atualizar Execução de Implantação** para essa implementação de atualização.

![Visão geral dos resultados da Implantação de Atualizações](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Criação de uma Implantação de Atualizações

Crie uma nova Implantação de Atualizações clicando no botão **Agendar implantação de atualizações** na parte superior da tela para abrir a página **Nova Implantação de Atualizações**. Você deve fornecer valores para as propriedades na tabela a seguir:

| Propriedade | DESCRIÇÃO |
| --- | --- |
| NOME |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Linux ou Windows|
| Computadores para atualizar |Selecione uma pesquisa Salva ou escolha Computador no menu suspenso e selecione computadores individuais |
|Classificações de origem|Selecione todas as classificações de atualização necessárias|
|Atualizações para excluir|Digite todos os KBs para excluir sem o prefixo 'KB'|
|Configurações de agendamento|Selecione o tempo para iniciar e selecione Uma Vez ou recorrente para a recorrência|
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos e não superior a 6 horas |

## <a name="search-logs"></a>Pesquisa da logs

Além dos detalhes fornecidos no portal, é possível fazer as pesquisas nos logs. Com a página **Controle de Alterações** aberta, clique em **Log Analytics**, isso abrirá a página **Pesquisa de Logs**

### <a name="sample-queries"></a>Consultas de exemplo

A tabela a seguir fornece pesquisas de logs de exemplo para os registros de atualização coletados por essa solução:

| Consultar | DESCRIÇÃO |
| --- | --- |
|Atualizar<br>&#124; onde UpdateState == "Needed" e Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |Todos os computadores com atualizações ausentes<br>Adicione uma das opções a seguir para limitar o SO:<br>OSType = "Windows"<br>OSType == "Linux" |
| Atualizar<br>&#124; onde UpdateState == "Needed" e Optional == false<br>&#124; onde Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |Atualizações ausentes para um computador específico (substitua o valor pelo nome de seu próprio computador)|
| Evento<br>&#124; onde EventLevelName == "error" e Computer em ((Update &#124; onde (Classification == "Security Updates" ou Classification == "Critical Updates")<br>&#124; onde UpdateState == "Needed" e Optional == false <br>&#124; distinct Computer)) |Eventos de erro para computadores sem as atualizações críticas ou de segurança obrigatórias |
| Atualizar<br>&#124; onde UpdateState == "Needed" e Optional == false<br>&#124; distinct Title |Atualizações ausentes distintas em todos os computadores |
| UpdateRunProgress<br>&#124; onde InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |Computadores com atualizações que falharam em uma execução de atualização<br>Adicione uma das opções a seguir para limitar o SO:<br>OSType = "Windows"<br>OSType == "Linux" |
| Atualizar<br>&#124; onde OSType == "Linux"<br>&#124; onde UpdateState != "Not needed" e (Classification == "Critical Updates" ou Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |Lista de todos os computadores Linux com uma atualização de pacote disponível que aborda vulnerabilidade crítica ou de segurança | 
| UpdateRunProgress<br>&#124; onde UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|Computadores que foram atualizados nesta atualização executam (substitua o valor pelo nome de sua implantação de atualização | 

## <a name="integrate-with-system-center-configuration-manager"></a>Integração com o System Center Configuration Manager

Os clientes que investiram no System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade dele no gerenciamento de atualizações de software como parte do ciclo de SUM (gerenciamento de atualização de software) deles.

Para saber como integrar a solução de gerenciamento ao System Center Configuration Manager, veja [Integrar o System Center Configuration Manager ao Gerenciamento de Atualizações](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Aplicação de patch de computadores Linux

As seções a seguir explicam possíveis problemas com aplicação de patch do Linux.

### <a name="package-exclusion"></a>Exclusão de pacotes

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes como um administrador localmente no computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de execuções do Gerenciamento de Atualizações, utilize o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64

![Pacotes a serem excluídos do Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Patches de segurança não estão sendo aplicados

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Isso filtra as atualizações aplicadas às que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada. Como o Gerenciamento de Atualizações realiza o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas no Gerenciamento de Atualizações como tendo impacto de segurança, embora o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo impacto de segurança nesse computador, e não são aplicadas. No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização pode não funcionar no openSUSE Linux devido ao modelo de patches diferente usado.

## <a name="troubleshooting"></a>solução de problemas

Esta seção fornece informações para ajudar a solucionar problemas da solução de Gerenciamento de Atualizações.

Se você encontrar problemas ao tentar integrar a solução ou uma máquina virtual, procure no log de eventos **Logs de Aplicativo e Serviços\Operations Manager** eventos com a ID 4502 e uma mensagem de evento contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. A tabela a seguir realça mensagens de erro específicas e uma possível resolução para cada uma.

| Mensagem | Motivo | Solução |
|----------|----------|----------|
| Não é possível registrar a máquina para gerenciamento de patch,<br>Falha no registro com exceção<br>System.InvalidOperationException: {"Message":"A máquina já está<br>registrada em uma conta diferente. "} | A máquina já foi integrada em outro espaço de trabalho para Gerenciamento de Atualizações | Execute a limpeza de artefatos antigos [excluindo o grupo de runbook híbrido](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|
| Não é possível registrar a máquina para gerenciamento de patch,<br>Falha no registro com exceção<br>System.Net.Http.HttpRequestException: ocorreu um erro ao enviar a solicitação. ---><br>System.Net.WebException: a conexão subjacente<br>foi fechada: ocorreu um erro<br>inesperado em um recebimento. ---> System.ComponentModel.Win32Exception:<br>O cliente e o servidor não podem se comunicar,<br>pois não possuem um algoritmo em comum | Proxy/Gateway/Firewall está bloqueando a comunicação | [Revisar os requisitos de rede](automation-offering-get-started.md#network-planning)|
| Não é possível registrar a máquina para gerenciamento de patch,<br>Falha no registro com exceção<br>Newtonsoft.Json.JsonReaderException: Erro ao analisar um valor infinito positivo. | Proxy/Gateway/Firewall está bloqueando a comunicação | [Revisar os requisitos de rede](automation-offering-get-started.md#network-planning)|
| O certificado apresentado pelo serviço <wsid>.oms.opinsights.azure.com<br>não foi emitido por uma autoridade de certificação<br>usado para serviços da Microsoft. Contato<br>seu administrador de rede para ver se há um proxy em execução que intercepta<br>a comunicação TLS/SSL. |Proxy/Gateway/Firewall está bloqueando a comunicação | [Revisar os requisitos de rede](automation-offering-get-started.md#network-planning)|
| Não é possível registrar a máquina para gerenciamento de patch,<br>Falha no registro com exceção<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>Falha ao criar um certificado autoassinado. ---><br>System.UnauthorizedAccessException: acesso negado. | Falha ao gerar certificado autoassinado | Verifique se a conta do sistema tem<br>acesso de leitura à pasta:<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|

## <a name="next-steps"></a>Próximas etapas

Continue no tutorial para saber como gerenciar atualizações para as VMs do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-troubleshoot-changes.md)

* Use Pesquisas de Log no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados das atualizações.
* [Crie alertas](../log-analytics/log-analytics-alerts.md) quando atualizações críticas forem detectadas como ausentes de um computador ou quando um computador tiver as atualizações automáticas desabilitadas.
