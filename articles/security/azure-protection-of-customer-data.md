---
title: Proteção de dados do cliente no Azure
description: Este artigo aborda como o Azure protege os dados do cliente.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 9a3b00e39f78f65b05b7d730447440d481979539
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101234"
---
# <a name="protection-of-customer-data-in-azure"></a>Proteção de dados do cliente no Azure   
O acesso aos dados de clientes pela equipe de operações e suporte da Microsoft é negado por padrão. Quando é concedido acesso aos dados do cliente, é necessária a aprovação de liderança e, em seguida, o acesso é cuidadosamente gerenciado e registrado. Os requisitos de controle de acesso são estabelecidos pela seguinte política de segurança do Microsoft Azure:

- Sem acesso aos dados do cliente por padrão
- Nenhuma conta de usuário ou administrador em VMs do cliente
- Conceda o privilégio mínimo necessário para concluir a tarefa; auditoria e log de solicitações de acesso

Equipe de suporte do Microsoft Azure é atribuída a contas exclusivas de corporativo do AD pela Microsoft. Microsoft Azure baseia-se no Microsoft Active Directory corporativo, gerenciado pelo MSIT, para controlar o acesso a sistemas de informações de chave. A autenticação multifator é necessária e acesso será concedido apenas dos consoles de seguros.

Todas as tentativas de acesso são monitoradas e podem ser exibidas por meio de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
Azure fornece aos clientes com segurança de dados de alta segurança – por padrão e como as opções de cliente.

**Segregação de dados** - Azure é um serviço multilocatário, o que significa que as múltiplas implantações de clientes e máquinas virtuais são armazenadas no mesmo hardware físico. Azure usa isolamento lógico para separar os dados de cada cliente dos dados de outros. Segregação fornece a escala e benefícios econômicos dos serviços multilocatários enquanto rigorosamente previne que os clientes acessem dados de uma da outra.

**Proteção de dados em repouso** -os clientes são responsáveis por garantir que dados armazenados no Azure são criptografados de acordo com seus padrões. O Azure oferece uma ampla variedade de recursos de criptografia, dando aos clientes a flexibilidade para escolher a solução que melhor atende às suas necessidades. O Azure Key Vault ajuda os clientes a manter com facilidade o controle das chaves usadas por aplicativos e serviços de nuvem para criptografar os dados. O Azure Disk Encryption permite criptografar as máquinas virtuais. A Criptografia do Serviço de Armazenamento do Microsoft Azure permite criptografar todos os dados colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito** -os clientes podem habilitar a criptografia para tráfego entre suas próprias VMs e os usuários finais. Azure protege os dados em trânsito para ou de componentes externos e dados em trânsito internamente, por exemplo, entre duas redes virtuais. Azure usa o padrão do setor Transport Layer Security (TLS) 1.2 ou acima de protocolo com chaves de criptografia RSA/SHA256 de 2048 bits, como recomendado pelo CESG/NCSC, para criptografar as comunicações entre:

- o cliente e a nuvem
- internamente entre sistemas do Azure e datacenters

**Criptografia** -criptografia de dados no armazenamento e em trânsito pode ser implantado por clientes como uma prática recomendada para garantir a confidencialidade e integridade dos dados. É fácil para os clientes a configurarem os serviços de nuvem do Azure para usar SSL para proteger as comunicações da Internet e até mesmo entre seus Azure hospedados VMs.

**Redundância de dados** -Microsoft garante que dados sejam protegidos, se houver um ataque cibernético ou danos físicos em um datacenter. Os clientes podem optar por:

- armazenamento no país para considerações de latência ou de conformidade
- armazenamento fora do país para fins de recuperação de desastre ou de segurança

Os dados podem ser replicados dentro de uma área geográfica selecionada para redundância, mas não serão transmitidos fora dele. Os clientes têm várias opções para replicação de dados, incluindo o número de cópias e o número e o local dos data centers de replicação.

Quando você cria a sua conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- Armazenamento com redundância local (LRS). O armazenamento com redundância local mantém três cópias dos seus dados. O LRS é replicado três vezes em uma única instalação, em uma única região. O LRS protege os dados contra falhas normais de hardware, mas não contra falha de uma única instalação.
- Armazenamento com redundância de zona (ZRS). O armazenamento com redundância de zona mantém três cópias dos seus dados. O ZRS é replicado três vezes por duas ou três instalações para fornecer maior durabilidade que o LRS. A replicação ocorre em uma única região ou em duas regiões. O ZRS assegura que seus dados irão durar em uma única região.
- Armazenamento com redundância geográfica (GRS). O armazenamento com redundância geográfica é habilitado para sua conta de armazenamento por padrão quando ela é criada. O GRS mantém seis cópias de seus dados. Com o GRS, seus dados são replicados três vezes dentro da região primária. Os dados também são replicados três vezes em uma região secundária a centenas de milhas de distância da região primária, oferecendo o nível mais alto de durabilidade. Em caso de falha na região primária, o Armazenamento do Azure realizará failover para a região secundária. O GRS assegura que seus dados serão duráveis em duas regiões separadas.

**Destruição de dados** - quando os clientes excluem dados ou deixe o Azure, a Microsoft segue os padrões estritos para substituir os recursos de armazenamento antes de reutilização, bem como a destruição física do hardware encerrado. Microsoft executa uma exclusão completa dos dados na solicitação de cliente e no término do contrato.

## <a name="customer-data-ownership"></a>Propriedade dos dados do cliente
Microsoft não inspeciona, aprova ou monitora os aplicativos que os clientes implantam no Azure. Além disso, a Microsoft não sabe que tipo de dados clientes escolhem armazenar no Azure. Microsoft não reivindica propriedade dos dados sobre as informações de cliente inseridas no Azure.

## <a name="records-management"></a>Gerenciamento de registros
Azure estabeleceu os requisitos de retenção de registros internos para dados de back-end. Os clientes são responsáveis por identificar seus próprios requisitos de retenção de registros. Para registros armazenados no Azure, o cliente é responsável por extrair seus dados e mantendo o conteúdo fora do Azure por um período de retenção especificado pelo cliente.

Azure fornece ao cliente a capacidade de exportar os dados e a auditoria de relatórios do produto. As exportações são salvas localmente para reter as informações para uma retenção definida pelo cliente de período de tempo.

## <a name="electronic-discovery-e-discovery"></a>Descoberta eletrônica (e-discovery)
Os clientes do Azure são responsáveis por manter a conformidade com os requisitos de descoberta eletrônica no uso de serviços do Azure. Se um cliente do Azure deve preservar os dados dos clientes, eles podem exportar e salvar os dados localmente. Além disso, os clientes podem solicitar exportações de seus dados do departamento de atendimento ao cliente do Azure. Além de permitir que os clientes exportar seus dados, o Azure realiza amplo registro em log e monitoramento internamente.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
