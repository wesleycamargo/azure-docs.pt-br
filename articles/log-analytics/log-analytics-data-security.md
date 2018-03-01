---
title: "Segurança de dados do Log Analytics | Microsoft Docs"
description: Saiba mais sobre como o Log Analytics protege a sua privacidade e seus dados.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: magoedte
ms.openlocfilehash: bfd9b3302c73e50408cdd68b25317630aa087d7f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="log-analytics-data-security"></a>Segurança de dados do Log Analytics
Este documento destina-se a fornecer informações específicas do Azure Log Analytics para complementar as informações na [Central de Confiabilidade do Azure](../security/security-microsoft-trust-center.md).  

Este artigo explica como os dados são coletados, processados e protegidos pelo Log Analytics no OMS (Operations Management Suite). Você pode usar agentes para se conectar ao serviço Web, usar o System Center Operations Manager para coletar dados operacionais ou recuperar dados de diagnóstico do Azure para uso pelo Log Analytics. 

O serviço Log Analytics gerencia seus dados baseados em nuvem com segurança usando os seguintes métodos:

* Segregação de dados
* Retenção de dados
* Segurança física
* Gerenciamento de incidentes
* Conformidade
* Certificações de padrões de segurança

Entre em contato conosco com quaisquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo nossas políticas de segurança nas [opções de suporte do Azure](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Segregação de dados
Depois que seus dados são ingeridos pelo serviço Log Analytics, os dados são mantidos separados logicamente em cada componente no serviço. Todos os dados são marcados por espaço de trabalho. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço. Os dados ficam armazenados em um banco de dados dedicado no cluster de armazenamento na região selecionada.

## <a name="data-retention"></a>Retenção de dados
Dados indexados de pesquisa de log são armazenados e retidos de acordo com o plano de preço. Para obter mais informações, consulte [Preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Como parte do seu [contrato de assinatura](https://azure.microsoft.com/support/legal/subscription-agreement/), a Microsoft manterá seus dados de acordo com os termos do contrato.  Quando os dados são excluídos, também excluímos a conta do Armazenamento do Azure onde os dados residem.  Quando os dados do cliente são removidos, não ocorre a destruição de nenhuma unidade física.  

A tabela a seguir lista algumas das soluções disponíveis e fornece exemplos de tipo de dados coletados.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Capacidade e Desempenho |Dados de desempenho e metadados |
| Avaliação de malware |Dados de configuração e metadados |
| Gerenciamento de atualizações |Metadados e dados de estado |
| Gerenciamento de Log |Logs de eventos de definidos pelo usuário, logs de eventos do Windows e/ou os logs do IIS |
| Controle de Alterações |Inventário de software, serviço Windows e metadados de daemon do Linux e de arquivo do Windows/Linux |
| Avaliação do SQL e Active Directory |Dados WMI, dados do registro, dados de desempenho e resultados de exibição do gerenciamento dinâmico do SQL Server |

A tabela a seguir mostra exemplos de tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Nome do Alerta, Descrição do Alerta, BaseManagedEntityId, ID do Problema, IsMonitorAlert, RuleId, ResolutionState, Prioridade, Gravidade, Categoria, Proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Observação:** quando você grava eventos com campos personalizados no log de eventos do Windows, o OMS os coleta. |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço Log Analytics é operado pelo pessoal da Microsoft e todas as atividades são registradas em log e podem ser auditadas. O Log Analytics funciona como um serviço do Azure e atende a todos os requisitos de segurança e conformidade do Azure. Você pode exibir detalhes sobre a segurança física dos ativos do Azure na página 18 da [Visão geral de Segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Os direitos de acesso físico a áreas protegidas são alterados dentro de um dia útil para qualquer pessoa que não tenha mais responsabilidade pelo serviço OMS, incluindo a transferência e encerramento. Você pode ler sobre a infraestrutura física global que usamos em [Datacenters da Microsoft](https://www.microsoft.com/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gerenciamento de incidentes
O OMS tem um processo de gerenciamento de incidentes que todos os serviços Microsoft seguem. Resumidamente, nós:

* Usamos um modelo de responsabilidade compartilhada em que uma parte da responsabilidade pela segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerencie incidentes de segurança do Azure:
  * Início de uma investigação sobre a detecção de um incidente
  * Avaliamos o impacto e a gravidade de um incidente por um membro da equipe de resposta a incidentes de plantão. Com base nas evidências, a avaliação pode ou não resultar em mais escalonamento para a equipe de resposta de segurança.
  * Diagnosticamos um incidente por especialistas de resposta de segurança para conduzir a investigação forense ou técnica, identificar estratégias de contenção, atenuação e solução alternativa. Se a equipe de segurança acreditar que os dados do cliente podem ter sido expostos a um indivíduo não autorizado ou ilícito, a execução paralela do processo de Notificação de incidente do cliente começa em paralelo.  
  * Estabilizamos e recuperamos do incidente. A equipe de resposta a incidentes cria um plano de recuperação para atenuar o problema. As etapas de contenção de crise, como colocar os sistemas afetados em quarentena, podem ocorrer imediatamente e em paralelo com o diagnóstico. Atenuações de prazo mais longo podem ser planejadas, o que ocorre após o risco imediato ter terminado.  
  * Fechamos o incidente e realizamos um post-mortem. A equipe de resposta a incidentes cria um post-mortem que descreve os detalhes do incidente, com a intenção de revisar as políticas, procedimentos e processos para evitar a recorrência do evento.
* Notifique os clientes dos incidentes de segurança:
  * Determinamos o escopo dos clientes afetados e fornecemos qualquer pessoa que seja afetada como um aviso detalhado conforme possível
  * Criamos um aviso para fornecer aos clientes informações suficientemente detalhadas para que possam realizar uma investigação do seu lado e atender quaisquer compromissos que tenham firmado com seus usuários finais ao não atrasar indevidamente o processo de notificação.
  * Confirmamos e declaramos o incidente, se necessário.
  * Notificamos os clientes com uma notificação de incidentes sem demora excessiva e acordo com qualquer compromisso contratual ou legal. As notificações de incidentes de segurança são entregue a um ou mais administradores do cliente por qualquer meio que a Microsoft selecione, inclusive por email.
* Conduza o treinamento e a prontidão de equipe:
  * Os funcionários da Microsoft devem concluir o treinamento de segurança e conscientização, o que os ajuda a identificar e relatar problemas de segurança suspeitos.  
  * Os operadores que trabalham no serviço Microsoft Azure têm obrigações de treinamento adicionais que envolvem seu acesso a sistemas confidenciais que hospedam dados do cliente.
  * A equipe de resposta de segurança da Microsoft recebe treinamento especializado para suas funções

Caso haja uma perda de dados de qualquer cliente, notificamos cada cliente dentro de um dia. No entanto, a perda de dados do cliente nunca ocorreu com o serviço. 

Para obter mais informações sobre como a Microsoft responde a incidentes de segurança, confira [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf) (Resposta de segurança do Microsoft Azure na nuvem).

## <a name="compliance"></a>Conformidade
O desenvolvimento do software Log Analytics e o serviço de segurança da informação da equipe e o programa de governança dão suporte aos requisitos do negócio e aderem a leis e regulamentos como descrito na [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/) e na [Central de Conformidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Como o Log Analytics estabelece os requisitos de segurança, identifica os controles de segurança, gerencia e monitora os riscos também são descritos lá. Anualmente, revisamos as políticas, padrões, procedimentos e diretrizes.

Cada membro de equipe de desenvolvimento recebe treinamento formal sobre a segurança de aplicativo. Internamente, usamos um sistema de controle de versão para o desenvolvimento de software. Cada projeto de software é protegido pelo sistema de controle de versão.

A Microsoft tem uma equipe de segurança e conformidade que monitora e analisa todos os serviços na Microsoft. Os responsáveis pela segurança de informações compõem a equipe e eles não são associados aos departamentos de engenharia que desenvolvem o Log Analytics. Os responsáveis pela segurança têm sua própria cadeia de gerenciamento e realizaram avaliações independentes dos produtos e serviços para garantir a segurança e a conformidade.

O conselho de diretores da Microsoft é notificado por um relatório anual sobre todos os programas de segurança da informação na Microsoft.

A equipe de serviço e desenvolvimento de software do Log Analytics trabalha ativamente com as equipes de conformidade e jurídica da Microsoft e outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestados
O Azure Log Analytics atende aos seguintes requisitos:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [PCI DSS (Padrão de Segurança de dados do Setor de Cartões de Pagamento (Compatível com PCI))](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pelo PCI Security Standards Council.
* Compatível com [SOC (Service Organization Controls) 1 Tipo 1 e SOC 2 Tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA e HITECH](https://www.microsoft.com/TrustCenter/Compliance/HIPAA) para empresas que tenham um contrato de sócio corporativo HIPAA
* Critérios de engenharia comum do Windows
* Microsoft Trustworthy Computing (a página pode estar em inglês)
* Como um serviço do Azure, os componentes que o Log Analytics usa seguem os requisitos de conformidade do Azure. Você pode ler mais em [Conformidade do Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Em algumas certificações/atestados, o Log Analytics está listado com o nome antigo de *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama a seguir mostra uma arquitetura de segurança de nuvem como o fluxo de informações de sua empresa e como elas são protegidas à medida que se movem para o serviço Log Analytics e por fim vistas por você no portal do Azure ou no portal clássico do OMS. Após o diagrama, mais informações sobre cada etapa.

![Imagem de coleta de dados de Log Analytics e segurança](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscrever-se no Log Analytics e coletar dados
Para sua organização enviar dados ao Log Analytics, você deve configurar um agente do Windows ou Linux em execução em máquinas virtuais do Azure, ou em computadores físicos ou virtuais em seu ambiente ou outro provedor de nuvem.  Se você usar o Operations Manager, você configura o agente do Operations Manager no grupo de gerenciamento. Os usuários (que podem ser você, outros usuários individuais ou um grupo de pessoas) criam um ou mais espaços de trabalho do Log Analytics e registram agentes usando uma das seguintes contas:

* [ID Organizacional](../active-directory/sign-up-organization.md)
* [Conta da Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Um espaço de trabalho do Log Analytics é onde os dados são coletados, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio para particionar dados e cada espaço de trabalho é exclusivo. Por exemplo, talvez você queira ter seus dados de produção gerenciados com um espaço de trabalho e seus dados de teste gerenciados com outro espaço de trabalho. Os espaços de trabalho também ajudam um administrador a controlar o acesso dos usuários aos dados. Cada espaço de trabalho pode ter várias contas de usuário associadas a ele, e cada conta de usuário pode acessar vários espaços de trabalho do Log Analytics. Você cria os espaços de trabalho com base na região do datacenter. Cada espaço de trabalho é replicado para outros datacenters na região, principalmente para disponibilidade do serviço Log Analytics.

Para o Operations Manager, o grupo de gerenciamento do Operations Manager estabelece uma conexão com o serviço Log Analytics. Você configura quais sistemas gerenciados por agente no grupo de gerenciamento têm permissão para coletar e enviar dados para o serviço. Dependendo da solução que você habilitou, dados dessas soluções são enviados diretamente de um servidor de gerenciamento do Operations Manager para o serviço Log Analytics ou por causa do volume de dados coletados pelo sistema gerenciado por agente, são enviados diretamente do agente para o serviço. Para sistemas não monitorados pelo Operations Manager, cada um se conecta com segurança ao serviço Log Analytics diretamente.

Toda a comunicação entre os sistemas conectados e o serviço do Log Analytics é criptografada.  O protocolo TLS (HTTPS) é usado para criptografia.  O processo SDL da Microsoft é seguido para garantir que o Log Analytics esteja atualizado com os avanços mais recentes nos protocolos criptográficos.

Cada tipo de agente coleta dados para o Log Analytics. Os tipos de dados coletados dependem dos tipos de soluções usadas. Você pode ver um resumo da coleta de dados em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Além disso, há informações mais detalhadas de coleção disponíveis para a maioria das soluções. Uma solução é um conjunto de exibições predefinidas, de consultas de pesquisa de log, de regras de coleta de dados e de lógica de processamento. Somente os administradores podem usar o Log Analytics para importar uma solução. Após a importação da solução, ela será movida para os servidores de gerenciamento do Operations Manager (se usado) e então para quaisquer agentes escolhidos por você. Depois disso, os agentes coletam os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados de agentes
Registre todos os tipos de agente com uma chave de registro e uma conexão segura será estabelecida entre o agente e o serviço Log Analytics usando a autenticação baseada em certificado e SSL com a porta 443. O Log Analytics usa um repositório secreto para gerar e manter as chaves. As chaves privadas são rotacionadas a cada 90 dias e armazenadas no Azure e são gerenciadas pelas operações do Azure que seguem práticas de conformidade e regulatórias estritas.

Com o Operations Manager, o grupo de gerenciamento registrado com um espaço de trabalho do Log Analytics estabelece uma conexão HTTPS segura com um servidor de gerenciamento do Operations Manager.

Para agentes do Windows ou do Linux em execução em máquinas virtuais do Azure, uma chave de armazenamento somente leitura é usada para ler eventos de diagnóstico nas tabelas do Azure.  

Com agentes subordinados a um grupo de gerenciamento do Operations Manager que esteja integrado ao Log Analytics, se o servidor de gerenciamento não puder se comunicar com o serviço por algum motivo, os dados coletados são armazenados localmente em um cache temporário no servidor de gerenciamento.   Eles tentam reenviar os dados a cada oito minutos durante duas horas.  Para dados que ignoram o servidor de gerenciamento e são enviados diretamente ao Log Analytics, o comportamento é consistente com o agente do Windows.  

O Windows ou os dados de agente do servidor de gerenciamento em cache são protegidos pelo armazenamento de credenciais do sistema operacional. Se o serviço não puder processar os dados depois de duas horas, os agentes colocarão os dados em fila. Se a fila encher, o agente começará a remover tipos de dados, começando com os dados de desempenho. O limite de fila do agente é uma chave do registro para que você possa modificá-lo, se necessário. Os dados coletados são compactados e enviados para o serviço, ignorando os bancos de dados de grupos de gerenciamento do Operations Manager, para que ele não adicione carga a eles. Depois que os dados coletados forem enviados, eles serão removidos do cache.

Conforme descrito acima, os dados do servidor de gerenciamento ou de agentes conectados diretamente são enviados por SSL para data centers do Microsoft Azure. Opcionalmente, você pode usar o ExpressRoute para fornecer segurança adicional para os dados. O ExpressRoute é uma maneira de se conectar diretamente ao Azure pela rede WAN existente, como um VPN MPLS (multi-protocol label switching), fornecida por um provedor de serviço de rede. Para obter mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço Log Analytics recebe e processa os dados
O serviço Log Analytics garante que os dados de entrada sejam de uma fonte confiável ao validar certificados e a integridade dos dados com a autenticação do Azure. Os dados brutos não processados são armazenados em um Hub de Eventos do Azure na região em que eventualmente serão armazenados os dados em repouso. Os tipos de dados armazenados dependem dos tipos de soluções importados e usados para coletar dados. Em seguida, o serviço Log Analytics processa os dados brutos e os consome no banco de dados.

O período de retenção dos dados coletados armazenados no banco de dados depende do plano selecionado quando o espaço de trabalho foi criado.  Para a camada paga, os dados coletados ficam disponíveis por 31 dias por padrão, mas podem ser estendidos para 365 dias.  Esses dados ainda não estão criptografados em repouso e estão previstos para meados de 2018. 

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Usar o Log Analytics para acessar os dados
Para acessar seu espaço de trabalho do Log Analytics, entre no portal do Azure usando a conta organizacional ou uma conta da Microsoft configurada anteriormente. Todo o tráfego entre o portal e o Log Analytics no serviço é enviado por um canal HTTPS seguro. Ao usar o portal, uma ID de sessão é gerada no cliente do usuário (navegador da Web) e dados são armazenados em um cache local até que a sessão seja encerrada. Após o encerramento, o cache é excluído. Os cookies do lado do cliente, que não contêm informações de identificação pessoal, não são removidos automaticamente. Os cookies de sessão são marcados como HTTPOnly e são protegidos. Após um período ocioso predeterminado, a sessão do portal do Azure é encerrada.

## <a name="next-steps"></a>Próximas etapas
* Saiba como coletar dados com o Log Analytics para as VMs do Azure seguindo o [guia de início rápido da VM do Azure](log-analytics-quick-collect-azurevm.md).  

*  Se você estiver querendo coletar dados de computadores físicos ou virtuais Windows ou Linux em seu ambiente, confira o [Guia de início rápido para computadores Linux](log-analytics-quick-collect-linux-computer.md) ou o [Guia de início rápido para computadores Windows](log-analytics-quick-collect-windows-computer.md)

