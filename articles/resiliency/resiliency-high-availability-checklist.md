<properties
   pageTitle="Lista de verificação de alta disponibilidade | Microsoft Azure"
   description="Uma lista de verificação rápida de configurações e de ações que você pode usar para garantir que esteja melhorando a disponibilidade de aplicativos com o Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/15/2016"
   ms.author="aglick"/>

#Lista de verificação de alta disponibilidade
Um dos grandes benefícios de usar o Azure é a capacidade de aumentar a disponibilidade (e a escalabilidade) de seus aplicativos com a ajuda da nuvem. Para certificar-se de que você esteja aproveitando a maioria dessas opções, a lista de verificação abaixo é destinada a ajudar você com alguns dos conceitos básicos de infraestrutura de chave para garantir que seus aplicativos sejam resilientes.

>[AZURE.NOTE] A maioria das sugestões abaixo é composta de coisas que podem ser implementadas a qualquer momento em seu aplicativo e, portanto, são excelentes para "correções rápidas". A melhor solução a longo prazo melhor geralmente envolve um design de aplicativo criado para a nuvem. Para obter uma lista de verificação sobre essas áreas mais orientadas ao design, leia a nossa [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md).

###Você está usando o Gerenciador de Tráfego em seus recursos?
Usando o Gerenciador de Tráfego ajuda você a rotear o tráfego da Internet entre regiões do Azure ou locais do Azure e no local. Você pode fazer isso por vários motivos, incluindo disponibilidade e latência. Se você quiser encontrar mais informações sobre como usar o Gerenciador de Tráfego para aumentar a resiliência e distribuir o tráfego por várias regiões, leia [Running VMs in multiple datacenters on Azure for high availability](../guidance/guidance-compute-multiple-datacenters.md) (Executando VMs em vários datacenters no Azure para alta disponibilidade).

__O que acontece se você não usar o Gerenciador de Tráfego?__ Se você não estiver usando o Gerenciador de Tráfego na frente do seu aplicativo, estará limitado a uma única região para seus recursos. Isso limita sua escala, aumenta a latência para os usuários que não estejam perto de sua região escolhida e reduz a proteção no caso de uma interrupção do serviço de toda a região.

###Você evitou usar uma única máquina virtual para qualquer função?
O bom design evita qualquer ponto único de falha. Isso é importante em todo o design de serviço (local ou na nuvem), mas é especialmente útil na nuvem, pois você pode aumentar a escalabilidade e a resiliência, expandindo (adicionando máquinas virtuais) em vez de escalar verticalmente (usando uma máquina virtual mais potente). Se você quiser saber mais sobre o design de aplicativos escalonáveis, leia [Alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-high-availability-azure-applications.md).

__O que acontece se você tiver uma única máquina virtual para uma função?__ Um único computador é um único ponto de falha e não está disponível para o [Contrato de Nível de Serviço da Máquina Virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). Nos melhores casos, o aplicativo será bem executado, mas esse não é um design resiliente, não é coberto pelo SLA de Máquina Virtual do Azure, e qualquer ponto único de falha aumenta a chance de tempo de inatividade se algo falhar.

###Você está usando um balanceador de carga na frente das VMs voltadas para a Internet do seu aplicativo?
Os balanceadores de carga permitem que você distribua o tráfego de entrada para seu aplicativo por um número arbitrário de máquinas. Você pode adicionar ou remover computadores do seu balanceador de carga a qualquer momento, o que funciona bem com máquinas virtuais (e também com o dimensionamento automático com Conjuntos de Escala de Máquina Virtual) para que você possa lidar facilmente com aumentos de tráfego ou com falhas de VM. Se quiser saber mais sobre balanceadores de carga, leia [Visão geral do Azure Load Balancer](../load-balancer/load-balancer-overview.md) e [Running multiple VMs on Azure for scalability and availability (Executando várias VMs no Azure para escalabilidade e disponibilidade)](../guidance/guidance-compute-multi-vm.md).

__O que acontece se você não estiver usando um balanceador de carga na frente das VMs voltadas para a Internet?__ Sem um balanceador de carga, você não será poderá escalar horizontalmente (adicionar mais máquinas virtuais) e sua única opção será escalar verticalmente (aumentar o tamanho da máquina virtual voltada para a Web). Você também encontrará um ponto único de falha com essa máquina virtual. Você também precisará escrever código DNS para observar se perdeu um computador voltado para a Internet e para remapear sua entrada DNS para o novo computador iniciado para assumir seu lugar.

###Você está usando conjuntos de disponibilidade define para seus servidores Web e de aplicativos sem monitoração de estado?
Colocar os computadores na mesma camada de aplicativo em um conjunto de disponibilidade torna suas VMs qualificadas para o SLA da VM do Azure. Fazer parte de um conjunto de disponibilidade também garante que seus computadores sejam colocados em domínios de atualização e domínios de falha diferentes (ou seja, computadores host diferentes que são corrigidos com patches em momentos diferentes) e em domínios de falha (ou seja, computadores host que compartilham uma fonte comum de energia e um comutador de rede). Sem estar em um conjunto de disponibilidade, suas VMs poderiam estar localizadas no mesmo computador host e, portanto, pode haver um ponto único de falha que não seja visível para você. Se você quiser saber mais sobre o aumento da disponibilidade de suas VMs usando conjuntos de disponibilidade, leia [Gerenciar a disponibilidade de máquinas virtuais](../virtual-machines/virtual-machines-windows-manage-availability.md).

__O que acontece se você não usar um conjunto de disponibilidade com seus servidores Web e de aplicativos sem monitoração de estado?__ Não usar um conjunto de disponibilidade significa que você não é capaz de aproveitar as vantagens do SLA da VM do Azure. Isso também significa que todos os computadores naquela camada do seu aplicativo poderão ficar offline se houver uma atualização no computador host (o computador que hospeda as VMs que estão sendo usadas) ou se houver uma falha de hardware comum.

###Você está usando VMSS (Conjuntos de Escala de Máquina Virtual) para seus servidores Web ou de aplicativo sem monitoração de estado?
Um bom design escalonável e resiliente usa VMSS para garantir que você possa aumentar/reduzir o número de computadores em uma camada de seu aplicativo (como a sua camada da Web). O VMSS permite que você defina como sua camada de aplicativo será dimensionada (adicionando ou removendo servidores com base em critérios à sua escolha). Se você quiser saber mais sobre como usar Conjuntos de Escalas de Máquina Virtual do Azure para aumentar sua resiliência em picos de tráfego, leia [Conjuntos de dimensionamento de máquina virtual — visão geral](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

__O que acontece se você não usar um Conjunto de Escala de Máquina Virtual com o servidor Web ou de aplicativo sem monitoração de estado?__ Sem um VMSS, você limita sua capacidade de dimensionar sem limites e de otimizar o uso de recursos. Um design sem VMSS tem um limite máximo de dimensionamento que precisa ser tratado com código adicional (ou manualmente). Essa ausência de um VMSS também significa que seu aplicativo pode não adicionar e remover os computadores com facilidade (independentemente da escala) para ajudá-lo a lidar com os grandes picos de tráfego (tais como durante uma promoção ou se seu site/aplicativo/produto se tornar viral).

###Você está usando o armazenamento premium e contas de armazenamento separadas para cada uma de suas máquinas virtuais?
É uma prática recomendada usar o armazenamento premium para as máquinas virtuais de produção. Além disso, você deve usar uma conta de armazenamento separada para cada máquina virtual (isso é verdadeiro para implantações em pequena escala. Para implantações maiores, você pode reutilizar as contas de armazenamento para vários computadores, mas há um balanceamento que precisa ser feito para garantir que haja balanceamento entre os domínios de atualização e entre as camadas do seu aplicativo). Se você quiser saber mais sobre o desempenho e a escalabilidade do Armazenamento do Azure, leia [Lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure](../storage/storage-performance-checklist.md).

__O que acontece se você não usar contas de armazenamento separadas para cada máquina virtual?__ Uma conta de armazenamento, assim como muitos outros recurso,s é um ponto único de falha. Embora existam muitas proteções e recursos de resiliência do Armazenamento do Azure, um ponto único de falha nunca é um bom design. Por exemplo, se os direitos de acesso forem corrompidos para essa conta, se um limite de armazenamento for atingido ou se um [limite de IOPS](../azure-subscription-service-limits.md#virtual-machine-disk-limits) for atingido, todas as máquinas virtuais que usam a conta de armazenamento serão afetadas. Além disso, se houver uma interrupção de serviço que afete um carimbo de armazenamento que inclua a conta de armazenamento específica, várias máquinas virtuais poderão ser afetadas.

###Você está usando um balanceador de carga ou uma fila entre cada camada do aplicativo?
Usar os balanceadores de carga ou as filas entre cada camada do aplicativo permite que você dimensione cada camada do aplicativo de forma fácil e independente. Você deve escolher entre essas tecnologias com base em suas necessidades de latência, de complexidade e de distribuição (ou seja, com base no tamanho da distribuição do seu aplicativo). Em geral, as filas tendem a ter latência mais alta e a adicionar complexidade mas oferecem vantagens a você pois permitem mais resiliência e distribuir seu aplicativo por áreas maiores (como em regiões). Se você quiser saber mais sobre como usar os balanceadores de carga internos ou as filas, leia [Visão geral do balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md) e [Filas do Azure e filas do Barramento de Serviço — comparações e contrastes](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

__O que acontece se você não usar um balanceador de carga ou uma fila entre cada camada do aplicativo?__ Sem um balanceador de carga, ou uma fila, entre cada camada do aplicativo, é difícil dimensionar seu aplicativo para cima ou para baixo e distribuir a carga entre vários computadores. Não fazer isso pode levar a um provisionamento inadequado de seus recursos e ao risco de tempo de inatividade, ou de uma experiência ruim para o usuário, caso você tenha alterações inesperadas no tráfego ou falhas do sistema.
 
###Seus Bancos de Dados SQL estão usando a replicação geográfica ativa? 
A Replicação Geográfica Ativa permite que você configure até 4 bancos de dados secundários legíveis, nas mesmas regiões ou em regiões diferentes. Os bancos de dados secundários estão disponíveis no caso de uma interrupção de serviço ou da incapacidade de conectar ao banco de dados primário. Se você quiser saber mais sobre a replicação geográfica ativa do Banco de Dados SQL, leia [Visão geral: replicação geográfica ativa para o Banco de Dados SQL](../sql-database/sql-database-geo-replication-overview.md).
 
 __O que acontece se você não usar a replicação geográfica ativa com seus bancos de dados SQL?__ Sem a replicação geográfica ativa, se seu banco de dados primário ficar offline (manutenção planejada, interrupção do serviço, falha de hardware etc.), o banco de dados do aplicativo ficará offline até que você consiga colocar seu banco de dados primário online em um estado íntegro.
 
###Você está usando um cache (Cache Redis do Azure) na frente de seus bancos de dados?
Se seu aplicativo tiver uma alta carga de banco de dados onde a maioria das chamadas de banco de dados é composta por leituras, você poderá aumentar a velocidade do seu aplicativo e reduzir a carga no banco de dados com a implementação de uma camada de cache na frente do seu banco de dados para transferir as operações de leitura. Você pode aumentar a velocidade do seu aplicativo e reduzir a carga do banco de dados (aumentando a escala que ele pode manipular), colocando uma camada de cache na frente do seu banco de dados. Se você quiser saber mais sobre o Cache Redis do Azure, leia [Diretrizes de caching](../best-practices-caching.md).
 
 __O que acontece se você não usar um cache na frente do seu banco de dados?__ Se seu computador de banco de dados for poderoso o suficiente para lidar com a carga de tráfego colocada nele, seu aplicativo responderá normalmente, embora isso possa significar que, em cargas menores, você estará pagando por um computador de banco de dados mais caro do que o necessário. Se seu computador de banco de dados não for poderoso o suficiente para lidar com a carga,então você começará a ter uma experiência de usuário insatisfatória com o aplicativo (latência, tempos limite e, possivelmente, tempo de inatividade do serviço).
 
###Você contata o suporte do Microsoft Azure quando espera um evento de alta escala?
O suporte do Azure pode ajudar você a aumentar os limites do seu serviço para lidar com eventos planejados de tráfego alto (como lançamentos de novos produtos ou feriados especiais). O suporte do Azure também conecta você a especialistas que podem ajudá-lo a examinar seu design com sua equipe de conta e encontrar a melhor solução para atender às suas necessidades de eventos de larga escala. Se quiser saber mais sobre como contatar o suporte do Azure, leia as [Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/).

__O que acontece se você não contatar o Suporte do Azure para um evento de larga escala?__ Se você não comunicar um evento de alto tráfego, ou não se planejar para ele, corre o risco de atingir determinados [limites de serviços do Azure](../azure-subscription-service-limits.md), criando uma experiência de usuário insatisfatória (ou pior, tempo de inatividade) durante o evento. As revisões de arquitetura e a comunicação prévia sobre picos podem ajudar a reduzir esses riscos.

###Você está usando uma CDN (Rede de Distribuição de Conteúdo) do Azure na frente de seus ativos estáticos e blobs de armazenamento voltados para a Web?
Usar uma CDN ajuda você a retirar a carga de seus servidores ao armazenar seu conteúdo em cache nos locais POP/borda da CDN localizados no mundo inteiro. Você pode fazer isso para reduzir a latência, aumentar a escalabilidade, diminuir a carga do servidor e como parte de uma estratégia para a proteção contra negação de ataques de negação de serviço (DOS). Se você quiser saber mais sobre como usar a CDN do Azure para aumentar a resiliência e diminuir a latência do cliente, leia [Visão geral da CDN (Rede de Distribuição de Conteúdo) do Azure](../cdn/cdn-overview.md).

__O que acontece se você não usar uma CDN?__ Se você não estiver usando uma CDN, todo seu tráfego de clientes virá diretamente para seus recursos. Isso significa que você verá cargas maiores nos servidores, o que reduz a escalabilidade deles. Além disso, seus clientes podem enfrentar latências maiores, já que as CDNs oferecem locais em todo o mundo que provavelmente estarão mais perto de seus clientes.

##Próximas etapas:
Se quiser ler mais sobre como projetar seus aplicativos para a alta disponibilidade, leia [Alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0622_2016-->