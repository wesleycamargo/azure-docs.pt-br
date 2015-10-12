<properties 
	pageTitle="Controle o crescimento explosivo com bancos de dados elásticos" 
	description="Um pool de bancos de dados elástico do Banco de Dados SQL do Azure é um conjunto de recursos disponíveis compartilhados por um grupo de bancos de dados elásticos." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/23/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Controle o crescimento explosivo com bancos de dados elásticos

Se você for um desenvolvedor de SaaS com dezenas, centenas ou até milhares de bancos de dados, um Pool de Banco de Dados Elástico simplificará o processo de criação, manutenção e gerenciamento de desempenho entre esses bancos de dados dentro de um orçamento que você controle.

Um padrão comum de aplicação do SaaS é que cada banco de dados tenha um cliente diferente, cada um com variáveis e imprevisíveis taxas consumo de recursos (CPU/IO/Memória resumida com DTU). Com esses picos e quedas de demanda para cada banco de dados, pode ser difícil de prever e provisionar recursos. Você se depara com duas opções: o provisionamento excessivo de recursos de banco de dados com base no uso de pico, o que resulta em pagamentos desnecessários. Ou o provisionamento insuficiente para poupar custos a detrimento do desempenho e da satisfação do cliente durante picos.

A Microsoft criou os Pools de Banco de Dados Elástico especificamente para ajudá-lo a resolver esse problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]


Pools de banco de dados elástico fornecem uma solução para clientes que precisam garantir que seus bancos de dados obtenham os recursos de desempenho de que precisam, quando precisam, ao mesmo tempo também fornecendo um mecanismo de alocação de recurso simples e um orçamento previsível. O dimensionamento de desempenho sob demanda dos bancos de dados individuais dentro de um pool de banco de dados flexível é possível porque cada banco de dados dentro de um pool usa eDTUs de um conjunto compartilhado associado ao pool. Isso permite que os bancos de dados sob carga pesada consumam mais para atender a demanda, enquanto bancos de dados sob carga leve consumam menos e bancos de dados sem carga não consomem qualquer eDTU. Ao provisionar recursos para o pool, em vez de para bancos de dados únicos, não apenas simplifica o gerenciamento de vários bancos de dados, como também proporciona um orçamento previsível para uma carga de trabalho de outra forma imprevisível.

Se forem necessários mais eDTUs para acomodar as necessidades de um pool (bancos de dados adicionais são adicionados a um pool ou os bancos de dados existentes começam a usar mais eDTUs), eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto negativo sobre os bancos de dados. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento.

![bancos de dados que compartilham eDTUs][1]

Bancos de dados que são ótimos candidatos a pools de banco de dados elástico normalmente têm períodos de atividade e períodos de inatividade. Considere o exemplo acima, em que se pode ver a atividade de um único banco de dados, de quatro bancos de dados e, por fim, de um agrupamento de bancos de dados elásticos com 20 bancos de dados. Esses bancos de dados com atividade variável ao longo do tempo são ótimos candidatos para pools de banco de dados elástico, porque não estão todos ativos ao mesmo tempo e podem compartilhar eDTUs. Nem todos os bancos de dados se encaixam nesse padrão. Há bancos de dados com uma demanda de recursos mais constante e esses bancos de dados são mais adequados para as camadas de serviço Basic, Standard e Premium, nas quais os recursos são atribuídos individualmente. Para obter assistência para determinar se seus bancos de dados se beneficiariam de um pool de banco de dados elástico, consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).

Você pode criar um pool de banco de dados elástico em minutos usando o portal do Microsoft Azure, PowerShell ou C#. Para obter detalhes, confira [Criar e gerenciar um pool de banco de dados elástico](sql-database-elastic-pool-portal.md). Para saber mais sobre pools de banco de dados elástico, incluindo detalhes sobre APIs e erros, consulte [Referência de pool de banco de dados elástico](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

## Gerenciar facilmente uma grande quantidade de bancos de dados com as ferramentas de banco de dados elástico

Além de fornecer um uso de recursos mais eficiente e um desempenho previsível, os Pools de Banco de Dados Elástico facilitam o desenvolvimento de aplicativos SaaS com ferramentas que simplificam a criação e o gerenciamento de sua camada de dados. A execução de tarefas de manutenção e a implementação de alterações em um conjunto grande de bancos de dados, um processo historicamente complexo e muito demorado, foi reduzido à execução de scripts em trabalhos elásticos. A capacidade de criar e executar um Trabalho de Banco de Dados Elástico elimina grande parte do trabalho mais pesado associado à administração de centenas ou milhares de bancos de dados. Para saber mais sobre o serviço de trabalhos de banco de dados elástico que permite a execução de scripts Transact-SQL em todos os bancos de dados elásticos de um pool, consulte a [Visão geral de trabalhos de bancos de dados elásticos](sql-database-elastic-jobs-overview.md).

Um conjunto avançado e eficaz de ferramentas de desenvolvedor para a implementação de padrões de aplicativos de bancos de dados elásticos também está disponível. Para bancos de dados fragmentados, outras ferramentas, como a ferramenta de mesclagem e divisão, oferecem a capacidade de dividir os dados de um fragmento e mesclá-los em outro. Isso reduz significativamente o trabalho do gerenciamento de bancos de dados fragmentados em larga escala. Para saber mais, confira o [Mapa de tópicos sobre ferramentas de banco de dados elásticos](sql-database-elastic-scale-documentation-map.md).

## Recursos de continuidade de negócios para bancos de dados em um pool

Atualmente em visualização, os bancos de dados elásticos dão suporte à maioria dos [recursos de continuidade de negócios](https://msdn.microsoft.com/library/azure/hh852669.aspx) disponíveis para bancos de dados únicos em servidores V12.

### Backup e restauração de bancos de dados ([Recuperação Pontual](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_PITR))

O sistema realiza backup automático dos bancos de dados em um pool de banco de dados elástico, e a política de retenção de backup é a mesma que a camada de serviços correspondente para bancos de dados únicos. Mais especificamente, um banco de dados elástico em um pool básico pode ser restaurado para qualquer ponto de restauração dentro dos últimos sete dias, um banco de dados elástico em um pool padrão pode ser restaurado para qualquer ponto de restauração dentro dos últimos 14 dias e um banco de dados elástico em um pool Premium pode ser restaurado para qualquer ponto de restauração dentro dos últimos 35 dias. Durante a visualização, os bancos de dados em um pool serão restaurados para um novo banco de dados no mesmo pool. Bancos de dados descartados sempre serão restaurados como um banco de dados autônomo fora do pool para o nível de desempenho mais baixo para essa camada de serviço. Por exemplo, um banco de dados elástico em um pool padrão que é descartado será restaurado como um banco de dados S0. Você pode executar operações de restauração de bancos de dados pelo Portal do Azure ou programaticamente usando a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.

### [Restauração geográfica](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_GEO)

A Restauração Geográfica permite recuperar o banco de dados de um pool para um servidor em uma região diferente. Durante a visualização, para restaurar um banco de dados de um pool em um servidor diferente, o servidor de destino de ter um pool com o mesmo nome do pool de origem. Se necessário, crie um novo pool no servidor de destino e dê a ele o mesmo nome antes de restaurar o banco de dados. Caso o servidor de destino não contenha um pool com o mesmo nome, a operação de Restauração Geográfica falhará. Você pode executar operações de Restauração Geográfica usando o Portal do Azure ou a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.


### [Replicação Geográfica](https://msdn.microsoft.com/library/azure/dn783447.aspx)

Os bancos de dados que já tiverem o recurso de Replicação Geográfica habilitado podem ser movidos para dentro e para fora de um Pool de Banco de Dados Elástico e a replicação continuará a funcionar da mesma maneira. Você pode habilitar a Replicação Geográfica em um banco de dados que já está no pool, se o servidor de destino que você especificar tiver um pool com o mesmo nome do pool de origem.

### Importação e exportação

É possível exportar um banco de dados de dentro de um pool. Atualmente, não há suporte para a importação de um banco de dados diretamente para um pool, mas você pode importar para um único banco de dados e, em seguida, mover o banco de dados para um pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=Oct15_HO1-->