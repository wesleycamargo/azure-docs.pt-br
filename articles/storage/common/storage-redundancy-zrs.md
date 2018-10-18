---
title: Compilar aplicativos de Armazenamento do Microsoft Azure altamente disponíveis no ZRS (armazenamento com redundância de zona) | Microsoft Docs
description: O ZRS (armazenamento com redundância de zona) oferece uma maneira simples de compilar aplicativos altamente disponíveis. O ZRS protege contra falhas de hardware no datacenter e contra alguns desastres regionais.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 0a4ebefd89414fa62ca69f2f9cc726f471694bee
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733609"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>ZRS (armazenamento com redundância de zona): aplicativos de Armazenamento do Microsoft Azure altamente disponíveis
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Compatível com cobertura e disponibilidade regional
Atualmente, o ZRS oferece suporte ao padrão de tipos de conta uso geral v2. Para saber mais sobre os tipos de contas de armazenamento, consulte [Visão geral de conta de Armazenamento do Azure](storage-account-overview.md).

O ZRS está disponível para blobs de blocos, blobs de página que não são disco, arquivos, tabelas e filas.

ZRS está geralmente disponível nas seguintes regiões:

- Leste dos EUA
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

O ZRS não pode proteger seus dados contra um desastre regional em que várias zonas permanentemente são afetadas. Em vez disso, o ZRS oferece resiliência para seus dados se ele ficar indisponível temporariamente. Para proteção contra desastres regionais, a Microsoft recomenda o uso de armazenamento com redundância geográfica (GRS). Para obter mais informações sobre GRS, consulte [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md).

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
> A Microsoft irá substituir e migrar as contas do ZRS clássico em 31 de março de 2021. Mais detalhes serão fornecidos para clientes do ZRS clássico antes da reprovação. 
>
> Quando o ZRS se torna [geralmente disponível](#support-coverage-and-regional-availability) em uma região, os clientes não poderão criar contas do ZRS clássico no portal nessa região. Usando o Microsoft PowerShell e a CLI do Azure para criar contas do ZRS clássico há suporte até que o ZRS clássico seja preterido.

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Uma réplica pode não estar disponível a menos que a Microsoft inicie o failover para o secundário. Uma conta do ZRS Clássico não pode ser convertida entre LRS ou GRS, e não tem métricas ou recursos de registro em log.

As contas do ZRS Clássico estão disponíveis somente para **blobs de blocos** em contas de armazenamento V1 (GPv1) de uso geral. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

As contas do ZRS Clássico não podem ser convertidas em ou do LRS, GRS ou RA-GRS. As contas do ZRS Clássico também não dão suporte a métricas ou a log.

Para migrar manualmente os dados da conta ZRS para ou de uma conta LRS, ZRS Classic, GRS ou RA-GRS, use o AzCopy, o Gerenciador de Armazenamento do Microsoft Azure, Azure PowerShell ou CLI do Azure. Você também pode criar sua própria solução de migração com uma das bibliotecas de cliente do Armazenamento do Microsoft Azure.

## <a name="see-also"></a>Consulte também
- [Replicação de Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure](storage-redundancy-grs.md)