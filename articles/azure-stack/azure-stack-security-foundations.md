---
title: Entender os controles de segurança do Azure Stack | Microsoft Docs
description: Como um administrador de serviço Saiba mais sobre os controles de segurança aplicados para o Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.openlocfilehash: a3bd314a1df3c45c76b2e3a5acb31c1474d0fdf5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009473"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Postura de segurança de infraestrutura do Azure Stack

*Aplica-se a: sistemas integrados do Azure Stack*

Considerações de segurança e regulamentos de conformidade estão entre os drivers principais para usar nuvens híbridas. O Azure Stack foi projetado para esses cenários, e é importante entender os controles já em vigor ao adotar o Azure Stack.

Coexistam de duas camadas de postura de segurança no Azure Stack. A primeira camada é a infraestrutura do Azure Stack, que inclui os componentes de hardware até o Gerenciador de recursos do Azure. A primeira camada inclui o administrador e os portais de locatário. A segunda camada consiste em cargas de trabalho criado, implantado e gerenciado por locatários. A segunda camada inclui itens como máquinas virtuais e sites da web de serviços de aplicativos.

## <a name="security-approach"></a>Abordagem de segurança

A postura de segurança para o Azure Stack foi projetada para proteger contra ameaças modernas e foi criada para atender aos requisitos dos padrões de conformidade principais. Como resultado, a postura de segurança da infraestrutura do Azure Stack baseia-se em dois pilares:

 - **Pressuposta**  
A partir da suposição de que o sistema já tiver sido violado, concentrar *detectando e limitar o impacto de violações* versus apenas tentando impedir ataques. 
 - **Reforçados por padrão**  
Como a infraestrutura é executado no hardware bem definida e software, Azure Stack *permite, configura e valida todos os recursos de segurança* por padrão.

Porque o Azure Stack é entregue como um sistema integrado, a postura de segurança da infraestrutura do Azure Stack é definida pela Microsoft. Assim como no Azure, locatários são responsáveis por definir a postura de segurança de suas cargas de trabalho de locatário. Este documento fornece conhecimento básico sobre a postura de segurança da infraestrutura do Azure Stack.

## <a name="data-at-rest-encryption"></a>Dados com criptografia em repouso
Todos os dados de infraestrutura e locatário do Azure Stack são criptografados em repouso usando o Bitlocker. Essa criptografia protege contra a perda física ou o roubo de componentes de armazenamento do Azure Stack. 

## <a name="data-in-transit-encryption"></a>Dados em criptografia de trânsito
Os componentes da infraestrutura do Azure Stack comunicar-se usando canais criptografados com o TLS 1.2. Certificados de criptografia são automaticamente gerenciados pela infra-estrutura. 

Todos os pontos de extremidade externos de infraestrutura, como os pontos de extremidade REST ou o portal do Azure Stack, dão suporte a TLS 1.2 para comunicações seguras. Certificados de criptografia, a partir de sua autoridade de certificação, empresa ou de terceiros devem ser fornecidos para os pontos de extremidade. 

Embora os certificados autoassinados possam ser usados para esses pontos de extremidade externos, a Microsoft aconselha em relação a usá-los. 

## <a name="secret-management"></a>Gerenciamento de segredos
Infraestrutura do Azure Stack usa uma infinidade de segredos, como senhas, para funcionar. A maioria deles é girada automaticamente com frequência, porque eles são contas de serviço gerenciado de grupo, gire a cada 24 horas.

Os segredos restantes que não são contas de serviço gerenciado de grupo podem ser giradas manualmente com um script no ponto de extremidade com privilégios.

## <a name="code-integrity"></a>Integridade de código
O Azure Stack utiliza o o Windows Server 2016 mais recentes recursos de segurança. Um deles é o Windows Defender Device Guard, que fornece a lista de permissões de aplicativo e garante que apenas autorizados a executar o código dentro da infraestrutura do Azure Stack. 

Código não autorizado é assinado pela Microsoft ou o parceiro OEM, e ele está incluído na lista de permitidos software que é especificado em uma política definida pela Microsoft. Em outras palavras, apenas o software que foi aprovado para ser executado na infraestrutura do Azure Stack pode ser executado. Qualquer tentativa de executar código não autorizado está bloqueada e uma auditoria é gerada.

A política de Device Guard também impede que os agentes de terceiros ou de software em execução na infraestrutura do Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Outro recurso de segurança do Windows Server 2016 no Azure Stack é o Windows Defender Credential Guard, que é usado para proteger as credenciais de infraestrutura do Azure Stack de Pass-the-Hash e ataques Pass-the-Ticket.

## <a name="antimalware"></a>Antimalware
Todos os componentes no Azure Stack (hosts Hyper-V e máquinas virtuais) é protegido com o Windows Defender antivírus.

Em cenários conectados, atualizações de mecanismos e definições de antivírus são aplicadas várias vezes ao dia. Em cenários desconectados, atualizações de antimalware são aplicadas como parte das atualizações mensais do Azure Stack. Ver [atualizar o Windows Defender antivírus no Azure Stack](azure-stack-security-av.md) para obter mais informações.

## <a name="constrained-administration-model"></a>Modelo de administração restrito
Administração do Azure Stack é controlada com o uso de três pontos de entrada, cada um com uma finalidade específica: 
1. O [Portal do administrador](azure-stack-manage-portals.md) fornece uma experiência de apontar e clicar para as operações diárias de gerenciamento.
2. O Azure Resource Manager expõe todas as operações de gerenciamento do Portal do administrador por meio da API REST, usado pelo PowerShell e CLI do Azure. 
3. Para operações específicas de baixo nível, por exemplo data center integração ou dar suporte a cenários, Azure Stack expõe um ponto de extremidade do PowerShell chamado [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md). Esse ponto de extremidade expõe somente um conjunto na lista de permissões de cmdlets e ele intensamente é auditado.

## <a name="network-controls"></a>Controles de rede
Infraestrutura do Azure Stack é fornecido com várias camadas de rede List(ACL) de controle de acesso. As ACLs impedir acesso não autorizado para os componentes da infraestrutura e limitam a comunicação de infraestrutura para somente os caminhos que são necessários para seu funcionamento. 

As ACLs de rede são aplicadas em três camadas:
1.  Topo de Rack
2.  Rede definida pelo software
3.  Firewalls de sistema operacional do host e a VM

## <a name="next-steps"></a>Próximas etapas

- [Saiba como girar seus segredos no Azure Stack](azure-stack-rotate-secrets.md)
