---
title: ADM (Monitor da Dependência de Aplicativos) no OMS (Operations Management Suite) | Microsoft Docs
description: O ADM (Monitor da dependência de Aplicativos) é uma solução do OMS (Operations Management Suite) que detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre serviços.  Este artigo fornece detalhes sobre a implantação do ADM em seu ambiente e sobre como usá-lo em uma variedade de cenários.
services: operations-management-suite
documentationcenter: ''
author: daseidma
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2016
ms.author: daseidma;bwren

---
# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>Usar a solução Monitor da Dependência de Aplicativos no OMS (Operations Management Suite)
![Ícone do Gerenciamento de Alertas](media/operations-management-suite-application-dependency-monitor/icon.png) O ADM (Monitor da dependência de Aplicativos) detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre serviços. Ele permite que você exiba os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais.  O Monitor da Dependência de Aplicativos mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.

Este artigo descreve os detalhes do uso do Monitor da Dependência de Aplicativos.  Para saber mais sobre como configurar o ADM e os agentes de integração, confira [Configurar a solução Monitor da Dependência de Aplicativos no OMS (Operations Management Suite)](operations-management-suite-application-dependency-monitor-configure.md)

> [!NOTE]
> No momento, o Monitor da Dependência de Aplicativos está em modo de visualização privada.  Você pode solicitar acesso à visualização privada do ADM em [https://aka.ms/getadm](https://aka.ms/getadm).
> 
> Durante o modo de visualização privada, todas as contas OMS terão acesso ilimitado ao ADM.  Os nós do ADM são gratuitos, mas os dados do Log Analytics para os tipos AdmComputer_CL e AdmProcess_CL serão limitados como qualquer outra solução.
> 
> Depois que o ADM entrar em visualização pública, ele só estará disponível para clientes gratuitos e pagos do Insight e do Analytics no Plano de Preços do OMS.  As contas de camada gratuita serão limitadas a cinco nós do ADM.  Se você estiver participando do modo de visualização privada e não estiver registrado no Plano de Preços do OMS quando o ADM entrar na visualização pública, o ADM será desabilitado. 
> 
> 

## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>Casos de uso: Fazer com que seus processos de TI reconheçam a dependência
### <a name="discovery"></a>Descoberta
O ADM compila automaticamente um mapa de referência comum de dependências em seus servidores, processos e serviços de terceiros.  Ele descobre e mapeia todas as dependências TCP, identificando conexões surpresa, sistemas remotos de terceiros dos quais você depende e dependências para áreas escuras tradicionais da rede, como DNS e AD.  O ADM descobre conexões de rede com falha que seus sistemas gerenciados estão tentando realizar, ajudando você a identificar possíveis erros de configuração do servidor, interrupções de serviço e problemas de rede.

### <a name="incident-management"></a>Gerenciamento de incidentes
O ADM ajuda a eliminar as suposições de isolamento do problema, mostrando a você como os sistemas estão conectados e afetando uns aos outros.  Além das conexões com falha, informações sobre clientes conectados ajudam a identificar balanceadores de carga configurados incorretamente, carga surpreendente ou excessiva em serviços essenciais e clientes não autorizados, como computadores de desenvolvedores conversando com sistemas de produção.  Fluxos de trabalho integrados com Controle de alteração do OMS também permitem que você verifique se um evento de alteração em uma máquina de back-end ou serviço pode explicar a causa raiz de um incidente.

### <a name="migration-assurance"></a>Garantia de migração
O ADM permite que você planeje, acelere e valide efetivamente as migrações do Azure, garantindo que nada seja deixado para trás e não ocorram interrupções ou surpresas.  Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos, avaliar a capacidade e a configuração do sistema e identificar se um sistema em execução ainda está atendendo aos usuários ou é candidato para encerramento em vez de migração.  Após a movimentação, você pode verificar a carga e a identidade do cliente para verificar se os sistemas de teste e os clientes estão se conectando.  Se o seu planejamento de sub-rede e definições de firewall tiverem problemas, as conexões com falha em mapas ADM apontarão para os sistemas que precisam de conectividade.

### <a name="business-continuity"></a>Continuidade dos negócios
Se você estiver usando o Azure Site Recovery e precisar de ajuda para definir a sequência de recuperação para seu ambiente de aplicativo, o ADM poderá mostrar automaticamente como os sistemas dependem uns dos outros para assegurar que seu plano de recuperação seja confiável.  Escolhendo um servidor crítico e exibindo seus clientes, você pode identificar os sistemas front-end que devem ser recuperados somente após a restauração e disponibilização desse servidor crítico.  Por outro lado, examinando as dependências de back-end de um servidor crítico, você pode identificar os sistemas que devem ser recuperados antes de seu sistema de foco ser restaurado.

### <a name="patch-management"></a>Gerenciamento de patch
O ADM aprimora seu uso da Avaliação de atualização do sistema OMS, mostrando que outras equipes e servidores dependem de seu serviço, para que você possa notificá-los com antecedência antes de desativar seus sistemas para aplicação de patch.  O ADM também aprimora o gerenciamento de patch no OMS mostrando se os seus serviços estão disponíveis e conectados corretamente após terem sido corrigidos e reiniciados. 

## <a name="mapping-overview"></a>Visão geral do mapeamento
Os agentes do ADM coletam informações sobre todos os processos conectados a TCP no servidor onde estão instalados, bem como detalhes sobre as conexões de entrada e saída de cada processo.  Usando a Lista de Máquinas no lado esquerdo da solução ADM, os computadores com agentes ADM podem ser selecionados a fim de visualizar suas dependências em um intervalo de tempo selecionado.  O mapa de dependência da máquina se concentra em uma máquina específica e mostra todas as máquinas que são clientes TCP diretos ou servidores dessa máquina.

![Visão geral do ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Máquinas podem ser expandidas no mapa para mostrar os processos em execução com conexões de rede ativas durante o intervalo de tempo selecionado.  Quando um computador remoto com um agente ADM é expandido para mostrar os detalhes do processo, somente os processos de comunicação com a máquina de foco são exibidos.  A contagem de máquinas de front-end sem agente se conectando com a máquina de foco é indicada no lado esquerdo dos processos aos quais elas se conectam.  Se a máquina de foco estiver fazendo uma conexão com uma máquina de back-end sem um agente, esse back-end será representado por um nó no mapa mostrando o endereço IPv4, e o nó poderá ser expandido para mostrar as portas individuais e os serviços com os quais a máquina de foco está se comunicando.

Por padrão, os mapas de ADM mostram os últimos 10 minutos de informações de dependência.  Usando os controles de tempo na parte superior esquerda, os mapas podem ser consultados com relação a intervalos históricos, até uma hora, a fim de mostrar como eram as dependências no passado, por exemplo, durante um incidente ou antes de uma alteração.    Os dados do ADM são armazenados por 30 dias em espaços de trabalho pagos, e por sete dias em espaços de trabalho gratuitos.

![Mapa de máquinas com propriedades da máquina selecionada](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Conexões com falha
As Conexões com falha são mostradas em mapas ADM para processos e computadores, com uma linha vermelha tracejada mostrando se um sistema cliente não puder acessar um processo ou uma porta.  Conexões com falha são reportadas de qualquer sistema com um agente ADM implantado, se esse sistema estiver tentando a conexão com falha.  O ADM mede isso observando os soquetes TCP que falham ao estabelecer uma conexão.  Isso pode ocorrer devido a um firewall, uma configuração errada no cliente ou servidor ou um serviço remoto não disponível. 

![Conexões com falha](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Entender as conexões com falha pode ajudar com a solução de problemas, validação da migração, análise de segurança e noções básicas sobre arquitetura em geral.  Às vezes, as conexões com falha são inofensivas, mas normalmente apontam diretamente para um problema, como um ambiente de failover ficando inacessível de repente ou duas camadas de aplicativo sem comunicação após uma migração na nuvem.  Na imagem acima, o IIS e WebSphere estão em execução, mas eles não podem se conectar. 

## <a name="computer-and-process-properties"></a>Propriedades do computador e do processo
Ao navegar em um mapa ADM, você pode selecionar máquinas e processos para obter contexto adicional sobre suas propriedades.  As máquinas fornecem informações sobre o nome DNS, endereços IPv4, capacidade de CPU e Memória, Tipo de VM, versão do Sistema Operacional, horário da Última Reinicialização e as IDs de seus agentes OMS e ADM.

Os detalhes do processo são coletados nos metadados do sistema operacional sobre processos em execução, incluindo nome do processo, descrição do processo, nome de usuário e domínio (no Windows), nome da empresa, nome do produto, versão do produto, diretório de trabalho, linha de comando e hora de início do processo.

![Propriedades do processo](media/operations-management-suite-application-dependency-monitor/process-properties.png)

O painel Resumo do Processo fornece informações adicionais sobre a conectividade do processo, incluindo suas portas associadas, conexões de entrada e saída e conexões com falha. 

![Resumo do processo](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integração de controle de alterações do OMS
A integração do ADM com o Controle de Alterações é automática quando as duas soluções estão habilitadas e configuradas em seu espaço de trabalho do OMS.

O painel Resumo de Máquina indica se os eventos de Controle de Alterações ocorreram no computador selecionado durante o intervalo de tempo selecionado.

![Painel Resumo de Máquina](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

O painel Controle de Alteração da Máquina mostra uma lista de todas as alterações, com a mais recente primeiro, juntamente com um link para detalhar a Pesquisa de Log para obter detalhes adicionais.
![Painel de Controle de Alteração da Máquina](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Veja a seguir um modo de exibição detalhado do evento de Alteração de Configuração após selecionar **Mostrar no Log Analytics**.
![Evento de Alteração de Configuração](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)

## <a name="log-analytics-records"></a>Registros do Log Analytics
Dados de inventário do processo e do computador do ADM estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) no Log Analytics.  Isso pode ser aplicado a cenários, incluindo o planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho do ad-hoc. 

Um registro é gerado por hora para cada computador e processo exclusivo, além dos registros gerados quando esse processo ou computador inicia ou é integrado ao ADM.  Esses registros têm as propriedades descritas nas tabelas a seguir. 

Há propriedades geradas internamente que você pode usar para identificar computadores e processos exclusivos:

* PersistentKey_s é definida exclusivamente pela configuração do processo, por exemplo, linha de comando e ID de usuário.  Ela é exclusiva a um determinado computador, mas pode ser repetida entre os computadores.
* ProcessId_s e ComputerId_s são globalmente exclusivas no modelo ADM.

### <a name="admcomputer_cl-records"></a>Registros de AdmComputer_CL
Registros com um tipo de **AdmComputer_CL** têm dados de inventário para servidores com agentes do ADM.  Esses registros têm as propriedades descritas na tabela a seguir.  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*AdmComputer_CL* |
| SourceSystem |*OpsManager* |
| ComputerName_s |Nome do computador Windows ou Linux |
| CPUSpeed_d |Velocidade da CPU em MHz |
| DnsNames_s |Lista de todos os nomes DNS para este computador |
| IPv4s_s |Lista de todos os endereços IPv4 usados por este computador |
| IPv6s_s |Lista de todos os endereços IPv6 usados por este computador.  (O ADM identifica endereços IPv6, mas não descobre dependências de IPv6.) |
| Is64Bit_b |true ou false com base no tipo de sistema operacional |
| MachineId_s |Um GUID interno, exclusivo em um espaço de trabalho do OMS |
| OperatingSystemFamily_s |Windows ou Linux |
| OperatingSystemVersion_s |Cadeia de caracteres longa da versão do sistema operacional |
| TimeGenerated |Data e hora de criação do registro. |
| TotalCPUs_d |Número de núcleos de CPU |
| TotalPhysicalMemory_d |Capacidade de memória em MB |
| VirtualMachine_b |true ou false com base se o sistema operacional é uma VM convidada |
| VirtualMachineID_g |ID da VM do Hyper-V |
| VirtualMachineName_g |Nome da VM do Hyper-V |
| VirtualMachineType_s |Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |

### <a name="admprocess_cl-type-records"></a>Registros do tipo AdmProcess_CL
Registros com um tipo de **AdmProcess_CL** têm dados de inventário para processos conectados com TCP em servidores com agentes do ADM.  Esses registros têm as propriedades descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*AdmProcess_CL* |
| SourceSystem |*OpsManager* |
| CommandLine_s |A linha de comando completa do processo |
| CompanyName_s |Nome da empresa (do Windows PE ou Linux RPM) |
| Description_s |Descrição longa do processo (de Windows PE ou Linux RPM) |
| FileVersion_s |Versão do arquivo executável (do Windows PE, somente Windows) |
| FirstPid_d |ID do Processo do sistema operacional |
| InternalName_s |Nome interno do arquivo executável (do Windows PE, somente Windows) |
| MachineId_s |GUID interno, exclusivo em um espaço de trabalho do OMS |
| Name_s |O nome executável do processo |
| Path_s |Caminho do sistema de arquivos do executável do processo |
| PersistentKey_s |GUID interno e exclusivo nesse computador |
| PoolId_d |ID interna para agregação de processos com base em linhas de comando semelhantes. |
| ProcessId_s |GUID interno, exclusivo em um espaço de trabalho do OMS |
| ProductName_s |Cadeia de caracteres do nome do produto (do Windows PE ou Linux RPM) |
| ProductVersion_s |Cadeia de caracteres de versão do produto (do Windows PE ou Linux RPM) |
| StartTime_t |Hora de início do processo no relógio do computador local |
| TimeGenerated |Data e hora de criação do registro. |
| UserDomain_s |Domínio do proprietário do processo (somente Windows) |
| UserName_s |Nome do proprietário do processo (somente Windows) |
| WorkingDirectory_s |Diretório de trabalho do processo |

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>Lista a capacidade de memória física de todos os computadores gerenciados.
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>Listar o nome, DNS, IP e versão do sistema operacional do computador.
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comando
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>Depois de exibir dados de evento para um processo específico, use a ID da máquina para recuperar o nome do computador
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores que executam SQL
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista de todas as versões de produto exclusivas de curl em meu datacenter
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um Grupo de Computadores de todos os computadores executando o CentOS
![Exemplo de consulta do ADM](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço do Monitor da Dependência de Aplicativos. A Microsoft usa esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço do Monitor da Dependência de Aplicativos. Os dados incluem informações sobre a configuração do software, como o sistema operacional e a versão, e também incluem o endereço IP, o nome DNS e nome da Estação de Trabalho a fim de fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para saber mais sobre o uso e a coleta de dados, veja a [Declaração de Privacidade do Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](../log-analytics/log-analytics-log-searches.md\] in Log Analytics to retrieve data collected by Application Dependency Monitor..md)

<!--HONumber=Oct16_HO2-->


