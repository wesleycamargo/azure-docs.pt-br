---
title: Replicação de dados no Armazenamento do Azure | Microsoft Docs
description: Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. Opções de replicação incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) RA-GRS (armazenamento com redundância geográfica com acesso de leitura).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/08/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 618e1f5249f2e05c26e91231b4283d82546a880b
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954480"
---
# <a name="azure-storage-replication"></a>Replicação de Armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A replicação de Armazenamento do Microsoft Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, que variam de falhas de hardware transitórias, interrupções de energia ou rede, desastres naturais de grandes proporções, e assim por diante. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região e até mesmo entre regiões.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

## <a name="choosing-a-replication-option"></a>Escolher uma opção de replicação

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de replicação:

* [Armazenamento com redundância local (LRS)](storage-redundancy-lrs.md)
* [Armazenamento com redundância de zona (ZRS)](storage-redundancy-zrs.md)
* [Armazenamento com redundância geográfica (GRS)](storage-redundancy-grs.md)
* [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

A tabela a seguir fornece uma visão geral rápida do escopo de durabilidade e disponibilidade que cada estratégia de replicação fornecerá a você para um determinado tipo de evento (ou evento de impacto semelhante).

| Cenário                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilidade de nó dentro de um datacenter                                                                 | SIM                             | sim                              | sim                                  | SIM                                  |
| Um data center inteiro (zonal ou não zonal) fica indisponível                                           | Não                               | sim                              | sim                                  | SIM                                  |
| Uma interrupção em toda a região                                                                                     | Não                               | Não                                | sim                                  | SIM                                  |
| Acesso de leitura aos dados (em uma região remota e replicada geograficamente) no caso de indisponibilidade em toda a região | Não                               | Não                               | Não                                    | SIM                                  |
| Projetado para fornecer \_\_ durabilidade de objetos em um determinado ano                                          | no mínimo 99,999999999% (11 9's) | no mínimo 99,9999999999% (12 9's) | no mínimo 99,99999999999999% (16 9's) | no mínimo 99,99999999999999% (16 9's) |
| Tipos de conta de armazenamento suportados                                                                   | GPv2, GPv1, Blob                | GPv2, GPv1 (por meio do PowerShell, CLI do Azure ou recurso de API do provedor)                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| SLA de disponibilidade para solicitações de leitura | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,99% (99,9% para a camada de acesso estática) |
| SLA de disponibilidade para solicitações de gravação | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) | Pelo menos 99,9% (99% para a camada de acesso esporádico) |

Para obter informações sobre cada opção de redundância de preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

Para obter informações sobre o armazenamento do Azure garante para durabilidade e disponibilidade, consulte o [SLA do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> O Armazenamento Premium dá suporte apenas ao LRS (armazenamento com redundância local). Para obter informações sobre o Armazenamento Premium, consulte [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Alterar estratégia de replicação
É possível alterar a estratégia de replicação da sua conta de armazenamento usando o [Portal do Azure](https://portal.azure.com/), [Microsoft Azure Powershell](storage-powershell-guide-full.md), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou uma das muitas [Bibliotecas de cliente do Azure](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). Alterar o tipo de replicação da sua conta de armazenamento não resulta em tempo de inatividade.

   > [!NOTE]
   > Atualmente, você não pode usar o Portal ou a API para converter sua conta no ZRS. Se você quiser converter a replicação da conta para ZRS, consulte [Armazenamento com redundância de zona (ZRS)](storage-redundancy-zrs.md) para mais detalhes.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Há algum custo para alterar a estratégia de replicação da minha conta?
Depende do caminho de conversão. A ordenação da oferta de redundância mais barata para a mais cara é LRS, ZRS, GRS e RA-GRS. Por exemplo, se você escolher *partir de* LRS para qualquer outra, terá encargos adicionais porque você está transitando para um nível de redundância mais sofisticado. Escolher *para* GRS ou RA-GRS acarretará um encargo de largura de banda de saída porque os dados (em sua região primária) estão sendo replicados para a região secundária remota. Esse é um encargo único na configuração inicial. Depois que os dados forem copiados, não haverá mais encargos de conversão. Você será cobrado apenas para replicar qualquer novo ou atualizações em dados existentes. Para obter detalhes sobre taxas de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se você alterar de GRS para LRS, não haverá custo adicional, mas seus dados replicados serão excluídos do local secundário.

## <a name="see-also"></a>Consulte também

- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [ZRS (armazenamento com redundância de zona): aplicativos de Armazenamento do Microsoft Azure altamente disponíveis](storage-redundancy-zrs.md)
- [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)
- [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
- [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper – Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
