---
title: Entender os controles de segurança do Azure Stack
description: Como um administrador de serviço Saiba mais sobre os controles de segurança aplicados para o Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: efa97c18a63954239475338c85f2145b8c6c6ac6
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767262"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Postura de segurança de infraestrutura do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Considerações de segurança e regulamentos de conformidade estão entre os drivers principais para usar nuvens híbridas. O Azure Stack é projetado para esses cenários. Este artigo explica os controles de segurança em vigor para o Azure Stack.

Coexistam de duas camadas de postura de segurança no Azure Stack. A primeira camada é a infraestrutura do Azure Stack, que inclui os componentes de hardware até o Gerenciador de recursos do Azure. A primeira camada inclui o administrador e os portais de locatário. A segunda camada consiste em cargas de trabalho criado, implantado e gerenciado por locatários. A segunda camada inclui itens como máquinas virtuais e sites da web de serviços de aplicativos.

## <a name="security-approach"></a>Abordagem de segurança

A postura de segurança para o Azure Stack foi projetada para proteger contra ameaças modernas e foi criada para atender aos requisitos dos padrões de conformidade principais. Como resultado, a postura de segurança da infraestrutura do Azure Stack baseia-se em dois pilares:

 - **Assume Breach**  
A partir da suposição de que o sistema já tiver sido violado, concentrar *detectando e limitar o impacto de violações* versus apenas tentando impedir ataques. 
 - **Reforçados por padrão**  
Como a infraestrutura é executado no hardware bem definida e software, Azure Stack *permite, configura e valida todos os recursos de segurança* por padrão.

Porque o Azure Stack é entregue como um sistema integrado, a postura de segurança da infraestrutura do Azure Stack é definida pela Microsoft. Assim como no Azure, locatários são responsáveis por definir a postura de segurança de suas cargas de trabalho de locatário. Este documento fornece conhecimento básico sobre a postura de segurança da infraestrutura do Azure Stack.

## <a name="data-at-rest-encryption"></a>Dados com criptografia em repouso
Todos os dados de infraestrutura e locatário do Azure Stack são criptografados em repouso usando o BitLocker. Essa criptografia protege contra a perda física ou o roubo de componentes de armazenamento do Azure Stack. Para obter mais informações, consulte [dados com criptografia em repouso no Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Dados em criptografia de trânsito
Os componentes da infraestrutura do Azure Stack comunicar-se usando canais criptografados com o TLS 1.2. Certificados de criptografia são automaticamente gerenciados pela infra-estrutura. 

Todos os pontos de extremidade externos de infraestrutura, como os pontos de extremidade REST ou o portal do Azure Stack, dão suporte a TLS 1.2 para comunicações seguras. Certificados de criptografia, a partir de sua autoridade de certificação, empresa ou de terceiros devem ser fornecidos para os pontos de extremidade. 

Embora os certificados autoassinados possam ser usados para esses pontos de extremidade externos, a Microsoft aconselha em relação a usá-los. 

## <a name="secret-management"></a>Gerenciamento de segredos
Infraestrutura do Azure Stack usa uma infinidade de segredos, como senhas, para funcionar. A maioria deles é girada automaticamente com frequência, porque eles são contas de serviço de Group-Managed que girar a cada 24 horas.

Os segredos restantes que não são contas de serviço Group-Managed podem ser giradas manualmente com um script no ponto de extremidade com privilégios.

## <a name="code-integrity"></a>Integridade de código
O Azure Stack utiliza o o Windows Server 2016 mais recentes recursos de segurança. Um deles é o Windows Defender Device Guard, que fornece a lista de permissões de aplicativo e garante que apenas autorizados a executar o código dentro da infraestrutura do Azure Stack. 

Código não autorizado é assinado pela Microsoft ou o parceiro OEM. O código não autorizado assinado está incluído na lista de permitidos software especificado em uma política definida pela Microsoft. Em outras palavras, apenas o software que foi aprovado para ser executado na infraestrutura do Azure Stack pode ser executado. Qualquer tentativa de executar código não autorizado está bloqueada e uma auditoria é gerada.

A política de Device Guard também impede que os agentes de terceiros ou de software em execução na infraestrutura do Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Outro recurso de segurança do Windows Server 2016 no Azure Stack é o Windows Defender Credential Guard, que é usado para proteger as credenciais de infraestrutura do Azure Stack de Pass-the-Hash e ataques Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware
Todos os componentes no Azure Stack (hosts Hyper-V e máquinas virtuais) é protegido com o Windows Defender antivírus.

Em cenários conectados, atualizações de mecanismos e definições de antivírus são aplicadas várias vezes ao dia. Em cenários desconectados, atualizações de antimalware são aplicadas como parte das atualizações mensais do Azure Stack. Para obter mais informações, consulte [atualizar o Windows Defender antivírus no Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Modelo de administração restrito
Administração do Azure Stack é controlada por meio de três pontos de entrada, cada um com uma finalidade específica: 
1. O [Portal do administrador](azure-stack-manage-portals.md) fornece uma experiência de apontar e clicar para as operações diárias de gerenciamento.
2. O Azure Resource Manager expõe todas as operações de gerenciamento do Portal do administrador por meio da API REST, usado pelo PowerShell e CLI do Azure. 
3. Para operações específicas de baixo nível, por exemplo data center integração ou dar suporte a cenários, Azure Stack expõe um ponto de extremidade do PowerShell chamado [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md). Esse ponto de extremidade expõe somente um conjunto na lista de permissões de cmdlets e ele intensamente é auditado.

## <a name="network-controls"></a>Controles de rede
Infraestrutura do Azure Stack é fornecido com várias camadas da lista de controle de acesso (ACL) de rede. As ACLs impedir acesso não autorizado para os componentes da infraestrutura e limitam a comunicação de infraestrutura para somente os caminhos que são necessários para seu funcionamento. 

As ACLs de rede são aplicadas em três camadas:
1.  Topo de Rack
2.  Rede definida pelo software
3.  Firewalls de sistema operacional do host e a VM

## <a name="regulatory-compliance"></a>Conformidade normativa

O Azure Stack passou por meio de uma avaliação formal por uma firma de auditoria independentes de terceiros. Como resultado, a documentação sobre como a infraestrutura do Azure Stack atende os controles aplicáveis de vários padrões de conformidade principais está disponível. A documentação não é uma certificação do Azure Stack devido aos padrões, incluindo vários controles relacionadas ao processo e pessoal. Em vez disso, os clientes podem usar esta documentação para iniciar rapidamente seu processo de certificação.

As avaliações incluem os seguintes padrões:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) aborda a indústria de cartões de pagamento.
- [Matriz de controle de nuvem CSA](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) é um mapeamento abrangente em vários padrões, incluindo FedRAMP moderado, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 e outros.
- [FedRAMP alto](https://www.fedramp.gov/fedramp-releases-high-baseline/) para clientes do governo.

A documentação de conformidade pode ser encontrada na [Portal de confiança do serviço de Microsoft](https://servicetrust.microsoft.com/ViewPage/Blueprint). Os guias de conformidade são um recurso protegido e exigem que você entre com suas credenciais de serviço de nuvem do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Saiba como girar seus segredos no Azure Stack](azure-stack-rotate-secrets.md)
- [PCI-DSS e documentos CSA-CCM para o Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [DoD e NIST documentos para o Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
