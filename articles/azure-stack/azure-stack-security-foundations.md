---
title: "Entender os controles de segurança de pilha do Azure | Microsoft Docs"
description: "Como um administrador de serviço Saiba mais sobre os controles de segurança aplicados a pilha do Azure"
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 106fcf7b0edc095a52e82d58ad48a73084b65d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-infrastructure-security-posture"></a>Postura de segurança de infraestrutura de pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Considerações sobre segurança e regulamentações de conformidade estão entre os drivers principais para usar nuvens híbrida. A pilha do Azure foi projetada para esses cenários e é importante entender os controles já em vigor quando a adoção de pilha do Azure.

Na pilha do Azure, há duas camadas de postura de segurança que coexistem. A primeira camada compreende a infra-estrutura de pilha do Azure, que vai de componentes de hardware completamente até o Gerenciador de recursos do Azure e inclui o administrador e os portais de locatário. A segunda camada consiste em cargas de trabalho que criarem, implantar e gerenciarem locatários e inclui coisas como máquinas virtuais ou sites da web de serviços de aplicativos.  

## <a name="security-approach"></a>Abordagem de segurança
A pilha do Azure foi projetada com uma postura de segurança para proteger contra ameaças modernas e foi criada para atender aos requisitos de padrões de conformidade principais. Como resultado, a postura de segurança da infraestrutura do Azure pilha baseia-se em duas colunas:

 - **Suponha que a violação.** Iniciando na suposição de que o sistema já tiver sido violado, vamos nos concentrar em *detectando e limitar o impacto de violações de* em vez de apenas tentar evitar ataques. 
 - **Protegido por padrão.**  Como a infraestrutura é executado em hardware bem definido e software, nós *habilitar, configurar e validar os recursos de segurança* que normalmente são deixadas para os clientes para implementar.

Porque a pilha do Azure é entregue como um sistema integrado, a postura de segurança da infraestrutura do Azure pilha é definida pela Microsoft.  Assim como no Azure, locatários serão responsáveis por definir a postura de segurança de suas cargas de trabalho de locatário. Este documento fornece conhecimento básico sobre a postura de segurança da infraestrutura de pilha do Azure.

## <a name="data-at-rest-encryption"></a>Dados em criptografia de rest
Todos os dados de infraestrutura e locatário de pilha do Azure são criptografados em repouso usando o Bitlocker. Isso protege contra perda física ou roubo de componentes de armazenamento do Azure pilha. 

## <a name="data-in-transit-encryption"></a>Dados de criptografia de trânsito
Os componentes da infraestrutura do Azure pilha se comunicar usando canais criptografados com o protocolo TLS 1.2. Certificados de criptografia são gerenciados automaticamente pela infraestrutura. 

Todos os pontos de extremidade externos de infraestrutura, como os pontos de extremidade REST ou o portal de pilha do Azure, oferecem suporte a TLS 1.2 para proteger comunicações. Certificados de criptografia, a partir de terceiros ou a autoridade de certificação de empresa devem ser fornecidos para os pontos de extremidade. 

Enquanto os certificados autoassinados podem ser usados para esses pontos de extremidade externos, a Microsoft aconselha contra usá-los. 

## <a name="secret-management"></a>Gerenciamento de informações secretas
Infraestrutura de pilha do Azure usa uma variedade de segredos, como senhas, a função. A maioria deles é girada automaticamente com frequência, porque são contas de serviço gerenciado de grupo, gire a cada 24 horas.

Os segredos restantes que não são contas de serviço gerenciado de grupo podem ser giradas manualmente com um script no ponto de extremidade com privilégios.

## <a name="code-integrity"></a>Integridade de código
Pilha do Azure utiliza o Windows Server 2016 mais recentes recursos de segurança. Um deles é o Windows Defender Device Guard, que fornece a lista branca de aplicativos e garante que apenas autorizados a executar o código dentro da infraestrutura de pilha do Azure. 

Código não autorizado está assinado pela Microsoft ou o parceiro OEM e ele está incluído na lista de permitidos software que é especificado em uma política definida pela Microsoft. Em outras palavras, apenas o software que foi aprovado para execução na infraestrutura de pilha do Azure pode ser executado. Qualquer tentativa de executar código não autorizado são bloqueados e uma auditoria é gerada.

A política de proteção do dispositivo também impede que os agentes de terceiros ou software em execução na infraestrutura de pilha do Azure.

## <a name="credential-guard"></a>Proteção de credenciais
Outro recurso de segurança do Windows Server 2016 na pilha do Azure é o protetor de credencial do Windows Defender, que é usado para proteger credenciais de infraestrutura do Azure pilha contra ataques de passagem de tíquete e Pass-the-Hash.

## <a name="antimalware"></a>Antimalware
Todos os componentes na pilha do Azure (hosts Hyper-V e máquinas virtuais) é protegido com o Windows Defender antivírus.

## <a name="constrained-administration-model"></a>Modelo de administração restrita
Administração na pilha do Azure é controlada pelo uso de três pontos de entrada, cada um com uma finalidade específica: 
1. O [Portal do administrador](azure-stack-manage-portals.md) fornece uma experiência de apontar e clicar para as operações diárias de gerenciamento.
2. Gerenciador de recursos do Azure expõe todas as operações de gerenciamento do Portal do administrador por meio de uma API REST, usado pelo PowerShell e a CLI do Azure. 
3. Para operações específicas de baixo nível, por exemplo dados Centro de integração ou oferecer suporte a cenários, pilha do Azure expõe um ponto de extremidade do PowerShell chamado [privilegiada do ponto de extremidade](azure-stack-privileged-endpoint.md). Esse ponto de extremidade expõe apenas um conjunto de lista branca de cmdlets e ele é muito auditado.

## <a name="network-controls"></a>Controles de rede
Infraestrutura de pilha do Azure vem com várias camadas de rede List(ACL) de controle de acesso.  As ACLs impedir acesso não autorizado para os componentes da infraestrutura e limitam as comunicações de infraestrutura somente os caminhos que são necessários para seu funcionamento. 

As ACLs de rede são impostas em três camadas:
1.  Alterna a parte superior do Rack
2.  Rede definida pelo software
3.  Firewalls de sistema operacional do host e a VM 


