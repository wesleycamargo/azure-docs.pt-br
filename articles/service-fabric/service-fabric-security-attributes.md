---
title: Atributos comuns de segurança para o Azure Service Fabric
description: Uma lista de verificação de atributos de segurança comuns para avaliar o Microsoft Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679220"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Atributos comuns de segurança para o Azure Service Fabric

A segurança é integrada a todos os aspectos de um serviço do Azure. Este artigo documenta os atributos comuns de segurança integrados do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | O cliente possui o cluster e a escala de máquina virtual definido no qual o cluster é criado. Criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquina virtual. |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de VNET</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim |  |
| Manipulação de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente possui o cluster e a escala de máquina virtual definido no qual o cluster é criado. Criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquina virtual. |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte para ponto de extremidade de serviço| Sim |  |
| Suporte a injeção de VNET| Sim |  |
| Isolamento de rede/suporte a firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte para túnel forçado | Sim | A rede do Azure fornece túnel forçado. |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Usando o suporte de terceiros e suporte de monitoramento do Azure. |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso - Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso - Autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer uma dessas duas funções. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Registro em log e auditoria de plano de dados| N/D | O cliente é proprietário do cluster.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | O controle de versão e a implantação da configuração de serviço são realizados pelo Azure Deploy. O controle de versão do código (aplicativo e tempo de execução) é realizado usando o Azure Build.