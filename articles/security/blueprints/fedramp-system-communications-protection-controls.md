---
title: "Automação da Especificação Técnica do Azure no FedRAMP - Proteção do sistema e das comunicações"
description: "Aplicativos Web para FedRAMP - Proteção de do sistema e das comunicações"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 02e2d07eb29d0d5d436afed1cdab4fe710674a8c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-communications-protection-sc"></a>Proteção do sistema e das comunicações (SC)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelo Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-sc-1"></a>Controle NIST 800-53 SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Política e procedimentos de proteção do sistema e das comunicações

**SC-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de proteção do sistema e das comunicações que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de proteção do sistema e das comunicações e os controles de proteção associados do sistema e das comunicações; e revisa e atualiza a política de proteção do sistema e das comunicações atual [Atribuição: frequência definida pela organização]; e os procedimentos de proteção do sistema e das comunicações [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de proteção do sistema e das comunicações de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-2"></a>Controle NIST 800-53 SC-2

#### <a name="application-partitioning"></a>Particionamento do aplicativo

**SC-2** O sistema de informações separa a funcionalidade de usuário (incluindo os serviços de interface do usuário) da funcionalidade de gerenciamento do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Essa Especificação Técnica do Azure separa a funcionalidade do usuário da funcionalidade de gerenciamento do sistema por meio da aplicação de controles de acesso lógico e a da arquitetura do sistema. A funcionalidade de usuário é limitada a interfaces de aplicativo Web implantados pelo cliente. As interfaces para a funcionalidade de gerenciamento do sistema são independentes das interfaces do usuário. Toda a conectividade de gerenciamento é executada por meio de um host bastião seguro (jumpbox) localizado em uma sub-rede de gerenciamento com regras do grupo de segurança de rede para limitar o acesso aos recursos de produção conforme adequado. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-3"></a>Controle NIST 800-53 SC-3

#### <a name="security-function-isolation"></a>Isolamento de função de segurança

**SC-3** O sistema de informações isola as funções de segurança de funções que não estão relacionadas a segurança.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows mantém domínios de execução separados para cada processo em execução designando um espaço de endereço virtual privado para cada processo. Além disso, a solução implementa uma arquitetura e controles de acesso criados para isolar a funcionalidade de segurança quando necessário. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-4"></a>Controle NIST 800-53 SC-4

#### <a name="information-in-shared-resources"></a>Informações em recursos compartilhados

**SC-4** O sistema de informações impede a transferência de informações não autorizadas e não intencionais por meio de recursos compartilhados do sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O sistema operacional gerencia recursos (por exemplo, memória, armazenamento), de modo que essas informações fiquem acessíveis somente para usuários e funções com permissões apropriadas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-5"></a>Controle NIST 800-53 SC-5

#### <a name="denial-of-service-protection"></a>Negação de prevenção de serviço

**SC-5** O sistema de informações protege contra ou limita os efeitos dos seguintes tipos de ataques negação de serviço: [Atribuição: tipos de ataques de negação de serviço ou referências a fontes para essas informações definidos pela organização] empregando [Atribuição: garantias de segurança definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta um Gateway de Aplicativo que inclue um firewall do aplicativo Web e recursos de balanceamento de carga. Máquinas virtuais implantadas com suporte a camada da Web, a camada de banco de dados e o Active Directory são implantados em um conjunto de disponibilidade escalonável. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-6"></a>Controle NIST 800-53 SC-6

#### <a name="resource-availability"></a>Disponibilidade de recursos

**SC-6** O sistema de informações protege a disponibilidade de recursos alocando [Atribuição: recursos definidos pela organização] por [Seleção (um ou mais); prioridade; cota; [Atribuição: garantias de segurança definidas pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. Cada processo do Windows fornece os recursos necessários para executar um programa. A prioridade de recursos é gerenciada pelo sistema operacional. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-7a"></a>Controle NIST 800-53 SC-7.c

#### <a name="boundary-protection"></a>Proteção de limite

**SC-7.a** O sistema de informações monitora e controla comunicações no limite externo do sistema e em limites internos-chave dentro do sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta um Gateway de Aplicativo, um balanceador de carga e configura as regras de grupo de segurança de rede para controlar as trocas em limites externos e entre sub-redes internas. O Gateway de Aplicativo, o balanceador de carga e o evento de grupo de segurança de rede e logs de diagnóstico são coletados pelo Log Analytics do OMS para permitir o monitoramento de clientes. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-7b"></a>Controle NIST 800-53 SC-7.b

#### <a name="boundary-protection"></a>Proteção de limite

**SC-7.b** O sistema de informações implementa sub-redes para componentes do sistema publicamente acessível que são [Seleção: fisicamente; logicamente] separados de redes organizacionais internas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema (por exemplo, o tráfego externo não pode acessar o banco de dados, o gerenciamento nem sub-redes do Active Directory). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-7c"></a>Controle NIST 800-53 SC-7.c

#### <a name="boundary-protection"></a>Proteção de limite

**SC-7.c** O sistema de informações se conecta a redes externas ou sistemas de informações somente por meio de interfaces gerenciadas que consistem em dispositivos com proteção de limites organizados de acordo com uma arquitetura de segurança organizacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta um Gateway de Aplicativo para gerenciar conexões externas para um aplicativo Web implantado pelo cliente. Conexões externas para acesso de gerenciamento são restritas ao host bastião/jumpbox implantado em uma sub-rede de gerenciamento com as regras de segurança de rede aplicadas para restringir as conexões externas a endereços IP autorizados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-3"></a>Controle NIST 800-53 SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Proteção de limite | Pontos de acesso

**SC-7 (3)** A organização limita o número de conexões de rede externas no sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta dois endereços IP públicos: um associado com o Gateway de Aplicativo; um associado ao host de bastiões gerenciamento/jumpbox. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-4a"></a>Controle NIST 800-53 SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**SC-7 (4).a** A organização implementa uma interface gerenciada para cada serviço de telecomunicação externa.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta dois endereços IP públicos: um associado com o Gateway de Aplicativo; um associado ao host de bastiões gerenciamento/jumpbox. O gerenciamento dessas interfaces é habilitada por meio da rede definida por software. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-4b"></a>Controle NIST 800-53 SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**SC-7 (4).b** A organização estabelece uma política de fluxo de tráfego para cada interface gerenciada.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta dois endereços IP públicos: um associado com o Gateway de Aplicativo; um associado ao host de bastiões gerenciamento/jumpbox. O gerenciamento dessas interfaces é habilitada por meio da rede definida por software. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-4c"></a>Controle NIST 800-53 SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**SC-7 (4).c** A organização protege a confidencialidade e a integridade das informações que estão sendo transmitidas em cada interface.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O gateway de aplicativo Web implantado por esta Especificação Técnica do Azure está configurado com um ouvinte HTTPS, garantindo confidencialidade e integridade de sessões de comunicações. Conexões de área de trabalho remota para o jumpbox também são criptografadas para fornecer confidencialidade e integridade. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-4d"></a>Controle NIST 800-53 SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**SC-7 (4).d** A organização documenta cada exceção à política de fluxo de tráfego com uma missão compatível/necessidade de negócios e a duração necessária.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os clientes não são responsáveis por operações do datacenter (para incluir serviços de telecomunicações). Todos os serviços de telecomunicação são fornecidos e gerenciados pelo Microsoft Azure. Esse controle é herdado do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-4e"></a>Controle NIST 800-53 SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externas

**SC-7 (4).e** A organização revisa exceções à política de fluxo de tráfego [Atribuição: frequência definida pela organização] e remove as exceções que não são mais compatíveis com uma missão explícita/necessidade de negócios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os clientes não são responsáveis por operações do datacenter (para incluir serviços de telecomunicações). Todos os serviços de telecomunicação são fornecidos e gerenciados pelo Microsoft Azure. Esse controle é herdado do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-5"></a>Controle NIST 800-53 SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Proteção de limite | Negar por padrão/permitir por exceção

**SC-7 (5)** O sistema de informações em interfaces gerenciadas nega o tráfego de comunicações de rede por padrão e permite o tráfego de comunicações de rede por exceção (ou seja, negar tudo, permitir por exceção).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Conjuntos de regras aplicados a grupos de segurança de rede implantados por essa Especificação Técnica do Azure são configurados usando um esquema negar por padrão. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-7"></a>Controle NIST 800-53 SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Proteção de limite | Impedir divisão de túnel para dispositivos remotos

**SC-7 (7)** O sistema de informações, em conjunto com um dispositivo remoto, impede que o dispositivo estabeleça simultaneamente conexões não remotas com o sistema e se comunique por meio de alguma outra conexão a recursos em redes externas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política de configuração de dispositivo remoto de nível empresarial do cliente pode abordar a divisão de túnel. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-8"></a>Controle NIST 800-53 SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Proteção de limite | Rotear o tráfego para os servidores proxy autenticados

**SC-7 (8)** O sistema de informações roteia [Atribuição: tráfego de comunicação interna definido pela organização] para [Atribuição: redes externas definidas pela organização] por meio de servidores proxy autenticados em interfaces gerenciadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rotear informações definidas por ele por meio de um proxy autenticado a uma rede externa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-10"></a>Controle NIST 800-53 SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Proteção de limite | Impedir o vazamento não autorizado

**SC-7 (10)** A organização impede o vazamento não autorizado de informações em interfaces gerenciadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A organização impede o vazamento não autorizado de informações em interfaces gerenciadas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-12"></a>Controle NIST 800-53 SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Proteção de limite | Proteção baseada em host

**SI-7 (12)** A organização implementa [Atribuição: mecanismos de proteção de limite baseados em host definidos pela organização] em [Atribuição: componentes do sistema de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implantadas por essa Especificação Técnica do Azure são configuradas com um firewall baseado em host habilitado. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-13"></a>Controle NIST 800-53 SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Proteção de limite | Isolamento de ferramentas de segurança/mecanismos/componentes de suporte

**SC-7 (13)** A organização isola [Atribuição: ferramentas de segurança de informações, mecanismos e componentes de suporte definidos pela organização] de outros componentes do sistema de informações internas implementando sub-redes separadas fisicamente com interfaces gerenciadas para outros componentes do sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta recursos em uma arquitetura com uma sub-rede de gerenciamento separado para implantação de cliente de ferramentas de segurança de informações e componentes de suporte. Subredes são separadas logicamente pelas regras de grupo do segurança de rede. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-18"></a>Controle NIST 800-53 SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Proteção de limite | Falha na segurança

**SC-7 (18)** A segurança do sistema de informações falha no caso de uma falha operacional de um dispositivo de proteção de limite.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Não há dispositivos de proteção de limite físico dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure implanta servidores de Gateway separados geograficamente e redundantes e VPN SSL. Quando um sistema de Gateway falha, ele falha com a segurança e o acesso é restringido no ambiente. Para estabelecer uma conexão com o ambiente do Microsoft Azure, um usuário deve estabelecer uma conexão separada com um servidor Gateway ativo gerenciado pelo Microsoft Azure. <br /> Além disso, se os dispositivos de rede do Microsoft Azure (incluindo roteadores de borda, roteadores de acesso, balanceadores de carga, switches de agregação e TORS) falharem, o circuito afetado é desconectado, assim, fazendo com que a segurança falhe. Uma falha de um dispositivo de rede do Microsoft Azure não pode levar a ou fazer com que informações externas ao sistema entrem no dispositivo, nem uma falha pode permitir uma versão de informações não autorizadas. A redundância interna permite que os ativos do Microsoft Azure falhem sem afetar a disponibilidade. |


 ### <a name="nist-800-53-control-sc-7-20"></a>Controle NIST 800-53 SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Proteção de limite | Isolamento dinâmico/diferenciação

**SC-7 (20)** O sistema de informações fornece a capacidade isolar/segregar dinamicamente [Atribuição: componentes dedo sistema de informações definidos pela organização] de outros componentes do sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por garantir que o sistema tenha a capacidade de isolar dinamicamente recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-7-21"></a>Controle NIST 800-53 SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Proteção de limite | Isolamento de componentes do sistema de informações

**SI-7 (21)** A organização emprega mecanismos de proteção de limite para separar [Atribuição: componentes do sistema de informações definidos pela organização] com suporte para [Atribuição: missões e/ou funções de negócios definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta recursos em uma arquitetura com uma sub-rede separada e Web, sub-rede do banco de dados, sub-rede do Active Directory e sub-rede de gerenciamento. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-8"></a>Controle NIST 800-53 SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Confidencialidade e integridade de transmissão

**SC-8** O sistema de informações protege o [Seleção (um ou mais): confidencialidade; integridade] de informações transmitidas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A implementação de SI-8 (1) satisfaz esse requisito de controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-8-1"></a>Controle NIST 800-53 SI-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Confidencialidade e integridade de transmissão | Proteção física criptográfica ou alternativa

**SC-8 (1)** O sistema de informações implementa mecanismos de criptografia para [Seleção (um ou mais): evitar a divulgação não autorizada de informações; detectar alterações nas informações] durante a transmissão, a menos que protegidas por [Atribuição: garantias físicas alternativa definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure configura recursos para se comunicar usando somente protocolos seguros. O componente WAF do Gateway de Aplicativo está configurado para aceitar comunicações de usos externos por HTTPS/TLS e comunicar-se com o pool de back-end somente por HTTPS/TLS. O SQL Server está configurado para se comunicar somente por meio de HTTPS/TLS. Serviços de Área de Trabalho Remota são configurados para usar conexões seguras. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-10"></a>Controle NIST 800-53 SC-10

#### <a name="network-disconnect"></a>Desconexão da rede

**SC-10** O sistema de informações termina a conexão de rede associada a uma sessão de comunicação no final da sessão ou após [Atribuição: período de tempo definido pela organização] de inatividade.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A autenticação para sessões de área de trabalho remota é gerenciada pelo Active Directory. Quando o acesso estiver desabilitado para um usuário no Active Directory, as sessões remotas serão terminadas imediatamente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-12"></a>Controle NIST 800-53 SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Estabelecimento de Chave de Criptografia e Gerenciamento

**SC-12** A organização estabelece e gerencia chaves de criptografia para criptografia obrigatória empregada dentro do sistema de informações de acordo com [Atribuição: requisitos definidos pela organização para geração, distribuição, armazenamento, acesso e destruição de chave].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta um Azure Key Vault. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. O Azure Key Vault pode gerar chaves usando um recurso de segurança de módulo de segurança de hardware (HSM) FIPS 140-2 nível 2. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-12-1"></a>Controle NIST 800-53 SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Disponibilidade

**SC-12 (1)** A organização mantém a disponibilidade de informações em caso de perda de chaves de criptografia por usuários.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O Azure Key Vault é usado para armazenar chaves de criptografia e segredos na Especificação Técnica do Azure. O Key Vault simplifica o processo de gerenciamento de chaves para chaves que acessam e criptografam dados. Os autenticadores a seguir são armazenadas no Key Vault: senha do Azure para implantar conta, senha de administrador da máquina virtual, senha de conta de serviço do SQL Server. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-12-2"></a>Controle NIST 800-53 SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Chaves simétricas

**SC-12 (2)** A organização produz, controla e distribui chaves de criptografia simétricas usando [Seleção: compatível com FIPS NIST; com aprovação NSA] tecnologia e processos de gerenciamento de chaves.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O Azure Key Vault é usado para produzir, controlar e distribuir chaves criptográficas. O Azure Key Vault pode gerar chaves usando um recurso de segurança de módulo de segurança de hardware (HSM) FIPS 140-2 nível 2. As chaves são armazenadas e gerenciadas em contêineres criptografados com segurança no Azure Key Vault. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-12-3"></a>Controle NIST 800-53 SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Estabelecimento de Chave de Criptografia e Gerenciamento | Chaves assimétricas

**SC-12 (3)** A organização produz, controla e distribui chaves de criptografia assimétricas usando [Seleção: tecnologia e processos de gerenciamento de chaves aprovadas por NSA; certificados PKI classe 3 aprovados ou material para chave pré-posicionada; certificados PKI classe 3 ou 4 aprovados e tokens de segurança de hardware que protegem a chave privada do usuário].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por produzir, controlar e distribuir chaves de criptografia assimétrica (se forem usadas em recursos implantados por ele). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-13"></a>Controle NIST 800-53 SC-13

#### <a name="cryptographic-protection"></a>Proteção criptográfica

**SC-13** O sistema de informações implementa [Atribuição: usos criptográficos definidos pela organização e tipo de criptografia necessário para cada uso] de acordo com leis federais, pedidos executivos, diretivas, políticas, regulamentações e padrões aplicáveis.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A autenticação do Windows, a área de trabalho remota e o BitLocker são empregadas por esta Especificação Técnica do Azure. Esses componentes podem ser configurados para confiar em módulos criptográficos validados por FIPS 140. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-15a"></a>Controle NIST 800-53 SC-15.a

#### <a name="collaborative-computing-devices"></a>Dispositivos de computação colaborativos

**SC-15.a** O sistema de informações proíbe a ativação remota de dispositivos de computação colaborativos com as exceções a seguir: [Atribuição: exceções definidas pela organização onde a ativação remota é permitida].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo de computação colaborativo implantado como parte dessa Especificação Técnica do Azure. Observação: há dispositivos de computação colaborativos físicos dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-15b"></a>Controle NIST 800-53 SC-15.b

#### <a name="collaborative-computing-devices"></a>Dispositivos de computação colaborativos

**SC-15.b** O sistema de informações fornece uma indicação explícita de uso para os usuários fisicamente presentes nos dispositivos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo de computação colaborativo implantado como parte dessa Especificação Técnica do Azure. Observação: há dispositivos de computação colaborativos físicos dentro do escopo de sistemas implantados no Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-17"></a>Controle NIST 800-53 SC-17

#### <a name="public-key-infrastructure-certificates"></a>Certificados de infraestrutura de chave pública

**SC-17** A organização emite certificados de chave pública em uma [Atribuição: política de certificado definida pela organização] ou obtém certificados de chave pública de um provedor de serviços aprovado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de uma infraestrutura de chave pública de nível corporativo para emissão de certificados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-18a"></a>Controle NIST 800-53 SC-18.a

#### <a name="mobile-code"></a>Código móvel

**SC-18.a** A organização define código móvel aceitável e inaceitável e tecnologias de código móvel.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O sistema de nível empresarial do cliente e os procedimentos de proteção de comunicações podem definir um código móvel como aceitável e inaceitável. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-18b"></a>Controle NIST 800-53 SC-18.b

#### <a name="mobile-code"></a>Código móvel

**SC-18.b** A organização estabelece restrições de uso e diretrizes de implementação para código móvel aceitável e tecnologias de código móvel.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O sistema de nível empresarial do cliente e os procedimentos de proteção de comunicações podem estabelecer restrições no uso de código móvel. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-18c"></a>Controle NIST 800-53 SC-18.c

#### <a name="mobile-code"></a>Código móvel

**SC-18.c** A organização autoriza, monitora e controla o uso de código móvel dentro do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de um processo de nível empresarial para autorização, monitoramento e controle do uso de código móvel. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-19a"></a>Controle NIST 800-53 SC-19.a

#### <a name="voice-over-internet-protocol"></a>Protocolo IP de Voice Over

**SC-19.a** A organização estabelece restrições de uso e diretrizes de implementação para Protocol IP de Voice Over (VoIP) baseadas no potencial para causar danos ao sistema de informações se usado de forma maliciosa.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhuma tecnologia de protocolo IP de voice over implantada como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-19b"></a>Controle NIST 800-53 SC-19.b

#### <a name="voice-over-internet-protocol"></a>Protocolo IP de Voice Over

**SC-19.b** A organização autoriza, monitora e controla o uso de VoIP dentro do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhuma tecnologia de protocolo IP de voice over implantada como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-20a"></a>Controle NIST 800-53 SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Serviço de resolução de nome/endereço seguro (fonte autoritativa)

**SC-20.a** O sistema de informações fornece a autenticação de origem de dados adicionais e artefatos de verificação de integridade junto com os dados de resolução de nome autoritativo que o sistema retorna em resposta a consultas de resolução de nome/endereço externo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por um serviço de nome seguro e resolução de endereço. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-20b"></a>Controle NIST 800-53 SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Serviço de resolução de nome/endereço seguro (fonte autoritativa)

**SC-20.b** O sistema de informações fornece os meios para indicar o status de segurança de zonas filhas e (se o filho dá suporte a serviços de resolução seguros) para habilitar a verificação de uma cadeia de confiança entre domínios pai e filho, durante a operação como parte de um namespace hierárquico distribuído.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por um serviço de nome seguro e resolução de endereço. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-21"></a>Controle NIST 800-53 SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Serviço de resolução de nome/endereço seguro (Resolvedor recursivo ou de cache)

**SC-21** O sistema de informações solicita e executa autenticação de origem de dados e verificação de integridade de dados nas respostas de resolução de nome/endereço que o sistema recebe de fontes autoritativas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por configurar recursos implantados por ele para solicitar e executar autenticação de origem de dados e verificação de integridade de dados em respostas de resolução de nome/endereço recebidas das fontes autoritativas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-22"></a>Controle NIST 800-53 SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Arquitetura e o provisionamento para serviço de resolução de nome/endereço

**SC-22** Os sistemas de informação que fornecem coletivamente o serviço de resolução de nome/endereço para uma organização são tolerantes a falhas e implementam separação de função interna/externa.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por garantir que os sistemas fornecendo serviços de resolução de endereço para recursos implantados por ele sejam tolerantes a falhas e implementem a separação de função interna/externa. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-23"></a>Controle NIST 800-53 SC-23

#### <a name="session-authenticity"></a>Autenticidade de sessão

**SC-23** O sistema de informações protege a autenticidade das sessões de comunicação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso remoto aos recursos implantados por esta Especificação Técnica do Azure, incluindo o portal do Azure, a conexão de área de trabalho remota e o gateway de aplicativo Web, é protegido com o uso de TLS. O TLS fornece autenticidade para comunicações no nível da sessão. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-23-1"></a>Controle NIST 800-53 SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Autenticidade de sessão | Invalidar identificadores de sessão no logout

**SC-23 (1)** O sistema de informações invalida identificadores de sessão no logout do usuário ou outra terminação de sessão.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso remoto aos recursos implantados por esta Especificação Técnica do Azure, incluindo o portal do Azure, a conexão de área de trabalho remota e o gateway de aplicativo Web, é protegido com o uso de TLS. O portal do Azure e sessões de área de trabalho remota invalidam identificadores de sessão no logout. A invalidação de sessão Web é imposta por meio do Gateway de aplicativo do Azure - regras de Firewall de aplicativo Web (WAF). O WAF aplica afinidade por cookies por sessão e executa o tempo limite da sessão após 30 minutos (configurável após a implantação para regras específicas da organização) de inatividade do cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-24"></a>Controle NIST 800-53 SC-24

#### <a name="fail-in-known-state"></a>Falha no estado conhecido

**SC-24** O sistema de informações falha em um [Atribuição: estado conhecido definido pela organização] para [Atribuição: tipos de falhas de definidos pela organização] preservando [Atribuição: informações de estado do sistema definidas pela organização] em falha.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por garantir a falha de recursos implantados por ele em um estado conhecido. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-28"></a>Controle NIST 800-53 SC-28

#### <a name="protection-of-information-at-rest"></a>Proteção de informações em repouso

**SC-28** O sistema de informações protege o [Seleção (um ou mais): confidencialidade; integridade] de [Atribuição: informações em repouso definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A implementação de SI-28 (1) satisfaz esse requisito de controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-sc-28-1"></a>Controle NIST 800-53 SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Proteção de informações de em repouso | Proteção de criptografia

**SC-28 (1)** O sistema de informações implementa mecanismos de criptografia para evitar a divulgação e a modificação não autorizadas de [Atribuição: informações definidas pela organização] em [Atribuição: componentes do sistema de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implantadas por essa Especificação Técnica do Azure implementam a criptografia de disco para proteger a confidencialidade e a integridade das informações em repouso. A criptografia de disco do Azure para Windows é implementada usando o recurso BitLocker do Windows. O SQL Server está configurado para usar dados TDE (Transparent Data Encryption), que executa criptografia e descriptografia de dados e arquivos de log em tempo real para proteger informações em repouso. O TDE fornece a garantia de que os dados armazenados não tenham ficado sujeitos a acesso não autorizado. Clientes podem optar por implementar controles de nível de aplicativo adicionais para proteger a integridade de informações armazenadas. A confidencialidade e a integridade de todos os blobs de armazenamento implantados por essa Especificação Técnica do Azure estão protegidas por meio da SSE (Criptografia do Serviço de Armazenamento) do Azure. A SSE protege dados em repouso em contas de armazenamento do Azure usando criptografia AES de 256 bits. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-sc-39"></a>Controle NIST 800-53 SC-39

#### <a name="process-isolation"></a>Isolamento do processo

**SC-39** O sistema de informações mantém um domínio de execução separado para cada processo em execução.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows mantém domínios de execução separados para cada processo em execução designando um espaço de endereço virtual privado para cada processo. |
| **Provedor (Microsoft Azure)** | Não aplicável |
