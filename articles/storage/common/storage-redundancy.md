---
title: "Replicação de dados no Armazenamento do Azure | Microsoft Docs"
description: "Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. Opções de replicação incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) RA-GRS (armazenamento com redundância geográfica com acesso de leitura)."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 18d0e8bc6cc1559f9ae1a1a4457aa85d2a206597
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="azure-storage-replication"></a>Replicação de Armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A replicação copia os dados para que eles fiquem protegidos contra falhas de hardware transitórias, preservando o tempo de atividade do aplicativo. 

Você pode optar por replicar seus dados no mesmo data center, em data centers na mesma região ou em regiões diferentes. Se os dados forem replicados em vários data centers ou várias regiões, eles também serão protegidos contra uma falha catastrófica em um único local.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de replicação:

* [Armazenamento com redundância local (LRS)](#locally-redundant-storage)
* [Armazenamento com redundância de zona (ZRS)](#zone-redundant-storage)
* [Armazenamento com redundância geográfica (GRS)](#geo-redundant-storage)
* [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](#read-access-geo-redundant-storage)

O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) é a opção padrão na criação de uma conta de armazenamento.

A tabela a seguir fornece uma visão geral das diferenças entre LRS, ZRS, GRS e RA-GRS. As próximas seções deste artigo abordam cada tipo de replicação mais detalhadamente.

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados entre vários datacenters. |Não  |sim |sim |sim |
| Os dados podem ser lidos de um local secundário, bem como do local primário. |Não  |Não  |Não  |sim |
| Concepção de modo a fornecer ___ durabilidade de objetos em um determinado ano. |no mínimo 99,999999999% (11 9's)|no mínimo 99,9999999999% (12 9's)|no mínimo 99,99999999999999% (16 9's)|no mínimo 99,99999999999999% (16 9's)|

Consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para informações sobre preços para as opções de redundância diferentes.

> [!NOTE]
> O Armazenamento Premium dá suporte apenas ao LRS (armazenamento com redundância local). Para obter informações sobre o Armazenamento Premium, consulte [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Armazenamento com redundância local
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>Contas do ZRS Clássico

A funcionalidade existente do ZRS agora é chamada de ZRS Clássico. As contas do ZRS Clássico estão disponíveis somente para blobs de blocos em contas de armazenamento V1 de uso geral. 

O ZRS Clássico replica os dados de forma assíncrona em datacenters em uma ou duas regiões. Uma réplica pode não estar disponível a menos que a Microsoft inicie o failover para o secundário. 

As contas do ZRS Clássico não podem ser convertidas em ou do LRS, GRS ou RA-GRS. As contas do ZRS Clássico também não dão suporte a métricas ou a log.   

Depois que o ZRS estiver em disponibilidade geral em uma região, você não poderá mais criar uma conta do ZRS Clássico no portal nessa região, mas poderá criar uma por outros meios.  
Um processo de migração automatizada do ZRS Clássico para o ZRS será fornecido no futuro.

Você pode migrar manualmente os dados da conta ZRS para ou de uma conta LRS, ZRS Classic, GRS ou RAGRS. Realize a migração manual usando o AzCopy, o Gerenciador de Armazenamento do Azure, o Azure PowerShell, a CLI do Azure ou uma das bibliotecas de clientes do Armazenamento do Azure.

> [!NOTE]
> A reprovação e a migração obrigatória das contas do ZRS Clássico estão previstas para 31 de março de 2021. A Microsoft enviará mais detalhes aos clientes do ZRS Clássico antes da reprovação.

Outras perguntas são respondidas na seção [Perguntas frequentes](#frequently-asked-questions). 

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura
O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) maximiza a disponibilidade para sua conta de armazenamento. RA-GRS fornece acesso somente leitura aos dados no local secundário, além de replicação geográfica em duas regiões.

Quando você habilita o acesso somente leitura aos dados na região secundária, os dados ficam disponíveis em um ponto de extremidade secundário, bem como no ponto de extremidade primário de sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

Algumas considerações a ter em mente ao usar o RA-GRS:

* Seu aplicativo precisa gerenciar com qual ponto de extremidade está interagindo ao usar o RA-GRS.
* Como a replicação assíncrona envolve um atraso, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária, por exemplo, no caso de um desastre regional.
* Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, consulte [Diretrizes de Recuperação de Desastre](../storage-disaster-recovery-guidance.md).
* O RA-GRS é para fins de alta disponibilidade. Para obter diretrizes de escalabilidade, examine a [lista de verificação de desempenho](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Como alterar o tipo de replicação geográfica de minha conta de armazenamento?

   Altere o tipo de replicação geográfica de sua conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md) ou uma das bibliotecas de clientes do Armazenamento do Azure.

   > [!NOTE]
   > As contas do ZRS não podem ser convertidas no LRS ou GRS. Da mesma forma, uma conta do LRS ou GRS existente não pode ser convertida em uma conta do ZRS.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. A alteração do tipo de replicação de minha conta de armazenamento resulta em tempo de inatividade?

   Não. A alteração do tipo de replicação de sua conta de armazenamento não resulta em tempo de inatividade.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Há custos adicionais para alteração do tipo de replicação de minha conta de armazenamento?

   Sim. Se você alterar de LRS para GRS (ou RA-GRS) para sua conta de armazenamento, incorrerá em um encargo adicional pela saída envolvida na cópia de dados existentes do local primário para o local secundário. Depois que os dados iniciais são copiados, não há mais nenhum encargo de saída adicional para a replicação geográfica do local primário para o secundário. Para obter detalhes sobre encargos de largura de banda, visite a [página Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Se você alterar de GRS para LRS, não haverá custo adicional, mas seus dados serão excluídos do local secundário.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Como RA-GRS pode me ajudar?

   O armazenamento GRS fornece replicação de seus dados de uma região primária para uma região secundária que está a centenas de quilômetros de distância da região primária. Com o GRS, os dados são duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não possa ser recuperada. O armazenamento RA-GRS oferece a replicação GRS e adiciona a capacidade de ler os dados no local secundário. Para obter sugestões sobre como criar para alta disponibilidade, consulte [Criando aplicativos altamente disponíveis usando o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Há uma maneira de descobrir o tempo necessário para replicar meus dados da região primária para a região secundária?

   Se você estiver utilizando o armazenamento RA-GRS, você poderá verificar a Hora da Última Sincronização de sua conta de armazenamento. A Hora da Última Sincronização é um valor de data/hora GMT. Todas as gravações primárias antes da Hora da Última Sincronização foram gravadas com êxito no local secundário, o que significa que estão disponíveis para serem lidas no local secundário. As gravações primárias após a hora da última sincronização podem ou não estarem disponíveis para leituras. Consulte esse valor usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md) ou uma das bibliotecas de clientes do Armazenamento do Azure.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Se houver uma interrupção na região primária, como fazer para mudar para a região secundária?

   Para obter mais informações, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. O que é RPO e RTO com GRS?

   **RPO (Objetivo de Ponto de Recuperação):** no GRS e RA-GRS, o serviço de armazenamento replica geograficamente de maneira assíncrona os dados do local primário para o secundário. No caso de um desastre regional grave na região primária, a Microsoft fará um failover para a região secundária. Se ocorrer um failover, as alterações recentes que ainda não foram replicadas geograficamente poderão ser perdidas. O número de minutos de dados potencialmente perdidos é chamado de RPO, e isso indica o momento em que os dados podem ser recuperados. Normalmente, o Armazenamento do Azure tem um RPO inferior a 15 minutos, embora atualmente não exista nenhum SLA sobre quanto tempo leva a replicação geográfica.

   **RTO (Objetivo de Tempo de Recuperação):** o RTO é uma medida de quanto tempo é necessário para fazer o failover e colocar a conta de armazenamento novamente online. O tempo para fazer o failover inclui as seguintes ações:

   * O tempo exigido pela Microsoft para determinar se os dados podem ser recuperados no local primário, ou se um failover é necessário.
   * O tempo para fazer o failover da conta de armazenamento, alterando as entradas DNS primárias para que elas apontem para o local secundário.

   A Microsoft leva a sério a responsabilidade de preservar seus dados. Se houver uma possibilidade de recuperação dos dados na região primária, a Microsoft atrasará o failover e se concentrará na recuperação de seus dados. Uma versão futura do serviço permitirá que você dispare um failover em um nível de conta, para você controlar o RTO por conta própria.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Para quais objetos do Armazenamento do Azure o ZRS dá suporte? 
Blobs de blocos, blobs de páginas (exceto aqueles que dão suporte a discos de VM), tabelas, arquivos e filas. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. O ZRS também inclui a replicação geográfica? 
No momento, o ZRS não dá suporte à replicação geográfica. Se o cenário exigir a replicação entre regiões para fins de recuperação de desastre, use uma conta de armazenamento GRS ou RA-GRS.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. O que acontece quando uma ou mais zonas ZRS ficam inativas? 
Quando a primeira zona fica inativa, o ZRS continua gravando réplicas dos dados entre as duas zonas restantes da região. Se uma segunda zona ficar inativa, o acesso de leitura e gravação não estará disponível até que, pelo menos, duas zonas estejam disponíveis novamente. 

## <a name="next-steps"></a>Próximas etapas
* [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Sobre as contas de armazenamento do Azure](../storage-create-storage-account.md)
* [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
* [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper – Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
