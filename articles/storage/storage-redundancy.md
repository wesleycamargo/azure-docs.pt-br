---
title: "Replicação de dados no Armazenamento do Azure | Microsoft Docs"
description: "Os dados na sua conta de Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. Opções de replicação incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) RA-GRS (armazenamento com redundância geográfica com acesso de leitura)."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 349be81b5d1d5ccc1510360974b4e3b10471cf7f
ms.openlocfilehash: 13cd31bdce89ae898a6e22a1d27b5aed819ccc0a


---
# <a name="azure-storage-replication"></a>Replicação de Armazenamento do Azure
Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A replicação copia os dados no mesmo data center ou em um segundo data center, dependendo de qual opção de replicação você escolher. A replicação protege seus dados e preserva o tempo de atividade do aplicativo no caso de falhas transitórias de hardware. Se os seus dados forem replicados para um segundo data center, isso também protegerá os dados contra uma falha catastrófica no local principal.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de replicação:

* [Armazenamento com redundância local (LRS)](#locally-redundant-storage)
* [Armazenamento com redundância de zona (ZRS)](#zone-redundant-storage)
* [Armazenamento com redundância geográfica (GRS)](#geo-redundant-storage)
* [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](#read-access-geo-redundant-storage)

O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) é a opção padrão na criação de uma nova conta de armazenamento.

A tabela a seguir apresenta uma visão geral das diferenças entre LRS, ZRS, GRS e RA-GRS, enquanto as seções posteriores lidam com cada tipo de replicação em mais detalhes.

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados entre vários datacenters. |Não |Sim |Sim |Sim |
| Os dados podem ser lidos do local secundário, bem como do local primário. |Não |Não |Não |Sim |
| Número de cópias de dados mantidas em nós separados. |3 |3 |6 |6 |

Consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para informações sobre preços para as opções de redundância diferentes.

> [!NOTE]
> O Armazenamento Premium dá suporte apenas ao LRS (armazenamento com redundância local). Para obter informações sobre o Armazenamento Premium, consulte [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](storage-premium-storage.md).
>
>

## <a name="locally-redundant-storage"></a>Armazenamento com redundância local
O LRS (armazenamento com redundância local) replica os dados três vezes em uma unidade de escala de armazenamento, que é hospedada em um datacenter na região em que você criou a conta de armazenamento. Uma solicitação de gravação retorna com êxito somente depois que tiver sido gravada nas três réplicas. Essas três réplicas residem em domínios de falha e domínios de atualização separados dentro de uma unidade de escala de armazenamento.

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e podem ser considerados nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de um serviço (distribuição). As três réplicas estão difundidas entre UDs e FDs em uma única unidade de escala de armazenamento para garantir que os dados estejam disponíveis, mesmo se a falha de hardware afetar um único rack ou quando os nós forem atualizados durante uma distribuição.

O LRS é a opção de custo mais baixo e oferece durabilidade menor em comparação com outras opções. Em caso de desastre no nível de datacenter (incêndio, inundação etc.), as três réplicas podem ser perdidas ou ficar irrecuperáveis. Para reduzir esse risco, o GRS (armazenamento com redundância geográfica) é a opção recomendada para a maioria dos aplicativos.

O armazenamento com redundância local ainda pode ser desejável em determinados cenários:

* Fornece largura de banda máxima das opções de replicação do Armazenamento do Azure.
* Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país devido a requisitos de governança de dados. Uma região emparelhada pode estar em outro país; confira [regiões do Azure](https://azure.microsoft.com/regions/) para obter informações sobre pares de regiões.

## <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona
O ZRS (armazenamento com redundância de zona) replica os dados de modo assíncrono entre datacenters em uma ou duas regiões, além de armazenar três réplicas semelhantes ao LRS, fornecendo assim maior durabilidade que o LRS. Os dados armazenados no ZRS são duráveis, mesmo se o datacenter primário estiver indisponível ou não for recuperável.
Os clientes que planejam usar o ZRS deve estar ciente de que:

* O ZRS está disponível apenas para blobs de blocos em contas de armazenamento de finalidades gerais e é compatível apenas com o serviço de armazenamento nas versões 2014-02-14 e posteriores.
* Como a replicação assíncrona envolve um atraso, no caso de um desastre local, é possível que as alterações que ainda não foram replicadas para o datacenter secundário sejam pedidas se os dados não puderem ser recuperados do primário.
* A réplica pode não estar disponível até que a Microsoft inicie o failover para o secundário.
* As contas do ZRS não podem ser convertidas posteriormente para LRS ou GRS. Da mesma forma, uma conta do LRS ou GRS existente não pode ser convertida em uma conta do ZRS.
* As contas do ZRS não têm recursos de log ou métricas.

## <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica
O GRS (armazenamento com redundância geográfica) replica seus dados para uma região secundária a centenas de quilômetros da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Para uma conta de armazenamento com GRS habilitado, uma atualização primeiro é confirmada para a região primária, na qual é replicada três vezes. Em seguida, a atualização é replicada de modo assíncrono para a região secundária, onde ela também é replicada três vezes.

Com o GRS, as regiões primária e secundária gerenciam réplicas entre domínios de falha e domínios de atualização separados em uma unidade de escala de armazenamento, conforme descrito com o LRS.

Considerações:

* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.
* A réplica não estará disponível, a menos que a Microsoft inicie o failover para a região secundária. Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](storage-disaster-recovery-guidance.md). 
* Se um aplicativo quiser ler na região secundária, o usuário deverá habilitar o RA-GRS.

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária é determinada com base na região primária e não pode ser alterada. A tabela a seguir mostra os emparelhamentos de regiões primárias e secundárias.

| Primário | Secundário |
| --- | --- |
| Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| Centro-Sul dos Estados Unidos |Centro-Norte dos EUA |
| Leste dos EUA |Oeste dos EUA |
| Oeste dos EUA |Leste dos EUA |
| Leste dos EUA 2 |Centro dos EUA |
| Centro dos EUA |Leste dos EUA 2 |
| Norte da Europa |Europa Ocidental |
| Europa Ocidental |Norte da Europa |
| Sudeste da Ásia |Ásia Oriental |
| Ásia Oriental |Sudeste da Ásia |
| China Oriental |Norte da China |
| Norte da China |China Oriental |
| Leste do Japão |Oeste do Japão |
| Oeste do Japão |Leste do Japão |
| Sul do Brasil |Centro-Sul dos Estados Unidos |
| Leste da Austrália |Sudeste da Austrália |
| Sudeste da Austrália |Leste da Austrália |
| Sul da Índia |Centro da Índia |
| Centro da Índia |Sul da Índia |
| Gov do Iowa nos EUA |Gov. dos EUA – Virgínia |
| Gov. dos EUA – Virgínia |Gov. dos EUA – Iowa |
| Canadá Central |Leste do Canadá |
| Leste do Canadá |Canadá Central |
| Oeste do Reino Unido |Sul do Reino Unido |
| Sul do Reino Unido |Oeste do Reino Unido |
| Alemanha Central |Nordeste da Alemanha |
| Nordeste da Alemanha |Alemanha Central |
| Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Centro-Oeste dos EUA |Oeste dos EUA 2 |

Para obter informações atualizadas sobre regiões com suporte do Azure, confira [Regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura
O RA-GRS (armazenamento com redundância geográfica de acesso de leitura) maximiza a disponibilidade da sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além de replicação em duas regiões fornecido por GRS.

Quando você habilita o acesso somente leitura aos dados na região secundária, seus dados ficam disponíveis em um ponto de extremidade secundário, além do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

Considerações:

* Seu aplicativo precisa gerenciar com qual ponto de extremidade está interagindo ao usar o RA-GRS.
* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.
* Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](storage-disaster-recovery-guidance.md). 
* O RA-GRS é para fins de alta disponibilidade. Para obter as diretrizes de escalabilidade, examine a [lista de verificação de desempenho](storage-performance-checklist.md).

## <a name="next-steps"></a>Próximas etapas
* [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md)
* [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
* [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)




<!--HONumber=Jan17_HO4-->


