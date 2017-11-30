---
title: "Automação das Especificações Técnicas do Azure FedRAMP – Auditoria e Responsabilidade"
description: "Aplicativos da Web para FedRAMP – Auditoria e Responsabilidade"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Auditoria e Responsabilidade (AU)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelo Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre as diretrizes e os procedimentos de teste para cada controle.

## <a name="nist-800-53-control-au-1"></a>Controle NIST 800-53 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Política e Procedimentos de Auditoria e Responsabilidade

**AU-1** A organização desenvolve, documenta e distribui a [Atribuição: equipe ou funções definidas pela organização] uma política de auditoria e responsabilidade que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de auditoria e responsabilidade e os controles de auditoria e responsabilidade associados; e revisa e atualiza a política de auditoria e responsabilidade [Atribuição: frequência definida pela organização]; e os procedimentos de auditoria e responsabilidade [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e procedimentos de auditoria e responsabilidade de nível empresarial do cliente podem ser suficientes para endereçar este controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-2a"></a>Controle NIST 800-53 AU-2.a

#### <a name="audit-events"></a>Eventos de Auditoria

**AU-2.a** A organização determina se o sistema de informações é capaz de realizar a auditoria dos seguintes eventos: [Atribuição: eventos auditáveis definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A capacidade de auditoria para estas Especificações Técnicas do Azure é fornecida pelo Azure Monitor e pelo serviço Log Analytics no OMS. O Azure Monitor fornece os logs de auditoria detalhados sobre atividade associada a recursos implantados. Esses e os logs de nível de sistema operacional são coletados pelo Log Analytics e armazenados no repositório do OMS. O Log Analytics correlaciona dados de auditoria em recursos implantados por essa solução e pode ser estendido para o aplicativo Web de implantação de cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-2b"></a>Controle NIST 800-53 AU-2.b

#### <a name="audit-events"></a>Eventos de Auditoria

**AU-2.b** A organização coordena a função de auditoria de segurança com outras entidades organizacionais que exigem informações relacionadas à auditoria para aprimorar o suporte mútuo e para ajudar a orientar a seleção de eventos auditáveis.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um processo de nível empresarial estabelecido que determina os eventos auditáveis. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-2c"></a>Controle NIST 800-53 AU-2.c

#### <a name="audit-events"></a>Eventos de Auditoria

**AU-2.c** A organização fornece uma razão pela qual os eventos auditáveis são considerados adequados para dar suporte a investigações após o fato de incidentes de segurança.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Eventos auditados por esta Especificação Técnica do Azure incluem informações suficientes para determinar quando ocorrem eventos, a origem do evento, o resultado do evento e outras informações detalhadas que dão suporte à investigação de incidentes de segurança. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-2d"></a>Controle NIST 800-53 AU-2.d

#### <a name="audit-events"></a>Eventos de Auditoria

**AU-2.d** A organização determina se os eventos a seguir devem ser auditados dentro do sistema de informações: [Atribuição: eventos auditados definidos pela organização (o subconjunto dos eventos auditáveis definidos em AU-2.a.) junto com a frequência de (ou a situação que exija) auditoria para cada evento identificado].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Eventos auditados por esta Especificação Técnica do Azure incluem os auditados pelos logs de atividades do Azure para os recursos implantados, logs de nível de sistema operacional, logs do Active Directory e os logs do SQL Server. Os clientes podem selecionar eventos adicionais a serem auditados para atender às necessidades da tarefa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-2-3"></a>Controle NIST 800-53 AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Eventos de Auditoria | Revisões e Atualizações

**AU-2 (3)** A organização revisa e atualiza os eventos auditados [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode contar com um processo de atualização e revisão periódico de nível empresarial estabelecido para o conjunto definido de eventos auditados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-3"></a>Controle NIST 800-53 AU-3

#### <a name="content-of-audit-records"></a>Conteúdo de registros de auditoria

**AU-3** O sistema de informações gera registros de auditoria que contêm informações que estabelecem qual tipo de evento ocorreu, quando o evento ocorreu, onde o evento ocorreu, a origem do evento, o resultado do evento e a identidade de qualquer pessoa ou entidade associadas ao evento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure depende dos recursos de auditoria internos do Azure, do Windows Server e do SQL Server. Essas soluções de auditoria capturam registros de auditoria com detalhes suficientes para atender aos requisitos deste controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-3-1"></a>Controle NIST 800-53 AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Conteúdo de Registros de Auditoria | Informações de Auditoria Adicionais

**AU-3 (1)** O sistema de informações gera registros de auditoria que contêm as seguintes informações adicionais: [Atribuição: informações adicionais mais detalhadas definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Eventos de Log de atividades do Azure usam um esquema detalhado que contém campos para mais de 20 tipos de informações de auditoria. Além do Log de atividades, esta Especificação Técnica do Azure implanta a solução do Log Analytics no OMS que dá suporte a um conjunto diversificado de fontes de dados, incluindo os logs do Windows, logs do Linux, logs de Diagnóstico do Azure e logs de cliente.  |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-3-2"></a>Controle NIST 800-53 AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Conteúdo de Registros de Auditoria | Gerenciamento Centralizado do Conteúdo do Registro de Auditoria planejada

**AU-3 (2)** O sistema de informações fornece configuração e gerenciamento centralizados do conteúdo a ser capturado em registros de auditoria gerados pelo [Atribuição: componentes de sistema de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todas as máquinas virtuais implantadas por essa Especificação técnica do Azure fazem parte de domínio do Active Directory implantado. Todas as máquinas virtuais integradas pelo domínio implementam uma política de grupo que pode ser configurada para gerenciar centralmente a configuração de sistema de auditoria de nível de sistema operacional. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-4"></a>Controle NIST 800-53 AU-4

#### <a name="audit-storage-capacity"></a>Capacidade de armazenamento de auditoria

**AU-4** A organização aloca a capacidade de armazenamento de registros de auditoria de acordo com [Atribuição: requisitos de armazenamento de registros de auditoria definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure aloca a capacidade de armazenamento suficiente para manter os registros de auditoria por um período de um ano. Todos os registros de auditoria são coletados pelo Log Analytics que está configurado para retenção de um ano. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-5a"></a>Controle NIST 800-53 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Resposta a Falhas de Processamento de Auditoria

**AU-5.a** O sistema de informações alerta [Atribuição: equipe ou funções definidas pela organização] no caso de uma falha no processamento de auditoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O status do serviço para o Azure Monitor e Log Analytics está disponível no site do status do Azure e na folha de integridade do serviço no portal do Azure. Alertas podem ser configurados pelo Log Analytics para fornecer notificação de outros tipos de falhas de processamento de auditoria. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-5b"></a>Controle NIST 800-53 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Resposta a Falhas de Processamento de Auditoria

**AU-5.b** O sistema de informações executa as seguintes ações adicionais: [Atribuição: ações a serem tomadas definidas pela organização (por exemplo, desligar o sistema de informações, substituir registros de auditoria mais antigos, parar de gerar registros de auditoria)].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todos os registros de auditoria gerados pelos recursos implantados por esta Especificação Técnica do Azure são coletados pelo Log Analytics e mantidos por um período de um ano. A alocação de armazenamento para esse armazenamento de registros de auditoria é alocada dinamicamente garantindo que a capacidade suficiente esteja disponível. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-5-1"></a>Controle NIST 800-53 AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Resposta a Falhas de Processamento de Auditoria | Capacidade de Armazenamento de Auditoria

**AU-5 (1)** O sistema de informações fornece um aviso para [Atribuição: equipe, funções e/ou locais definidos pela organização] em [Atribuição: período de tempo definido pela organização] quando o volume de armazenamento de registros de auditoria alocado atingir [Atribuição: percentual definido pela organização] da capacidade máxima de armazenamento de registros de auditoria do repositório.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todos os registros de auditoria gerados pelos recursos implantados por esta Especificação Técnica do Azure são coletados pelo Log Analytics e mantidos por um período de um ano. A alocação de armazenamento para esse armazenamento de registros de auditoria é alocada dinamicamente garantindo que a capacidade suficiente esteja disponível. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-5-2"></a>Controle NIST 800-53 AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Resposta a Falhas de Processamento de Auditoria | Alertas em Tempo Real

**AU-5 (2)** O sistema de informações fornece um alerta em [Atribuição: período de tempo real definido pela organização] para [Atribuição: equipe, funções e/ou locais definidos pela organização] quando ocorrem os seguintes eventos de falha de auditoria: [Atribuição: eventos de falha de auditoria que exigem alertas em tempo real definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O status do serviço do Azure está disponível na folha de integridade do serviço no portal do Azure. Alertas podem ser configurados pelo Log Analytics para fornecer notificação de outros tipos de falhas de processamento de auditoria. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-6a"></a>Controle NIST 800-53 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Revisão de Auditoria, Análise e Relatórios

**AU-6.a** A organização revisa e analisa os registros de auditoria do sistema de informações [Atribuição: frequência definida pela organização] para obter indicações de [Atribuição: atividade inadequada ou incomum atividade definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e analisar os registros de auditoria dos recursos por ele implantados (para incluir aplicativos, sistemas operacionais, bancos de dados e software). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-6b"></a>Controle NIST 800-53 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Revisão de Auditoria, Análise e Relatórios

**AU-6.b** A organização relata as descobertas para [Atribuição: equipe ou funções definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por relatar as descobertas de atividade inadequada ou incomum (definido na AU-06.a) em recursos implantados pelo cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-1"></a>Controle NIST 800-53 AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Revisão de Auditoria, análise e Relatórios | Integração do Processo

**AU-6 (1)** A organização implanta mecanismos automatizados para integrar processos de revisão de auditoria, análise e relatórios para dar suporte a processos organizacionais de investigação e resposta a atividades suspeitas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de uma capacidade de revisão, análise e relatório de auditoria centralizada de nível empresarial. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-3"></a>Controle NIST 800-53 AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Revisão, análise e relatório de auditoria | Correlacionar repositórios de auditoria

**AU-6 (3)** A organização analisa e correlaciona registros de auditoria em diferentes repositórios para obter reconhecimento situacional de toda a organização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa a solução de Log Analytics no OMS para centralizar os dados de auditoria em recursos implantados, dando suporte ao reconhecimento situacional de toda a organização. Os clientes podem escolher integrar ainda mais o Log Analytics com outros sistemas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-4"></a>Controle NIST 800-53 AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Revisão, análise e relatório de auditoria | Revisão e análise central

**AU-6 (4)** O sistema de informações fornece a capacidade de revisar e analisar centralmente registros de auditoria de vários componentes no sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa a solução de Log Analytics no OMS para centralizar os dados de auditoria em recursos implantados, dando suporte à revisão, à análise e a relatórios centralizados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-5"></a>Controle NIST 800-53 AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Revisão, análise e relatório de auditoria | Recursos de integração/verificação e monitoramento

**AU-6 (5)** A organização integra a análise dos registros de auditoria com a análise de [Seleção (um ou mais): informações de verificação de vulnerabilidade; dados de desempenho; informações de monitoramento do sistema de informações; [Atribuição: informações/dados definidos pela organização coletados de outras fontes]] para melhorar ainda mais a capacidade de identificar atividades incomuns ou inadequadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta a solução de segurança e auditoria do OMS. Essa solução fornece uma visão abrangente da postura de segurança. O painel de Segurança e auditoria fornece percepções de alto nível do estado de segurança de recursos implantados usando os dados disponíveis em soluções do OMS implantadas, integrando dados de log e dados de vulnerabilidade da avaliação de linha de base e patch. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-6"></a>Controle NIST 800-53 AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Revisão, análise e relatório de auditoria | Correlação com o monitoramento físico

**AU-6 (6)** A organização correlaciona informações de registros de auditoria com informações obtidas do monitoramento do acesso físico para melhorar ainda mais a capacidade de identificar atividades suspeitas, inadequadas, incomuns ou maléficas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a nenhum recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | A equipe SIM do Microsoft Azure usa os dados de monitoramento físico relacionados e os correlaciona com registros de auditoria para determinar se houve qualquer violação lógica associada ou comportamento suspeito quando incidentes físicos são detectados. |


 ### <a name="nist-800-53-control-au-6-7"></a>Controle NIST 800-53 AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Revisão de Auditoria, análise e Relatórios | Ações Permitidas

**AU-6 (7)** A organização especifica as ações permitidas para cada [Seleção (um ou mais): processo de sistema de informações; função; usuário] associado com a revisão, análise e relatório de informações de auditoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais do Windows implantadas por essa Especificação Técnica do Azure implementam permissões em nível de sistema operacional que restringem as ações que um usuário pode executar em relação a informações de auditoria. Dentro do Azure, os usuários ou grupos de usuários podem ser atribuídos a funções (por exemplo, proprietário, colaborador, leitor ou uma função personalizada) para restringir as ações disponíveis em relação a todos os recursos ou soluções implantadas, incluindo o Log Analytics.  |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-6-10"></a>Controle NIST 800-53 AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Revisão de Auditoria, análise e Relatórios | Ajuste de Nível de Auditoria

**AU-6 (10)** A organização ajusta o nível de revisão, análise e relatório de auditoria dentro do sistema de informações quando houver uma alteração no risco com base em informações de imposição da lei, informações de inteligência ou outras fontes confiáveis de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável para ajustar o nível de revisão, análise e emissão de relatórios de auditoria para recursos implantados pelo cliente (incluindo aplicativos, sistemas operacionais, bancos de dados e software) quando houver uma alteração no risco com base nas informações fornecidas por imposição da lei, inteligência ou outras fontes confiáveis. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-7a"></a>Controle NIST 800-53 AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Redução de Auditoria e Geração de Relatórios

**AU-7.a** O sistema de informações fornece uma funcionalidade de redução de auditoria e geração de relatório que oferece suporte aos requisitos de revisão, análise e relatórios de auditoria sob demanda e investigações após o fato de incidentes de segurança.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa a solução Log Analytics no OMS. O Log Analytics fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-7b"></a>Controle NIST 800-53 AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Redução de Auditoria e Geração de Relatórios

**AU-7.b** O sistema de informações fornece uma funcionalidade de redução de auditoria e geração de relatórios que não altera o conteúdo original ou a ordenação de tempo de registros de auditoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa a solução Log Analytics no OMS. O Log Analytics fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. O conteúdo e a ordenação de tempo de registros de auditoria não são alterados quando coletados pelo Log Analytics. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-7-1"></a>Controle NIST 800-53 AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Redução de Auditoria e Geração de Relatórios | Processamento de Automação

**AU-7 (1)** O sistema de informações fornece a capacidade de processar os registros de auditoria para eventos de interesse com base em [Atribuição: campos de auditoria com registros de auditoria definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa a solução Log Analytics no OMS. O Log Analytics fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação. O Log Analytics inclui uma linguagem de consulta avançada para extrair os dados armazenados no repositório. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-8a"></a>Controle NIST 800-53 AU-8.a

#### <a name="time-stamps"></a>Carimbos de Data/Hora

**AU-8.a** O sistema de informações usa relógios do sistema interno para gerar os carimbos de data e hora para registros de auditoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Recursos implantados por esta Especificação Técnica do Azure usam relógios do sistema internos para gerar os carimbos de data e hora para registros de auditoria. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-8b"></a>Controle NIST 800-53 AU-8.b

#### <a name="time-stamps"></a>Carimbos de Data/Hora

**AU-8.b** O sistema de informações registra os carimbos de data e hora para registros de auditoria que podem ser mapeados para o Tempo Universal Coordenado (UTC) ou para a Hora de Greenwich (GMT) e atende a [Atribuição: granularidade de medição de tempo definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Recursos implantados por esta Especificação Técnica do Azure usam relógios do sistema internos para gerar os carimbos de data e hora para registros de auditoria. Os carimbos de data e hora são registrados em UTC. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-8-1a"></a>Controle NIST 800-53 AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Carimbos de Data e Hora | Sincronização com fonte de horário autoritativo

**AU-8 (1) .a** O sistema de informações compara os relógios internos do sistema de informações [Atribuição: frequência definida pela organização] com [Atribuição: fonte de horário autoritativo definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Recursos implantados por esta Especificação Técnica do Azure usam relógios do sistema internos para gerar os carimbos de data e hora para registros de auditoria. Relógios internos do sistema estão configurados para sincronização com uma fonte de horário autoritativo. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-8-1b"></a>Controle NIST 800-53 AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Carimbos de Data e Hora | Sincronização com fonte de horário autoritativo

**AU-8 (1).b** O sistema de informações sincroniza os relógios internos do sistema com a fonte de horário autoritativo quando a diferença de tempo for maior que [Atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Recursos implantados por esta Especificação Técnica do Azure usam relógios do sistema internos para gerar os carimbos de data e hora para registros de auditoria. Relógios internos do sistema estão configurados para sincronização com uma fonte de horário autoritativo. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-9"></a>Controle NIST 800-53 AU-9

#### <a name="protection-of-audit-information"></a>Proteção de Informações de Auditoria

**AU-9** O sistema de informações protege as ferramentas de auditoria e informações de auditoria contra acesso, modificação e exclusão não autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Controles de acesso lógicos são usados para proteger informações e ferramentas de auditoria dentro da Especificação Técnica do Azure contra acesso, modificação e exclusão não autorizados. O Azure Active Directory impõe o acesso aprovado lógico usando associações de grupo baseadas em função. A capacidade de exibir informações de auditoria e usar as ferramentas de auditoria pode ser limitada aos usuários que requerem essas permissões. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-9-2"></a>Controle NIST 800-53 AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Proteção de informações de auditoria | Backup de auditoria em componentes / sistemas físicos separados

**AU-9 (2)** O sistema de informações faz backup de registros de auditoria [Atribuição: frequência definida pela organização] em um componente do sistema ou um sistema fisicamente diferente do sistema ou componente que está sendo auditado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa o serviço do Log Analytics no OMS. VMs implantadas e contas de armazenamento de diagnóstico do Azure são fontes conectadas ao Log Analytics e são mantidas separadamente de sua origem. Os dados são coletados pelo OMS quase em tempo real. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-9-3"></a>Controle NIST 800-53 AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Proteção de informações de auditoria | Proteção de criptografia

**AU-9 (3)** O sistema de informações implementa mecanismos de criptografia para proteger a integridade das ferramentas de auditoria e informações de auditoria.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa o serviço do Log Analytics no OMS. O Log Analytics garante que os dados de entrada sejam de uma fonte confiável ao validar certificados e a integridade dos dados com a autenticação do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-9-4"></a>Controle NIST 800-53 AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Proteção de informações de auditoria | Acesso ao subconjunto de usuários com privilégios

**AU-9 (4)** A organização autoriza o acesso ao gerenciamento da funcionalidade de auditoria apenas a [Atribuição: subconjunto de usuários privilegiados definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Controles de acesso lógicos são usados para proteger informações e ferramentas de auditoria dentro da Especificação Técnica do Azure contra acesso, modificação e exclusão não autorizados. O Azure Active Directory impõe o acesso aprovado lógico usando associações de grupo baseadas em função. A capacidade de exibir informações de auditoria e usar as ferramentas de auditoria pode ser limitada aos usuários que requerem essas permissões.
 |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-10"></a>Controle NIST 800-53 AU-10

#### <a name="non-repudiation"></a>Não recusa

**AU-10** O sistema de informações protege contra a falsa negação de uma pessoa (ou processo atuando em nome de uma pessoa) sobre a realização de [Atribuição: ações a serem cobertas pelo não repúdio definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A capacidade de auditoria para estas Especificações Técnicas do Azure é fornecida pelo Azure Monitor e pelo serviço Log Analytics no OMS. O Azure Monitor fornece os logs de auditoria detalhados sobre atividade associada a recursos implantados. Esses e os logs de nível de sistema operacional são coletados pelo Log Analytics e armazenados no repositório do OMS. Esses logs continham registros detalhados de informações de eventos do sistema e podem ajudar a proteger contra não recusa. Além disso, o acesso aos dados de log é restringido usando o controle de acesso baseado em função para evitar modificação ou exclusão não autorizada de dados do log. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-11"></a>Controle NIST 800-53 AU-11

#### <a name="audit-record-retention"></a>Retenção de registros de auditoria

**AU-11** A organização mantém registros de auditoria por [Atribuição: período de tempo consistente com a política de retenção de registros definido pela organização] para fornecer suporte para investigações após o fato de incidentes de segurança e para atender aos requisitos de retenção de informações organizacionais e regulatórias.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa o serviço do Log Analytics no OMS. O Log Analytics fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Depois de coletados, os dados são mantidos por um ano de acordo com a configuração do Log Analytics. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-12a"></a>Controle NIST 800-53 AU-12.a

#### <a name="audit-generation"></a>Geração de auditoria

**AU-12.a** O sistema de informações fornece a funcionalidade de geração de registro de auditoria para eventos auditáveis definidos na AU-2 a. em [Atribuição: componentes do sistema de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Eventos auditados por esta Especificação Técnica do Azure incluem os auditados pelos logs de atividades do Azure para os recursos implantados, logs de nível de sistema operacional, logs do Active Directory e os logs do SQL Server. Os clientes podem selecionar eventos adicionais a serem auditados para atender às necessidades da tarefa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-12b"></a>Controle NIST 800-53 AU-12.b

#### <a name="audit-generation"></a>Geração de auditoria

**AU-12.b** O sistema de informações permite que [Atribuição: equipe ou funções definidas pela organização] selecione quais eventos auditáveis devem ser examinados pelos componentes específicos do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso a funções de auditoria é restringido usando o controle de acesso baseado em função no Azure e no nível do sistema operacional da máquina virtual. A configuração de eventos selecionados a serem auditados por recursos implantados por esta Especificação Técnica do Azure pode ser definida por usuários com autorização baseada em função apropriada. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-au-12c"></a>Controle NIST 800-53 AU-12.c

#### <a name="audit-generation"></a>Geração de auditoria

**AU-12.c** O sistema de informações gera registros de auditoria para os eventos definidos na AU-2.d. com o conteúdo definido na AU-3.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Eventos auditados por esta Especificação Técnica do Azure incluem os auditados pelos logs de atividades do Azure para os recursos implantados, logs de nível de sistema operacional, logs do Active Directory e os logs do SQL Server. Os clientes podem selecionar eventos adicionais a serem auditados para atender às necessidades da tarefa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-12-1"></a>Controle NIST 800-53 AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Geração de auditoria | Trilha de auditoria em todo o sistema / correlacionada ao tempo

**AU-12 (1)** O sistema de informações compila registros de auditoria de [Atribuição: componentes do sistema de informações definidos pela organização] em uma trilha de auditoria de todo o sistema (lógico ou físico) que está correlacionado com a hora para dentro de [Atribuição: nível de tolerância para relacionamento entre carimbos de hora de registros individuais na trilha de auditoria definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implementa o serviço do Log Analytics no OMS. O Log Analytics fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Carimbos de data/hora do registro de auditoria não são alterados; portanto, a trilha de auditoria é correlacionada com a hora. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-au-12-3"></a>Controle NIST 800-53 AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Geração de auditoria | Alterações feitas por pessoas autorizadas

**AU-12 (3)** O sistema de informações fornece a capacidade para [Atribuição: pessoas ou funções definidas pela organização] para alterar a auditoria a ser executada em [Atribuição: componentes do sistema de informações definidos pela organização] com base em [Atribuição: critérios de evento selecionável definidos pela organização] em [atribuição: limites de tempo definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso a funções de auditoria é restringido usando o controle de acesso baseado em função no Azure e no nível do sistema operacional da máquina virtual. A configuração de eventos selecionados a serem auditados por recursos implantados por esta Especificação Técnica do Azure pode ser definida por usuários com autorização baseada em função apropriada. |
| **Provedor (Microsoft Azure)** | Não aplicável |
