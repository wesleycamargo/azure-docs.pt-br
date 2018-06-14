---
title: Plano gráfico de processamento de pagamento do Azure – Requisitos de senha
description: Requisito 2 de PCI DSS
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3ca83294702a138713f31e34b0e94b1ccb87be0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894153"
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Requisitos de senha para ambientes em conformidade com PCI DSS 
## <a name="pci-dss-requirement-2"></a>Requisito 2 de PCI DSS

**Não usar padrões de fornecedores para senhas do sistema e outros parâmetros de segurança**

> [!NOTE]
> Esses requisitos são definidos pelo [PCI (Payment Card Industry) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI DSS (Padrão de Segurança de Dados PCI), versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Os indivíduos mal-intencionados (externos e internos a uma entidade) geralmente usam as senhas padrão e outras configurações padrão dos fornecedores para comprometer sistemas. Essas configurações e senhas são bem conhecidas por comunidades de hackers e facilmente determinadas por meio de informações públicas.

## <a name="pci-dss-requirement-21"></a>Requisito 2.1 de PCI DSS
 
**2.1** Sempre altere os padrões dos fornecedores e remova ou desabilite as contas padrão desnecessárias **antes** de instalar um sistema na rede.
Isso se aplica a TODAS as senhas padrão, incluindo, mas não apenas, as usadas por sistemas operacionais, softwares que fornecem serviços de segurança, contas de aplicativo e do sistema, terminais de PDV (Ponto de Venda), cadeias de caracteres da comunidade do protocolo SNMP etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Os requisitos de política de senha do Microsoft Azure Active Directory são impostos para as novas senhas fornecidas pelos clientes dentro do portal do AADUX. As alterações de senha de autoatendimento iniciadas pelo cliente exigem a validação da senha anterior. As senhas de redefinição do administrador devem ser alteradas após o logon subsequente. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore requer que os usuários usem senhas fortes para todos os usuários. Não há nenhuma conta de convidado ou de exemplo habilitada na demonstração.<br /><br />Esta solução não tem acesso sem fio nem SNMP implantados.|



### <a name="pci-dss-requirement-211"></a>Requisito 2.1.1 de PCI DSS

**2.1.1** Para ambientes sem fio conectados ao ambiente de dados do titular do cartão ou que transmitam dados do titular do cartão, altere TODOS os padrões do fornecedor sem fio no ato de instalação, incluindo, mas não apenas, as chaves de criptografia sem fio padrão, as senhas e as cadeias de caracteres da comunidade SNMP.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Esta solução não tem acesso sem fio nem SNMP implantados.|



## <a name="pci-dss-requirement-22"></a>Requisito 2.2 de PCI DSS

**2.2** Desenvolva padrões de configuração para todos os componentes do sistema. Garanta que esses padrões tratem de todas as vulnerabilidades de segurança conhecidas e sejam consistentes com os padrões de proteção de sistema estabelecidos pelo setor.
As fontes dos padrões de proteção de sistema estabelecidas pela indústria podem incluir, mas não apenas:
- CIS (Center for Internet Security)
- ISO (Organização Internacional de Normalização)
- SANS (SysAdmin, Audit, Network, Security) Institute
- NIST (Instituto Nacional de Padrões e Tecnologia)

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Para o Microsoft Azure, a equipe de OSSC Technical Security Services desenvolve os padrões de configuração de segurança para os sistemas no ambiente do Microsoft Azure que estejam de acordo com os padrões de proteção estabelecidos pela indústria. Essas configurações são documentadas nas linhas de base do sistema e as alterações de configuração relevantes são comunicadas para as equipes afetadas (por exemplo, a equipe de IPAK). Os procedimentos são implementados para monitorar a conformidade com os padrões de configuração de segurança. Os padrões de configuração de segurança para os sistemas no ambiente do Microsoft Azure estão de acordo com os padrões de proteção estabelecidos pela indústria e são revistos pelo menos uma vez por ano. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore fornece a proteção de todos os serviços no escopo do ambiente de dados do titular do cartão (CDE). <br /><br />A Contoso Webstore também implanta a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece uma visão centralizada do estado de segurança de todos os recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.<br /><br />A Contoso Webstore utiliza a Operations Management Suite para registrar em log todas as alterações no sistema. O Log Analytics fornece registro em log extensivo de alterações. A precisão das alterações pode ser examinada e verificada. Para obter instruções mais específicas, consulte [Diretriz de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>Requisito 2.2.1 de PCI DSS

**2.2.1** Implemente apenas uma função primária por servidor para evitar que as funções que exigem diferentes níveis de segurança de parceria coexistam no mesmo servidor. (Por exemplo, servidores Web, servidores de banco de dados e DNS devem ser implantados em servidores separados.) 

> [!NOTE]
> Quando se usam as tecnologias de virtualização, implemente apenas uma função primária por componente de sistema virtual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Os serviços da Contoso Webstore são implantados como Serviços PaaS. Todos os serviços são isolados e segmentados usando a segmentação de rede.<br /><br />A Contoso Webstore também usa um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para impor as principais práticas recomendadas. Para obter mais informações, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>Requisito 2.2.2 de PCI DSS

**2.2.2** Habilite somente os serviços, protocolos, daemons necessários etc., conforme exigir a função do sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As configurações de hardware e software do Microsoft Azure são revistas pelo menos trimestralmente para identificar e eliminar quaisquer funções, portas, protocolos e serviços desnecessários. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Os serviços da Contoso Webstore são implantados como Serviços PaaS. Todos os serviços são isolados e segmentados usando a segmentação de rede.<br /><br />A Contoso Webstore também usa um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para impor as principais práticas recomendadas. Para obter mais informações, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>Requisito de PCI DSS 2.2.3

**2.2.3** Implemente recursos de segurança adicionais para todos os protocolos, serviços ou daemons necessários considerados inseguros. 

> [!NOTE]
> Quando se usa SSL/TLS hoje obsoleto, os requisitos no Apêndice A2 devem ser concluídos.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Os serviços da Contoso Webstore são implantados como Serviços PaaS. Todos os serviços são isolados e segmentados usando a segmentação de rede. A implantação também fornece a proteção de todos os serviços no escopo do CDE. <br /><br />A Contoso Webstore também implanta a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece uma visão centralizada do estado de segurança de todos os recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.<br /><br />A Contoso Webstore também usa um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para impor as principais práticas recomendadas. Para obter mais informações, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>Requisito 2.2.4 de PCI DSS

**2.2.4** Configure parâmetros de segurança do sistema para evitar o uso indevido.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Azure garante que somente as pessoas autorizadas sejam capazes de configurar os controles de segurança da plataforma do Windows Azure, usando os controles de acesso de vários fatores e uma necessidade comercial documentada. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore utiliza o AAD e RBAC do AD para gerenciar e garantir que os parâmetros de segurança sejam implantados corretamente. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>Requisito de PCI DSS 2.2.5

**2.2.5** Remova todos os recursos desnecessários, como scripts, drivers, recursos, subsistemas, sistemas de arquivos e servidores Web desnecessários.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore fornece documentação sobre como os limites são estabelecidos. O modelo de ameaça da Contoso e o diagrama de fluxo de dados ilustram todos os serviços usados e os controles habilitados.|



## <a name="pci-dss-requirement-23"></a>Requisito de PCI DSS 2.3

**2.3** Criptografe todo o acesso administrativo de não console usando a criptografia forte. 

> [!NOTE]
> Quando se usa SSL/TLS hoje obsoleto, os requisitos no Apêndice A2 devem ser concluídos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure garante que o uso da criptografia forte seja imposto durante o acesso à infraestrutura do hipervisor. O Microsoft Azure também garante que os clientes que usam o Portal de Gerenciamento do Microsoft Azure sejam capazes de acessar seus consoles de serviço/IaaS com a criptografia forte. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore mostra como as senhas fortes podem ser implementadas em uma solução. Além disso, pode-se executar todos os testes para verificar se a criptografia está implementada em toda a solução.<br /><br />A Contoso Webstore também usa um [Ambiente do serviço de aplicativo (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) para impor as principais práticas recomendadas. Para obter mais informações, consulte [Diretriz de PCI - Ambiente do Serviço de Aplicativo](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>Requisito de PCI DSS 2.4

**2.4** Mantenha um inventário dos componentes do sistema que estejam no escopo do PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Na documentação fornecida, pode-se conferir o inventário de solução PaaS de demonstração da Contoso Webstore. Para saber mais, confira [Diretriz de PCI - Soluções de Gerenciamento pré-instaladas](payment-processing-blueprint.md#management-solutions).|



## <a name="pci-dss-requirement-25"></a>Requisito de PCI DSS 2.5

**2.5** Certifique-se de que as políticas de segurança e os procedimentos operacionais para gerenciar os padrões do fornecedor e outros parâmetros de segurança estejam documentadas, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | A Contoso Webstore fornece a documentação que contém informações sobre parâmetros de segurança e elementos de serviço de documentos. |



## <a name="pci-dss-requirement-26"></a>Requisito de PCI DSS 2.6

**2.6** Os provedores de hospedagem compartilhada devem proteger os dados de ambiente hospedado e titular de cartão de cada entidade. Esses provedores devem atender a requisitos específicos, conforme detalhado no *Apêndice A: Requisitos adicionais de PCI DSS para provedores de hospedagem compartilhada.*

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. O Microsoft Azure não é um provedor de hospedagem compartilhada. |
| **Cliente<br />(Plano gráfico do&nbsp;PCI&#8209;DSS)** | Não aplicável. O Microsoft Azure não é um provedor de hospedagem compartilhada.|




