---
title: "Automação das Especificações Técnicas do Azure FedRAMP - Controle de Acesso"
description: Aplicativos Web para FedRAMP - Controle de Acesso
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>Controle de Acesso (AC)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA Departamento de Comércio como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-ac-1"></a>Controle NIST 800-53 AC-1

#### <a name="access-control-policy-and-procedures"></a>Procedimentos e Política de Controle de Acesso

**AC-1** A organização desenvolve, documenta e distribui à [Atribuição: pessoal ou funções definidos pela organização] uma política de controle de acesso que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de controle de acesso e os controles de acesso associados; e analisa e atualiza a política de controle de acesso atual [Atribuição: frequência definida pela organização]; e os procedimentos de controle de acesso [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de controle de acesso de nível corporativo podem ser suficientes para atender esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2a"></a>Controle NIST 800-53 AC-2.a

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.a** A organização identifica e seleciona os seguintes tipos de contas do sistema de informações para dar suporte a funções de negócios/missões organizacionais: [Atribuição: tipos de conta do sistema de informações definidas pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica do Azure depende e implementa os seguintes tipos de conta do sistema: usuários do Active Directory do Azure (usados para implantar a solução e gerenciar o acesso aos recursos do Azure), os usuários do sistema operacional Windows (gerenciados pelo Active Directory), a conta de serviço do SQL Server. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2b"></a>Controle NIST 800-53 AC-2.b

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.b** A organização atribui gerentes de conta para contas de do sistema de informações.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por atribuir gerenciadores às contas identificadas na AC-02.a. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2c"></a>Controle NIST 800-53 AC-2.c

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.c** A organização estabelece as condições de associação e função do grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por estabelecer os critérios de função e associação do grupo para tipos de contas controladas pelo cliente (consulte AC-02.a). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2d"></a>Controle NIST 800-53 AC-2.d

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.d** A organização especifica os usuários autorizados do sistema de informações, grupo e associação de função e autorizações de acesso (ou seja, privilégios) e outros atributos (conforme necessário) para cada conta.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um processo estabelecido de autorização de conta de nível corporativo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2e"></a>Controle NIST 800-53 AC-2.e

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.e** A organização necessita de aprovações de [Atribuição: pessoal ou funções definidos pela organização] para solicitações para criar contas do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um processo estabelecido de autorização de conta de nível corporativo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2f"></a>Controle NIST 800-53 AC-2.f

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.f** A organização cria, habilita, modifica, desabilita e remove contas do sistema de informações de acordo com [Atribuição: procedimentos ou condições definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um processo estabelecido de gerenciamento de conta de nível corporativo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2g"></a>Controle NIST 800-53 AC-2.g

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.g** A organização monitora o uso de contas do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o painel de identidade e acesso de soluções do OMS para segurança e auditoria. Esse painel permite que os gerentes de conta monitorem o uso de contas do sistema de informações. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2h"></a>Controle NIST 800-53 AC-2.h

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.h** A organização notifica os gerentes de conta quando as contas não são mais necessárias; quando os usuários são demitidos ou transferidos; e quando o uso de informações específicas do sistema ou as informações aos interessados se altera.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de controle de acesso de nível corporativo do cliente podem estabelecer um processo para notificar o gerente de conta apropriado quando uma conta não é mais necessária. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2i"></a>Controle NIST 800-53 AC-2.i

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.i** A organização autoriza o acesso ao sistema de informações com base em uma autorização de acesso válido; o uso pretendido do sistema; e outros atributos conforme exigido pela organização ou funções de negócios/missões associados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controle de acesso de nível corporativo do cliente podem definir um processo de autorização de acesso. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2j"></a>Controle NIST 800-53 AC-2.j

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.j** A organização analisa as contas para conformidade com os requisitos de gerenciamento de conta [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar as contas controladas pelo cliente com a frequência necessária para determinar se as contas estão em conformidade com todos os requisitos da organização. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-2k"></a>Controle NIST 800-53 AC-2.k

#### <a name="account-management"></a>Gerenciamento de Conta

**AC-2.k** A organização estabelece um processo para emitir novamente as credenciais de conta/grupo compartilhado (se implantados) quando as pessoas são removidas do grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controle de acesso de nível corporativo do cliente podem definir um processo para gerenciar credenciais de conta do grupo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-1"></a>Controle NIST 800-53 AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Gerenciamento de conta | Gerenciamento de conta de sistema automatizado

**AC-2 (1)** A organização emprega mecanismos automatizados para dar suporte ao gerenciamento de contas do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o painel de identidade e acesso de soluções do OMS para segurança e auditoria. Esse painel permite que os gerentes de conta monitorem o uso de contas do sistema de informações. O OMS pode ser configurado para enviar alertas quando atividades atípicas ou suspeitas ou outros eventos predefinidos ocorrerem. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-2"></a>Controle NIST 800-53 AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Gerenciamento de conta | Remoção de contas temporárias / emergenciais

**AC-2 (2)** O sistema de informações automaticamente [Seleção: remove; desabilita] contas temporárias e emergenciais após [Atribuição: período de tempo para cada tipo de conta de definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure não implanta contas temporárias ou emergenciais. Se não for desativado manualmente, o controlador de domínio implantado desabilita automaticamente todas as contas inativas após 35 dias. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-3"></a>Controle NIST 800-53 AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Gerenciamento de conta | Desabilitar contas inativas

**AC-2 (3)** O sistema de informações desativa automaticamente contas inativas após [Atribuição: período de tempo definido pelo organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O controlador de domínio implantado por essa especificação técnica do Azure está configurado para desabilitar todas as contas de usuário após 35 dias de inatividade. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-4"></a>Controle NIST 800-53 AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Gerenciamento de conta | Ações de auditoria automatizadas

**AC-2 (4)** O sistema de informações realiza automaticamente a auditoria de criar, modificar, habilitar, desabilitar e remover e notifica, [Atribuição: pessoal ou funções definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica implementa os seguintes tipos de conta do sistema: usuários do Azure Active Directory, usuários do sistema operacional Windows, a conta de serviço do SQL Server. Ações de gerenciamento de conta do Active Directory do Azure geram um evento no log de atividades do Azure; Ações de gerenciamento de conta no nível do sistema operacional geram um evento no log do sistema. Esses logs coletados pelo Log Analytics e armazenados no repositório do OMS. O OMS pode ser configurado para enviar alertas quando ocorrem eventos predefinidos.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-5"></a>Controle NIST 800-53 AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Gerenciamento de conta | Inatividade por logout

**AC-2 (5)** A organização exige que os usuários façam logoff quando [Atribuição: o período de tempo definido pela organização de inatividade esperada ou descrição de quando fazer logoff].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode estabelecer uma política que os usuários fazem logoff quando eles sabem que ficarão inativos por um período de tempo (ou outros fatores). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-7a"></a>Controle NIST 800-53 AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>Gerenciamento de conta | Esquemas com base em função

**AC-2 (7).a** A organização estabelece e administra contas de usuário com privilégios de acordo com um esquema de acesso com base em função que organiza informações permitidas de acesso ao sistema e privilégios em funções.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica implementa os seguintes tipos de conta do sistema: usuários do Azure Active Directory, usuários do sistema operacional Windows, a conta de serviço do SQL Server. Os privilégios de conta do Active Directory do Azure são implementados usando o controle de acesso com base em função ao atribuir usuários a funções; os privilégios de conta do Active Directory são implementados usando o controle de acesso com base em função ao atribuir usuários a grupos de segurança. Esses esquemas com base em função podem ser estendidos pelo cliente para atender às necessidades da tarefa. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-7b"></a>Controle NIST 800-53 AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>Gerenciamento de conta | Esquemas com base em função

**AC-2 (7).b** A organização monitora as atribuições de função privilegiada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o painel de identidade e acesso de soluções do OMS para segurança e auditoria. Esse painel permite que os gerentes de conta monitorem o uso de contas do sistema de informações. Essa solução pode ser consultada para relatar as atribuições de função privilegiada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-7c"></a>Controle NIST 800-53 AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>Gerenciamento de conta | Esquemas com base em função

**AC-2 (7).c** A organização retira [atribuição: ações definidas pela organização] quando as atribuições de função com privilégios não são mais apropriadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por executar uma ação em contas controladas pelo cliente quando as atribuições de função com privilégios não são mais apropriadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-9"></a>Controle NIST 800-53 AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Gerenciamento de conta | Restrições no uso de contas compartilhadas / grupo

**AC-2 (9)** A organização só permite o uso de contas compartilhadas/de grupo que atendam aos [atribuição: condições de organização definidas para estabelecer contas compartilhadas/de grupo].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As contas compartilhadas/de grupo não são habilitadas nos recursos implantados por estas especificações técnicas do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-10"></a>Controle NIST 800-53 AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Gerenciamento de conta | Encerramento de credencial de conta compartilhada / grupo

**AC-2 (10)** O sistema de informações encerra as credenciais de conta/grupo compartilhadas quando os membros saírem do grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As contas compartilhadas/de grupo não são habilitadas nos recursos implantados por estas especificações técnicas do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-11"></a>Controle NIST 800-53 AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Gerenciamento de conta | Condições de uso

**AC-2 (11)** O sistema de informações força [Atribuição: circunstâncias definidas pela organização e/ou condições de uso] para [atribuição: contas do sistema de informações definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo pode ser estabelecida no Active Directory e configurada para implementar restrições de horário do dia ou outras condições de uso da conta. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-12a"></a>Controle NIST 800-53 AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gerenciamento de conta | Monitoramento de conta / Uso atípico 

**AC-2 (12).a** A organização monitora informações de contas do sistema para [Atribuição: uso atípico definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o painel de identidade e acesso de soluções do OMS para segurança e auditoria. Este painel permite que os gerentes de conta monitorem tentativas de acesso com base nos recursos implantados. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-12b"></a>Controle NIST 800-53 AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>Gerenciamento de conta | Monitoramento de conta / Uso atípico 

**AC-2 (12).b** A organização relata uso atípico de contas do sistema de informações para [atribuição: pessoal ou funções definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o painel de identidade e acesso de soluções do OMS para segurança e auditoria. Este painel permite que os gerentes de conta monitorem tentativas de acesso com base nos recursos implantados. Essa solução pode ser configurada para enviar alertas quando atividades atípicas forem suspeitas ou outros eventos predefinidos ocorrerem. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-2-13"></a>Controle NIST 800-53 AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Gerenciamento de conta | Desativar contas para pessoas físicas de alto risco

**AC-2 (13)** A organização desativa a contas de usuários que demonstrem um risco significativo em [Atribuição: período de tempo definido pela organização] de descoberta do risco.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível empresarial do cliente e procedimentos podem definir condições para desabilitar contas de usuários que mostrem um risco significativo para a organização. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-3"></a>Controle NIST 800-53 AC-3

#### <a name="access-enforcement"></a>Regras de acesso

**AC-3** O sistema de informação impõe autorizações aprovadas para acesso lógico às informação e aos recursos do sistema de acordo com as políticas de acesso aplicáveis.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica do Azure impõe autorizações de acesso lógico usando controle de acesso com base em função imposto pelo Azure Active Directory, atribuindo usuários a funções, pelo Active Directory atribuindo usuários a grupos de segurança e pelos controles no nível do sistema operacional do Windows. As funções do Azure Active Directory atribuídas aos usuários ou grupos controlam o acesso lógico a recursos no Azure no nível do recurso, do grupo ou da assinatura. Grupos de segurança do Active Directory controlam o acesso lógico a funções e recursos no nível do sistema operacional. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-4"></a>Controle NIST 800-53 AC-4

#### <a name="information-flow-enforcement"></a>Política de fluxo de informações

**AC-4** O sistema de informações impõe autorizações aprovadas para controlar o fluxo de informações dentro do sistema e entre sistemas interconectados com base em [Atribuição: políticas de controle de fluxo de informações definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação técnica do Azure impõe restrições ao fluxo de informações com o uso de grupos de segurança de rede aplicado às sub-redes cujos recursos são implantados, Application Gateway e o balanceador de carga. Os grupos de segurança da rede garantem que o fluxo de informações é controlado entre os recursos com base em regras aprovadas. O gateway de aplicativo e o balanceador de carga fazem a rota do tráfego dinamicamente para recursos específicos com base em funções aprovadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-4-8"></a>Controle NIST 800-53 AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Política de fluxo de informações | Filtros de política de segurança

**AC-4 (8)** O sistema de informações impõe o controle de fluxo de informações usando [atribuição: filtros de política de segurança definidos pela organização] como base para decisões de controle de fluxo para [atribuição: fluxos de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por impor o controle do fluxo de informações nos recursos implantados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-4-21"></a>Controle NIST 800-53 AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Política de fluxo de informações | Separação lógica / física de fluxos de informações

**AC-4 (21)** O sistema de informações separam os fluxos de informações logicamente ou fisicamente usando [atribuição: mecanismos e/ou técnicas definidos pela organização] para realizar [atribuição: separações necessárias definidas pela organização por tipos de informações].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por separar os fluxos de informações nos recursos implantados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-5a"></a>Controle NIST 800-53 AC-5.a

#### <a name="separation-of-duties"></a>Separação de funções

**AC-5.a** A organização separa [atribuição: tarefas de indivíduos definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela separação de tarefas entre contas controladas pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-5b"></a>Controle NIST 800-53 AC-5.b

#### <a name="separation-of-duties"></a>Separação de funções

**AC-5.b** A organização documenta a separação de funções de indivíduos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por documentar a separação de tarefas entre contas controladas pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-5c"></a>Controle NIST 800-53 AC-5.c

#### <a name="separation-of-duties"></a>Separação de funções

**AC-5.c** A organização define autorizações de acesso ao sistema de informações para dar suporte a separação de funções.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o controle de acesso com base em função que pode ser configurado para separar as funções de acordo com os requisitos da organização. Os privilégios de conta do Active Directory do Azure são implementados usando o controle de acesso com base em função ao atribuir usuários a funções; os privilégios de conta do Active Directory são implementados usando o controle de acesso com base em função ao atribuir usuários a grupos de segurança. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-6"></a>Controle NIST 800-53 AC-6

#### <a name="least-privilege"></a>Privilégio mínimo

**AC-6** A organização emprega o princípio de privilégio mínimo, permitindo que apenas acessos autorizados de usuários (ou processos atuando em nome dos usuários) que são necessários para realizar tarefas atribuídas conforme missões organizacionais e funções de negócios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o controle de acesso com base em função para dar privilégio somente a usuários explicitamente atribuídos. Os privilégios de conta do Active Directory do Azure são implementados usando o controle de acesso com base em função ao atribuir usuários a funções; os privilégios de conta do Active Directory são implementados usando o controle de acesso com base em função ao atribuir usuários a grupos de segurança.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-1"></a>Controle NIST 800-53 AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Privilégios mínimos | Autorizar o acesso a funções de segurança

**AC-6 (1)** A organização autoriza explicitamente o acesso ao [atribuição: funções de segurança definidas pela organização (implantadas em hardware, software e firmware) e informações relevantes de segurança].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Procedimentos de controle de acesso de nível corporativo do cliente podem definir um processo de autorização de acesso que inclui acesso a funções de segurança. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-2"></a>Controle NIST 800-53 AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Privilégios mínimos | Acesso sem privilégios para funções de risco

**AC-6 (2)** A organização exige que os usuários de contas do sistema de informações, ou funções, com acesso ao [atribuição: funções de segurança definidas pela organização ou informações relevantes de segurança], utilizem contas sem privilégios ou funções, ao acessar funções de risco.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode exigir que os usuários utilizem contas sem privilégios ao acessarem funções de risco. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-3"></a>Controle NIST 800-53 AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Privilégios mínimos | Acesso à rede para comandos com privilégios

**AC-6 (3)** A organização autoriza o acesso à rede para [atribuição: comandos privilegiados definidos pela organização] somente para [atribuição: necessidades operacionais atraentes definidas pela organização] e documenta as razões para tal acesso no plano de segurança para o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode definir comandos com privilégios que podem ser acessados através de uma rede. Observação: os clientes não têm nenhum acesso físico à infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-5"></a>Controle NIST 800-53 AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Privilégios mínimos | Contas com privilégios

**AC-6 (5)** A organização restringe contas com privilégios no sistema de informações para [atribuição: pessoal ou funções definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode definir restrições para o uso de contas com privilégios. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-7a"></a>Controle NIST 800-53 AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>Privilégios mínimos | Revisão de privilégios do usuário

**AC-6 (7).a** As análises da organização [atribuição: frequência definida pela organização] os privilégios atribuídos ao [atribuição: funções ou classes de usuários definidos pela organização] para validar a necessidade desses privilégios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar os privilégios de usuário de contas controladas pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-7b"></a>Controle NIST 800-53 AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>Privilégios mínimos | Revisão de privilégios do usuário

**AC-6 (7).b** A organização reatribui ou remove os privilégios, se necessário, para refletir corretamente as necessidades organizacionais da missão/dos negócios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por atribuir novamente ou remover privilégios para contas controladas pelo cliente, quando necessário. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-8"></a>Controle NIST 800-53 AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Privilégios mínimos | Níveis de privilégio para execução de código

**AC-6 (8)** O sistema de informações impede que [atribuição: software definido pela organização] sejam executados nos níveis de privilégio mais alto que usuários que executam o software.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o controle de acesso com base em função para dar privilégio somente a usuários explicitamente atribuídos. As proteções de nível de sistema operacional da máquina virtual não permitem que o software seja executado em um nível de privilégio mais alto que o dos usuários que executam o software. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-9"></a>Controle NIST 800-53 AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Privilégios mínimos | Auditoria do uso de funções com privilégios

**AC-6 (9)** O sistema de informações faz auditoria da execução de funções privilegiadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o serviço do Log Analytics no OMS. As VMs implantadas e as contas de armazenamento de diagnósticos do Azure são fontes conectadas ao Log Analytics que garantem que a execução de funções com privilégio sejam auditadas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-6-10"></a>Controle NIST 800-53 AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Privilégios mínimos | Proibir usuários sem privilégios de executar funções com privilégios

**AC-6 (10)** O sistema de informações impede que usuários sem privilégios executem funções privilegiadas como desabilitar, burlar ou alterar proteções/contramedidas de segurança implementadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure implementa o controle de acesso com base em função para dar privilégio somente a usuários explicitamente atribuídos.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-7a"></a>Controle NIST 800-53 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Tentativas de logon malsucedidas

**AC 7.a** O sistema de informações impõe um limite de [atribuição: número definido pela organização] tentativas consecutivas inválidas de logon por um usuário durante uma [atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O portal do Azure limita tentativas inválidas consecutivas de logon por usuários. Uma política de grupo é aplicada no nível do sistema operacional para todas as máquinas virtuais implantadas por essa especificação técnica do Azure. A política limita tentativas consecutivas inválidas de logon pelos usuários para não mais de três dentro de um período de 15 minutos. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-7b"></a>Controle NIST 800-53 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Tentativas de logon malsucedidas

**AC 7.b** O sistema de informações automaticamente [seleção: bloqueia a conta/nó para um [atribuição: período de tempo definido pela organização]; bloqueia a conta/nó até ser liberado por um administrador; atrasa a próxima solicitação de logon de acordo com a [ Atribuição: algoritmo de retardo definido pela organização]] quando o número máximo de tentativas for excedido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O portal do Azure bloqueia tentativas consecutivas inválidas de logon por usuários. Uma política de grupo é aplicada no nível do sistema operacional para todas as máquinas virtuais implantadas por essa especificação técnica do Azure. A política bloqueia contas por três horas após três tentativas consecutivas inválidas de logon pelos usuários. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-7-2"></a>Controle NIST 800-53 AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Tentativas de logon malsucedidas | Limpar / apagar de dispositivo móvel

**AC-7 (2)** O sistema de informações limpa/apaga informações de [atribuição: dispositivos móveis definidos pelo usuário] com base em [atribuição: requisitos de limpar/apagar definidos pela organização] após [atribuição: número definido pela organização] tentativas consecutivas inválidas de logon do dispositivo.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Dispositivos móveis não estão dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite dispositivos móveis dentro dos limites do Azure. Sendo assim, esse controle não se aplica ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>Controle NIST 800-53 AC-8.a

#### <a name="system-use-notification"></a>Notificação do uso do sistema

**AC 8.a** O sistema de informações exibe para os usuários [atribuição: mensagem ou faixa de notificação de uso do sistema definido pela organização] antes de conceder acesso ao sistema que fornece avisos de privacidade e segurança consistente com leis federais aplicáveis, pedidos executivos, diretivas, políticas, normas, padrões, orientação e estados que os usuários estão acessando nos EUA Sistema de informações do governo; uso do sistema de informações pode ser monitorado, registrado e está sujeita a auditoria; uso não autorizado do sistema de informações é proibido e sujeito a penalidades criminais e civis; e uso do sistema de informações indica o consentimento para monitoramento e gravação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo implementa uma notificação de uso do sistema que é exibido para os usuários para login. Obs.: A especificação técnica do Azure implementa um exemplo de notificação de uso do sistema. O cliente deve editar este texto para atender aos requisitos da organização e/ou do corpo regulador. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-8b"></a>Controle NIST 800-53 AC-8.b

#### <a name="system-use-notification"></a>Notificação do uso do sistema

**AC-8.b** O sistema de informações mantém a mensagem de notificação ou faixa na tela até que os usuários reconheçam as condições de uso e tomem medidas explícitas para fazer logon ou mesmo acessar o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo implementa uma notificação de uso do sistema que é exibido para os usuários para antes do logon. O usuário deve confirmar a notificação para fazer logon. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-8c"></a>Controle NIST 800-53 AC-8.c

#### <a name="system-use-notification"></a>Notificação do uso do sistema

**AC-8.c** O sistema de informações para sistemas publicamente acessíveis exibe informações de uso do sistema [atribuição: condições definidas pela organização], antes de conceder acesso adicional; exibe referências, se houver, para monitoramento, gravação, ou auditoria que são consistentes com acomodações de privacidade para esses sistemas que geralmente proibem essas atividades; e inclui uma descrição de usos autorizados do sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por exibir uma notificação de uso do sistema em todos os recursos de implantação de cliente acessíveis publicamente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-10"></a>Controle NIST 800-53 AC-10

#### <a name="concurrent-session-control"></a>Controle de sessões simultâneas

**AC-10** O sistema de informações limita o número de sessões simultâneas para cada [atribuição: conta definida pela organização e/ou tipo de conta] para [atribuição: número definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Uma política do sistema operacional é implementada para máquinas virtuais implementadas pela especificação técnica do Azure. A política implementa restrições de sessão simultânea (duas sessões). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-11a"></a>Controle NIST 800-53 AC-11.a

#### <a name="session-lock"></a>Bloqueio de sessão

**AC 11.a** O sistema de informações impede o acesso adicional para o sistema por meio de um bloqueio de sessão após [atribuição: período de tempo definido pela organização] de inatividade ou depois de receber uma solicitação de um usuário.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP. Os usuários podem iniciar manualmente o bloqueio. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-11b"></a>Controle NIST 800-53 AC-11.b

#### <a name="session-lock"></a>Bloqueio de sessão

**AC 11.b** O sistema de informações mantém o bloqueio de sessão até que o usuário restabeleça o acesso usando procedimentos estabelecidos de identificação e autenticação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP. Os usuários devem autenticar novamente para desbloquear a sessão.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-11-1"></a>Controle NIST 800-53 AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Bloqueio de sessão | Exibir padrão de ocultação

**AC-11 (1)** O sistema de informações oculta, por meio do bloqueio da sessão, as informações anteriormente visíveis na tela com uma imagem que pode ser exibida publicamente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta especificação yécnica do Azure implanta um controlador de domínio para o qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo implementa um bloqueio de inatividade para sessões RDP. O bloqueio de sessão oculta informações anteriormente visíveis. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-12"></a>Controle NIST 800-53 AC-12

#### <a name="session-termination"></a>Encerramento da sessão

**AC-12** O sistema de informações encerra automaticamente uma sessão de usuário após [atribuição: condições ou eventos que desencadeiam a necessidade de desconectar da sessão definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A configuração de host da sessão da área de trabalho remota para as máquinas virtuais do Windows implantadas por esse projeto do Azure pode ser configurada para atender aos requisitos de encerramento de sessão da organização. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-12-1a"></a>Controle NIST 800-53 AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Encerramento da sessão | Logoffs iniciados pelo usuário / Exibição de mensagem

**AC-12 (1).a** O sistema de informações fornece uma funcionalidade de logoff para sessões de comunicação iniciadas pelo usuário sempre que a autenticação é usada para acessar [atribuição: recursos de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os sistemas operacionais do Azure e da máquina virtual implantados por essa especificação técnica do Azure permitem usos para iniciar um logoff. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-12-1b"></a>Controle NIST 800-53 AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Encerramento da sessão | Logoffs iniciados pelo usuário / Exibição de mensagem

**AC-12 (1).b** O sistema de informações exibe uma mensagem de logoff explícita para os usuários indicando o encerramento confiável de sessões de comunicação autenticadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os sistemas operacionais do Azure e da máquina virtual implantados por essa especificação técnica do Azure permitem usos para iniciar um logoff. O processo de logoff fornece indicação para os usuários de que a sessão foi encerrada. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-14a"></a>Controle NIST 800-53 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Ações permitidas sem identificação ou autenticação

**AC-14.a** A organização identifica [atribuição: ações de usuário definidas pela organização] que podem ser executadas no sistema de informações sem identificação ou autenticação consistente com funções organizacionais de missões/negócios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar as ações que podem ser executadas nos recursos de implantação de cliente sem identificação ou autenticação (por exemplo, como exibir uma página da web acessível publicamente). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-14b"></a>Controle NIST 800-53 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Ações permitidas sem identificação ou autenticação

**AC-14.b** Uma organização documenta e fornece lógica de suporte no plano de segurança para o sistema de informações, ações do usuário não requerem identificação ou autenticação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por fornecer a documentação para ações de usuário não exigindo identificação ou autenticação em recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-17a"></a>Controle NIST 800-53 AC-17.a

#### <a name="remote-access"></a>Acesso remoto

**AC-17.a** A organização estabelece e documenta as restrições de uso, requisitos de configuração/conexão e diretrizes de implementação para cada tipo de acesso remoto permitido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode definir restrições para o uso de acesso remoto. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-17b"></a>Controle NIST 800-53 AC-17.b

#### <a name="remote-access"></a>Acesso remoto

**AC-17.b** A organização autoriza o acesso remoto para o sistema de informações antes de permitir conexões.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de controle de acesso de nível corporativo do cliente podem estabelecer um processo de autorização para o acesso remoto. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-1"></a>Controle NIST 800-53 AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Acesso remoto | Controle / monitoramento automatizado

**AC-17 (1)** O sistema de informações monitora e controla os métodos de acesso remoto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure fornece acesso remoto para o sistema de informações por meio do portal do Azure, por meio de conexão de área de trabalho remota através de um jumpbox e por meio de um aplicativo web implementado pelo cliente. O acesso por meio do portal do Azure e das sessões de área de trabalho remota são auditados e podem ser monitorados pelo OMS. O cliente deve implementar controles de acesso remoto, conforme necessário, para o aplicativo web. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-2"></a>Controle NIST 800-53 AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Acesso remoto | Proteção de confidencialidade / Integridade usando a criptografia

**AC-17 (2)** O sistema de informações implementa mecanismos de criptografia para proteger a confidencialidade e integridade das sessões de acesso remoto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso remoto aos recursos implantados por esta especificação técnica do Azure, incluindo o portal do Azure, a conexão de área de trabalho remota e o gateway de aplicativo Web, é protegido com o uso de TLS. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-3"></a>Controle NIST 800-53 AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Acesso remoto | Pontos de controle de acesso gerenciado

**AC-17 (3)** O sistema de informações encaminha todos os acessos remotos por meio de [atribuição: número definido pela organização] pontos de controle de acesso de rede gerenciados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso remoto para o aplicativo web ideal implantado por esse projeto do Azure é através de um gateway de aplicativo. O acesso remoto para todos os outros recursos é por meio de um jumpbox. Há outros pontos de extremidade não acessíveis publicamente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-4a"></a>Controle NIST 800-53 AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>Acesso remoto | comandos / acesso com privilégios

**AC-17 (4) .a** A organização autoriza a execução de comandos com privilégios e acesso a informações relevantes de segurança por meio do acesso remoto somente para [atribuição: necessidades definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode definir comandos com privilégios que podem ser acessados remotamente e incluem uma lógica. Observação: os clientes não têm nenhum acesso de rede direto à infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-4b"></a>Controle NIST 800-53 AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>Acesso remoto | comandos / acesso com privilégios

**AC-17 (4).b** A organização documenta a lógica para tal acesso no plano de segurança para o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de controle de acesso de nível corporativo do cliente pode definir comandos com privilégios que podem ser acessados remotamente e incluem uma lógica. Observação: os clientes não têm nenhum acesso de rede direto à infraestrutura do Azure. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-17-9"></a>Controle NIST 800-53 AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Acesso remoto | Desconectar / desabilitar o acesso

**AC-17 (9)** A organização fornece a capacidade de desconectar ou desabilitar rapidamente o acesso remoto para o sistema de informações em [atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa especificação técnica do Azure fornece acesso remoto para o sistema de informações por meio do portal do Azure, por meio de conexão da área de trabalho remota através de um jumpbox e por meio de um aplicativo de web. Se uma conta do Active Directory do Azure foi desabilitada ou removida, o acesso ao portal do Azure é desconectado imediatamente. Da mesma forma, se uma conta no nível de sistema operacional da máquina virtual foi desabilitada ou removida, o acesso à área de trabalho remoto por meio de jumpbox é desconectado imediatamente. Os clientes devem implementar a desconexão ao acesso remoto para o aplicativo web. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-18a"></a>Controle NIST 800-53 AC-18.a

#### <a name="wireless-access"></a>Acesso sem fio

**AC-18.a** A organização estabelece as restrições de uso, requisitos de configuração/conexão e diretrizes de implementação para acesso remoto.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure estabelece restrições de uso, requisitos de configuração/conexão e diretrizes de implementação para acesso sem fio através de rede padrão de segurança, que proíbe explicitamente o uso de sem fio no ambiente do Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>Controle NIST 800-53 AC-18.b

#### <a name="wireless-access"></a>Acesso sem fio

**AC-18.b** A organização autoriza o acesso sem fio para o sistema de informações antes de permitir tais conexões.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso sem fio em data centers do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>Controle NIST 800-53 AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Acesso sem fio | Autenticação e criptografia

**AC-18 (1)** O sistema de informações protege o acesso sem fio no sistema usando a autenticação de [seleção (um ou mais): usuários; dispositivos] e criptografia.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso sem fio no ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>Controle NIST 800-53 AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Acesso sem fio | Desabilitar rede sem fio

**AC-18 (3)** A organização desabilita, quando não destinado para uso, recursos de rede sem fio internamente inseridos em componentes do sistema de informações antes da emissão e implantação.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso sem fio no ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>Controle NIST 800-53 AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Acesso sem fio | Restringir as configurações por usuários

**AC-18 (4)** A organização identifica e autoriza explicitamente os usuários com permissão para configurar recursos de rede sem fio de forma independente.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso sem fio no ambiente do Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>Controle NIST 800-53 AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Acesso sem fio | Antenas / níveis de energia de transmissão

**AC-18 (5)** A organização seleciona antenas de rádio e calibra níveis de energia de transmissão para reduzir a probabilidade de que sinais utilizáveis possam ser recebidos fora dos limites controlados da organização.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum acesso sem fio dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso sem fio no ambiente do Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>Controle NIST 800-53 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Controle de acesso para dispositivos móveis

**AC-19.a** A organização estabelece as restrições de uso, requisitos de configuração/conexão e diretrizes de implementação para dispositivos móveis controlados pela organização.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo móvel controlado pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite dispositivos móveis dentro dos limites do Azure. Sendo assim, esse controle não se aplica ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>Controle NIST 800-53 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Controle de acesso para dispositivos móveis

**AC 19.b** A organização autoriza a conexão de dispositivos móveis para os sistemas de informações organizacionais.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo móvel controlado pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite dispositivos móveis dentro dos limites do Azure. Sendo assim, esse controle não se aplica ao Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>Controle NIST 800-53 AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Controle de acesso para dispositivos móveis | total de dispositivo / baseado no contêiner de criptografia

**AC-19 (5)** A organização emprega [seleção: criptografia de dispositivo completo; criptografia de contêiner] para proteger a confidencialidade e a integridade das informações em [atribuição: dispositivos móveis definidos pela organização].

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo móvel controlado pelo cliente dentro do escopo de sistemas implantados no Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite dispositivos móveis dentro dos limites do Azure. Sendo assim, esse controle não se aplica ao Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>Controle NIST 800-53 AC-20.a

#### <a name="use-of-external-information-systems"></a>Uso de sistemas de informações externas

**AC-20.a** A organização estabelece os termos e condições, que não são consistentes com relação de confiança estabelecida com outras organizações, proprietário, operacional, e/ou manutenção dos sistemas de informações externas, permitindo que pessoas autorizadas acessem o sistema de informações de sistemas de informações externas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de aquisição de serviços do sistema de informações externas de nível empresarial do cliente inclui uma cláusula referente ao uso das ofertas de serviços de nuvem previstas no FedRAMP.  Azure recebeu uma autorização provisional para operar (P-ATO) por placa conjunta de autorização do FedRAMP (JAB) permitindo o uso de aquisição de serviços de nuvem do Azure por agências do governo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-20b"></a>Controle NIST 800-53 AC-20.b

#### <a name="use-of-external-information-systems"></a>Uso de sistemas de informações externas

**AC-20.b** A organização estabelece os termos e condições que não são consistentes com relação de confiança estabelecida com outras organizações, proprietário, operacional, e/ou manutenção dos sistemas de informações externas, permitindo que pessoas autorizadas acessem o sistema de informações de sistemas de informações externas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de aquisição de serviços do sistema de informações externas de nível empresarial do cliente inclui uma cláusula referente ao uso das ofertas de serviços de nuvem previstas no FedRAMP.  Azure recebeu uma autorização provisional para operar (P-ATO) por placa conjunta de autorização do FedRAMP (JAB) permitindo o uso de aquisição de serviços de nuvem do Azure por agências do governo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-20-1"></a>Controle NIST 800-53 AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Uso de sistemas de informação externas | Limites de uso autorizado

**AC-20 (1)** A organização permite que pessoas autorizadas usem um sistema de informações externas para acessar o sistema de informações ou para processar, armazenar ou transmitir informações de organização controlada somente quando a organização verificar a implementação de controles de segurança necessárias no sistema externo conforme especificado na diretiva de segurança de informações da organização e o plano de segurança; ou retém informações aprovadas sistema contratos de processamento ou a conexão com a entidade organizacional que hospeda o sistema externo de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O grupo de tecnologia de informações de nível empresarial do cliente pode verificar a conformidade de provedor de serviço de nuvem com os requisitos de segurança de informações de organização e conceder aprovação de toda a empresa para utilizar as ofertas associadas ao serviço de nuvem. Azure obteve autorização provisória para operar (P-ATO) pela Comissão de Autorização Conjunta (JAB) do FedRAMP. O Azure é avaliado por uma organização de avaliação independente (3PAO) aprovada pelo FedRAMP para verificar a conformidade com o controle de segurança do FedRAMP e outros requisitos. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ac-20-2"></a>Controle NIST 800-53 AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Uso de sistemas de informação externas | Dispositivos de armazenamento portáteis

**AC-20 (2)** A organização [seleção: restringe; proíbe] o uso de dispositivos de armazenamento portátil controlados por indivíduos autorizados em sistemas de informações externas.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a qualquer recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | A Microsoft não permite dispositivos de armazenamento portáteis controlados pelo cliente no ambiente Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>Controle NIST 800-53 AC-21.a

#### <a name="information-sharing"></a>Compartilhamento de informações

**AC-21.a** A organização permite que os usuários autorizados determinem se autorizações de acesso atribuídas ao parceiro de compartilhamento coincidem as restrições de acesso a informações de compartilhamento de informações [atribuição: organização informações definidas pelo compartilhamento circunstâncias em que é necessário a critério do usuário].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A Política de controle de acesso de nível empresarial do cliente pode incluir cláusulas sobre o compartilhamento de informações. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-21b"></a>Controle NIST 800-53 AC-21.b

#### <a name="information-sharing"></a>Compartilhamento de informações

**AC-21.b** A organização emprega [atribuição: organização definido automatizados mecanismos ou processos manuais] para ajudar os usuários na tomada de decisões de colaboração/compartilhamento de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um recurso de suporte de decisão de compartilhamento de informações de nível empresarial. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-22a"></a>Controle NIST 800-53 AC-22.a

#### <a name="publicly-accessible-content"></a>Conteúdo publicamente acessível

**AC-22.a** A organização designa indivíduos autorizados para publicar informações em um sistema de informações publicamente acessível.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de controle de acesso de nível empresarial do cliente podem designar a indivíduos autorizados a postar informações publicamente acessíveis. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-22b"></a>Controle NIST 800-53 AC-22.b

#### <a name="publicly-accessible-content"></a>Conteúdo publicamente acessível

**AC-22.b** A organização treina indivíduos autorizados para garantir que informações publicamente acessíveis não contenham informações confidenciais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de treinamento de nível corporativo para indivíduos autorizados para postar informações publicamente acessíveis. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-22c"></a>Controle NIST 800-53 AC-22.c

#### <a name="publicly-accessible-content"></a>Conteúdo publicamente acessível

**AC-22.c** A organização analisa o conteúdo proposto de informações antes de serem postados no sistema para garantir que informações confidenciais não sejam incluídas nas informações publicamente acessível.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de controle de acesso de nível empresarial do cliente podem estabelecer um processo para a revisão periódica de conteúdo publicado de sistemas acessíveis. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ac-22d"></a>Controle NIST 800-53 AC-22.d

#### <a name="publicly-accessible-content"></a>Conteúdo publicamente acessível

**AC-22.d** A organização analisa o conteúdo no sistema de informações publicamente acessível para informações confidenciais [atribuição: organização definido frequência] e remove essas informações, se descoberto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de controle de acesso de nível empresarial do cliente podem estabelecer um processo para a revisão periódica de conteúdo publicado de sistemas acessíveis. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |

