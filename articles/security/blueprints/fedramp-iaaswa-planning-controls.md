---
title: "Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web para FedRAMP – Planejamento"
description: "Automação de Aplicativos Web para FedRAMP – Planejamento"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="planning-pl"></a>PL (Planejamento)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 Controle PL-1

#### <a name="security-planning-policy-and-procedures"></a>Política e procedimentos de planejamento de segurança

**CP-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de planejamento de segurança que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de planejamento de segurança e os controles de planejamento de segurança associados; e revisa e atualiza a política de planejamento de segurança atual [Atribuição: frequência definida pela organização]; e os procedimentos de planejamento de segurança [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e procedimentos de planejamento de segurança de nível empresarial do cliente pode ser suficiente para endereçar este controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 Controle PL-2.a

#### <a name="system-security-plan"></a>Plano de segurança do sistema

**PL-2.a** A organização desenvolve um plano de segurança para o sistema de informação que é consistente com a arquitetura corporativa da organização; define explicitamente o limite de autorização para o sistema; descreve o contexto operacional do sistema de informação em termos de missões e processos de negócios; fornece a categorização de segurança do sistema de informação, incluindo o raciocínio de apoio; descreve o ambiente operacional para o sistema de informação e relacionamentos ou conexões com outros sistemas de informação; fornece uma visão geral dos requisitos de segurança para o sistema; identifica quaisquer sobreposições relevantes, se aplicável; descreve os controles de segurança instalados ou planejados para atender a esses requisitos, incluindo uma justificativa para as decisões de ajustes; e é revisado e aprovado pelo funcionário autorizado ou representante designado antes da implementação do plano.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver um SSP (plano de segurança do sistema) que atenda aos critérios definidos pela autorização de destino (por exemplo, FedRAMP). Os clientes podem fazer referência à Publicação especial do NIST 800-18 R1, Guia para o desenvolvimento de planos de segurança para sistemas de informação federais. O SSP do cliente deve abordar os controles herdados do Microsoft Azure e referir-se ao SSP do Microsoft Azure para detalhes de implementação. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 Controle PL-2.b

#### <a name="system-security-plan"></a>Plano de segurança do sistema

**PL-2.b** A organização distribui cópias do plano de segurança e comunica mudanças subsequentes ao plano para [Atribuição: pessoal ou funções definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por distribuir o plano de segurança do sistema. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 Controle PL-2.c

#### <a name="system-security-plan"></a>Plano de segurança do sistema

**PL-2.c** A organização revisa o plano de segurança para o sistema de informações [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar o plano de segurança do sistema. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 Controle PL-2.d

#### <a name="system-security-plan"></a>Plano de segurança do sistema

**PL-2.d** A organização atualiza o plano para abordar mudanças no sistema de informação/ambiente de operação ou problemas identificados durante a implementação do plano ou avaliações de controle de segurança.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por atualizar o plano de segurança do sistema. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 Controle PL-2.e

#### <a name="system-security-plan"></a>Plano de segurança do sistema

**PL-2.e** A organização protege o plano de segurança de divulgação e modificação não autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger o plano de segurança do sistema. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 Controle PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>Plano de segurança do sistema | Plano / Coordenar com outras entidades organizacionais

**PL-2 (3)** A organização planeja e coordena atividades relacionadas à segurança que afetam o sistema de informações com [Atribuição: indivíduos ou grupos definidos pela organização] antes de realizar tais atividades para reduzir o impacto em outras entidades organizacionais.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pelo planejamento e coordenação de atividades relacionadas à segurança para reduzir o impacto em outras entidades organizacionais. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 Controle PL-4.a

#### <a name="rules-of-behavior"></a>Regras de comportamento

**PL-4.a** A organização estabelece e disponibiliza prontamente às pessoas que precisam de acesso ao sistema de informação, as regras que descrevem suas responsabilidades e o comportamento esperado em relação à informação e ao uso do sistema de informação.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As regras de comportamento de nível empresarial do cliente podem ser suficientes para resolver esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 Controle PL-4.b

#### <a name="rules-of-behavior"></a>Regras de comportamento

**PL-4.b** A organização recebe um reconhecimento assinado de tais indivíduos, indicando que eles leram, entenderam e concordaram em respeitar as regras de comportamento, antes de autorizar o acesso à informação e ao sistema de informação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As regras de comportamento de nível empresarial do cliente podem ser suficientes para resolver esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 Controle PL-4.c

#### <a name="rules-of-behavior"></a>Regras de comportamento

**PL-4.c** A organização revisa e atualiza as regras de comportamento [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As regras de comportamento de nível empresarial do cliente podem ser suficientes para resolver esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 Controle PL-4.d

#### <a name="rules-of-behavior"></a>Regras de comportamento

**PL-4.d** A organização exige que os indivíduos que assinaram uma versão anterior das regras de comportamento, que leiam e assinem novamente quando as regras de comportamento forem revisadas/atualizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As regras de comportamento de nível empresarial do cliente podem ser suficientes para resolver esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 Controle PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Regras de comportamento | Restrições de redes e redes sociais

**PL-4 (1)** A organização inclui as regras de comportamento, restrições explícitas sobre o uso de redes sociais/sites de redes e postagem de informações organizacionais em sites públicos.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As regras de comportamento de nível empresarial do cliente podem incluir restrições em redes sociais/sites de rede. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 Controle PL-8.a

#### <a name="information-security-architecture"></a>Arquitetura de segurança da informação

**PL-8.a** A organização desenvolve uma arquitetura de segurança da informação para o sistema de informação que descreve a filosofia geral, os requisitos e a abordagem a serem tomadas em relação à proteção da confidencialidade, integridade e disponibilidade de informações organizacionais; descreve como a arquitetura de segurança da informação está integrada e fornece suporte à arquitetura corporativa; e descreve quaisquer pressupostos de segurança de informações sobre dependências em serviços externos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pelo desenvolvimento de uma arquitetura de segurança da informação para recursos implantados pelo cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 Controle PL-8.b

#### <a name="information-security-architecture"></a>Arquitetura de segurança da informação

**PL-8.b** A organização revisa e atualiza a arquitetura de segurança da informação [Atribuição: frequência definida pela organização] para refletir as atualizações na arquitetura corporativa.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e atualizar a arquitetura de segurança da informação. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 Controle PL-8.c

#### <a name="information-security-architecture"></a>Arquitetura de segurança da informação

**PL-8.c** A organização garante que as mudanças planejadas na arquitetura de segurança da informação sejam refletidas no plano de segurança, no CONOPS (Conceito de Operações) de segurança e aquisições/aquisições organizacionais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por contabilizar as mudanças planejadas na arquitetura de segurança da informação. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |
