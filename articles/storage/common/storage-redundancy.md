---
title: "Replicação de dados no Armazenamento do Azure | Microsoft Docs"
description: "Os dados na sua conta do Armazenamento do Microsoft Azure são replicados para garantir durabilidade e alta disponibilidade. Opções de replicação incluem LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) RA-GRS (armazenamento com redundância geográfica com acesso de leitura)."
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
ms.date: 05/15/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c35f53c5c80a67cb33b457e9e3235d0e745536cf
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="azure-storage-replication"></a>Replicação de Armazenamento do Azure

Os dados na sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. A replicação copia os dados no mesmo data center ou em um segundo data center, dependendo de qual opção de replicação você escolher. A replicação protege seus dados e preserva o tempo de atividade do aplicativo no caso de falhas transitórias de hardware. Se os seus dados forem replicados para um segundo data center, eles estarão protegidos contra uma falha catastrófica no local principal.

A replicação garante que sua conta de armazenamento atenda ao [SLA (Contrato de Nível de Serviço) do Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte o SLA para obter informações sobre as garantias do Armazenamento do Azure para disponibilidade e durabilidade.

Ao criar uma conta de armazenamento, você deve selecionar uma das seguintes opções de replicação:

* [Armazenamento com redundância local (LRS)](#locally-redundant-storage)
* [Armazenamento com redundância de zona (ZRS)](#zone-redundant-storage)
* [Armazenamento com redundância geográfica (GRS)](#geo-redundant-storage)
* [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](#read-access-geo-redundant-storage)

O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) é a opção padrão na criação de uma conta de armazenamento.

A tabela a seguir apresenta uma visão geral das diferenças entre LRS, ZRS, GRS e RA-GRS, enquanto as seções posteriores lidam com cada tipo de replicação em mais detalhes.

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados entre vários datacenters. |Não |Sim |Sim |Sim |
| Os dados podem ser lidos de um local secundário, bem como do local primário. |Não |Não |Não |Sim |
| Número de cópias de dados mantidas em nós separados. |3 |3 |6 |6 |

Consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para informações sobre preços para as opções de redundância diferentes.

> [!NOTE]
> O Armazenamento Premium dá suporte apenas ao LRS (armazenamento com redundância local). Para obter informações sobre o Armazenamento Premium, consulte [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure](../storage-premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Armazenamento com redundância local
O LRS (armazenamento com redundância local) replica os dados três vezes em uma unidade de escala de armazenamento, que é hospedada em um datacenter na região em que você criou a conta de armazenamento. Uma solicitação de gravação retorna com êxito somente depois que tiver sido gravada nas três réplicas. Essas três réplicas residem em domínios de falha e domínios de atualização separados dentro de uma unidade de escala de armazenamento.

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e podem ser considerados nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de um serviço (distribuição). As três réplicas estão difundidas entre UDs e FDs em uma única unidade de escala de armazenamento para garantir que os dados estejam disponíveis, mesmo se a falha de hardware afetar um único rack ou quando os nós forem atualizados durante uma distribuição.

O LRS é a opção de custo mais baixo e oferece durabilidade menor em comparação com outras opções. Em caso de desastre no nível de datacenter (incêndio, inundação etc.), as três réplicas podem ser perdidas ou ficar irrecuperáveis. Para reduzir esse risco, o GRS (armazenamento com redundância geográfica) é a opção recomendada para a maioria dos aplicativos.

O armazenamento com redundância local ainda pode ser desejável em determinados cenários:

* Fornece largura de banda máxima das opções de replicação do Armazenamento do Azure.
* Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país devido a requisitos de governança de dados. Uma região emparelhada poderia estar em outro país. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

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
* A réplica não estará disponível, a menos que a Microsoft inicie o failover para a região secundária. Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](../storage-disaster-recovery-guidance.md). 
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
| Oeste da Índia |Sul da Índia |
| Gov do Iowa nos EUA |Gov. dos EUA – Virgínia |
| Gov. dos EUA – Virgínia |Governo dos EUA do Texas |
| Governo dos EUA do Texas |Governo dos EUA do Arizona |
| Governo dos EUA do Arizona |Governo dos EUA do Texas |
| Canadá Central |Leste do Canadá |
| Leste do Canadá |Canadá Central |
| Oeste do Reino Unido |Sul do Reino Unido |
| Sul do Reino Unido |Oeste do Reino Unido |
| Alemanha Central |Nordeste da Alemanha |
| Nordeste da Alemanha |Alemanha Central |
| Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Centro-Oeste dos EUA |Oeste dos EUA 2 |

Para obter informações atualizadas sobre regiões com suporte do Azure, confira [Regiões do Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> A região secundária do US Gov - Virgínia é US Gov - Texas. Anteriormente, o US Gov - Virgínia utilizada o US Gov - Iowa como uma região secundária. As contas de armazenamento que ainda utilizam US Gov - Iowa como uma região secundária estão sendo migradas para US Gov - Texas como uma segunda região. 
> 
> 

## <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura
O RA-GRS (armazenamento com redundância geográfica de acesso de leitura) maximiza a disponibilidade da sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além de replicação em duas regiões fornecido por GRS.

Quando você habilita o acesso somente leitura aos dados na região secundária, seus dados ficam disponíveis em um ponto de extremidade secundário, além do ponto de extremidade primário para sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

Considerações:

* Seu aplicativo precisa gerenciar com qual ponto de extremidade está interagindo ao usar o RA-GRS.
* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam pedidas se os dados não puderem ser recuperados da região primária.
* Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](../storage-disaster-recovery-guidance.md). 
* O RA-GRS é para fins de alta disponibilidade. Para obter as diretrizes de escalabilidade, examine a [lista de verificação de desempenho](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Como alterar o tipo de replicação geográfica da minha conta de armazenamento?

   É possível alterar o tipo de replicação geográfica de sua conta de armazenamento entre LRS, GRS e RA-GRS utilizando o [Portal do Azure](https://portal.azure.com/), o [Azure Powershell](storage-powershell-guide-full.md) ou, programaticamente, utilizando uma das muitas Bibliotecas de Clientes de Armazenamento. Observe que as contas ZRS não podem ser convertidas em LRS ou GRS. Da mesma forma, uma conta do LRS ou GRS existente não pode ser convertida em uma conta do ZRS.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. Haverá algum tempo inoperante se eu alterar o tipo de replicação da minha conta de armazenamento?

   Não, não haverá tempo inoperante.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. Haverá algum custo adicional se eu alterar o tipo de replicação da minha conta de armazenamento?

   Sim. Se você alterar de LRS para GRS (ou RA-GRS) para sua conta de armazenamento isso implicará em um encargo adicional por saída associado com a cópia de dados existentes do local primário para o local secundário. Depois que os dados iniciais forem copiados não haverá mais nenhuma taxa de saída adicional para a replicação geográfica dos dados do local primário para o secundário. Os detalhes das taxas de largura de banda podem ser encontrados na página [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Se você alterar de GRS para LRS não haverá custo adicional, mas seus dados serão excluídos do local secundário.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Como RA-GRS pode me ajudar?
   
   O armazenamento GRS fornece replicação de seus dados de uma região primária para uma região secundária que está a centenas de quilômetros de distância da região primária. Nesse caso, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não possa ser recuperada. O armazenamento RA-GRS inclui isso e adiciona a capacidade de ler os dados do local secundário. Para algumas ideias sobre como aproveitar essa habilidade, consulte [Criar aplicativos altamente disponíveis utilizando o armazenamento RA-GRS ](../storage-designing-ha-apps-with-ragrs.md). 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Há uma maneira de descobrir o tempo necessário para replicar meus dados da região primária para a secundária?
   
   Se você estiver utilizando o armazenamento RA-GRS, você poderá verificar a Hora da Última Sincronização de sua conta de armazenamento. A Hora da Última Sincronização é um valor de data/hora GMT; todas as gravações primárias antes da hora da última sincronização foram gravadas com êxito para o local secundário, significando que estão disponíveis para serem lidas a partir do local secundária. As gravações primárias após a hora da última sincronização podem ou não estarem disponíveis para leituras. Você pode consultar esse valor utilizando o [Portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md), ou programaticamente, utilizando a API REST ou uma das Bibliotecas de Clientes de Armazenamento. 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. Como posso mudar para a região secundária se houver uma interrupção na região principal?
   
   Consulte o artigo [O que fazer se ocorrer uma interrupção de armazenamento do Azure ](../storage-disaster-recovery-guidance.md) para obter mais informações.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. O que é RPO e RTO com GRS?
   
   RPO (Objetivo de Ponto de Recuperação): no GRS e no RA-GRS, o serviço de armazenamento sincroniza de maneira assíncrona os dados do local primário para o secundário. Se houver um grande desastre regional e for necessário realizar um failover, as recentes alterações delta que não foram replicadas geograficamente poderão ser perdidas. O número de minutos de dados potencialmente perdidos é referido como o RPO (o que significa o momento em que os dados podem ser recuperados). Normalmente, temos um RPO inferior a 15 minutos, embora atualmente não exista SLA em quanto tempo demora a replicação geográfica.

   RTO (Objetivo de Tempo de Recuperação): essa é uma medida de quanto tempo demora para fazer o failover e recuperar a conta de armazenamento online, caso seja necessário realizar um failover. O tempo para realizar o failover inclui o seguinte:
    * O tempo que demora para investigar e determinar se será possível recuperar os dados no local principal ou, se haverá a necessidade de realizar um failover.
    * Realizar failover da conta alterando as entradas DNS primárias para apontar para o local secundário.

   Nós assumimos a responsabilidade de preservar seus dados com muita seriedade, portanto, se houver alguma chance de recuperar os dados, adiaremos o failover e nos concentraremos na recuperação dos dados no local primário. No futuro, planejamos fornecer uma API para permitir que você acione um failover em um nível de conta, permitindo que você próprio controle o RTO, mas isso ainda não está disponível.
   
## <a name="next-steps"></a>Próximas etapas
* [Criando aplicativos altamente disponíveis usando o armazenamento de RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Sobre as contas de armazenamento do Azure](../storage-create-storage-account.md)
* [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage-scalability-targets.md)
* [Armazenamento com redundância geográfica com acesso de leitura e opções de redundância do Armazenamento do Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper – Armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


