---
title: "Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web para FedRAMP – Manutenção"
description: "Automação de Aplicativos Web para FedRAMP – Manutenção"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="maintenance-ma"></a>Manutenção (MA)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-ma-1"></a>Controle NIST 800-53 MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Política e procedimentos de manutenção do sistema

**MA-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de manutenção do sistema que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de manutenção do sistema e os controles de manutenção do sistema associados; e revisa e atualiza a política de manutenção do sistema atual [Atribuição: frequência definida pela organização]; e os procedimentos de manutenção do sistema [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de manutenção do sistema de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-2a"></a>Controle NIST 800-53 MA-2.a

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2** A organização agenda, executa, documenta e analisa os registros de manutenção e reparos em componentes do sistema de informações de acordo com as especificações do fabricante ou do fornecedor e/ou os requisitos organizacionais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-2b"></a>Controle NIST 800-53 MA-2.b

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.b** A organização aprova e monitora todas as atividades de manutenção, sejam elas executadas no local ou remotamente, independentemente de o equipamento passar pela manutenção no local ou for removido para outro local.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-2c"></a>Controle NIST 800-53 MA-2.c

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.c** A organização exige que [Atribuição: equipe ou funções definidas pela organização] aprovem expressamente a remoção do sistema de informações ou dos componentes do sistema das instalações organizacionais para a realização de manutenção ou reparos fora do local.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure exige que os ativos proprietários (por exemplo, dispositivo ou servidor de rede) que necessitam de transferência externa obtenham aprovação expressa do proprietário do ativo. |


 ## <a name="nist-800-53-control-ma-2d"></a>Controle NIST 800-53 MA-2.d

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.d** A organização faz a limpeza de equipamentos para remover todas as informações de mídia associadas antes de removidas das instalações organizacionais para manutenção ou reparos externos.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | O Padrão de Proteção de Ativos do Microsoft Azure define as precauções de manuseio necessárias para a transferência externa de ativos. O Padrão de Proteção de Ativos exige que os ativos de armazenamento de dados sejam limpos de uma maneira consistente com o NIST SP 800-88, Diretrizes para Limpeza de Mídia, antes de sair do datacenter. |


 ## <a name="nist-800-53-control-ma-2e"></a>Controle NIST 800-53 MA-2.e

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA-2.e** A organização verifica todos os controles de segurança potencialmente afetados para conferir se tais controles ainda estão funcionando corretamente após as ações de manutenção ou reparo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-2f"></a>Controle NIST 800-53 MA-2.f

#### <a name="controlled-maintenance"></a>Manutenção controlada

**MA 2.f** A organização inclui [Atribuição: informações relacionadas a manutenção definida pela organização] em registros de manutenção organizacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela manutenção controlada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-2-2a"></a>Controle NIST 800-53 MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Manutenção Controlada | Atividades de Manutenção Automatizada

**MA-2 (2).a** A organização emprega mecanismos automatizados para agendar, conduzir e documentar manutenções e reparos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por automatizar as atividades de manutenção. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-2-2b"></a>Controle NIST 800-53 MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Manutenção Controlada | Atividades de Manutenção Automatizada

**MA-2 (2).b** A organização produz registros atualizados, precisos e completos de todas as ações de manutenção e reparo solicitadas, agendadas, em andamento e concluídas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por automatizar as atividades de manutenção. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-3"></a>Controle NIST 800-53 MA-3

#### <a name="maintenance-tools"></a>Ferramentas de manutenção

**MA-3** A organização aprova, controla e monitora as ferramentas de manutenção do sistema de informações.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure utiliza diversas ferramentas para realizar manutenções. As ferramentas de manutenção de software são aprovadas, controladas e mantidas por meio do processo de liberação e alteração do Microsoft Azure. <br /> A equipe de Serviços de Site mantém um inventário das ferramentas de manutenção aprovadas para uso no data center (consulte MA-3). A equipe de manutenção é instruída a usar as ferramentas de manutenção fornecidas. A aprovação de gerenciamento do datacenter é necessária para usar ferramentas não fornecidas pelo datacenter. Ferramentas manuais físicas (chaves de fenda, chaves inglesas, etc.) estão isentas desse controle. <br /> Cada instalação contém uma caixa de bloqueio físico restrita ou um espaço com acesso controlado para o armazenamento de ferramentas de manutenção especializadas, como escopos de éter de fluke, testadores de canal de fibra de fluke, toners Ethernet, etc. A equipe de Serviços de Site executa verificações rotineiras de inventário para conferir o status de todas as ferramentas. O acesso à caixa de bloqueio ou ao espaço de armazenamento de manutenção é controlado nos logs do leitor de crachás de acesso, que estão disponíveis na eventualidade de uma investigação. |


 ### <a name="nist-800-53-control-ma-3-1"></a>Controle NIST 800-53 MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Ferramentas de manutenção | Ferramentas de inspeção

**MA-3 (1)** A organização inspeciona as ferramentas de manutenção transportadas para determinada instalação pela equipe de manutenção para verificar se há modificações inadequadas ou não autorizadas.

**Responsibilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | A equipe de Serviços de Site do Microsoft Azure mantém um inventário das ferramentas de manutenção aprovadas para uso no datacenter (consulte o MA-3 para obter mais detalhes). A equipe de manutenção é instruída a usar as ferramentas de manutenção fornecidas. A aprovação do DMC é necessária para usar ferramentas não fornecidas pelo datacenter. |


 ### <a name="nist-800-53-control-ma-3-2"></a>Controle NIST 800-53 MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Ferramentas de manutenção | Mídia de inspeção

**MA-3 (2)** A organização verifica se nas mídias que contêm programas de diagnóstico e teste há códigos mal-intencionados antes de usá-las no sistema de informações.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure proíbe o uso de mídias de armazenamento ou de computação móvel no ambiente de produção de data centers do Microsoft Azure sem aprovação da gerência do data center. É proibido o uso de mídia pessoais no ambiente de produção de data centers do Microsoft Azure. <br /> O Microsoft Azure implementou um processo para inspecionar laptops antes de serem usados no ambiente de produção de data centers do Microsoft Azure. Os agentes de segurança são treinados para confrontar pessoal que utiliza laptops no ambiente de produção com o intuito de verificar se os laptops foram submetidos a inspeções e aprovados. |


 ### <a name="nist-800-53-control-ma-3-3"></a>Controle NIST 800-53 MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Ferramentas de manutenção | Evitar remoção não autorizada

**MA-3 (3)** A organização impede a remoção não autorizada de equipamentos de manutenção que contêm informações organizacionais verificando se não há nenhuma informação organizacional no equipamento; limpando ou destruindo o equipamento; mantendo o equipamento na instalação; ou obtendo uma isenção da [Atribuição: equipes ou funções definidas pela organização] autorizando expressamente a remoção doo equipamento da instalação.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure utiliza ferramentas de manutenção específicas para datacenters que são mantidas na instalação e jamais removidas. Cada instalação contém uma caixa de bloqueio físico restrita ou um espaço de armazenamento que mantém ferramentas, como escopos de éter de fluke, testadores de canal de fibra de fluke, toners Ethernet, etc. O acesso é controlado da caixa de bloqueio ou do espaço de armazenamento nas DCAT para impedir acesso não autorizado às ferramentas de manutenção. <br /> As informações organizacionais são protegidas durante a manutenção por controles no MA-4. Para acessar informações organizacionais, o usuário deve ter contas e autenticadores com privilégios. |


 ## <a name="nist-800-53-control-ma-4a"></a>Controle NIST 800-53 MA-4.a

#### <a name="nonlocal-maintenance"></a>Manutenção não local

**MA-4.a** A organização aprova e monitora atividades de manutenção e diagnóstico não locais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar manutenções não locais em sistemas operacionais implantados por clientes. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-4b"></a>Controle NIST 800-53 MA-4.b

#### <a name="nonlocal-maintenance"></a>Manutenção não local

**MA-4.b** A organização permite o uso de ferramentas de manutenção e diagnóstico não locais somente se consistentes com a política organizacional e documentadas no plano de segurança do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar manutenções não locais em sistemas operacionais implantados por clientes. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-4c"></a>Controle NIST 800-53 MA-4.c

#### <a name="nonlocal-maintenance"></a>Manutenção não local

**MA-4.c** A organização utiliza autenticadores fores no estabelecimento de sessões de manutenção e diagnóstico não locais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar manutenções não locais em sistemas operacionais implantados por clientes. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-4d"></a>Controle NIST 800-53 MA-4.d

#### <a name="nonlocal-maintenance"></a>Manutenção não local

**MA-4.d** A organização mantém registros de atividades de manutenção e diagnóstico não locais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar manutenções não locais em sistemas operacionais implantados por clientes. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-4e"></a>Controle NIST 800-53 MA-4.e

#### <a name="nonlocal-maintenance"></a>Manutenção não local

**MA-4.e** A organização encerra conexões de rede e de sessão quando a manutenção não local é concluída.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar manutenções não locais em sistemas operacionais implantados por clientes. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-4-2"></a>Controle NIST 800-53 MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Manutenção não-local | Documentar manutenção não local

**MA-4 (2)** A organização documenta no plano de segurança do sistema de informações as políticas e procedimentos referentes ao estabelecimento e uso de conexões de manutenção e diagnóstico não locais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por documentar manutenções não locais no plano de segurança de sistemas operacionais implantados por clientes. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-4-3"></a>Controle NIST 800-53 MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Manutenção não-local | Segurança/limpeza comparável

**MA-4 (3)** A organização exige a execução de serviços de manutenção e diagnóstico em sistemas de informações que implementam determinado recurso de segurança comparável ao recurso implementado no sistema sob manutenção; ou remove o componente que passará pela manutenção no sistema de informações antes dos serviços de manutenção ou diagnóstico não locais, limpa o componente (com respeito às informações organizacionais) antes de remover das instalações organizacionais e, após a execução do serviço, inspeciona e limpa o componente (com respeito a software potencialmente mal-intencionado) antes de reconectar o componente ao sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar toda manutenção não local de sistemas operacionais implantados por clientes em um sistema de informações equipado com segurança comparável. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-4-6"></a>Controle NIST 800-53 MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Manutenção não-local | Proteção criptográfica

**MA-4 (6)** O sistema de informações implementa mecanismos criptográficos para proteger a integridade e a confidencialidade das comunicações de manutenção e diagnóstico não locais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar mecanismos criptográficos ao executar manutenções e diagnósticos não locais de sistemas operacionais implantados por clientes. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-5a"></a>Controle NIST 800-53 MA-5.a

#### <a name="maintenance-personnel"></a>Equipe de manutenção

**MA 5.a** A organização estabelece um processo de autorização de equipes de manutenção e mantém uma lista de organizações ou equipes de manutenção autorizadas.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os processos de acompanhamento e autorização de funcionários de manutenção de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-5b"></a>Controle NIST 800-53 MP-5.b

#### <a name="maintenance-personnel"></a>Equipe de manutenção

**MA 5.b** A organização garante que equipes não acompanhadas executando manutenções no sistema de informações obtiveram as autorizações de acesso exigidas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os processos de acompanhamento e autorização de funcionários de manutenção de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-5c"></a>Controle NIST 800-53 MA-5.c

#### <a name="maintenance-personnel"></a>Equipe de manutenção

**MA 5.c** A organização designa equipes organizacionais com as devidas autorizações de acesso e a competência técnica exigidas para supervisionar as atividades de manutenção de equipes que não dispõem das autorizações de acesso exigidas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os processos de acompanhamento e autorização de funcionários de manutenção de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-5-1a"></a>Controle NIST 800-53 MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Equipe de manutenção | Indivíduos sem acesso apropriado

**MA-5 (1).a** A organização implemente procedimentos para o uso de equipes de manutenção que carecem das devidas autorizações de segurança ou não são cidadãos dos EUA, o que inclui os requisitos a seguir; as equipes de manutenção sem as devidas autorizações ou aprovações formais de acesso são acompanhadas e supervisionadas durante a execução de atividades de manutenção e diagnóstico no sistema de informações por pessoal aprovado da organização e totalmente munido das devidas de autorizações e tecnicamente qualificados; antes de iniciar a manutenção das atividades de diagnóstico por equipes que carecem das devidas autorizações ou aprovações formais de acesso, todos os componentes de armazenamento de informações voláteis no sistema de informações são limpos, e toda mídia de armazenamento não volátil é removida ou fisicamente desconectada do sistema e guardada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os processos de acompanhamento e autorização de funcionários de manutenção de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ma-5-1b"></a>Controle NIST 800-53 MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Equipe de manutenção | Indivíduos sem acesso apropriado

**MA-5 (1).b** A organização desenvolve e implementa proteções de segurança alternativas caso não seja possível limpar, remover ou desconectar determinado componente do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os processos de acompanhamento e autorização de funcionários de manutenção de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ma-6"></a>Controle NIST 800-53 MA-6

#### <a name="timely-maintenance"></a>Manutenção oportuna

**MA-6** A organização obtém suporte à manutenção e/ou peças sobressalentes para [Atribuição: componentes do sistema de informações definidos pela organização] em [Atribuição: período de tempo definido pela organização] de falha.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | Os data centers do Microsoft Azure mantêm equipes de manutenção residentes para dar suporte a sistemas de infraestrutura de datacenter críticos, bem como às operações do datacenter. As equipes identificaram componentes do sistema de segurança e tecnologia críticos para os quais mantêm peças sobressalentes no local. Os sistemas críticos foram projetados em configurações de N + 1, enquanto os serviços são projetados para serem resilientes. Isso permite que a equipe de gerenciamento de datacenter alcance os objetivos de recuperação caso haja interrupção de serviço ou uma situação de plano de contingência. Os serviços críticos do sistema de informações são provenientes de mais de um datacenter para impedir interrupções no serviço devido a um incidente em um dos data centers. Os aplicativos de clientes são responsáveis por implantar em vários data centers para fornecer redundância e resiliência. |
