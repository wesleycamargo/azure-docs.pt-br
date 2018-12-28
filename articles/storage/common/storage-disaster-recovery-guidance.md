---
title: O que fazer caso ocorra uma interrupção de Armazenamento do Azure | Microsoft Docs
description: O que fazer caso ocorra uma interrupção de Armazenamento do Azure
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 39a938d45c8f15c21b44bb5b04b1429fb4733b5a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323261"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>O que fazer se uma interrupção no Armazenamento do Azure ocorrer
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas em uma ou mais regiões. Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes de alto nível para serviços de Armazenamento do Azure.

## <a name="how-to-prepare"></a>Como se preparar
É essencial para todos os clientes preparar seu próprio plano de recuperação de desastre. O esforço para se recuperar de uma interrupção de armazenamento geralmente envolve a equipe de operações e procedimentos automatizados para reativar os aplicativos em um estado funcional. Consulte a documentação do Azure abaixo para compilar seu próprio plano de recuperação de desastre:

* [Lista de verificação de disponibilidade](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Projeto de aplicativos resilientes do Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Serviço do Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Replicação de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Como detectar
A maneira recomendada de determinar o status de serviço do Azure é assinar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>O que fazer se uma interrupção no Armazenamento ocorrer
Se um ou mais serviços de Armazenamento estão indisponíveis no momento em uma ou mais regiões, há duas opções a serem consideradas. Se você quiser acesso imediato a seus dados, considere a Opção 2.

### <a name="option-1-wait-for-recovery"></a>Opção 1: Aguardar a recuperação
Nesse caso, nenhuma ação sua é necessária. Estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço do Azure. Você pode ver o status atual do serviço no nosso [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opção 2: Copiar os dados do secundário
Se você escolheu [RA-GRS (armazenamento com redundância geográfica de acesso de leitura)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recomendado) para suas contas de armazenamento, você terá acesso de leitura aos dados da região secundária. Você pode usar ferramentas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) e [biblioteca de Movimentação de Dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados da região secundária para outra conta de armazenamento em uma região não afetada e, em seguida, apontar os aplicativos para essa conta de armazenamento para disponibilidade de leitura e gravação.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>O que esperar se ocorrer um failover de Armazenamento
Se você tiver escolhido [GRS (armazenamento com redundância geográfica)](storage-redundancy-grs.md) ou [RA-GRS (armazenamento com redundância geográfica de acesso de leitura)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recomendado), o Armazenamento do Azure mantém seus dados duráveis em duas regiões (primária e secundária). Em ambas as regiões, o Armazenamento do Azure mantém constantemente várias réplicas de seus dados.

Quando um desastre regional afetar sua região primária, primeiro tentaremos restaurar o serviço nessa região para fornecer a melhor combinação de RTO e RPO. Dependendo da natureza do desastre e seus impactos, em algumas ocasiões raras não será possível restaurar a região primária. Nesse ponto, executaremos um failover geográfico. A replicação de dados entre regiões é um processo assíncrono que envolve um atraso, portanto, é possível que as alterações que ainda não foram replicadas para a região secundária sejam perdidas.

Alguns pontos sobre a experiência de failover geográfico de armazenamento:

* O failover geográfico de armazenamento somente será disparado pela equipe do Armazenamento do Azure – não é necessária nenhuma ação do cliente. O failover é disparado quando a equipe de armazenamento do Azure esgota todas as opções de restauração de dados na mesma região, o que fornece a melhor combinação de RTO e RPO.
* Os pontos de extremidade de serviço armazenamento existente para blobs, tabelas, filas e arquivos permanecerão os mesmos após o failover; a entrada DNS fornecida pela Microsoft precisa ser atualizada para mudar da região primária para a região secundária. A Microsoft executará essa atualização automaticamente como parte do processo de failover geográfico.
* Antes e durante o failover geográfico, você não terá acesso de gravação à conta de armazenamento devido ao impacto do desastre, mas você poderá ler por meio do secundário se sua conta de armazenamento tiver sido configurada como RA-GRS.
* Quando o failover de área geográfica for concluído e as alterações de DNS forem propagadas, o acesso de leitura e gravação à conta de armazenamento será restaurado se você tiver GRS ou RA-GRS. O ponto de extremidade que antes era seu ponto de extremidade secundário se tornará seu ponto de extremidade primário. 
* Você pode verificar o status da localização primária e consultar o último horário de failover de área geográfica da conta de armazenamento. Para obter mais informações, confira [Contas de armazenamento – Obter Propriedades](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/getproperties).
* Após o failover, sua conta de armazenamento estará em funcionamento total, mas em um estado "degradado", pois ela estará hospedada em uma região autônoma sem a possibilidade de replicação geográfica. Para reduzir esse risco, podemos restaurar a região primária original e, em seguida, fazer um failback geográfico para restaurar o estado original. Se a região primária original for irrecuperável, alocaremos outra região secundária.

## <a name="best-practices-for-protecting-your-data"></a>Melhores Práticas para proteger seus dados
Há algumas abordagens recomendadas para fazer backup de seus dados de armazenamento regularmente.

* Discos VM – Use o [serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) para fazer backup dos discos VM usados pelas máquinas virtuais do Azure.
* Blobs de bloco – Ative [exclusão reversível](../blobs/storage-blob-soft-delete.md) para proteger contra exclusões de nível de objeto e substituições ou copiar os blobs para outra conta de armazenamento em outra região usando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), ou o [biblioteca de Movimentação de Dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tabelas – Use [AzCopy](storage-use-azcopy.md) para exportar os dados da tabela para outra conta de armazenamento em outra região.
* Arquivos – use [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os arquivos para outra conta de armazenamento em outra região.

Para obter informações sobre como criar aplicativos que se beneficiam do recurso RA-GRS, confira [Criação de aplicativos altamente disponíveis usando o Armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
