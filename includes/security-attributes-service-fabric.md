---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513251"
---
## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim | O cliente é proprietário do cluster e do conjunto de dimensionamento de VMs (máquinas virtuais) em que o cluster se baseia. O Azure Disk Encryption pode ser habilitado no conjunto de dimensionamento de VMs. |
| Criptografia em trânsito:<ul><li>Criptografia ExpressRoute</li><li>Na criptografia de rede virtual</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim |  |
| Manipulação de chave de criptografia (CMK, BYOK, etc.)| Sim | O cliente é proprietário do cluster e do conjunto de dimensionamento de VMs (máquinas virtuais) em que o cluster se baseia. O Azure Disk Encryption pode ser habilitado no conjunto de dimensionamento de VMs. |
| Criptografia de nível de coluna (Serviços de Dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Status do ponto de extremidade de serviço| Sim |  |
| Suporte a injeção de VNET| Sim |  |
| Isolamento de rede/suporte a firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte para túnel forçado | Sim | A rede do Azure fornece túnel forçado. |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte a Monitoramento do Azure (Log Analytics, Application Insights, etc.)| Sim | Usando o suporte de terceiros e o suporte do Monitoramento do Azure. |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Gerenciamento de acesso – autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Gerenciamento de acesso – autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer uma dessas duas funções. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Registro em log e auditoria de plano de dados| N/D | O cliente é proprietário do cluster.  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | O controle de versão e a implantação da configuração de serviço são realizados pelo Azure Deploy. O controle de versão do código (aplicativo e tempo de execução) é realizado usando o Azure Build.
 |