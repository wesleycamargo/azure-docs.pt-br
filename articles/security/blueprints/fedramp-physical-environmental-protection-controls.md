---
title: "Projeto de segurança e conformidade do Azure – Automação de aplicativos Web para FedRAMP – Proteção física e ambiental"
description: "Automação de aplicativos Web para FedRAMP – Proteção física e ambiental"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 792b9da0f4e5ec73c39f56a6e4805cf3c37133c4
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="physical-and-environmental-protection-pe"></a>Proteção Física e Ambiental (PE)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-pe-1"></a>Controle NIST 800-53 PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Política e Procedimentos de Proteção Física e Ambiental

**PE-1** A organização desenvolve, documenta e distribui a [Atribuição: equipe ou funções definidas pela organização] uma política de proteção física e ambiental que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de proteção física e ambiental e os controles de proteção física e ambiental associados; e revisa e atualiza a política de proteção física e ambiental atual [Atribuição: frequência definida pela organização]; e os procedimentos de proteção do sistema e das comunicações [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de proteção física e ambiental de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pe-2a"></a>Controle NIST 800-53 PE-2.a

#### <a name="physical-access-authorizations"></a>Autorizações de Acesso Físico

**PE-2.a** A organização desenvolve, aprova e mantém uma lista de pessoas com acesso autorizado ao recurso de onde reside o sistema de informações.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. O acesso físico a um datacenter da Microsoft deve ser aprovado pela equipe de gerenciamento de Datacenter (DCM) usando a ferramenta de acesso do datacenter. Atribuições de acesso exigem uma data de término, após a qual o acesso é removido automaticamente e deve ser reaprovado. Além disso, quando o acesso não for necessário, responsáveis pela segurança de datacenter ou gerenciamento solicitar manualmente o encerramento de acesso. |


 ## <a name="nist-800-53-control-pe-2b"></a>Controle NIST 800-53 PE-2.b

#### <a name="physical-access-authorizations"></a>Autorizações de Acesso Físico

**PE-2.b** A organização emite credenciais de autorização para acesso ao recurso.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. A ferramenta de acesso de datacenter é a origem autoritativa listando todas as pessoas com acesso autorizado a um datacenter específico. A ferramenta está vinculada com dispositivos de controle de acesso de segurança física do datacenter e autoriza o acesso com base nos níveis de acesso que foram aprovados pela equipe do DCM. Níveis de acesso são atribuídos na ferramenta para ou uma notificação emitida por um usuário da Microsoft ou uma notificação de acesso temporário que é atribuída no datacenter pelo Supervisor de sala de controle (CRS). Níveis de acesso são aprovados pela equipe do DCM. Além das credenciais atribuídas a selos físicos, algumas áreas do datacenter exigem o registro de dados biométricos do usuário (geometria da mão ou impressão digital). |


 ## <a name="nist-800-53-control-pe-2c"></a>Controle NIST 800-53 PE-2.c

#### <a name="physical-access-authorizations"></a>Autorizações de Acesso Físico

**PE-2.c** A organização revisa a lista de acesso detalhando acesso autorizado recurso por indivíduos [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Além da revogação de acesso descrita na parte A, Microsoft Azure revisa listas de acesso autorizado para datacenters do Azure a cada 90 dias para remoção/acesso individual conforme o necessário de atualização. |


 ## <a name="nist-800-53-control-pe-2d"></a>Controle NIST 800-53 PE-2.d

#### <a name="physical-access-authorizations"></a>Autorizações de Acesso Físico

**PE-2.d** A organização remove indivíduos da lista de acesso do recurso quando o acesso não for mais necessário.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure remove o acesso automaticamente quando a data de término da atribuição de acesso é atingida. Quando o acesso não for necessário, responsáveis pela segurança de datacenter ou gerenciamento solicitarão manualmente o encerramento de acesso. Além disso, o Microsoft Azure removerá autorizações acesso desnecessárias descobertas como resultado da revisão de lista de acesso descrita na parte C. |


 ## <a name="nist-800-53-control-pe-3a"></a>Controle NIST 800-53 PE-3.a

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.a** A organização impõe autorizações de acesso físico a [Atribuição: pontos de entrada/saída para o recurso onde reside o sistema de informações definidos pela organização], verificando as autorizações de acessos individuais antes de conceder acesso ao recurso; e controlando a entrada/saída para o recurso usando [Seleção (um ou mais): [Atribuição: dispositivos/sistemas de controle de acesso físico definidos pela organização]; protege].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure impõe autorizações de acesso físico para todos os pontos de acesso físico para datacenters do Azure usando pessoal, alarmes, vídeo, autenticação multifator e dispositivos do portal armadilha humana 24/7. |


 ## <a name="nist-800-53-control-pe-3b"></a>Controle NIST 800-53 PE-3.b

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.b** A organização mantém os logs de auditoria de acesso físico para [Atribuição: pontos de entrada/saída definidos pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Todos os acessos para instalações de datacenter do Azure são registrados e auditados. |


 ## <a name="nist-800-53-control-pe-3c"></a>Controle NIST 800-53 PE-3.c

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.c** A organização fornece [Atribuição: proteções de segurança definidas pela organização] para controlar o acesso a áreas de recurso oficialmente designadas como acessíveis publicamente.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Datacenters do Azure não contêm áreas que são designadas como publicamente acessíveis. |


 ## <a name="nist-800-53-control-pe-3d"></a>Controle NIST 800-53 PE-3.d

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.d** A organização controla todas os visitantes e monitora a atividade de visitante [Atribuição: circunstâncias que exigem escorts e monitoramento do visitante definidas pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Todos os visitantes que tiveram acesso aprovado para o datacenter (consulte PE-2) são designados como 'Somente Escort' em suas notificações ou através de outras indicações visuais (por exemplo, crachás coloridos) e devem permanecer com seus escorts em todos os momentos. Visitantes escortados não têm nenhum nível de acesso concedido a eles e podem apenas trafegar no acesso de seus escorts. Escorts monitoraram todas as atividades de seu visitante enquanto no data center. |


 ## <a name="nist-800-53-control-pe-3e"></a>Controle NIST 800-53 PE-3.e

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.e** A organização protege as chaves, combinações e outros dispositivos de acesso físico.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Chaves físicas e notificações de acesso temporário são protegidas no Centro de operações de segurança (SOC). Os agentes de segurança estão funcionando 24x7. Chaves são check-out para pessoas específicas correspondendo a notificação de acesso da pessoa para a chave física. Inventários de chave são realizados durante cada turno e as chaves não podem ficar fora do local. |


 ## <a name="nist-800-53-control-pe-3f"></a>Controle NIST 800-53 PE-3.f

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.f** Os inventários de organização [Atribuição: dispositivos de acesso físico definidos pela organização] cada [atribuição: frequência definida pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Dispositivos de acesso físico em datacenters do Azure estão sendo inventariados pelo menos anualmente. Chaves e notificações de acesso temporário são inventariadas várias vezes ao dia durante cada turno. Leitores de notificação de acesso e dispositivos similares de acesso são vinculados para o sistema de segurança física em que o status é representado continuamente. |


 ## <a name="nist-800-53-control-pe-3g"></a>Controle NIST 800-53 PE-3.g

#### <a name="physical-access-control"></a>Controles de Acesso Físico

**PE-3.g** A organização altera combinações e chaves [Atribuição: frequência definida pela organização] e/ou quando as chaves forem perdidas, combinações forem comprometidas ou indivíduos forem transferidos ou encerrados.  

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Datacenters do Microsoft Azure têm procedimentos para implementar em casos em que uma notificação de acesso ou chave for perdida ou uma pessoa seja finalizada ou transferida. Em caso de encerramento ou transferência, o acesso da pessoa é removido imediatamente do sistema e removido de sua notificação de acesso. |


 ### <a name="nist-800-53-control-pe-3-1"></a>Controle NIST 800-53 PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>Controle de Acesso Físico | Acesso de Sistema de Informações

**PE-3 (1)** A organização impõe autorizações de acesso físico ao sistema de informações além dos controles de acesso físico para o recurso em [Atribuição: espaços físicos que contém um ou mais componentes do o sistema de informações definidos pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure restringe ainda mais áreas dentro dos datacenters da Microsoft que tenham sistemas críticos (por exemplo, colocações, ambientes críticos, salas MDF, etc.) por meio de vários mecanismos de segurança como controle de acesso eletrônico, dispositivos biométricos, e controles anti-passback. Acesso ao Azure colocations recebem como um nível superior, separado de acesso DCAT que outras áreas do acesso do datacenter. Além disso, todos os fornecedores e FTEs do Azure que tenham acesso às colocações do Azure Governamental são exigidos a formalmente passar por uma triagem da Microsoft Cloud Screening e verificação de cidadania estadunidense antes de terem acesso autorizado ao ambiente. Consulte a seção PS-03 para obter mais detalhes sobre a triagem de nuvem para os colocations Azure Governamental. |


 ## <a name="nist-800-53-control-pe-4"></a>Controle NIST 800-53 PE-4

#### <a name="access-control-for-transmission-medium"></a>Controle de Acesso de Média de Transmissão

**PE-4** A organização controla o acesso físico a [Atribuição: linhas de transmissão e distribuição de sistema de informações definidas por organização] em instalações organizacionais usando [Atribuição: proteções de segurança definidas pela organização] .

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure implementou o controle de acesso de média de transmissão por meio do design e da criação das salas de quadro de distribuição principal (MDF) e colocations para proteger linhas de distribuição e transmissão de informações do sistema contra danos acidentais interrupção e violação física. Acesso para salas MDF e colocations exigem (notificação de acesso e biometria) de autenticação de dois fatores. Isso garante que o acesso seja restrito a somente pessoas autorizadas (consulte PE-2, PE-3). Dentro de MDF, transmissão e distribuição de linhas são protegidas contra danos acidentais, interrupção e violação física com o uso de canais de metal, racks bloqueados, compartimentos ou bandejas de cabo. |


 ## <a name="nist-800-53-control-pe-5"></a>Controle NIST 800-53 PE-5

#### <a name="access-control-for-output-devices"></a>Controle de Acesso para Dispositivos de Saída

**PE-5** A organização controla o acesso físico aos dispositivos de saída do sistema de informações para impedir que pessoas não autorizadas obtenham a saída.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Data centers do Microsoft Azure não têm dispositivos de saída (monitores, impressoras, dispositivos de áudio, etc.) permanentemente conectados aos ativos do Azure ou ativos compartilhados do Azure. Além de não ter dispositivos de saída, responsáveis pela segurança executam explicações passo a passo física do recurso várias vezes por turno procurando itens, como racks sendo protegidos e portas sendo bloqueadas. Acesso de Datacenter é limitado às pessoas que aprovaram autorizações de acesso. Colocations exigem (notificação de acesso e biometria) de autenticação de dois fatores para ganhar acesso. |


 ## <a name="nist-800-53-control-pe-6a"></a>Controle NIST 800-53 PE-6.a

#### <a name="monitoring-physical-access"></a>Monitorando Acesso Físico

**PE-6.a** A organização monitora o acesso físico ao recurso de localização do sistema de informações para detectar e responder a incidentes de segurança física da organização.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Acesso físico é monitorado com a implementação de dispositivos de segurança e processos em data centers. Exemplos incluem monitoramento eletrônico 24x7 de controle de acesso, alarmes e sistemas de vídeo bem como 24x7 em patrulhas de segurança de site para terrenos e recurso. O Supervisor de Sala de Controle está localizado no SOC em todos os momentos para fornecer monitoramento de acesso físico no data center. |


 ## <a name="nist-800-53-control-pe-6b"></a>Controle NIST 800-53 PE-6.b

#### <a name="monitoring-physical-access"></a>Monitorando Acesso Físico

**PE-6.b** A organização revisa os logs de acesso físico [Atribuição: frequência definida pela organização] e após a ocorrência de [Atribuição: eventos definidos pelo organização ou potenciais indicações de eventos].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Logs de acesso físico são examinados continuamente e mantidos para revisão investigativa subsequente. |


 ## <a name="nist-800-53-control-pe-6c"></a>Controle NIST 800-53 PE-6.c

#### <a name="monitoring-physical-access"></a>Monitorando Acesso Físico

**PE-6.c** A organização coordena os resultados de análises e investigações com a capacidade de resposta a incidentes organizacional. 

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Eventos de segurança que ocorrem dentro do datacenter são documentados pela equipe de segurança. A equipe de segurança cria relatórios que capturam os detalhes de um evento de segurança depois que o evento ocorre. <br /> Para incidentes que exigem a notificação do governo, a equipe de segurança do Microsoft Azure trabalhará com o provedor de aplicativo principal (por exemplo, O365) para notificar o cliente de órgão do governo, nos certificados e FedRAMP nas diretrizes US-CERT (consulte IR-6). |


 ### <a name="nist-800-53-control-pe-6-1"></a>Controle NIST 800-53 PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorando o Acesso Físico | Alarmes de Invasão / Equipamento de Vigilância

**PE-6 (1)** A organização monitora alarmes de invasão física e equipamentos de vigilância.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Além da segurança no local 24x7, data centers do Microsoft Azure (concedidos e totalmente gerenciados) também utilizam monitoramento CCTV e sistemas de alarme. Alarmes são monitorados e respondidos pelo Supervisor de Sala de Controle colocado 24x7 no SOC. Durante uma situação de resposta, o Supervisor de Sala de Controle utiliza câmeras na área do incidente que está sendo investigada para dar ao respondente informações em tempo real. |


 ### <a name="nist-800-53-control-pe-6-4"></a>Controle NIST 800-53 PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorando o Acesso Físico | Acesso Físico aos Sistemas de Informações de Monitoramento

**PE-6 (4)** A organização impõe autorizações de acesso físico ao sistema de informações além dos controles de acesso físico para o recurso em [Atribuição: espaços físicos que contém um ou mais componentes do o sistema de informações definidos pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure monitora o acesso físico para as instalações, bem como os sistemas de informações dentro dos datacenters. Todo o equipamento de serviços online da Microsoft é colocado em locais em data centers, onde o acesso físico é monitorado. Todos os espaços de colocação e MDF são protegidos por controle de acesso, alarmes e vídeo. |


 ## <a name="nist-800-53-control-pe-8a"></a>Controle NIST 800-53 PE-8.a

#### <a name="visitor-access-records"></a>Registros de Acesso de Visitante

**PE-8.a** A organização mantém registros de visitante de acesso ao recurso de localização do sistema de informações para [Atribuição: período de tempo definido pelo organização].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Registros de acesso do Datacenter são mantidos na ferramenta de acesso do datacenter na forma de solicitações aprovadas. Conforme descrito em PE-3, visitantes são exigidos a serem escortados em todos os momentos. O acesso de escort dentro do datacenter é registrado e se necessário pode estar correlacionado ao visitante para análise futura. |


 ## <a name="nist-800-53-control-pe-8b"></a>Controle NIST 800-53 PE-8.b

#### <a name="visitor-access-records"></a>Registros de Acesso de Visitante

**PE-8.b** A organização revisa os registros de acesso de visitante [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Os visitantes com uma solicitação de acesso aprovado terão o registro de acesso revisado no momento em que sua identificação for verificada em relação a um formulário de ID emitida pelo governo ou notificação emitida pela Microsoft. Conforme descrito em PE-3, visitantes são exigidos a serem escortados em todos os momentos enquanto no datacenter. |


 ### <a name="nist-800-53-control-pe-8-1"></a>Controle NIST 800-53 PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>Registros de Acesso de Visitante | Revisão / Manutenção de Registros Automatizada

**PE-8 (1)** A organização emprega mecanismos automatizados para facilitar a manutenção e análise dos registros de acesso de visitante.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure mantém registros de acesso de datacenter em DCAT na forma de solicitações DCAT aprovadas. Solicitações DCAT só podem ser aprovadas pela equipe do DCM. Níveis de acesso dentro do datacenter são atribuídos e gerenciados em DCAT. Acesso de Datacenter é revisado trimestralmente. Todo o acesso aos data centers do Azure é registrado no DCAT e está disponível para futuras investigações possíveis. Visitantes são exigidos a serem escortados em todos os momentos. O acesso de escort dentro do datacenter é registrado dentro do sistema de monitoramento de alarme e se necessário pode estar correlacionado ao visitante para análise futura. Acesso de visitante está sendo revisado continuamente pelo escort atribuído e o supervisor de sala de controle por meio de CCTV e o sistema de monitoramento de alarme. Os visitantes não são fornecidos com acesso e devem ser acompanhados de seus escorts em todos os momentos. |


 ## <a name="nist-800-53-control-pe-9"></a>Controle NIST 800-53 PE-9

#### <a name="power-equipment-and-cabling"></a>Equipamento de Energia e Cabeamento

**PE-9** A organização protege o cabeamento de alimentação para o sistema de informações de danos e a destruição e equipamentos de energia.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. O Microsoft Azure fornece espaços protetivos e rotulação apropriada para os cabos. Equipamentos de infraestrutura do Microsoft Azure, por exemplo, cabos, linhas elétricas e geradores devem ser colocados em ambientes que foram projetados para serem protegidos contra riscos ambientais, como roubo, incêndio, explosivos, fumaça, água, poeira, vibração, terremoto, chemicals prejudiciais, interferência elétrica, falhas de energia elétricas problemas (picos). Todos os ativos de serviços online portátil (por exemplo, racks, servidores, dispositivos de rede) devem ser bloqueados ou presos no local para fornecer proteção contra danos de roubo ou movimentação. Cabos de energia e sistema de informação dentro de um ambiente do Microsoft Azure são rotulados adequadamente e protegidos contra interceptação ou danos. Cabos de energia e sistema de informação são separados uns dos outros em todos os pontos em um ambiente para evitar interferência. |


 ## <a name="nist-800-53-control-pe-10a"></a>Controle NIST 800-53 PE-10.a

#### <a name="emergency-shutoff"></a>Desligamento de Emergência

**PE-10.a** A organização fornece a capacidade de desligar energia para o sistema de informações ou componentes individuais em situações de emergência.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure instalou botões de emergência Power Off (EPO) em locais dentro do datacenter conforme exigido pelo código de incêndio local. Em alguns datacenters gerenciados do Microsoft Azure, o design do datacenter não requer mais botões EPO. |


 ## <a name="nist-800-53-control-pe-10b"></a>Controle NIST 800-53 PE-10.b

#### <a name="emergency-shutoff"></a>Desligamento de Emergência

**PE-10.b** A organização coloca opções de desligamento por emergência ou dispositivos em [Atribuição: local pelo sistema de informações ou componente do sistema definido pela organização] para facilitar o acesso fácil e seguro para a equipe.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Botões EPO são estrategicamente colocados para permitir a ativação em situações de emergência. Botões EPO podem ser colocado no colocations, Centros de Operações de Recursos operados (FOCs), ou conforme exigido pelo código de incêndio local. |


 ## <a name="nist-800-53-control-pe-10c"></a>Controle NIST 800-53 PE-10.c

#### <a name="emergency-shutoff"></a>Desligamento de Emergência

**PE-10.c** A organização protege a capacidade de suspensão de energia de emergência de ativação não autorizada.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Para evitar a ativação acidental, botões EPO podem ter um compartimento de proteção, requerer ativação dupla ou utilizar um alarme audível como um aviso antes da ativação. Além disso, os botões EPO estão sob vigilância em vídeo. |


 ## <a name="nist-800-53-control-pe-11"></a>Controle NIST 800-53 PE-11

#### <a name="emergency-power"></a>Alimentação de Emergência

**PE-11** A organização fornece uma fonte de alimentação ininterrupta de curto prazo para facilitar a [Seleção (um ou mais): um desligamento ordenado do sistema de informações; transição do sistema de informações a longo prazo potência alternativo] no caso de um perda da fonte de alimentação principal.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure implementou alimentação emergência protegendo equipamentos de datacenter e circuitos com uma fonte de alimentação ininterrupta fornecem sistema (no-break) que fornece uma fonte de alimentação de curto prazo para fornecer a potência até geradores possa ficar online. |


 ### <a name="nist-800-53-control-pe-11-1"></a>Controle NIST 800-53 PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Alimentação de Emergência | Fonte de Alimentação Alternativa a Longo Prazo - Recurso Operacional Mínimo

**PE-11 (1)** A organização fornece uma fonte de energia alternativa a longo prazo para o sistema de informações que é capaz de manter no mínimo necessário recurso operacional no caso de uma perda estendido da fonte de alimentação principal.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implantou uma fonte de energia alternativa a longo prazo para o sistema de informações que é capaz de manter no mínimo necessário o recurso operacional no caso de uma perda estendida da fonte de alimentação principal. Quando a alimentação falha ou atinge um nível de tensão inaceitável, sistemas de UPS (no-break) são instantaneamente ativados e assumem a carga de energia. Isso fornece capacidade suficiente para executar os servidores até que os geradores possam assumir. Geradores de emergência fornecem a potência de backup para interrupções prolongadas e manutenção planejada e podem operar o data center com reservas de combustível no local no caso de um desastre natural. O Azure mantém gerador a diesel em muitos dos nossos datacenters. Geradores são usados quando necessário para ajudar a manter a estabilidade da grade ou em reparo extraordinário e situações de manutenção que nos exigem levar nossos datacenters fora da grade de alimentação. |


 ## <a name="nist-800-53-control-pe-12"></a>Controle NIST 800-53 PE-12

#### <a name="emergency-lighting"></a>Iluminação de Emergência

**PE-12** A organização emprega e mantém a iluminação de emergência automática para o sistema de informações que ativa no caso de uma interrupção ou queda de energia e que abrange as saídas de emergência e rotas de evacuação da instalação.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Data centers do Microsoft Azure (concedidos e totalmente gerenciados) implementam a iluminação de emergência na forma de sobrecarga de emergência iluminação circuitos dedicados feita por sistemas de gerador (consulte PE-11). Iluminação de emergência automática é implementada ao longo de todas as rotas de evacuação, saídas de emergência e dentro do colocations de acordo com o Código de Segurança à Vida da Associação de Proteção e Fogo Nacional (NFPA). Caso a alimentação do local for perdida, a iluminação de emergência alternará automaticamente a potência fornecida pelos sistemas no-break e gerador. Os sistemas de emergência de iluminação em data centers do Microsoft Azure passam por manutenção de rotina para garantir que eles permaneçam em funcionamento adequado. |


 ## <a name="nist-800-53-control-pe-13"></a>Controle NIST 800-53 PE-13

#### <a name="fire-protection"></a>Proteção ao Fogo

**PE-13** A organização emprega e mantém dispositivos/sistemas de detecção e supressão de incêndio para o sistema de informação que são suportados por uma fonte de energia independente.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure implementou proteção contra incêndio instalando detecção e acionando sistemas em data centers do Microsoft Azure. <br /> Data centers do Microsoft Azure implementam mecanismos de detecção de incêndio robustos. A abordagem de proteção do Microsoft Azure inclui o uso de detectores fotoelétricos de fumaça instalados abaixo do chão e no teto, que são integrados ao sistema de incêndio e proteção contra incêndios. Além disso, há sistemas Xtralis VESDA (Aparelho de Detecção de Fumaça Muito Antecipada) em cada colocação que monitora o ar. Unidades VESDA são sistemas de amostragem de ar altamente sensíveis instaladas em vários espaços de alto valor. Unidades VESDA permitem respostas investigativas antes de um alarme de detecção de incêndio real. <br /> Caixas de alarme de incêndio ‘Estação Pull' são instaladas em datacenters para notificação do alarme de incêndio manual. Extintores de incêndio estão localizados em datacenters e são propriamente inspecionados, servidos e marcados anualmente. A equipe de segurança patrulha todas as áreas do prédio várias vezes cada turno. A equipe de Datacenter executa uma apresentação de site diariamente, garantindo a inspeção de incêndio de todos os requisitos que estão sendo atendidos. <br /> Áreas que contêm equipamentos confidenciais (colocations, MDFs, etc.) são protegidas pelo bloqueio duplo pré-ação (cano seco) contra rociadores de incêndio. Rociadores de cano seco são um sistema de ação prévia de dois estágios que requer tanto uma ativação de cabeçalho contra incêndios (devido ao calor), bem como detecção de fumaça para liberar água. A ativação principal contra incêndios libera a pressão de ar dos canos que permite que os canos preencham com água. Água é lançada quando um detector de fumaça ou calor também é ativado. <br /> Detecção/supressão de incêndio e sistemas de iluminação de emergência são conectados com o datacenter no-break e os sistemas de gerador fornecendo para uma fonte de alimentação redundante. |


 ### <a name="nist-800-53-control-pe-13-1"></a>Controle NIST 800-53 PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>Proteção contra Fogo | Sistemas / Dispositivos de Detecção

**PE-13 (1)** A organização emprega acionar dispositivos/sistemas de detecção para o sistema de informações que ativar automaticamente e notificar [Atribuição: pessoal ou funções definidas pela organização] e [Atribuição: respondentes de emergência definidos pela organização] no caso de um incêndio.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure emprega dispositivos/sistemas de detecção para o sistema de informações que ativa automaticamente e notifica a equipe de datacenter junto com respondentes emergências no caso de um incêndio. Se um dos mecanismos de detecção de incêndio é ativado em qualquer colocação, o departamento de incêndio local será notificado automaticamente por meio do sistema de alarme de incêndio. Além disso, os sistemas de proteção ao fogo e detecção de fogo estão vinculados no sistema de segurança notificando a equipe de segurança e o local. |


 ### <a name="nist-800-53-control-pe-13-2"></a>Controle NIST 800-53 PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>Proteção contra Fogo | Sistemas / Dispositivos de Supressão

**PE-13 (2)** A organização emprega dispositivos/sistemas de supressão para o sistema de informações que fornecem uma notificação automática para qualquer ativação [Atribuição: pessoal ou funções definidas pela organização] e [Atribuição: respondentes de emergência definidos pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Se um dos mecanismos de detecção de incêndio é ativado no datacenter, o departamento de incêndio local será notificado automaticamente por meio do sistema de alarme de incêndio. Além disso, os sistemas de proteção ao fogo e detecção de fogo estão vinculados no sistema de segurança notificando a equipe de segurança e o local. |


 ### <a name="nist-800-53-control-pe-13-3"></a>Controle NIST 800-53 PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>Proteção contra Fogo | Supressão de Incêndio Automática

**PE-13 (3)** A organização emprega uma funcionalidade de supressão de incêndio automática para o sistema de informações quando o recurso não é atendido em uma base contínua.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Data centers do Microsoft Azure estão funcionando 24x7. Sistemas de supressão de fogo ativam automaticamente sem a intervenção manual quando uma situação de alarme de incêndio for detectada. |


 ## <a name="nist-800-53-control-pe-14a"></a>Controle NIST 800-53 PE-14.a

#### <a name="temperature-and-humidity-controls"></a>Controles de Umidade e Temperatura

**PE-14.a** A organização mantém os níveis de temperatura e umidade dentro do recurso em que o sistema de informações reside em [Atribuição: níveis aceitáveis definidos pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure mantém os níveis de temperatura e umidade acordo com diretrizes Society American de aquecimento, Refrigerating e engenheiros de ar condicionado (ASHRAE). Níveis de temperatura e umidade são monitorados continuamente pelo Sistema de Gerenciamento de Prédio (BMS). |


 ## <a name="nist-800-53-control-pe-14b"></a>Controle NIST 800-53 PE-14.b

#### <a name="temperature-and-humidity-controls"></a>Controles de Umidade e Temperatura

**PE-14.b** A organização monitora os níveis de temperatura e umidade [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Nos datacenters da Microsoft Azure, níveis de temperatura e umidade são monitorados continuamente pelo Sistema de Gerenciamento de Prédio (BMS). Pessoal do datacenter monitora o BMS do Centro de Operações (FOC), para que eles possam gerenciar a temperatura e umidade dentro do datacenter antes que quaisquer pontos de alarme sejam excedidos. |


 ### <a name="nist-800-53-control-pe-14-2"></a>Controle NIST 800-53 PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Controles de Umidade e Temperatura | Monitoramento Com Alarmes / Notificações

**PE-14 (2)** A organização emprega monitoramento de temperatura e umidade que fornece um alarme ou uma notificação de alterações potencialmente prejudiciais a equipe ou equipamento.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Nos datacenters da Microsoft Azure, níveis de temperatura e umidade são monitorados continuamente pelo Sistema de Gerenciamento de Prédio (BMS). Pessoal do datacenter monitora o BMS do Centro de Operações (FOC), para que eles possam gerenciar a temperatura e umidade dentro do datacenter antes que quaisquer pontos de alarme sejam excedidos. |


 ## <a name="nist-800-53-control-pe-15"></a>Controle NIST 800-53 PE-15

#### <a name="water-damage-protection"></a>Proteção contra Danos de Água

**PE-15** A organização protege o sistema de informações contra danos resultantes de vazamento de água, fornecendo válvulas mestres de isolamento ou desligamento que são conhecidas e acessíveis pelo pessoal da chave, funcionando corretamente.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. O Microsoft Azure fornece detecção de água/vazamento em áreas com um risco de vazamento de água (por exemplo, Unidades de Manipuladores de Ar). Sistemas de supressão de fogo também têm alarmes de detecção de vazamento que são monitorados. O sistema de detecção de água/vazamento é integrado com o sistema de notificação e alarme do local. Os rociadores de incêndio em data centers são zoneados. Equipe do datacenter está familiarizada com procedimentos de emergência exigindo o uso de válvulas de desligamento de água e suas localizações. Risers contra incêndios têm a capacidade de serem desligados individualmente ou como um grupo por meio de válvulas de porta. Todos os rociadores no espaço crítico são rociadores de tipo pré-ação duplamente intertravados que requerem duas formas de ativação antes do fluxo ser iniciado. A pressão do rociador de incêncio é monitorada e avisada contra vazamento de água. |


 ### <a name="nist-800-53-control-pe-15-1"></a>Controle NIST 800-53 PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>Proteção contra Danos de Água | Suporte de Automação

**PE-15 (1)** A organização emprega mecanismos automatizados para detectar a presença de água perto do sistema de informações e alertas [Atribuição: pessoal ou funções definidas pela organização].

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure utiliza os mecanismos automatizados para detectar a presença de água em data centers e alerta a equipe do data center. O Microsoft Azure fornece detecção de água/vazamento em áreas com um risco de vazamento de água (por exemplo, Unidades de Manipuladores de Ar). Sistemas de supressão de fogo também têm alarmes de detecção de vazamento que são monitorados. O sistema de detecção de água/vazamento é integrado com o sistema de notificação e alarme do local. A pressão do rociador de incêncio é monitorada e avisada contra vazamento de água. |


 ## <a name="nist-800-53-control-pe-16"></a>Controle NIST 800-53 PE-16

#### <a name="delivery-and-removal"></a>Entrega e Remoção

**PE-16** A organização autoriza, monitora e controla [Atribuição: tipos definidos pela organização de componentes do sistema de informações] inserindo e saindo do recurso e mantém registros desses itens.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Microsoft Azure implementa esse requisito em nome dos clientes. Microsoft Azure implementa aplicação rigorosa do que é permitido para entrar e sair do datacenter. Todos os componentes/ativos de sistema são controlados no banco de dados de ferramenta de gerenciamento ativo. |


 ## <a name="nist-800-53-control-pe-17a"></a>Controle NIST 800-53 PE-17.a

#### <a name="alternate-work-site"></a>Site de Trabalho Alternado

**PE-17.a** A organização emprega [Atribuição: controles de segurança definidos pela organização] em locais de trabalho alternativo.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os fornecimentos de site de trabalho alternado do nível de empresa do cliente pode ser suficiente para endereçar este controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pe-17b"></a>Controle NIST 800-53 PE-17.b

#### <a name="alternate-work-site"></a>Site de Trabalho Alternado

**PE-17.b** A organização avalia como viável, a eficácia de segurança em sites de trabalho alternativo.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os fornecimentos de site de trabalho alternado do nível de empresa do cliente pode ser suficiente para endereçar este controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pe-17c"></a>Controle NIST 800-53 PE-17.c

#### <a name="alternate-work-site"></a>Site de Trabalho Alternado

**PE-17.c** A organização fornece um meio para funcionários se comunicarem com a equipe de segurança de informações em caso de problemas ou incidentes de segurança.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os fornecimentos de site de trabalho alternado do nível de empresa do cliente pode ser suficiente para endereçar este controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pe-18"></a>Controle NIST 800-53 PE-18

#### <a name="location-of-information-system-components"></a>Local dos Componentes do Sistema de Informações

**PE-18** A organização posiciona componentes do sistema de informações de instalação para minimizar o dano potencial de [Atribuição: danos ambientais e físicos definidos pela organização] e para minimizar a oportunidade para não autorizado acesso.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | Azure implementa a abordagem de design de datacenter estratégica para atender o local do controle de componentes do sistema de informações. Todo equipamento de serviços online da Microsoft é colocado em locais nos quais tenham sido construídos para serem protegidos de riscos ambientais tais como roubo, fogo, explosivos, fumaça, água, poeira, vibrações, terremotos, químicos danosos, interferência elétrica, surtos de energia, distúrbios elétricos (picos), e radiação. O recurso e a infraestrutura implementaram braçadeiras sísmicas para proteção contra riscos ambientais. Todos os espaços de colocação e MDF são protegidos por controle de acesso, alarmes e vídeo. O recurso também é patrulhado por oficiais de segurança 24x7. Todos os ativos do Azure portáteis estão bloqueados ou presos no local para fornecer proteção contra danos de roubo ou movimentação. |


