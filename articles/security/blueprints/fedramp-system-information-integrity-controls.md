---
title: "Automação da Especificação Técnica do Azure no FedRAMP - Integridade do sistema e das informações"
description: "Aplicativos Web para FedRAMP - Integridade do sistema e das informações"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>Integridade do sistema e das informações (SI)

> [!NOTE]
> Estes controles são definidos pelo NIST e pelo Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

## <a name="nist-800-53-control-si-1"></a>Controle NIST 800-53 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Política e procedimentos de integridade do sistema e das informações

**SA-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de integridade do sistema e das informações que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de integridade do sistema e das informações e os controles associados de integridade do sistema e das informações; e revisa e atualiza a política de integridade do sistema e das informações atual [Atribuição: frequência definida pela organização]; e os procedimentos de integridade do sistema e das informações [Atribuição: frequência definida pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de integridade do sistema e das informações de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-2a"></a>Controle NIST 800-53 SI-2.a

#### <a name="flaw-remediation"></a>Correção de falhas

**SI-2.a** A organização identifica, relata e corrige as falhas do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta a solução de controle e automação de OMS para acompanhar o status das atualizações para as máquinas virtuais do Windows implantadas nesta arquitetura. No painel do OMS, o bloco de gerenciamento de atualizações exibe o status de correção de falhas para todos os servidores Windows implantados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-2b"></a>Controle NIST 800-53 SI-2.b

#### <a name="flaw-remediation"></a>Correção de falhas

**SI-2.b** A organização testa atualizações de software e firmware relacionadas à correção de falhas para eficácia e possíveis efeitos colaterais antes da instalação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por testar atualizações relacionadas à correção de falhas de eficácia e possíveis efeitos colaterais antes da instalação em recursos implantados pelo cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-2c"></a>Controle NIST 800-53 SI-2.c

#### <a name="flaw-remediation"></a>Correção de falhas

**SI-2.c** A organização instala atualizações de software e firmware relevantes à segurança dentro de [Atribuição: período de tempo definido pela organização] da versão das atualizações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais do Windows implantadas por essa Especificação Técnica do Azure são configuradas por padrão para receberem atualizações automáticas do serviço Windows Update. Essa solução também implanta a solução de automação e controle do OMS por meio da qual implantações de atualizações podem ser criadas para implantar patches para servidores Windows quando necessário. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-2d"></a>Controle NIST 800-53 SI-2.d

#### <a name="flaw-remediation"></a>Correção de falhas

**SI-2.d** A organização incorpora a correção de falhas no processo de gerenciamento de configuração organizacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por incluir a correção de falhas no gerenciamento de configuração. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-2-1"></a>Controle NIST 800-53 SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Correção de falhas | Gerenciamento central

**SI-2 (1)** A organização gerencia centralmente o processo de correção de falhas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta a solução de controle e automação de OMS para acompanhar o status das atualizações para as máquinas virtuais do Windows implantadas nesta arquitetura. No painel do OMS, o bloco de gerenciamento de atualizações exibe o status de correção de falhas para todos os servidores Windows implantados. Implantações de atualizações podem ser criadas para implantar patches em servidores Windows quando necessário. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-2-2"></a>Controle NIST 800-53 SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Correção de falhas | Status da correção automática de falhas

**SI-2 (2)** A organização emprega mecanismos automatizados [Atribuição: frequência definida pela organização] para determinar o estado dos componentes do sistema de informações em relação à correção de falhas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta a solução de controle e automação de OMS para acompanhar o status das atualizações para as máquinas virtuais do Windows implantadas nesta arquitetura. Para cada computador gerenciado do Windows, uma verificação é executada duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status for alterado e, nesse caso, é iniciada uma verificação de conformidade. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-2-3a"></a>Controle NIST 800-53 SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correção de falhas | Tempo para corrigir falhas / Benchmarks para ações corretivas

**SI-2 (3).a** A organização mede o tempo entre a identificação de falhas e correção de falhas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por corrigir falhas em recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-2-3b"></a>Controle NIST 800-53 SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Correção de falhas | Tempo para corrigir falhas / Benchmarks para ações corretivas

**SI-2 (3).b** A organização estabelece [Atribuição: benchmarks definidos pela organização] para realizar ações corretivas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode se basear em benchmarks de nível corporativo para processos de correção de falhas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-3a"></a>Controle NIST 800-53 SI-3.a

#### <a name="malicious-code-protection"></a>Proteção contra código mal-intencionado

**SI-3.a** A organização emprega mecanismos de proteção contra código mal-intencionado em pontos de entrada e saída do sistema de informações para detectar e eliminar código mal-intencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-3b"></a>Controle NIST 800-53 SI-3.b

#### <a name="malicious-code-protection"></a>Proteção contra código mal-intencionado

**SI-3.b** A organização atualiza mecanismos de proteção contra código mal-intencionado sempre que novas versões estiverem disponíveis de acordo com a política e os procedimentos de gerenciamento de configuração organizacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. A extensão está configurada para atualizar automaticamente o mecanismo antimalware e assinaturas de proteção quando a versão ficar disponível. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-3c"></a>Controle NIST 800-53 SI-3.c

#### <a name="malicious-code-protection"></a>Proteção contra código mal-intencionado

**SI-3.c** A organização configura mecanismos de proteção contra código mal-intencionado para executar verificações periódicas do sistema de informações [Atribuição: frequência definida pela organização] e verificações em tempo real dos arquivos de fontes externas em [Seleção (um ou mais); ponto de extremidade; pontos de entrada/saída de rede] conforme os arquivos são baixados, abertos ou executados de acordo com a política de segurança organizacional; e [Seleção (um ou mais): bloquear código mal-intencionado; colocar em código mal-intencionado em quarentena; enviar alerta para o administrador; [Atribuição: ação definida pelo organização]] em resposta à detecção de códigos mal-intencionados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. Essa extensão está configurada para executar verificações em tempo real e periódicas (semanalmente), atualizar automaticamente o mecanismo de malware e as assinaturas de proteção e executar ações de correção automáticas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-3d"></a>Controle NIST 800-53 SI-3.d

#### <a name="malicious-code-protection"></a>Proteção contra código mal-intencionado

**SI-3.d** A organização endereça o recebimento de falsos positivos durante a detecção e a eliminação de código mal-intencionado e o possível impacto resultante na disponibilidade do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela proteção contra código mal-intencionado. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-3-1"></a>Controle NIST 800-53 SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Proteção contra código mal-intencionado | Gerenciamento central

**SI-3 (1)** A organização gerencia centralmente os mecanismos de proteção contra código mal-intencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. O Azure OMS fornece uma funcionalidade centralizada para revisar o status atual da solução antimalware. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-3-2"></a>Controle NIST 800-53 SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Proteção contra código mal-intencionado | Atualizações automáticas

**SI-3 (2)** O sistema de informações atualiza automaticamente mecanismos de proteção contra código mal-intencionado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. A extensão está configurada para atualizar automaticamente o mecanismo antimalware e assinaturas de proteção quando a versão ficar disponível. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-3-7"></a>Controle NIST 800-53 SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Proteção contra código mal-intencionado | Detecção baseada em não assinatura

**SI-3 (7)** O sistema de informações implementa mecanismos de detecção de código mal-intencionado com base em não assinatura.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta proteções antimalware baseadas em host em todas as máquinas virtuais do Windows implementadas usando a extensão de máquina virtual Microsoft Antimalware. Essa extensão está configurada para executar a detecção heurística. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4a"></a>Controle NIST 800-53 SI-4.a

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.a** A organização monitora o sistema de informações para detectar ataques e indicadores de possíveis ataques de acordo com [Atribuição: objetivos de monitoração definidos pela organização]; e conexões locais, de rede e remotas não autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta o Log Analytics e a solução de segurança e auditoria do OMS. Essa solução fornece uma visão abrangente de postura de segurança, ataques e indicadores de possíveis ataques. O painel de Segurança e auditoria fornece insight de alto nível do estado de segurança de recursos implantados usando os dados disponíveis em soluções do OMS implantadas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4b"></a>Controle NIST 800-53 SI-4.b

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.b** A organização identifica o uso não autorizado de sistema de informações por meio de [Atribuição: técnicas e métodos definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta a solução de segurança e auditoria do OMS. O domínio de identidade e acesso fornece um painel com uma visão geral do estado de identidade do sistema de informações, incluindo o número de tentativas com falha de logon e o número atual de contas que estão conectadas. As informações disponíveis neste painel podem ajudá-lo na identificação de uma possível atividade suspeita. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4c"></a>Controle NIST 800-53 SI-4.c

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.c** A organização implanta dispositivos de monitoramento estrategicamente dentro do sistema de informações para coletar informações essenciais determinadas pela organização; e nos locais ad hoc dentro do sistema para rastrear tipos específicos de transações de interesse para a organização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta o Log Analytics e a solução de segurança e auditoria do OMS. O painel de Segurança e auditoria fornece insight de alto nível do estado de segurança de recursos implantados usando os dados disponíveis em soluções do OMS implantadas, incluindo insight sobre dados de monitoramento do sistema operacional da VM. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4d"></a>Controle NIST 800-53 SI-4.d

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.d** A organização protege as informações obtidas de ferramentas de monitoramento de invasão de acesso, modificação e exclusão não autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Controles de acesso lógicos são usados para proteger informações de monitoramento dentro da Especificação Técnica do Azure contra acesso, modificação e exclusão não autorizados. O Azure Active Directory impõe o acesso aprovado lógico usando associações de grupo baseadas em função. A capacidade de exibir informações de monitoramento e usar as ferramentas de monitoramento pode ser limitada aos usuários que requerem essas permissões. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4e"></a>Controle NIST 800-53 SI-4.e

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.e** A organização aumenta o nível de atividade de monitoramento do sistema de informações sempre que houver uma indicação de maior risco para operações e ativos organizacionais, indivíduos, outras organizações ou o país com base em informações de imposição da lei, informações de inteligência ou outras fontes confiáveis de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4f"></a>Controle NIST 800-53 SI-4.f

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.f** A organização obtém opinião legal com relação a atividades de monitoramento do sistema de informações de acordo com as leis federais, pedidos executivos, diretivas, políticas ou regulamentações aplicáveis.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-4g"></a>Controle NIST 800-53 SI-4.g

#### <a name="information-system-monitoring"></a>Monitoramento do sistema de informações

**SI-4.g** A organização fornece [Atribuição: informações de monitoramento do sistema de informações definidas pela organização] para [Atribuição: equipe ou funções definidas pela organização] [Seleção (um ou mais): conforme necessário; [Atribuição: frequência definida pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-1"></a>Controle NIST 800-53 SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitoramento do sistema de informações | Sistema de detecção de intrusão em todo o sistema

**SI-4 (1)** A organização conecta e configura as ferramentas de detecção de intrusões individuais em um sistema de detecção de intrusão em todo o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-2"></a>Controle NIST 800-53 SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitoramento do sistema de informações | Ferramentas automatizadas para análise em tempo real

**SI-4 (2)** A organização emprega ferramentas automatizadas para dar suporte a análise de eventos quase em tempo real.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta o Log Analytics e várias soluções de OMS, incluindo a solução de segurança e auditoria do OMS. O Log Analytics fornece análise de eventos quase em tempo real em todos os recursos implantados. As soluções de OMS fornecem uma visão abrangente da postura de segurança em domínios de soluções. O OMS fornece insight do estado de segurança de recursos implantados usando os dados disponíveis em soluções do OMS implantadas. OMS pode ser configurado para gerar alertas com base em critérios definidos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-4"></a>Controle NIST 800-53 SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitoramento do sistema de informações | Tráfego de comunicações de entrada e saída

**SI-4 (4)** O sistema de informações monitora o tráfego de comunicações de entrada e saída [Atribuição: frequência definida pela organização] para atividades ou condições incomuns ou não autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-5"></a>Controle NIST 800-53 SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Monitoramento do sistema de informações | Alertas gerados pelo sistema

**SI-4 (5)** Os alertas do sistema de informações [Atribuição: equipe ou funções definidas pela organização] quando ocorrem as indicações de comprometimento ou possível comprometimento a seguir: [Atribuição: indicadores de comprometimento definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta várias soluções de OMS, incluindo a solução de segurança e auditoria do OMS. O Log Analytics fornece análise de eventos quase em tempo real em todos os recursos implantados. As soluções de OMS fornecem uma visão abrangente da postura de segurança em domínios de soluções. OMS pode ser configurado para gerar alertas com base em critérios definidos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-11"></a>Controle NIST 800-53 SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitoramento do sistema de informações | Analisar anomalias no tráfego de comunicações

**SI-4 (11)** A organização analisa o tráfego de comunicações de saída no limite externo do sistema de informações de organização e selecionou [Atribuição: pontos interiores definidos pela organização dentro do sistema (por exemplo, sub-redes, subsistemas)] para descobrir anomalias.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar anomalias no tráfego de comunicações para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-14"></a>Controle NIST 800-53 SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitoramento do sistema de informações | Detecção de intrusão sem fio

**SI-4 (14)** A organização emprega um sistema de detecção de intrusão sem fio para identificar dispositivos sem fio não autorizados e para detectar tentativas de ataque e possíveis comprometimentos/violações no sistema de informações.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Nenhum hardware controlado pelo cliente, incluindo dispositivos sem fio, é permitido em datacenters do Azure. |
| **Provedor (Microsoft Azure)** | O Microsoft Azure monitora regularmente para sinais sem fio não autorizados trimestralmente conforme discutido em AC-18. <br /> O Microsoft Azure implementa esse controle em nome dos clientes de PaaS e IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>Controle NIST 800-53 SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitoramento do sistema de informações | Correlacionar as informações de monitoramento

**SI-4 (16)** A organização correlaciona informações de ferramentas de monitoramento utilizadas em todo o sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure implanta o Log Analytics e várias soluções de OMS, incluindo a solução de segurança e auditoria do OMS. O OMS fornece insight do estado de segurança de recursos implantados usando os dados disponíveis em soluções do OMS implantadas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-18"></a>Controle NIST 800-53 SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitoramento do sistema de informações | Analisar o tráfego/vazamento oculto

**SI-4 (18)** A organização analisa o tráfego de comunicações de saída no limite externo do sistema de informações de organização (por exemplo, perímetro do sistema) e em [Atribuição: pontos interiores definidos pela organização dentro do sistema (por exemplo, subsistemas, sub-redes)] para detectar vazamento oculto de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar o tráfego de comunicações para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-19"></a>Controle NIST 800-53 SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitoramento do sistema de informações | Indivíduos que apresentam um risco maior

**SI-4 (19)** A organização implementa [Atribuição: monitoramento adicional definido pela organização] de indivíduos que foram identificados por [Atribuição: fontes definidas pela organização] como apresentando um maior nível de risco.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar os indivíduos que representam um risco maior. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-20"></a>Controle NIST 800-53 SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Monitoramento do sistema de informações | Usuários privilegiados

**SI-4 (20)** A organização implementa [Atribuição: monitoramento adicional definido pela organização] de usuários privilegiados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por monitorar usuários privilegiados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-22"></a>Controle NIST 800-53 SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitoramento do sistema de informações | Serviços de rede não autorizados

**SI-4 (22)** O sistema de informações detecta serviços de rede que não foram autorizados ou aprovados por [Atribuição: processos de autorização ou aprovação definidos pela organização] e [Seleção (um ou mais): auditorias; alertas [Atribuição: equipe ou funções definidas pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por detectar serviços de rede não autorizados. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-23"></a>Controle NIST 800-53 SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Monitoramento do sistema de informações | Dispositivos baseados em host

**SI-4 (23)** A organização implementa [Atribuição: mecanismos de monitoramento baseados em host definidos pela organização] em [Atribuição: componentes do sistema de informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta Especificação Técnica do Azure coleta dados de monitoramento de recursos implantados, incluindo dados de recursos de monitoramento baseados em host. O Microsoft Monitoring Agent é instalado em todas as máquinas virtuais do Windows para coletar dados de monitoramento usados pelo Log Analytics e outras soluções de OMS. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-4-24"></a>Controle NIST 800-53 SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitoramento do sistema de informações | Indicadores de comprometimento

**SI-4 (24)** O sistema de informações descobre, coleta, distribui e usa indicadores de comprometimento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por descobrir, coletar, distribuir e usar indicadores de comprometimento para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-5a"></a>Controle NIST 800-53 SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>Alertas, avisos e diretivas de segurança

**SI-5.a** A organização recebe alertas, avisos e diretivas de segurança do sistema de informações [Atribuição: organizações externas definidas pela organização] continuamente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar alertas, avisos e diretivas de segurança para recursos implantados por ele (para incluir aplicativos, sistemas operacionais, bancos de dados e software). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-5b"></a>Controle NIST 800-53 SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>Alertas, avisos e diretivas de segurança

**SI-5.b** A organização gera alertas, avisos e diretivas de segurança interna conforme necessário.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar alertas, avisos e diretivas de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-5c"></a>Controle NIST 800-53 SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>Alertas, avisos e diretivas de segurança

**SI-5.c** A organização dissemina alertas, avisos e diretivas de segurança para: [Seleção (um ou mais): [Atribuição: equipe ou funções definidas pela organização]; [Atribuição: elementos definidos pela organização dentro da organização]; [Atribuição: organizações externas definidas pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar alertas, avisos e diretivas de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-5d"></a>Controle NIST 800-53 SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>Alertas, avisos e diretivas de segurança

**SI-5.d** A organização implementa as diretivas de segurança de acordo com períodos de tempo estabelecidos ou notifica a organização emissora do grau de não conformidade.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar alertas, avisos e diretivas de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-5-1"></a>Controle NIST 800-53 SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alertas, avisos e diretivas de segurança | Alertas e avisos automatizados

**SI-5 (1)** A organização emprega mecanismos automatizados para tornar informações de alerta e aviso de segurança disponíveis em toda a organização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar alertas, avisos e diretivas de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-6a"></a>Controle NIST 800-53 SI-6.a

#### <a name="security-function-verification"></a>Verificação de função de segurança

**SI-6.a** O sistema de informações verifica a operação correta de [Atribuição: funções de segurança definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para recursos implantados por ele (para incluir aplicativos, sistemas operacionais, bancos de dados e software). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-6b"></a>Controle NIST 800-53 SI-6.b

#### <a name="security-function-verification"></a>Verificação de função de segurança

**SI-6.b** O sistema de informações executa essa verificação [Seleção (um ou mais): [Atribuição: estados de transição de sistema definidos pela organização]; sob comando de usuário com privilégios apropriados; [Atribuição: frequência definida pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-6c"></a>Controle NIST 800-53 SI-6.c

#### <a name="security-function-verification"></a>Verificação de função de segurança

**SI-6.c** O sistema de informações notifica [Atribuição: equipe ou funções definidas pela organização] de testes de verificação de segurança com falha.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-6d"></a>Controle NIST 800-53 SI-6.d

#### <a name="security-function-verification"></a>Verificação de função de segurança

**SI-6.d** O sistema de informações [Seleção (um ou mais): desliga o sistema de informações; reinicia o sistema de informações; [Atribuição: ações alternativas definidas pela organização]] quando as anomalias são descobertas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-7"></a>Controle NIST 800-53 SI-7

#### <a name="software-firmware-and-information-integrity"></a>Integridade de software, firmware e informações

**SI-7** A organização emprega ferramentas de verificação de integridade para detectar alterações não autorizadas a [Atribuição: software, firmware e informações definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows fornece validação de integridade de arquivo em tempo real, proteção e recuperação de arquivos do sistema de núcleo que são instalados como parte do Windows ou de atualizações do sistema Windows autorizadas pelo recurso WRP (Proteção de Recursos do Windows). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-7-1"></a>Controle NIST 800-53 SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Integridade de software, firmware e informações | Verificações de integridade

**SI-7 (1)** O sistema de informações executa uma verificação de integridade de [Atribuição: software, firmware e informações definidos pela organização] [Seleção (um ou mais): na inicialização; em [Atribuição: estados de transição ou eventos relevantes para a segurança definidos pela organização]; [Atribuição: frequência definida pela organização]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows fornece validação de integridade de arquivo em tempo real, proteção e recuperação de arquivos do sistema de núcleo que são instalados como parte do Windows ou de atualizações do sistema Windows autorizadas pelo recurso WRP (Proteção de Recursos do Windows). WRP permite a verificação de integridade em tempo real. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-7-2"></a>Controle NIST 800-53 SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Integridade de software, firmware e informações | Notificações automatizadas de violações de integridade

**SI-7 (2)** A organização emprega ferramentas automatizadas que fornecem notificação para [Atribuição: equipe ou funções definidas pela organização] após a descoberta de discrepâncias durante a verificação de integridade.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows fornece validação de integridade de arquivo em tempo real, proteção e recuperação de arquivos do sistema de núcleo que são instalados como parte do Windows ou de atualizações do sistema Windows autorizadas pelo recurso WRP (Proteção de Recursos do Windows).  |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-7-5"></a>Controle NIST 800-53 SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Integridade de software, firmware e informações | Resposta automatizada de violações de integridade

**SI-7 (5)** O sistema de informações automaticamente [Seleção (um ou mais): desliga o sistema de informações; reinicia o sistema de informações; implementa [Atribuição: garantias de segurança definidas pela organização]] quando violações de integridade são descobertas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por responder automaticamente a violações de integridade em recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-7-7"></a>Controle NIST 800-53 SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Integridade de software, firmware e informações | Integração de detecção e resposta

**SI-7 (7)** A organização incorpora a detecção de [Atribuição: alterações relevantes de segurança definidas pela organização para o sistema de informações] não autorizadas para a capacidade de resposta a incidentes organizacionais.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger a integridade de software e informações para recursos implantados por ele. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-7-14"></a>Controle NIST 800-53 SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Integridade de software, firmware e informações | Código executável binário ou de máquina

**SI-7 (14)** A organização proíbe o uso de código executável binário ou de máquina de fontes com garantia limitada ou sem garantia e sem o fornecimento do código-fonte; e fornece exceções para o requisito de código-fonte somente para missão/requisitos operacionais atraentes e com a aprovação do representante com autorização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os procedimentos de integridade do sistema e de informações em nível corporativo do cliente podem estabelecer requisitos para obter o código-fonte do código executável binário ou de máquina de algumas fontes. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-8a"></a>Controle NIST 800-53 SI-8.a

#### <a name="spam-protection"></a>Proteção contra spam

**SI-8.a** A organização emprega mecanismos de proteção contra spam em pontos de entrada e saída do sistema de informações para detectar e tomar ação com relação a mensagens não solicitadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não existem servidores email implantados como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-8b"></a>Controle NIST 800-53 SI-8.b

#### <a name="spam-protection"></a>Proteção contra spam

**SI-8.b** A organização atualiza mecanismos de proteção contra spam sempre que novas versões estiverem disponíveis de acordo com a política e os procedimentos de gerenciamento de configuração organizacional.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não existem servidores email implantados como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-8-1"></a>Controle NIST 800-53 SI-8 (1)

#### <a name="spam-protection--central-management"></a>Proteção contra spam | Gerenciamento central

**SI-8 (1)** A organização gerencia centralmente os mecanismos de proteção contra spam.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não existem servidores email implantados como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-si-8-2"></a>Controle NIST 800-53 SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Proteção contra spam | Atualizações automáticas

**SI-8 (2)** O sistema de informações atualiza automaticamente mecanismos de proteção contra spam.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não existem servidores email implantados como parte dessa Especificação Técnica do Azure. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-10"></a>Controle NIST 800-53 SI-10

#### <a name="information-input-validation"></a>Validação de entrada de informações

**SI-10** O sistema de informações verifica a validade de [Atribuição: entradas de informações definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela validação de entrada de informações para recursos implantados por ele (para incluir aplicativos, sistemas operacionais, bancos de dados e software). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-11a"></a>Controle NIST 800-53 SI-11.a

#### <a name="error-handling"></a>Tratamento de erros

**SI-11.a** O sistema de informações gera mensagens de erro que fornecem as informações necessárias para ações corretivas sem revelar informações que podem ser exploradas por adversários.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os recursos implantados por esta Especificação Técnica do Azure empregam sistemas operacionais comerciais e aplicativos de software. Este software usa melhores práticas do setor para garantir que informações confidenciais não serão reveladas em mensagens de erro. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-11b"></a>Controle NIST 800-53 SI-11.b

#### <a name="error-handling"></a>Tratamento de erros

**SI-11.b** O sistema de informações revela as mensagens de erro somente para [Atribuição: equipe ou funções definidas pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Os recursos implantados por esta Especificação Técnica do Azure empregam sistemas operacionais comerciais e aplicativos de software. Este software usa melhores práticas para fornecer mensagens de erro que são apropriadas no contexto dos usos com recebimento de mensagem. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-12"></a>Controle NIST 800-53 SI-12

#### <a name="information-handling-and-retention"></a>Manipulação e retenção de informações

**SI-12** A organização manipula e retém informações do sistema de informações e saída de informações do sistema de acordo com leis federais, pedidos executivos, diretivas, políticas, regulamentações, padrões e requisitos operacionais aplicáveis.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por manipular e reter informações dentro de recursos implantados pelo cliente (para incluir aplicativos, sistemas operacionais, bancos de dados e software) e a saída de informações desses recursos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-si-16"></a>Controle NIST 800-53 SI-16

#### <a name="memory-protection"></a>Proteção de memória

**SI-16** O sistema de informações implementa [Atribuição: garantias de segurança definidas pela organização] para proteger sua memória de execução de código não autorizado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implantadas por essa Especificação Técnica do Azure executam sistemas operacionais Windows. O Windows tem proteções em vigor para impedir a execução de código em locais de memória restritos: NX (No Execute), ASLR (Address Space Layout Randomization) e DEP (Prevenção de Execução de Dados). |
| **Provedor (Microsoft Azure)** | Não aplicável |
