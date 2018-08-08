---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 86c301748b58e7642df9a738c70b4fe70be3310b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400001"
---
O armazenamento com redundância de zona (ZRS) replica seus dados de forma síncrona em três clusters de armazenamento em uma única região. Cada cluster de armazenamento é fisicamente separado dos outros e reside em sua própria zona de disponibilidade (AZ). Cada zona de disponibilidade e o cluster ZRS dentro dele, é autônomo, com recursos de rede e utilitários separados.

Armazenar dados em uma conta do ZRS assegura que você é capaz de acessar e gerenciar seus dados no caso de uma região ficar indisponível. O ZRS oferece excelente desempenho e baixa latência. O ZRS oferece as mesmas [metas de escalabilidade](../articles/storage/common/storage-scalability-targets.md) como [armazenamento localmente redundante (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Considere o ZRS para cenários que exigem consistência forte, forte durabilidade e alta disponibilidade, mesmo se uma interrupção ou desastre natural renderiza um zonal data center não estiver disponível. O ZRS oferece durabilidade para objetos de armazenamento de, pelo menos, 99,9999999999% (doze noves) em um ano específico.

Para obter informações sobre zonas de disponibilidade, consulte [visão geral de Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Compatível com cobertura e disponibilidade regional
Atualmente, o ZRS oferece suporte ao padrão de tipos de conta [uso geral v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts). O ZRS está disponível para blobs de blocos, blobs de página que não são disco, arquivos, tabelas e filas. Além disso, todos os seus logs da [Análise de Armazenamento](../articles/storage/common/storage-analytics.md)e as [Métricas de Armazenamento](../articles/storage/common/storage-enable-and-view-metrics.md)

ZRS está geralmente disponível nas seguintes regiões:

- Leste dos EUA 2
- Oeste dos EUA 2
- Centro dos EUA
- Norte da Europa
- Europa Ocidental
- França Central
- Sudeste Asiático

A Microsoft continua a habilitar o ZRS em mais regiões do Azure. Verifique a página [Atualizações de Serviço do Azure](https://azure.microsoft.com/updates/) regularmente para obter informações sobre novas regiões.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>O que acontece quando uma zona fica indisponível?

Seus dados permanecem resilientes se uma região ficar indisponível. A Microsoft recomenda que você continue a seguir práticas para manipulação de falha transitórias, como implementação de políticas de novas tentativas com retirada exponencial. Quando uma zona não estiver disponível, o Azure realiza atualizações da rede, como a reposição de DNS. Essas atualizações podem afetar seu aplicativo se você estiver acessando seus dados antes que sejam concluídos.

O ZRS não pode proteger seus dados contra um desastre regional em que várias zonas permanentemente são afetadas. Em vez disso, o ZRS oferece resiliência para seus dados se ele ficar indisponível temporariamente. Para proteção contra desastres regionais, a Microsoft recomenda o uso de armazenamento com redundância geográfica (GRS). Para obter mais informações sobre GRS, consulte [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Convertendo em replicação ZRS
Hoje, você pode usar o portal do Azure ou a API de provedor de recursos de armazenamento para alterar o tipo de redundância da sua conta, desde que você está migrando para ou do LRS, GRS e RA-GRS. Com o ZRS, no entanto, migração não é simples porque ela envolve a movimentação de dados físico de um carimbo de armazenamento único para vários carimbos dentro de uma região. 

Você tem duas opções principais para a migração de ou para o ZRS. Manualmente, você pode copiar ou mover dados para uma nova conta do ZRS de sua conta existente. Você também pode solicitar uma migração ao vivo. A Microsoft recomenda enfaticamente que você execute uma migração manual porque não há nenhuma garantia em relação a quando uma migração ao vivo for realizada. Uma rota de migração manual fornece mais flexibilidade do que uma migração ao vivo, e você está no controle do cronograma da migração.

Para executar uma migração manual, você tem opções de uma variedade:
- Use ferramentas existentes, como o AzCopy, armazenamento do SDK, ferramentas de terceiros confiáveis, etc.
- Se você estiver familiarizado com o Hadoop ou HDInsight, você pode anexar tanto source e de destino (ZRS) da conta para seu cluster e usar algo como DistCp massivamente paralelizar o processo de cópia de dados
- Criar suas próprias ferramentas aproveitando um tipo do SDK de armazenamento

Se, no entanto, uma migração manual resultar em algum tempo de inatividade do aplicativo e você não conseguir absorver de sua parte, a Microsoft fornece uma opção de migração ao vivo. Uma migração ao vivo é uma migração in-loco que permite que você continue usando sua conta de armazenamento existente, enquanto os dados são migrados entre carimbos de armazenamento de origem e de destino. Durante a migração, você ainda terá o mesmo nível de durabilidade e SLA de disponibilidade como faria normalmente.

Migração ao vivo vêm com algumas restrições, no entanto. Elas estão listados abaixo.

- Enquanto o Microsoft abordará sua solicitação de migração ao vivo imediatamente, não há nenhuma garantia em relação a quando a migração será concluída. Se você precisar que os dados estejam no ZRS por um determinado tempo, em seguida, você deve fazer uma migração manual. Em geral, quanto mais dados você tem em sua conta, mais tempo levará para migrar esses dados. 
- Você só pode executar uma migração ao vivo de uma conta usando a replicação de LRS ou GRS. Se sua conta usa o RA-GRS, você precisará migrar primeiro para um desses tipos de replicação antes de continuar. Essa etapa intermediária garante que o secundário somente lê o ponto de extremidade que RA-GRS fornece é removido antes da migração.
- Sua conta deve conter dados.
- Há suporte para migrações de intraregião apenas. Se você quiser migrar os dados para uma conta do ZRS localizada em uma região diferente da conta de origem, você deve executar uma migração manual.
- Há suporte para apenas tipos de conta de armazenamento padrão. Você não pode migrar a partir de uma conta de armazenamento premium.

As solicitações de migração dinâmica vão através do portal de Suporte do Azure. No portal, você deve selecionar a conta de armazenamento que você deseja converter para o ZRS.
1. Clique em **Nova Solicitação de Suporte**
2. Verifique as noções básicas. Clique em **Próximo**. 
3. Na seção **Problema**, 
    - Deixe a Severidade como-está.
    - Tipo de problema = **Migração de dados**
    - Categoria = **Migrar para o ZRS dentro de uma região**
    - Título = **migração de conta do ZRS** (ou algo descritivo)
    - Detalhes = gostaria de migrar para o ZRS de [LRS, GRS] na região de______. 
4. Clique em **Próximo**.
5. Verifique se as Informações de Contato estão corretas na folha de informações de contato.
6. Clique em **Enviar**.

Em seguida, um profissional de suporte entrará em contato com você. Essa pessoa será disponibilizada para fornecer qualquer ajuda que você puder exigir. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS clássico: é uma opção herdada para redundância de blobs de bloco
> [!NOTE]
> A reprovação e a migração obrigatória das contas do ZRS Clássico estão previstas para 31 de março de 2021. A Microsoft enviará mais detalhes aos clientes do ZRS Clássico antes da reprovação. A Microsoft planeja fornecer um processo de migração automatizada do Clássico ZRS para o ZRS no futuro.

>[!NOTE]
> Depois que o ZRS estiver em [disponibilidade geral](#support-coverage-and-regional-availability) em uma região, você não poderá mais criar uma conta do ZRS Clássico no portal nessa região, mas poderá criar uma por outros meios. No entanto, você ainda pode criar uma por outros meios, como o Microsoft PowerShell e a CLI do Azure, ou seja, até que o ZRS clássico seja preterido.

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Uma réplica pode não estar disponível a menos que a Microsoft inicie o failover para o secundário. As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento [V1 (GPv1) de uso geral](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts). Uma conta do ZRS Clássico não pode ser convertida entre LRS ou GRS, e não tem métricas ou recursos de registro em log.

As contas do ZRS Clássico não podem ser convertidas em ou do LRS, GRS ou RA-GRS. As contas do ZRS Clássico também não dão suporte a métricas ou a log.

Para migrar manualmente os dados da conta ZRS para ou de uma conta LRS, ZRS Classic, GRS ou RA-GRS, use o AzCopy, o Gerenciador de Armazenamento do Microsoft Azure, Azure PowerShell ou CLI do Azure. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.
