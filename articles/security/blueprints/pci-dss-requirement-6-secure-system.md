---
title: "Diagrama de processamento de pagamento do Azure – Requisitos de proteção do sistema"
description: Requisito 6 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Requisitos de proteção do sistema para ambientes em conformidade com o PCI DSS 
## <a name="pci-dss-requirement-6"></a>Requisito 6 de PCI DSS

**Desenvolver e manter os aplicativos e sistemas protegidos**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Indivíduos inescrupulosos usam vulnerabilidades de segurança para obter acesso privilegiado aos sistemas. Muitas dessas vulnerabilidades são corrigidas por patches de segurança distribuídos pelo fornecedor, que devem ser instalados pelas entidades que gerenciam os sistemas. Todos os sistemas devem ter todos os patches de software apropriados para a proteção contra o comprometimento dos dados do titular do cartão por indivíduos ou softwares mal-intencionados.

> [!NOTE]
> Patches de software apropriados são os patches que foram avaliados e testados o suficiente para determinar que eles não entram em conflito com as configurações de segurança existentes. Para aplicativos desenvolvidos internamente, várias vulnerabilidades podem ser evitadas pelo uso de processos de desenvolvimento de sistema padrão e técnicas de codificação seguras.

## <a name="pci-dss-requirement-61"></a>Requisito 6.1 de PCI DSS

**6.1** Estabelecer um processo para identificar vulnerabilidades de segurança, usando fontes confiáveis externas para obter informações de vulnerabilidade de segurança e atribuir uma classificação de risco (por exemplo, "alto", "médio," ou "baixo") às vulnerabilidades de segurança recém-descobertas.

> [!NOTE]
> As classificações de risco devem ser baseadas nas melhores práticas do setor e na ponderação do possível impacto. Por exemplo, os critérios de classificação de vulnerabilidades podem incluir a consideração da pontuação de base CVSS e/ou a classificação por fornecedor e/ou tipo de sistema afetado. 
> 
> Os métodos de avaliação de vulnerabilidades e atribuição de classificações de risco variam com base no ambiente e na estratégia de avaliação de risco de uma organização. As classificações de risco devem, no mínimo, identificar todas as vulnerabilidades consideradas de "alto risco" para o ambiente. Além da classificação de risco, as vulnerabilidades podem ser consideradas "críticas" se representam uma ameaça iminente ao ambiente, afetam sistemas críticos e/ou resultam em um possível comprometimento caso não sejam resolvidas. Exemplos de sistemas críticos podem incluir sistemas de segurança, sistemas e dispositivos voltados ao público, bancos de dados e outros sistemas que armazenam, processam ou transmitem os dados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Procedimentos foi estabelecidos e implementados para verificar vulnerabilidades em hosts de hipervisor no limite de escopo. A verificação de vulnerabilidade é executada em sistemas operacionais de servidor, bancos de dados e dispositivos de rede com ferramentas de verificação de vulnerabilidade apropriadas. As verificações de vulnerabilidade são executadas, no mínimo, a cada trimestre. Contratos do Microsoft Azure com avaliadores independentes para executar o teste de penetração dos limites do Microsoft Azure. Os exercícios de Red-Team também são executados rotineiramente e os resultados são usados para fazer aperfeiçoamentos de segurança. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF e o conjunto de regras OWASP habilitado. Para saber mais, confira [Diretriz de PCI - Mitigação do risco de vulnerabilidades de segurança](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>Requisito 6.2 de PCI DSS

**6.2** Verificar se todos os componentes de sistema e software estão protegidos contra vulnerabilidades conhecidas instalando os patches de segurança aplicáveis distribuídos pelo fornecedor. Instale os patches de segurança essenciais no prazo de um mês a contar do lançamento.

> [!NOTE]
> Os patches de segurança essenciais devem ser identificados de acordo com o processo de classificação de risco definido no Requisito 6.1.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure é responsável por fazer com que todos os dispositivos de rede e softwares do sistema operacional do hipervisor estejam protegidos de vulnerabilidades conhecidas instalando os patches de segurança aplicáveis distribuídos pelo fornecedor. A menos que um cliente solicite não usar o serviço, haverá um processo de gerenciamento de patches para evitar e corrigir as vulnerabilidades no nível do sistema operacional sem demora. Os servidores de produção são verificados para validação da conformidade com os patches mensalmente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore é uma solução de serviço PaaS. O Azure fornece a manutenção de todos os patches de serviço.|



## <a name="pci-dss-requirement-63"></a>Requisito 6.3 de PCI DSS

**6.3** Desenvolver aplicativos internos e externos (incluindo acesso administrativo baseado na Web aos aplicativos) com segurança, da seguinte maneira:
- De acordo com o PCI DSS (por exemplo, autenticação de segurança e registro em log)
- Com base em padrões e/ou melhores práticas do setor
- Incorporando a segurança das informações em todo o ciclo de vida de desenvolvimento de software 

> [!NOTE]
> Isso se aplica a todos os softwares desenvolvidos internamente e a softwares personalizados desenvolvidos por terceiros.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os pontos de extremidade e aplicativos do Microsoft Azure são desenvolvidos de acordo com a metodologia SDL (Microsoft Security Development Lifecycle), que está em conformidade com os requisitos de DSS. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore foi projetada para seguir as melhores práticas do setor para proteger CHD. As diretrizes de implantação fornecem detalhes sobre as medidas de segurança, e o registro em log está habilitado.|



### <a name="pci-dss-requirement-631"></a>Requisito 6.3.1 de PCI DSS

**6.3.1** Remover o desenvolvimento, o teste e/ou as contas de aplicativo personalizado, as IDs de usuário e as senhas antes que os aplicativos fiquem ativos ou sejam disponibilizados para os clientes.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Uma FSR (Análise Final de Segurança) é executada em versões principais, antes da implantação de produção, por um Supervisor de Segurança designado de fora da equipe de desenvolvimento do Azure para fazer com que apenas aplicativos prontos para produção sejam lançados. Como parte dessa revisão final, a remoção de todas as contas de teste e dos dados de teste é verificada. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um serviço de preparo que é registrado em log e isolado. Cada uma das camadas de rede tem um NSG [grupo de segurança de rede dedicado]. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>Requisito 6.3.2 de PCI DSS

**6.3.2** Revisar código personalizado antes do lançamento na produção ou para os clientes a fim de identificar possíveis vulnerabilidades de codificação (usando processos manuais ou automatizados) para incluir pelo menos o seguinte:
- As alterações de código são examinadas por indivíduos que não sejam o autor do código original e por indivíduos que tenham conhecimento sobre técnicas de revisão de código e práticas de codificação seguras.
- As revisões de código fazem com que o código seja desenvolvido de acordo com diretrizes de codificação seguras
- As correções apropriadas são implementadas antes do lançamento
- Os resultados da revisão de código são analisados e aprovados pela gerência antes do lançamento 

> [!NOTE]
> Esse requisito de revisões de código se aplica a todo código personalizado (internos e voltados ao público), como parte do ciclo de vida de desenvolvimento do sistema. 
>
> As revisões de código podem ser realizadas por pessoal interno experiente ou por terceiros. Os aplicativos Web voltados ao público também estão sujeitos a controles adicionais, para tratar ameaças e vulnerabilidades continuamente depois da implementação, conforme definido no Requisito 6.6 de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os pontos de extremidade e aplicativos do Microsoft Azure são desenvolvidos de acordo com a metodologia SDL (Microsoft Security Development Lifecycle). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um serviço de preparo que é registrado em log e isolado. Cada uma das camadas de rede tem um NSG [grupo de segurança de rede dedicado]. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>Requisito 6.4 de PCI DSS

**6.4** Seguir processos e procedimentos de controle de alterações para todas as alterações de componentes do sistema. Os processos devem incluir os itens a seguir (confira Requisitos 6.4.1 a 6.4.6).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | A Microsoft segue as diretrizes NIST sobre implicações de segurança no desenvolvimento de software, no sentido de que a segurança da informação deve estar integrada ao SDLC desde a criação do sistema. A integração contínua das práticas de segurança no Microsoft SDL permite:<ul><li>A identificação antecipada e redução de vulnerabilidades de segurança e configurações incorretas</li><li>O reconhecimento de possíveis desafios de codificação de software causados por controles de segurança obrigatórios</li><li>A identificação dos serviços de segurança compartilhados e a reutilização das ferramentas de melhores práticas de segurança que melhoram a postura de segurança por meio de métodos e técnicas comprovadas</li><li>Imposição do programa de gerenciamento de riscos já abrangente da Microsoft</li></ul>O Microsoft Azure definiu processos de gerenciamento de alterações e de versões para controlar a implementação de grandes alterações, incluindo:<ul><li>A identificação e a documentação da alteração planejada</li><li>A identificação de metas de negócios, prioridades e cenários durante o planejamento do produto</li><li>A especificação de design de componente/recurso</li><li>A revisão de prontidão operacional com base em critérios predefinidos/lista de verificação para avaliar o risco/impacto geral</li><li>O gerenciamento de alterações, autorização e teste com base em critérios de entrada/saída para os ambientes de DEV (desenvolvimento), INT (teste de integração), STAGE (pré-produção) e PROD (produção), conforme apropriado. Os clientes são responsáveis por seus próprios aplicativos hospedados no Microsoft Azure.</li></ul> |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A demonstração da Contoso Webstore fornece um serviço de preparo que é registrado em log e isolado. <br /><br />Cada uma das camadas de rede tem um NSG [grupo de segurança de rede dedicado]. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](payment-processing-blueprint.md#network-security-groups).<br /><br />As alterações são registradas usando o Operations Management Suite, e Runbooks são usados para coletar logs. A [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece um amplo registro em log das alterações. A precisão das alterações pode ser examinada e verificada. Para obter instruções mais específicas, consulte [Diretriz de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>Requisito 6.4.1 de PCI DSS

**6.4.1** Separar ambientes de desenvolvimento/teste dos ambientes de produção e impor a separação com controles de acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um serviço de preparo que é registrado em log e isolado. Cada uma das camadas de rede tem um NSG [grupo de segurança de rede dedicado]. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>Requisito 6.4.2 de PCI DSS

**6.4.2** Separação de funções entre ambientes de desenvolvimento/teste e produção

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um serviço de preparo que é registrado em log e isolado. Cada uma das camadas de rede tem um NSG [grupo de segurança de rede dedicado]. Para saber mais, confira [Diretriz de PCI – Grupos de Segurança de Rede](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>Requisito 6.4.3 de PCI DSS

**6.4.3** Os dados de produção (PANs dinâmicas) não são usados para teste ou desenvolvimento.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não tem nenhum dado de PAN (número de conta primária) dinâmica.|



### <a name="pci-dss-requirement-644"></a>Requisito 6.4.4 de PCI DSS

**6.4.4** Remoção de dados e contas de teste dos componentes do sistema antes que o sistema fique ativo ou entre em produção.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore não tem contas de teste.|



### <a name="pci-dss-requirement-645"></a>Requisito 6.4.5 de PCI DSS

**6.4.5** Alterar procedimentos de controle para a implementação de patches de segurança e as modificações de software para incluir o seguinte:
- **6.5.4.1** Documentação do impacto.
- **6.5.4.2** Aprovação da alteração documentada por pessoas autorizadas.
- **6.5.4.3** Testar a funcionalidade para verificar que a alteração não afeta negativamente a segurança do sistema.
- **6.5.4.4** Procedimentos de recuo.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore é uma solução de serviço PaaS. E o Azure fornece a manutenção de todos os patches de serviço.|



### <a name="pci-dss-requirement-646"></a>Requisito 6.4.6 de PCI DSS

**6.4.6** Na conclusão de uma alteração significativa, todos os requisitos de PCI DSS relevantes devem ser implementados em todos os sistemas e redes novos ou alterados, e a documentação deve ser atualizada conforme aplicável.

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore é uma solução de serviço PaaS. E o Azure fornece a manutenção de todos os patches de serviço.|



## <a name="pci-dss-requirement-65"></a>Requisito 6.5 de PCI DSS

**6.5** Lidar com vulnerabilidades de codificação comuns nos processos de desenvolvimento de software da seguinte maneira:
- Treinar os desenvolvedores pelo menos anualmente em técnicas de codificação seguras atualizadas, incluindo como evitar vulnerabilidades de codificação comuns.
- Desenvolver aplicativos com base em diretrizes de codificação seguras.

> [!NOTE]
> As vulnerabilidades listadas em 6.5.1 a 6.5.10 estavam atualizadas em relação às melhores práticas do setor quando esta versão do PCI DSS foi publicada. No entanto, conforme as melhores práticas para gerenciamento de vulnerabilidades forem atualizadas (por exemplo, o Guia OWASP, SANS CWE Top 25, Codificação segura CERT, etc.), as melhores práticas atualizadas deverão ser usadas para esses requisitos. 
> 
> [!NOTE]
> Os Requisitos 6.5.1 a 6.5.6, abaixo, se aplicam a todos os aplicativos (internos ou externos). Os Requisitos 6.5.7 a 6.5.10, abaixo, se aplicam a aplicativos Web e interfaces de aplicativo (internos ou externos). 

- **6.5.1** Falhas de injeção, particularmente injeção SQL. Leve em conta também a possibilidade de falhas de injeção de comando de SO, LDAP e XPath, bem como outras falhas de injeção.
- **6.5.2** Estouros de buffer
- **6.5.3** Armazenamento criptográfico não protegido
- **6.5.4** Comunicações não protegidas
- **6.5.5** Tratamento de erro incorreto
- **6.5.6** Todas as vulnerabilidades de "alto risco" identificadas no processo de identificação de vulnerabilidades (conforme definido no Requisito 6.1 de PCI DSS)
- **6.5.7** XSS (script entre sites)
- **6.5.8** Controle de acesso incorreto (como referências de objeto de direcionamento não protegidas, falha em restringir o acesso a URL, passagem de diretório e falha em restringir o acesso do usuário a funções)
- **6.5.9** CSRF (solicitação intersite forjada)
- **6.5.10** Gerenciamento de sessão e autenticação desfeito

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 6.4](#pci-dss-requirement-6-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A demonstração da Contoso Webstore fornece orientações para desenvolvimento seguro, um DFD e um modelo de ameaça para explicar as práticas de desenvolvimento seguro.|



## <a name="pci-dss-requirement-66"></a>Requisito 6.6 de PCI DSS

**6.6** Para aplicativos Web voltados ao público, tratar novas ameaças e vulnerabilidades de forma contínua e verificar se esses aplicativos estão protegidos contra ataques conhecidos com um dos seguintes métodos:

- Revisar aplicativos de web voltados ao público por meio de ferramentas ou métodos de avaliação de segurança e vulnerabilidade do aplicativo manuais ou automatizados, pelo menos uma vez ao ano e sempre em caso de alterações. 

   > [!NOTE]
   > Essa avaliação não é a mesma coisa que as verificações de vulnerabilidade executadas no Requisito 11.2. 

- Instalar uma solução técnica automatizada que detecta e impede ataques baseados na Web (por exemplo, um firewall de aplicativo Web) na frente de aplicativos Web voltados ao público, para verificar todo o tráfego continuamente.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure testa aplicativos Web voltados ao público como parte do processo de SDL antes que os aplicativos sejam implantados no ambiente de produção. Além disso, a Microsoft verifica todos os aplicativos Web voltados ao público em produção regularmente para detectar possíveis vulnerabilidades. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A solução de referência reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF e o conjunto de regras OWASP habilitado. Para saber mais, confira [Diretriz de PCI - Mitigação do risco de vulnerabilidades de segurança](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>Requisito 6.7 de PCI DSS

**6.7** Garantir que políticas de segurança e procedimentos operacionais para desenvolver e manter sistemas e aplicativos de segurança estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A solução de referência reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com WAF e o conjunto de regras OWASP habilitado. Para saber mais, confira [Diretriz de PCI - Mitigação do risco de vulnerabilidades de segurança](payment-processing-blueprint.md#application-gateway).|




