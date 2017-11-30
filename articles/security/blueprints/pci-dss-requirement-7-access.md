---
title: Diagrama de Processamento de Pagamento do Azure - Requisitos de Acesso
description: Requisito de PCI DSS 7
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Requisitos de acesso para ambientes em conformidade com PCI DSS 
## <a name="pci-dss-requirement-7"></a>Requisito de PCI DSS 7

**Restringir o acesso aos dados do titular do cartão segundo a necessidade de conhecimento**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Para garantir que dados críticos só possam ser acessados por funcionários autorizados, os sistemas e processos devem estar em uso, para limitar o acesso com base na necessidade de conhecimento e de acordo com as responsabilidades do trabalho.

A expressão “necessidade de conhecimento” refere-se aos direitos concedidos para acesso apenas ao mínimo de dados e privilégios necessário à execução de um trabalho.

## <a name="pci-dss-requirement-71"></a>Requisito de PCI DSS 7.1

**7.1** Limitar o acesso aos componentes do sistema e dados do titular do cartão apenas às pessoas cujo trabalho exija tal acesso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Azure impõe políticas de ISMS existentes relativas ao acesso pela equipe do Azure a componentes do sistema Azure, verificação de eficácia do controle de acesso, fornecimento de acesso administrativo Just-In-Time, revogação do acesso por parte da equipe quando não for mais necessário e garantia do acesso da equipe ao ambiente da plataforma Azure em caso de necessidade da empresa. O acesso do Azure a ambientes de clientes é altamente restrito e permitido somente com a aprovação do cliente.<br /><br />Foram estabelecidos procedimentos para restringir o acesso físico ao data center apenas a funcionários, fornecedores, prestadores de serviço e visitantes autorizados. A verificação de segurança e o check-in são exigidos para funcionários que precisem de acesso temporário às instalações internas do data center. Os logs de acesso físico são examinados cada trimestre pelas equipes do Azure. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por limitar o acesso a componentes do sistema e dados do titular do cartão apenas às pessoas cujo trabalho exija tal acesso. Isso inclui a limitação e a restrição do acesso ao Portal de Gerenciamento do Azure, bem como especificar contas ou funções com permissão para criar, modificar ou excluir serviços de PaaS.|



### <a name="pci-dss-requirement-711"></a>Requisito de PCI DSS 7.1.1

**7.1.1** Define as necessidades de acesso de cada função, inclusive:
- Componentes de sistema e recursos de dados que cada função precisa acessar para executar seu trabalho
- Nível de privilégio necessário (por exemplo, usuário, administrador etc.) para acessar recursos

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por definir e documentar processos de aprovação de ID de Usuário, definir o mínimo de privilégios, restringir o acesso a dados do titular do cartão, usar IDs exclusivas, fornecer separação de responsabilidades e revogar o acesso do usuário quando não for mais necessário.|



### <a name="pci-dss-requirement-712"></a>Requisito de PCI DSS 7.1.2

**7.1.2** Restringir o acesso a IDs de usuário com privilégios aos privilégios mínimos necessários para cumprir as responsabilidades do trabalho.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure adotou políticas de segurança corporativa e organizacional aplicáveis, inclusive uma Política de Segurança de Informações. As políticas foram aprovadas, publicadas e comunicadas ao Windows Azure. A Política de Segurança de Informações do Microsoft Azure exige que o acesso aos ativos do Microsoft Azure seja concedido sob justificativa de negócios, com a autorização do proprietário do ativo e com base em princípios de "necessidade de conhecimento" e "privilégios mínimos". A política também cumpre requisitos para o ciclo de vida de gerenciamento de acesso, inclusive provisionamento de acesso, autorização de acesso, remoção de autenticação de direitos de acesso e revisões periódicas de acesso. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). As funções de usuário limitam-se a tarefas com base no cenário de demonstração documentado.|



### <a name="pci-dss-requirement-713"></a>Requisito de PCI DSS 7.1.3

**7.1.3** Atribuir acesso com base na classificação de trabalho e na atuação individual dos funcionários.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cria três contas durante a implantação: admin, sqladmin e edna (o usuário padrão conectado ao aplicativo Web durante a execução de demonstração). As funções de usuário limitam-se a tarefas com base no cenário de demonstração documentado.|



### <a name="pci-dss-requirement-714"></a>Requisito de PCI DSS 7.1.4

**7.1.4** Exigem aprovação documentada por participantes autorizados, com especificação dos privilégios necessários.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | Os clientes são responsáveis por limitar o acesso a componentes do sistema e dados do titular do cartão apenas às pessoas cujo trabalho exija tal acesso. Isso inclui a limitação e a restrição do acesso ao Portal de Gerenciamento do Azure, bem como especificar contas ou funções com permissão para criar, modificar ou excluir serviços de PaaS.|



## <a name="pci-dss-requirement-72"></a>Requisito de PCI DSS 7.2

**7.2** Estabelecer um sistema de controle de acesso aos componentes de sistemas que restringe o acesso com base na necessidade de conhecimento de um usuário, e é definido como "negar tudo", a menos que haja uma permissão específica.
Este sistema de controle de acesso deve incluir:
- 7.2.1 Cobertura de todos os componentes do sistema.
- 7.2.2 Atribuição de privilégios às pessoas com base na classificação e na função do trabalho.
- 7.2.3 O padrão configuração é "negar tudo".

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa o Azure Active Directory para restringir o acesso apenas a usuários designados. Para saber mais, consulte [Diretriz de PCI - Gerenciamento de Identidades](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>Requisito do PCI DSS 7.3

**7.3** Garantir que políticas de segurança e procedimentos operacionais para restringir o acesso a dados do titular do cartão estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A documentação da Contoso Webstore fornece um caso de uso e uma descrição de quem usa CHD e de que forma isso ocorre.|




