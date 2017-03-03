---
title: "Mapa do Serviço no OMS (Operations Management Suite) | Microsoft Docs"
description: "O Mapa do Serviço é uma solução do OMS (Operations Management Suite) que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços.  Este artigo fornece detalhes sobre a implantação do Mapa do Serviço em seu ambiente e sobre como usá-lo em diversos cenários."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: 638410921c6dad72e1bbe0c035243cea70a3deb1
ms.openlocfilehash: 4bab1ba9c30cee50baeddc06931a3997aac0f33f
ms.lasthandoff: 02/16/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>Como usar a solução Mapa do Serviço no OMS (Operations Management Suite)
O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais.  O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.

Este artigo descreve os detalhes de como usar o Mapa do Serviço.  Para saber mais sobre como configurar o Mapa do Serviço e os agentes de integração, confira [Configurar a solução Mapa do Serviço no OMS (Operations Management Suite)](operations-management-suite-service-map-configure.md)


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de uso: Fazer com que seus processos de TI reconheçam a dependência

### <a name="discovery"></a>Descoberta
O Mapa do Serviço compila automaticamente um mapa de referência comum de dependências em seus servidores, processos e serviços de terceiros.  Ele descobre e mapeia todas as dependências TCP, identificando conexões surpresa, sistemas remotos de terceiros dos quais você depende e dependências para áreas escuras tradicionais da rede, como o Active Directory.  O Mapa do Serviço descobre conexões de rede com falha que seus sistemas gerenciados estão tentando realizar, ajudando você a identificar possíveis erros de configuração do servidor, interrupções de serviço e problemas de rede.

### <a name="incident-management"></a>Gerenciamento de incidentes
O Mapa do Serviço ajuda a eliminar as suposições de isolamento do problema, mostrando a você como os sistemas estão conectados e afetando uns aos outros.  Além das conexões com falha, informações sobre clientes conectados ajudam a identificar balanceadores de carga configurados incorretamente, carga surpreendente ou excessiva em serviços essenciais e clientes não autorizados, como computadores de desenvolvedores conversando com sistemas de produção.  Fluxos de trabalho integrados com Controle de alteração do OMS também permitem que você verifique se um evento de alteração em uma máquina de back-end ou serviço pode explicar a causa raiz de um incidente.

### <a name="migration-assurance"></a>Garantia de migração
O Mapa do Serviço permite que você planeje, acelere e valide efetivamente as migrações do Azure, garantindo que nada seja deixado para trás e não ocorram interrupções ou surpresas.  Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos, avaliar a capacidade e a configuração do sistema e identificar se um sistema em execução ainda está atendendo aos usuários ou é candidato para encerramento em vez de migração.  Após a movimentação, você pode verificar a carga e a identidade do cliente para verificar se os sistemas de teste e os clientes estão se conectando.  Se o seu planejamento de sub-rede e definições de firewall tiverem problemas, as conexões com falha em mapas do Mapa do Serviço apontarão para os sistemas que precisam de conectividade.

### <a name="business-continuity"></a>Continuidade dos negócios
Se você estiver usando o Azure Site Recovery e precisar de ajuda para definir a sequência de recuperação para seu ambiente de aplicativo, o Mapa do Serviço poderá mostrar automaticamente como os sistemas dependem uns dos outros para assegurar que seu plano de recuperação seja confiável.  Escolhendo um servidor crítico e exibindo seus clientes, você pode identificar os sistemas front-end que devem ser recuperados somente após a restauração e disponibilização desse servidor crítico.  Por outro lado, examinando as dependências de back-end de um servidor crítico, você pode identificar os sistemas que devem ser recuperados antes de seu sistema de foco ser restaurado.

### <a name="patch-management"></a>Gerenciamento de patch
O Mapa do Serviço aprimora seu uso da Avaliação de atualização do sistema OMS, mostrando que outras equipes e servidores dependem de seu serviço, para que você possa notificá-los com antecedência antes de desativar seus sistemas para aplicação de patch.  O Mapa do Serviço também aprimora o gerenciamento de patch no OMS mostrando se os seus serviços estão disponíveis e conectados corretamente após terem sido corrigidos e reiniciados.


## <a name="mapping-overview"></a>Visão geral do mapeamento
Os agentes do Mapa do Serviço coletam informações sobre todos os processos conectados a TCP no servidor onde estão instalados, bem como detalhes sobre as conexões de entrada e saída de cada processo.  Usando a Lista de Máquinas no lado esquerdo da solução Mapa do Serviço, os computadores com agentes do Mapa do Serviço podem ser selecionados a fim de visualizar suas dependências em um intervalo de tempo selecionado.  O mapa de dependência da máquina se concentra em uma máquina específica e mostra todas as máquinas que são clientes TCP diretos ou servidores dessa máquina.

![Visão geral do Mapa do Serviço](media/oms-service-map/service-map-overview.png)

Máquinas podem ser expandidas no mapa para mostrar os processos em execução com conexões de rede ativas durante o intervalo de tempo selecionado.  Quando um computador remoto com um agente do Mapa do Serviço é expandido para mostrar os detalhes do processo, somente os processos de comunicação com a máquina de foco são exibidos.  A contagem de máquinas de front-end sem agente se conectando com a máquina de foco é indicada no lado esquerdo dos processos aos quais elas se conectam.  Se o computador em foco estiver fazendo uma conexão com um computador de back-end sem um agente, o servidor de back-end será incluído em um Grupo de Portas de Servidor com outras conexões com o mesmo número de porta.

Por padrão, os mapas do Mapa do Serviço mostram os últimos 10 minutos de informações de dependência.  Usando os controles de tempo na parte superior esquerda, os mapas podem ser consultados com relação a intervalos históricos, até uma hora, a fim de mostrar como eram as dependências no passado, por exemplo, durante um incidente ou antes de uma alteração.    Os dados do Mapa do Serviço são armazenados por 30 dias em espaços de trabalho pagos, e por sete dias em espaços de trabalho gratuitos.

## <a name="status-badges"></a>Notificações de status
Na parte inferior de cada servidor no mapa pode haver uma lista de notificações de status que passam informações sobre o status do servidor.  As notificações indicam que há algumas informações relevantes para o servidor de uma das integrações de solução do OMS.  Clicar em uma notificação levará você diretamente até os detalhes do status no painel à direita.  As notificações de status disponíveis atualmente incluem Alertas, Alterações, Segurança e Atualizações.

![Conexões com falha](media/oms-service-map/status-badges.png)

## <a name="failed-connections"></a>Conexões com falha
As Conexões com falha são mostradas em mapas do Mapa do Serviço para processos e computadores, com uma linha vermelha tracejada mostrando se um sistema cliente não puder acessar um processo ou uma porta.  Conexões com falha são reportadas de qualquer sistema com um agente do Mapa do Serviço implantado, se esse sistema estiver tentando a conexão com falha.  O Mapa do Serviço mede isso observando os soquetes TCP que falham ao estabelecer uma conexão.  Isso pode ocorrer devido a um firewall, uma configuração errada no cliente ou servidor ou um serviço remoto não disponível.

![Conexões com falha](media/oms-service-map/failed-connections.png)

Entender as conexões com falha pode ajudar com a solução de problemas, validação da migração, análise de segurança e noções básicas sobre arquitetura em geral.  Às vezes, as conexões com falha são inofensivas, mas normalmente apontam diretamente para um problema, como um ambiente de failover ficando inacessível de repente ou duas camadas de aplicativo sem comunicação após uma migração na nuvem.

## <a name="client-groups"></a>Grupos de Clientes
Os Grupos de Clientes são caixas no mapa que representam computadores cliente que não possuem Agentes de Dependência.  Um único Grupo de Clientes representa os clientes de um processo individual.

![Grupos de clientes](media/oms-service-map/client-groups.png)

Para ver os endereços IP dos servidores em um Grupo de Clientes, selecione o grupo.  O conteúdo do grupo será listado no Painel Propriedades.

![Propriedades do grupo de clientes](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de Portas do Servidor
Os Grupos de Portas do Servidor são caixas que representam portas de servidor em servidores que não possuem Agentes de Dependência.  A caixa listará a porta do servidor com uma contagem do número de servidores que têm conexões com essa porta.  Expanda a caixa para ver conexões e servidores individuais.  Se houver apenas um servidor na caixa, o nome ou endereço IP será listado.

![Grupos de portas do servidor](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu de contexto
Clicar nos três pontos no canto superior direito de qualquer servidor mostrará o menu de contexto do servidor.

![Conexões com falha](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Carregar Mapa do Servidor
Carregar Mapa do Servidor conduzirá até um novo mapa com o servidor selecionado como a nova Máquina de Foco.

### <a name="showhide-self-links"></a>Mostrar/Ocultar Self Links
Mostrar Self Links redesenhará o nó do servidor incluindo quaisquer self links, que são conexões TCP que começam e terminam em processos no servidor.  Se os self links estiverem em exibição, o menu mudará para Ocultar Self Links, permitindo que os usuários ativem ou desativem o desenho de self links.

## <a name="computer-summary"></a>Resumo do computador
O painel Resumo do Computador inclui uma visão geral do Sistema operacional do servidor e contagens de dependência junto com diversos dados de outras soluções do OMS, incluindo Métricas de Desempenho, Controle de Alterações, Segurança, Atualizações etc.

![Resumo do computador](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades do computador e do processo
Ao navegar em um mapa do Mapa do Serviço, você pode selecionar máquinas e processos para obter contexto adicional sobre suas propriedades.  As máquinas fornecem informações sobre o nome DNS, endereços IPv4, capacidade de CPU e Memória, Tipo de VM, versão do Sistema Operacional, horário da Última Reinicialização e as IDs de seus agentes OMS e Mapa do Serviço.

![Propriedades do computador](media/oms-service-map/machine-properties.png)

Os detalhes do processo são coletados nos metadados do sistema operacional sobre processos em execução, incluindo nome do processo, descrição do processo, nome de usuário e domínio (no Windows), nome da empresa, nome do produto, versão do produto, diretório de trabalho, linha de comando e hora de início do processo.

![Propriedades do processo](media/oms-service-map/process-properties.png)

O painel Resumo do Processo fornece informações adicionais sobre a conectividade do processo, incluindo suas portas associadas, conexões de entrada e saída e conexões com falha.

![Resumo do processo](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>Integração de alertas do OMS
O Mapa do Serviço integra-se aos Alertas do OMS para mostrar os alertas acionados para o servidor selecionado no intervalo de tempo selecionado.  O servidor mostrará um ícone se houver alertas atuais, e o painel de Alertas do Computador listará os alertas

![Painel Alertas do Computador](media/oms-service-map/machine-alerts.png)

Observe que para o Mapa do Serviço ser capaz de exibir alertas relevantes, é necessário criar uma regra de alerta para que seja acionado para um computador específico.  Para criar alertas apropriadas:
- Inclua uma cláusula para agrupar por computador: "intervalo de 1 minuto por Computador"
- Escolha o alerta com base na medida Métrica

![Configuração do alerta](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>Integração de eventos de Log do OMS
O Mapa do Serviço integra-se à Pesquisa de Logs para mostrar uma contagem de todos os eventos de log disponíveis para o servidor selecionado no intervalo de tempo selecionado.  Você pode clicar em qualquer linha na lista de contagens de eventos para pular para a Pesquisa de Logs e ver os eventos de log individuais.

![Eventos de log](media/oms-service-map/log-events.png)

## <a name="oms-change-tracking-integration"></a>Integração de controle de alterações do OMS
A integração do Mapa do Serviço com o Controle de Alterações é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do OMS.

O painel Controle de Alteração da Máquina mostra uma lista de todas as alterações, com a mais recente primeiro, juntamente com um link para detalhar a Pesquisa de Log para obter detalhes adicionais.
![Painel de Controle de Alteração da Máquina](media/oms-service-map/change-tracking.png)

Veja a seguir um modo de exibição detalhado do evento de Alteração de Configuração após selecionar **Mostrar no Log Analytics**.
![Evento de Alteração de Configuração](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>Integração de desempenho do OMS
O painel Desempenho do Computador mostra as métricas de desempenho padrão para o servidor selecionado.  As métricas incluem Utilização de CPU, Utilização de Memória, Bytes de Rede Enviados e Recebidos, e uma lista dos principais processos por Bytes de Rede enviados e recebidos.
![Painel de Controle de Alteração da Máquina](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>Integração de segurança do OMS
A integração do Mapa do Serviço com Segurança e Auditoria é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do OMS.

O painel Segurança do Computador mostra dados da solução de Segurança e Auditoria do OMS para o servidor selecionado.  O painel listará um resumo de quaisquer problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado.  Clicar em qualquer um dos problemas de segurança mostrará uma Pesquisa de Log com detalhes sobre os problemas.
![Painel de Controle de Alteração da Máquina](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>Integração de atualizações do OMS
A integração do Mapa do Serviço com o Gerenciamento de Atualização é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do OMS.

O painel Atualizações do Computador mostra dados da solução de Gerenciamento de Atualização do OMS para o servidor selecionado.  O painel listará um resumo de quaisquer atualizações pendentes para o servidor durante o intervalo de tempo selecionado.
![Painel de Controle de Alteração da Máquina](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registros do Log Analytics
Dados de inventário do processo e do computador do Mapa do Serviço estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) no Log Analytics.  Isso pode ser aplicado a cenários, incluindo o planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho do ad-hoc.

Um registro é gerado por hora para cada computador e processo exclusivo, além dos registros gerados quando um processo ou computador inicia ou é integrado ao Mapa do Serviço.  Esses registros têm as propriedades descritas nas tabelas a seguir.  Os campos e valores nos eventos de ServiceMapComputer_CL mapeiam para campos do recurso Máquina na API de ARM do ServiceMap.  Os campos e valores nos eventos de ServiceMapProcess_CL mapeiam para os campos do recurso Processo na API de ARM do ServiceMap.  O campo ResourceName_s corresponde ao campo de nome do recurso correspondente do ARM. Observação - como os recursos do Service Map aumentam, esses campos estão sujeitos a alterações.


Há propriedades geradas internamente que você pode usar para identificar computadores e processos exclusivos:

- Computador - Use ResourceId ou ResourceName_s para identificar exclusivamente um computador em um Espaço de Trabalho do OMS.
- Processo - Use ResourceId para identificar exclusivamente um processo dentro de um Espaço de Trabalho do OMS. ResourceName_s é exclusivo dentro do contexto do computador no qual o processo está em execução (MachineResourceName_s) 

Como vários registros podem existir para um determinado processo e computador em um determinado intervalo de tempo, as consultas podem retornar mais de um registro para o mesmo computador ou processo. Para incluir somente o registro mais recente adicione "| dedup ResourceId" à consulta.

### <a name="servicemapcomputercl-records"></a>Registros ServiceMapComputer_CL
Registros com um tipo de **ServiceMapComputer_CL** têm dados de inventário para servidores com agentes do Mapa do Serviço.  Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificador exclusivo para a máquina dentro do espaço de trabalho |
| ResourceName_s | identificador exclusivo para a máquina no espaço de trabalho |
| ComputerName_s | FQDN do computador |
| Ipv4Addresses_s | uma lista de endereços IPv4 do servidor |
| Ipv6Addresses_s | uma lista de endereços IPv6 do servidor |
| DnsNames_s | matriz de nomes de DNS |
| OperatingSystemFamily_s | windows ou linux |
| OperatingSystemFullName_s | nome completo do sistema operacional  |
| Bitness_s | número de bits da máquina (32 bits) ou (64 bits) |
| PhysicalMemory_d | memória física em MB |
| Cpus_d | número de cpus |
| CpuSpeed_d | velocidade da cpu em MHz|
| VirtualizationState_s | "desconhecido", "físico", "virtual", "hipervisor" |
| VirtualMachineType_s | "hiperv", "vmware" etc. |
| VirtualMachineNativeMachineId_g | ID da VM conforme atribuído pelo seu hipervisor |
| VirtualMachineName_s | Nome da VM |
| BootTime_t | tempo de inicialização |



### <a name="servicemapprocesscl-type-records"></a>Registros do tipo ServiceMapProcess_CL Type
Registros com um tipo de **ServiceMapProcess_CL** têm dados de inventário para processos conectados com TCP em servidores com agentes do Mapa do Serviço.  Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | identificador exclusivo para o processo dentro do espaço de trabalho |
| ResourceName_s | identificador exclusivo para o processo dentro da máquina na qual está sendo executado|
| MachineResourceName_s | nome do recurso máquina |
| ExecutableName_s | nome executável do processo |
| StartTime_t | hora de início do pool de processos |
| FirstPid_d | primeiro pid no pool de processos |
| Description_s | descrição do processo |
| CompanyName_s | nome da empresa |
| InternalName_s | nome interno |
| ProductName_s | nome do produto |
| ProductVersion_s | versão do produto |
| FileVersion_s | versão do arquivo |
| CommandLine_s | linha de comando |
| ExecutablePath _s | caminho até o arquivo executável |
| WorkingDirectory_s | diretório de trabalho |
| UserName | conta na qual o processo está em execução |
| UserDomain | domínio no qual o processo está em execução |


## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

### <a name="list-all-known-machines"></a>Lista todas as máquinas conhecidas
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista a capacidade de memória física de todos os computadores gerenciados.
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>Listar o nome, DNS, IP e sistema operacional do computador.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comando
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Localizar uma máquina (registro mais recente) por nome de recurso
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar uma máquina (registro mais recente) por endereço ip
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>Listar todos os processos conhecidos em uma determinada máquina
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores que executam SQL
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista de todas as versões de produto exclusivas de curl em meu datacenter
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um Grupo de Computadores de todos os computadores executando o CentOS
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>API REST
Todos os dados do servidor, do processo e de dependência no Mapa de Serviço estão disponíveis por meio da [API REST do Mapa de Serviço](https://docs.microsoft.com/en-us/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Mapa do Serviço. A Microsoft usa esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço Mapa do Serviço. Os dados incluem informações sobre a configuração do software, como o sistema operacional e a versão, e também incluem o endereço IP, o nome DNS e nome da Estação de Trabalho a fim de fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para saber mais sobre o uso e a coleta de dados, veja a [Declaração de Privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [pesquisas de logs](../log-analytics/log-analytics-log-searches.md) no Log Analytics para recuperar os dados coletados pelo Mapa do Serviço.


## <a name="feedback"></a>Comentários
Você tem algum comentário sobre o Mapa de Serviço ou sobre esta documentação?  Visite nossa [página User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.

