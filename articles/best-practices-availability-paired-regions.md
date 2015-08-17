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
A Figura 2 a seguir mostra um aplicativo hipotético, que usa o par regional para recuperação de desastre. Os números verdes realçam as atividades entre regiões dos três serviços do Azure (computação do Azure, armazenamento e banco de dados) e como eles são configurados para replicar entre regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.


![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## Atividades entre regiões
Como mencionado na Figura 2.

![1Verde](./media/best-practices-availability-paired-regions/1Green.png) **Computação do Azure (PaaS)** – Você deve provisionar recursos de computação adicionais com antecedência para garantir que recursos estejam disponíveis em outra região durante um desastre. Para obter mais informações, confira [Orientações Técnicas de Continuidade de Negócios do Azure](https://msdn.microsoft.com/library/azure/hh873027.aspx).

![2Verde](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento do Azure** - O GRS (armazenamento com redundância geográfica) é configurado por padrão quando uma conta de armazenamento do Azure é criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](../storage/storage-redundancy.md).


![3Verde](./media/best-practices-availability-paired-regions/3Green.png) **Bancos de dados SQL do Azure** – Com a replicação geográfica padrão do SQL do Azure, você pode configurar a replicação assíncrona de transações para uma região emparelhada. Com replicação geográfica premium, você pode configurar a replicação para qualquer região do mundo; no entanto, é recomendável que esses recursos sejam implantados em uma região emparelhada para a maioria dos cenários de recuperação de desastre. Para obter mais informações, consulte [Replicação geográfica no Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Verde](./media/best-practices-availability-paired-regions/4Green.png) **ARM (Gerenciador de Recursos do Azure)** - o ARM fornece inerentemente isolamento lógico dos componentes do gerenciamento de serviço entre regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## Benefícios de uma região emparelhada
Como mencionado na Figura 2.

![5Laranja](./media/best-practices-availability-paired-regions/5Orange.png) **Isolamento físico** – Sempre que possível, o Azure prefere pelo menos 300 milhas de separação entre os datacenters em um par regional, embora isso não seja possível ou conveniente em todas as regiões. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).

![6Laranja](./media/best-practices-availability-paired-regions/6Orange.png) **Replicação fornecida pela plataforma** - Alguns serviços, como o armazenamento com redundância geográfica, fornecem replicação automática para a região emparelhada.

![7Laranja](./media/best-practices-availability-paired-regions/7Orange.png) **Pedido de recuperação da região** – No caso de uma interrupção ampla, é priorizada a recuperação de uma região de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![8Laranja](./media/best-practices-availability-paired-regions/8Orange.png) **Atualizações sequenciais** – As atualizações planejadas do sistema Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e falhas lógicas na eventualidade de uma atualização incorreta.


![9Laranja](./media/best-practices-availability-paired-regions/9Orange.png) **Residência dados** – Uma região reside na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de vigência fiscal e legal.

<!---HONumber=August15_HO6-->