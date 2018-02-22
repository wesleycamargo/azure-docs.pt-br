---
title: "Especificações técnicas de segurança e conformidade do Azure – Automação de Aplicativos Web para FedRAMP – Gerenciamento de configurações"
description: "Automação de Aplicativos Web para FedRAMP – Gerenciamento de configurações"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6566783769d37ee829df3894fdb5673b4edafd2c
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="configuration-management-cm"></a>Gerenciamento de Configuração (CM)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-cm-1"></a>Controle NIST 800-53 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Procedimentos e Política de Gerenciamento de Configuração

**CM-1** A organização desenvolve, documenta e distribui a [Atribuição: equipe ou funções definidas pela organização] uma política de gerenciamento de configuração que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de gerenciamento de configuração e os controles de gerenciamento de configuração associados; e revisa e atualiza a política de gerenciamento de configuração atual [Atribuição: frequência definida pela organização]; e os procedimentos de avaliação de risco [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e procedimentos de gerenciamento de configuração de nível empresarial do cliente pode ser suficiente para endereçar este controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-2"></a>Controle NIST 800-53 CM-2

#### <a name="baseline-configuration"></a>Configuração de Linha de Base

**CM-2** A organização desenvolve, documenta e mantém o sob o controle de configuração, uma configuração de linha de base atual do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os modelos do Azure Resource Manager e os recursos que compõem estas especificações técnicas representam uma linha de base para "configuração como código" para a arquitetura implantada. A solução é fornecida pelo GitHub, que pode ser usado para controle de configuração. A solução inclui uma linha de base de Configuração de Estado Desejado (DSC) para cada máquina virtual implantada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-1a"></a>Controle NIST 800-53 CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de Linha de Base | Revisões e Atualizações

**CM-2 (1).a** A organização revisa e atualiza a configuração de linha de base do sistema de informações [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e atualizar a configuração de linha de base dos recursos por ele implantados (para incluir aplicativos, sistemas operacionais, bancos de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-1b"></a>Controle NIST 800-53 CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de Linha de Base | Revisões e Atualizações

**CM-2 (1).b** A organização revisa e atualiza a configuração de linha de base do sistema de informações quando requisitado devido a [Atribuição: circunstâncias definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e atualizar a configuração de linha de base dos recursos implantados do cliente quando requeridos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-1c"></a>Controle NIST 800-53 CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuração de Linha de Base | Revisões e Atualizações

**CM-2 (1).c** A organização analisa e atualiza a configuração de linha de base do sistema de informações como parte integral de atualizações e instalações de componentes do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e atualizar a configuração de linha de base dos recursos implantados do cliente quando requeridos. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-2"></a>Controle NIST 800-53 CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configuração de linha de base | Suporte de Automação para Precisão / Moeda

**CM-2 (2)** A organização emprega mecanismos automatizados para manter uma configuração atualizada, completa, precisa e prontamente disponível de linha de base do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os modelos do Azure Resource Manager e os recursos que compõem estas especificações técnicas representam uma linha de base para "configuração como código" para a arquitetura implantada. A solução é fornecida pelo GitHub, que pode ser usado para controle de configuração. No portal do Azure, um script de automação está disponível para todos os recursos implantados e fornece uma representação sempre atualizada desses recursos.  |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-3"></a>Controle NIST 800-53 CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configuração de Linha de Base | Retenção de Configurações Anteriores

**CM-2 (3)** A organização retém [Atribuição: versões anteriores das configurações de linha de base do sistema de informações definidas pela organização] para oferecer suporte à reversão.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por manter versões anteriores de configurações de linha de base para os recursos implantados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-2-7a"></a>Controle NIST 800-53 CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuração de Linha de Base | Configurar dispositivos, Componentes ou Sistemas de Áreas de Alto Risco

**CM-2 (7).a** Os problemas de organização [Atribuição: dispositivos, componentes do sistema ou sistemas de informações definidos pela organização] com [Atribuição: configurações definidas pela organização] indivíduos viajando para locais que a organização considerar de risco significativo.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo físico controlado pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | Conteúdo de cliente do Microsoft Azure nunca é armazenado fora do Microsoft Azure, que é fisicamente localizados nos Estados Unidos. Equipe do Microsoft Azure não viaja com dispositivos contidos no inventário do Microsoft Azure. Sendo assim, esse controle não se aplica ao Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>Controle NIST 800-53 CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuração de Linha de Base | Configurar dispositivos, Componentes ou Sistemas de Áreas de Alto Risco

**CM-2 (7).b** A organização se aplica [Atribuição: proteções de segurança definidas pela organização] para os dispositivos quando retornam os indivíduos.

**Responsibilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo físico controlado pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | Conteúdo de cliente do Microsoft Azure nunca é armazenado fora do Microsoft Azure e a equipe do Microsoft Azure não viaja com dispositivos contidos no inventário do Microsoft Azure, portanto, esse controle não é aplicável. |


 ## <a name="nist-800-53-control-cm-3a"></a>Controle NIST 800-53 CM-3.a

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.a** A organização determina os tipos de alterações no sistema de informações que são controlados por configuração.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por determinar os tipos de mudanças de recursos implantados por clientes (que incluem aplicativos, sistemas operacionais, bancos de dados e software) são controladas pela configuração. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3b"></a>Controle NIST 800-53 CM-3.b

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.b** A organização revisa as alterações propostas controladas por configuração para o sistema de informações da organização e aprova ou desaprova tais alterações considerando explicitamente para análise de impacto de segurança.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar as alterações propostas controladas pela configuração aos recursos implantados pelo cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3c"></a>Controle NIST 800-53 CM-3.c

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.c** A organização documenta decisões de mudança de configuração associadas com o sistema de informação.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por documentar as alterações propostas controladas pela configuração aos recursos implantados pelo cliente (veja CM-03.b). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3d"></a>Controle NIST 800-53 CM-3.d

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.d** A organização implementa as alterações controladas por configuração aprovadas para o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar mudanças controladas por configuração aprovadas em CM-03.b. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3e"></a>Controle NIST 800-53 CM-3.e

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.e** A organização mantém registros de alterações no sistema de informações de configuração controlada [atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por reter um registro de mudanças de configuração controlada para recursos implantados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3f"></a>Controle NIST 800-53 CM-3.f

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.f** A organização audita e revisa as atividades associadas a configuração controlada alterações no sistema de informações da organização.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável para auditar e revisar as alterações de configuração. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-3g"></a>Controle NIST 800-53 CM-3.g

#### <a name="configuration-change-control"></a>Controle de Alteração de Configuração

**CM-3.g** A organização coordena e fornece supervisão para atividades de controle de alteração de configuração por meio de organização [Atribuição: elemento de controle de alteração de configuração definida pela organização (por exemplo, comitê, quadro)] que age [Seleção ( um ou mais): [Atribuição: frequência definida pela organização]; [Atribuição: condições de alteração de configuração definida pela organização]].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por coordenar e fornecer supervisão para atividades de controle de alteração de configuração. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1a"></a>Controle NIST 800-53 CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).a** A organização emprega mecanismos automatizados para documentar alterações propostas para o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar mecanismos automatizados para documentar alterações propostas (consulte CM-03.b). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1b"></a>Controle NIST 800-53 CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).b** A organização emprega mecanismos automatizados para notificar [Atribuição: autoridades de aprovação definidas e organizadas] das alterações propostas para as informações de sistema e solicitação de aprovação de alteração.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar um mecanismo automatizado para rotear e solicitar aprovação para mudanças propostas para recursos implantados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1c"></a>Controle NIST 800-53 CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).c** A organização emprega mecanismos automatizados para realçar as alterações propostas para o sistema de informações que não foram aprovados ou desaprovadas pelo [Atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar um mecanismo automatizado para realçar propostas de mudanças não revisadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1d"></a>Controle NIST 800-53 CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).d** A organização emprega mecanismos automatizados para proibir mudanças no sistema de informação até que as aprovações designadas sejam recebidas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar um mecanismo automatizado para proibir a implementação de mudanças recusadas para recursos implantados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1e"></a>Controle NIST 800-53 CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).e** A organização emprega mecanismos automatizados para documentar alterações para o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar um mecanismo automatizado para documentar toda a implementação para recursos implantados de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-1f"></a>Controle NIST 800-53 CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Controle de Alterações de Configuração | Documento / Notificação / Proibição de Alterações Automatizadas

**CM-3 (1).f** A organização emprega mecanismos automatizados para notificar [Atribuição: pessoal definido pela organização] quando mudanças aprovadas para o sistema de informação são completadas.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar um mecanismo automatizado para fornecer notificações quando mudanças aprovadas para recursos implantados de cliente são completadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-2"></a>Controle NIST 800-53 CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Controle de Alterações de Configuração | Testar / Validar / Alterações de Documentos

**CM-3 (2)** A organização testa, valida e documenta as alterações no sistema de informações antes de implementar as alterações no sistema operacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por testar, validar e documentadas alterações aos recursos de cliente implantados antes da implementação. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-4"></a>Controle NIST 800-53 CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Controle de alterações de configuração | Representante de segurança

**CM-3 (4)** A organização necessita de um representante de segurança de informações ser um membro de [Atribuição: elemento de controle de alteração de configuração definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela atribuição de um representante de segurança de informações para ser um membro do elemento de controle de alterações definido no CM-03.g. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-3-6"></a>Controle NIST 800-53 CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Controle de Alterações de Configuração | Gerenciamento de Criptografia

**CM-3 (6)** A organização garante que os mecanismos de criptografia usados para fornecer [Atribuição: proteções de segurança definidas pela organização] estão sob o gerenciamento de configuração.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por garantir que os mecanismos de criptografia estejam sob o gerenciamento de configuração. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-4"></a>Controle NIST 800-53 CM-4

#### <a name="security-impact-analysis"></a>Análise de Impacto de Segurança

**CM-4** A organização analisa as alterações no sistema de informações para determinar possíveis impactos de segurança antes da implementação da alteração.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar as mudanças propostas para recursos implantados por ele. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-4-1"></a>Controle NIST 800-53 CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Análise de Impacto de Segurança | Ambientes de Teste Separados

**CM-4 (1)** A organização analisa as alterações no sistema de informações em um ambiente de teste separado antes da implementação em um ambiente operacional, procurando os efeitos de segurança devidos a falhas, vulnerabilidades, incompatibilidade ou intencionais más intenções.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela análise de alterações propostas para recursos de cliente implantado em um ambiente de teste antes da implementação em um ambiente operacional. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-5"></a>Controle NIST 800-53 CM-5

#### <a name="access-restrictions-for-change"></a>Restrições de Acesso para Alteração

**CM-5** A organização define, documenta, aprova e impõe restrições de acesso físico e lógico associadas a alterações no sistema de informações.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os privilégios da conta do Azure Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a funções com controle estrito sobre quais usuários podem exibir e controlar os recursos implantados. Os privilégios de conta do Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a grupos de segurança. Esses grupos de segurança controlam as ações que os usuários podem executar em relação à configuração do sistema operacional. Esses esquemas com base em função podem ser estendidos pelo cliente para atender às necessidades da tarefa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-5-1"></a>Controle NIST 800-53 CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Acessar Restrições para Alterar | Automatizada de Imposição de Acesso / Auditoria

**CM-5 (1)** O sistema de informações impõe restrições de acesso e oferece suporte à auditoria das ações de imposição.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os privilégios da conta do Azure Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a funções com controle estrito sobre quais usuários podem exibir e controlar os recursos implantados. Os privilégios de conta do Active Directory são implementados usando o controle de acesso baseado em função, atribuindo usuários a grupos de segurança. Esses grupos de segurança controlam as ações que os usuários podem executar em relação à configuração do sistema operacional. Todos os acessos e tentativas de acesso são auditados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-5-2"></a>Controle NIST 800-53 CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Acessar Restrições para Alterar | Revise as Alterações do Sistema

**CM-5 (2)** A organização revisa as alterações do sistema de informações [Atribuição: frequência definida pela organização] e [Atribuição: circunstâncias definidas pela organização] para determinar se ocorreram alterações não autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar as alterações aos recursos implantados pelo cliente para determinar se ocorreram alterações não autorizadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-5-3"></a>Controle NIST 800-53 CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Restrições de Acesso para Alteração | Componentes Assinados

**CM-5 (3)** O sistema de informações impede a instalação de [Atribuição: componentes de software e firmware definidos pela organização] sem a verificação de que o componente foi assinado digitalmente usando um certificado que seja reconhecido e aprovado pela organização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implantadas por estas especificações técnicas implementam o Windows AppLocker para especificar quais usuários podem instalar e/ou executar aplicativos específicos. Além disso, todas as janelas de atualizações do sistema operacional Windows são digitalmente assinadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-5-5a"></a>Controle NIST 800-53 CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Acessar Restrições para Alterar | Limitar a Produção / Operação Privilégios

**CM-5 (5).a** A organização limita os privilégios para alterar os componentes do sistema de informações e as informações relacionadas ao sistema em um ambiente de produção ou operacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por limitar os privilégios para fazer alterações em ambientes operacionais ou de produção implantado pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-5-5b"></a>Controle NIST 800-53 CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Acessar Restrições para Alterar | Limitar a Produção / Operação Privilégios

**CM-5 (5).b** A organização revisa e revalida os privilégios [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e reavaliando privilégios definidos em CM-05(05).a. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-6a"></a>Controle NIST 800-53 CM-6.a

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6.a** A organização estabelece e documenta as definições de configuração de produtos de tecnologia da informação utilizados dentro do sistema de informações usando [Atribuição: listas de verificação de configuração de segurança definidas pela organização] que reflitam o modo mais restritivo consistente com os requisitos operacionais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas incluem uma linha de base de Configuração de estado desejado (DSC) para cada máquina virtual implantada. Esses scripts declarativos do PowerShell definem e configuram os recursos aos quais elas são aplicadas. A linha de base DSC incluída para os recursos implantados por essa solução pode ser estendida pelo cliente para atender às necessidades de missão. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-6b"></a>Controle NIST 800-53 CM-6.b

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6.b** A organização implementa as definições de configuração.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas incluem uma linha de base de Configuração de estado desejado (DSC) para cada máquina virtual implantada. As linhas de base são aplicadas automaticamente a máquinas virtuais durante a implantação usando a extensão de máquina virtual de script personalizado. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-6c"></a>Controle NIST 800-53 CM-6.c

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6.c** A organização identifica, documenta e aprovar quaisquer desvios das definições de configuração estabelecidos para [Atribuição: componentes de sistema de informações da organização definido] com base em [Atribuição: organização definido requisitos operacionais].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar, documentar e aprovar quaisquer desvios das definições de configuração estabelecidos para recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-6d"></a>Controle NIST 800-53 CM-6.d

#### <a name="configuration-settings"></a>Definições de configuração

**CM-6.d** A organização monitora e controla mudanças para as configurações de acordo com as políticas organizacionais e os procedimentos.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica implanta uma DSC de Automação. DSC de Automação alinha as configurações de máquina com uma configuração específica da organização definida. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-6-1"></a>Controle NIST 800-53 CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Conjunto de Configuração | Gerenciamento Central Automatizado / Aplicativo / Verificação

**CM-6 (1)** A organização emprega mecanismos automatizados para gerenciar centralmente, aplicar e verificar as configurações para [Atribuição: componentes de sistema de informações definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica implanta uma DSC de Automação do Azure. DSC de Automação alinha as configurações de máquina com uma configuração específica da organização definida e monitora continuamente por mudanças. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-6-2"></a>Controle NIST 800-53 CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Conjunto de Configuração | Responder a Alterações Não Autorizadas

**CM-6 (2)** A organização emprega [Atribuição: proteções de segurança definidas pela organização] para responder a alterações não autorizadas [Atribuição: conjuntos de configurações definidas pelo organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica implanta uma DSC de Automação do Azure. Parte do Azure Operations Management Suite (OMS), o DSC de Automação pode ser configurado para gerar um alerta ou para solucionar problemas de configuração quando detectado. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-7a"></a>Controle NIST 800-53 CM-7.a

#### <a name="least-functionality"></a>Funcionalidade Mínima

**CM-7.a** A organização configura o sistema de informações para fornecer apenas os recursos essenciais.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os recursos implantados por estas especificações técnicas são configurados para fornecer a funcionalidade mínima para a finalidade pretendida. A linha de base de Configuração de Estado Desejado (DSC) é incluída para cada máquina virtual. Esses scripts declarativos do PowerShell definem e configuram os recursos aos quais elas são aplicadas. A linha de base DSC incluída para os recursos implantados por essa solução pode ser estendida pelo cliente para atender às necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-7b"></a>Controle NIST 800-53 CM-7.b

#### <a name="least-functionality"></a>Funcionalidade Mínima

**CM-7.b** A organização proíbe ou restringe o uso das seguintes funções, portas, protocolos e serviços: [Atribuição: funções, portas, protocolos e/ou serviços proibidos ou restritos definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam os grupos de segurança de rede e o Gateway de Aplicativo do Azure para restringir o uso de portas e protocolos apenas àqueles que são necessários. Application Gateway, grupos de segurança de rede e as linhas de base do DSC para máquinas virtuais podem ser melhor configuradas pelo cliente para restringir o uso de funções e serviços para fornecer apenas a funcionalidade que se destina, protocolos e portas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-1a"></a>Controle NIST 800-53 CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>Funcionalidade Mínima | Revisão periódica

**CM-7 (1).a** A organização examina o sistema de informações [Atribuição: frequência definida pela organização] para identificar funções, portas, protocolos e serviços desnecessários e/ou não seguros.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e analisar os registros de auditoria dos recursos por ele implantados (para incluir aplicativos, sistemas operacionais, bancos de dados e software) para identificar funções, portas, protocolos, e serviços desnecessários e/ou inseguros. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-1b"></a>Controle NIST 800-53 CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>Funcionalidade Mínima | Revisão periódica

**CM-7 (1).b** A organização desabilita o sistema de informações [Atribuição: funções, portas, protocolos e serviços desnecessários e/ou não seguros definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desabilitar funções, portas, protocolos e serviços que foram considerados inseguros ou desnecessários. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-2"></a>Controle NIST 800-53 CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Funcionalidade Mínima | Impedir a Execução do Programa

**CM-7 (2)** O sistema de informações impede a execução do programa de acordo com [Seleção (um ou mais): [Atribuição: políticas definidas por organização sobre software programa uso e restrições]; regras para autorizar os termos e condições de uso do programa de software].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por impedir a execução do programa de acordo com políticas de uso do programa de software definida pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-5a"></a>Controle NIST 800-53 CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade Mínima | Um Software autorizado / Lista de Permissões

**CM-7 (5).a** A organização identifica [Atribuição: programas de software autorizado a executar no sistema de informações definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar programas autorizados. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-5b"></a>Controle NIST 800-53 CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade Mínima | Um Software autorizado / Lista de Permissões

**CM-7 (5).b** A organização emprega uma política negar tudo, permitir por exceção para permitir a execução de programas de software autorizado no sistema de informações.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar uma política negar tudo, permitir por exceção para permitir a execução de programas de software autorizado nos recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-7-5c"></a>Controle NIST 800-53 CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidade Mínima | Um Software autorizado / Lista de Permissões

**CM-7 (5).c** A organização revisa e atualiza a lista de programas de software autorizados [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por revisar e atualizar a lista de programas de software autorizados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-8a"></a>Controle NIST 800-53 CM-8.a

#### <a name="information-system-component-inventory"></a>Inventário de Componentes do Sistema de Informação

**CM-8.a** A organização desenvolve e documenta um inventário dos componentes do sistema de informações que reflete o atual sistema de informações com precisão; inclui todos os componentes dentro do limite de autorização do sistema de informações; está em o nível de granularidade considerado necessário para rastrear e informar; e inclui [Atribuição: informações consideradas necessárias para alcançar a responsabilidade de componente do sistema de informação eficientes definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. Azure Resource Manager fornece uma lista de recursos implantados sempre atualizada e pode ser personalizado para marcar e agrupar recursos para gerenciamento de estoque. Recursos implantados por esta solução recebem uma marca de recurso específica que pode ser associada com o limite de sistema. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-8b"></a>Controle NIST 800-53 CM-8.b

#### <a name="information-system-component-inventory"></a>Inventário de Componentes do Sistema de Informação

**CM-8.b** A organização revisa e atualiza o inventário de componente do sistema de informação [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. O Azure Resource Manager fornece uma lista sempre atualizada de recursos implantados disponíveis para revisão no portal do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-1"></a>Controle NIST 800-53 CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventário de Componente do Sistema de Informações | Atualizações durante Instalações / Remoções

**CM-8 (1)** A organização atualiza o inventário de componentes do sistema de informações como parte integrante de instalações de componentes, remoções e atualizações do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. A folha de recursos no portal do Azure lista todos os recursos implantados, fornecendo um inventário sempre atualizado conforme recursos são implantados e removidos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-2"></a>Controle NIST 800-53 CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Inventário de Componentes do Sistema de Informações | Manutenção Automatizada

**CM-8 (2)** A organização emprega mecanismos automatizados para manter uma configuração atualizada, completa, precisa e prontamente disponível de linha de base do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. A folha de recursos no portal do Azure lista todos os recursos implantados, fornecendo um inventário sempre atualizado conforme recursos são implantados e removidos. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-3a"></a>Controle NIST 800-53 CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventário de Componentes do Sistema de Informação | Detecção de Componente Não Autorizado Automatizada

**CM-8 (3).a** A organização emprega mecanismos automatizados [Atribuição: frequência definida pela organização] para detectar a presença dos componentes de hardware, software e firmware não autorizados dentro do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar mecanismos automatizados para detectar a presença de software não autorizado em recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-3b"></a>Controle NIST 800-53 CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventário de Componentes do Sistema de Informação | Detecção de Componente Não Autorizado Automatizada

**CM-8 (3).b** A organização executa as seguintes ações quando forem detectados componentes não autorizados: [Seleção (um ou mais): desabilita o acesso à rede por tais componentes; isola os componentes; notifica [Atribuição: equipe ou funções definidas pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por tomar ação quando um software não autorizado for detectado. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-4"></a>Controle NIST 800-53 CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Inventário de Componentes do Sistema de Informações | Informação de Responsabilidade

**CM-8 (4)** A organização inclui as informações de inventário de componente de sistema informações um meio para identificar por [Seleção (um ou mais): nome, posição; função], indivíduos responsáveis/responsável para administrar esses componentes.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. As marcas de recurso do Azure são de chave / valor pares que podem ser empregadas para categorizar os recursos para fins de gerenciamento e/ou responsabilidade. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-8-5"></a>Controle NIST 800-53 CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventário de Componente do Sistema de Informações | Nenhuma Estatística Duplicada de Componentes

**CM-8 (5)** A organização verifica que todos os componentes dentro do limite de autorização do sistema de informações não são duplicados em outros inventários de componente do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Estas especificações técnicas implantam todos os recursos em um grupo de recursos do Azure Resource Manager. O Azure Resource Manager fornece uma lista sempre atualizada de recursos implantados. Recursos implantados por esta solução recebem uma marca de recurso específica que pode ser associada com o limite de sistema. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-9a"></a>Controle NIST 800-53 CM-9.a

#### <a name="configuration-management-plan"></a>Plano de Gerenciamento de Configuração

**CM-9.a** A organização desenvolve, documenta e implementa um plano de gerenciamento de configuração para o sistema de informações que endereça funções, responsabilidades, e processos e procedimentos de gerenciamento de configuração.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar, e implementar um plano de gerenciamento de configuração para recursos de implantação do cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-9b"></a>Controle NIST 800-53 CM-9.b

#### <a name="configuration-management-plan"></a>Plano de Gerenciamento de Configuração

**CM-9.b** A organização desenvolve, documentos e implementa um plano de gerenciamento de configuração para o sistema de informações que estabelece um processo para identificação de itens de configuração durante o ciclo de vida de desenvolvimento sistema ciclo e gerenciar a configuração dos itens de configuração.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar, e implementar um plano de gerenciamento de configuração para recursos de implantação do cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-9c"></a>Controle NIST 800-53 CM-9.c

#### <a name="configuration-management-plan"></a>Plano de Gerenciamento de Configuração

**CM-9.c** A organização desenvolve, documentos e implementa um plano de gerenciamento de configuração para o sistema de informações que define a configuração de itens para o sistema de informações e coloca os itens de configuração em configuração gerenciamento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar, e implementar um plano de gerenciamento de configuração para recursos de implantação do cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-9d"></a>Controle NIST 800-53 CM-9.d

#### <a name="configuration-management-plan"></a>Plano de Gerenciamento de Configuração

**CM-9.d** A organização desenvolve, documenta e implementa um plano de gerenciamento de configuração para o sistema de informações que protege o plano de gerenciamento de configuração de divulgação não autorizado e a modificação.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por desenvolver, documentar, e implementar um plano de gerenciamento de configuração para recursos de implantação do cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-10a"></a>Controle NIST 800-53 CM-10.a

#### <a name="software-usage-restrictions"></a>Restrições de Uso de Software

**CM-10.a** A organização usa o software e a documentação associada em acordo com acordos entre parceiros comerciais e leis de direitos autorais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Licenças do Windows e do SQL Server estão inclusas para os recursos implantados por estas especificações técnicas. Este é um recurso interno do Azure. Organizações com contratos de licença de software existente podem considerar a implantação de modelos de licença alternativa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-10b"></a>Controle NIST 800-53 CM-10.b

#### <a name="software-usage-restrictions"></a>Restrições de Uso de Software

**CM-10.b** A organização controla o uso de software e documentação associada protegida pelas licenças de quantidade para controlar copiagem e distribuição.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Licenças do Windows e do SQL Server estão inclusas para os recursos implantados por estas especificações técnicas. O usuário não é necessário para controlar o uso das licenças separadamente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-10c"></a>Controle NIST 800-53 CM-10.c

#### <a name="software-usage-restrictions"></a>Restrições de Uso de Software

**CM-10.c** A organização controla e documenta o uso de tecnologia para garantir que esse recurso não é usado para a distribuição não autorizada, exibição, desempenho ou a reprodução de trabalho protegido pelos direitos autorais de compartilhamento de arquivos de ponto a ponto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhum recurso de compartilhamento de arquivos de ponto a ponto implantado por estas especificações técnicas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-10-1"></a>Controle NIST 800-53 CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Restrições de Uso de Software | Software Livre

**CM-10 (1)** A organização estabelece as seguintes restrições sobre o uso de software livre: [Atribuição: restrições definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Política de gerenciamento de configuração de nível empresarial do cliente pode tratar restrições no uso de software livre. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-11a"></a>Controle NIST 800-53 CM-11.a

#### <a name="user-installed-software"></a>Software Instalado pelo Usuário

**CM-11.a** A organização estabelece [Atribuição: políticas definidas pela organização] que controlam a instalação de software por usuários.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por estabelecer uma política que controla a instalação de software nos recursos de cliente implantado por usuários. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-11b"></a>Controle NIST 800-53 CM-11.b

#### <a name="user-installed-software"></a>Software Instalado pelo Usuário

**CM-11.b** A organização impõe políticas de instalação de software por meio de [Atribuição: métodos definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela aplicação de políticas de instalação de software. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-cm-11c"></a>Controle NIST 800-53 CM-11.c

#### <a name="user-installed-software"></a>Software Instalado pelo Usuário

**CM-11.c** A organização monitora política de conformidade em [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar a conformidade dos recursos de implantação de cliente com as políticas identificadas no CM-11.a. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-cm-11-1"></a>Controle NIST 800-53 CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Software instalado pelo Usuário | Alertas para Instalações Não Autorizadas

**CM-11 (1)** Os alertas do sistema de informações [Atribuição: pessoal ou funções definidos pela organização] quando a instalação não autorizada do software é detectada.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer alertas quando a instalação não autorizada do software é detectada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |

