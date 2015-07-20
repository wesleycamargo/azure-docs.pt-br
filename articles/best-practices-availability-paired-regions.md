<properties
	pageTitle="Melhore a continuidade de negócios com pares da região do Azure"
	description="Use pares regionais para manter aplicativos resilientes durante falhas de data centers."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# Melhorar a disponibilidade usando Pares da Região do Azure

## As regiões em pares do Azure explicadas

O Azure opera em várias regiões geográficas em todo o mundo. Uma região geográfica do Azure é uma área definida do mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais data centers.

Cada região do Azure está emparelhada com outra região na mesma região geográfica (com exceção do Sul do Brasil que está associado a uma região fora de sua região geográfica), formando um par regional.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – Diagrama do par da região do Azure



| painel Geografia do app&#39;s selecionado | Regiões emparelhadas | |
| :-------------| :-------------   | :-------------   |
| América do Norte | Centro-Norte dos EUA | Centro-Sul dos Estados Unidos |
| América do Norte | Leste dos EUA | Oeste dos EUA |
| América do Norte | Leste dos EUA 2 | Centro dos EUA |
| Europa | Norte da Europa | Europa Ocidental |
| Ásia | Sudeste da Ásia | Ásia Oriental |
| China | China Oriental | Norte da China |
| Japão | Leste do Japão | Oeste do Japão |
| Brasil | Sul do Brasil (1) | Centro-Sul dos Estados Unidos |
| Austrália | Leste da Austrália | Sudeste da Austrália|
| Governo dos EUA | Gov do Iowa nos EUA | Gov da Virgínia nos EUA |

Tabela 1 - Mapeamento de pares regionais do Azure

> (1) O Sul do Brasil é exclusivo porque ele está associado a uma região fora de sua própria região geográfica. Observe que a região secundária do Sul do Brasil é Centro-Sul dos Estados Unidos mas da região secundária do Centro-Sul dos Estados Unidos não é o Sul do Brasil.

É recomendável que você replique as cargas de trabalho entre os pares regionais para se beneficiar das políticas de isolamento e a disponibilidade do Azure. Por exemplo, as atualizações do sistema Azure planejadas são implantadas em sequência (não ao mesmo tempo) em regiões emparelhadas. Isso significa que, mesmo no caso de uma atualização falhar, ambas as regiões não serão afetadas simultaneamente. Além disso, no caso improvável de uma interrupção ampla, a recuperação de pelo menos uma região de cada par é priorizada.

## Exemplo de par regional
A Figura 2 a seguir mostra um par regional hipotético. Os números verdes realçam as atividades entre regiões dos três serviços do Azure (computação do Azure, armazenamento e banco de dados) e como eles são configurados para replicar entre regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.


![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

![1Verde](./media/best-practices-availability-paired-regions/1Green.png) **Computação do Azure** – Você deve provisionar recursos de computação adicionais com antecedência para garantir que recursos estejam disponíveis em outra região durante um desastre. É recomendável que você implante esses recursos em uma região emparelhada para a recuperação de desastres.

![2Verde](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento do Azure** -armazenamento com redundância geográfica (GRS) é configurado por padrão quando é criada uma conta de armazenamento do Azure. Com o GRS, seus dados são replicados três vezes dentro da região primária e três vezes na região emparelhada. O GRS só permite replicação para a região emparelhada e não pode ser configurado de forma diferente. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](../storage/storage-redundancy.md).


![3Verde](./media/best-practices-availability-paired-regions/3Green.png) **Bancos de dados SQL do Azure** – Com a replicação geográfica do SQL do Azure, você pode configurar a replicação assíncrona de transações para outros servidores de banco de dados do Microsoft Azure. A replicação geográfica padrão só permite a replicação assíncrona para a região emparelhada. Com replicação geográfica premium, você pode configurar a replicação para qualquer região do mundo; no entanto, é recomendável que esses recursos sejam implantados em uma região emparelhada para a recuperação de desastres. Para obter mais informações, consulte [Replicação geográfica no Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Verde](./media/best-practices-availability-paired-regions/4Green.png) **Serviços de gerenciamento independentes** - o ARM (Gerenciador de Recursos do Azure) inerentemente fornece isolamento lógico dos componentes do gerenciamento de serviço entre regiões – nenhuma configuração é necessária. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## Benefícios de uma região emparelhada

Como mencionado na Figura 2.

![5Laranja](./media/best-practices-availability-paired-regions/5Orange.png) **Isolamento físico** – Sempre que possível, o Azure prefere pelo menos 300 milhas de separação entre os data centers em um par regional, embora isso não seja possível ou conveniente em todas as regiões. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).

![6Laranja](./media/best-practices-availability-paired-regions/6Orange.png) **Estado do aplicativo consistente** - Se você estiver usando um serviço que permita a replicação apenas para uma região emparelhada (como armazenamento com redundância geográfica), você deve garantir que todos os outros serviços também direcionam a região emparelhada para replicação. Isso garante que um estado consistente de aplicativo durante o failover.

![7Laranja](./media/best-practices-availability-paired-regions/7Orange.png) **Pedido de recuperação da região** – No caso de uma interrupção ampla, a recuperação de uma região é priorizada de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![8Laranja](./media/best-practices-availability-paired-regions/8Orange.png) **Atualizações sequenciais** – As atualizações planejadas do sistema Azure são distribuídas em regiões emparelhados sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e falhas lógicas no caso de uma atualização incorreta.


![9Laranja](./media/best-practices-availability-paired-regions/9Orange.png) **Residência dados** – Uma região reside na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de vigência fiscal e legal.

<!---HONumber=July15_HO2-->