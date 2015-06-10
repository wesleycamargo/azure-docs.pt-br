<properties 
	pageTitle="Pool de bancos de dados elásticos do Banco de Dados SQL do Azure (visualização)" 
	description="Um pool de bancos de dados elástico é um conjunto de recursos disponíveis compartilhados por um grupo de bancos de dados elásticos." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/11/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Pools elásticos do Banco de Dados SQL (visualização)

Se você for um desenvolvedor de SaaS com dezenas, centenas ou até milhares de bancos de dados, um pool elástico simplificará o processo de criação, manutenção e gerenciamento de desempenho entre esses bancos de dados dentro de um orçamento que você controle.

Um padrão comum de aplicação do SaaS é que cada banco de dados tenha um cliente diferente, cada um com variáveis e imprevisíveis taxas consumo de recursos (CPU/IO/Memória resumida com eDTU). Com esses picos e quedas de demanda para cada banco de dados, pode ser difícil de prever e provisionar recursos. Você se depara com duas opções: o provisionamento excessivo de recursos de banco de dados com base no uso de pico, o que resulta em pagamentos desnecessários. Ou o provisionamento insuficiente para poupar custos a detrimento do desempenho e da satisfação do cliente durante picos.

A Microsoft criou pools elásticos especificamente para ajudá-lo a resolver esse problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Um pool de bancos de dados elástico é um conjunto de recursos disponíveis compartilhados pelos bancos de dados elásticos no pool. Você pode adicionar bancos de dados ao pool ou removê-los a qualquer momento. Esses bancos de dados elásticos compartilham os recursos (expressados em unidades de taxa de transferência de banco de dados elásticos ou eDTUs) e a capacidade de armazenamento do pool, mas cada banco de dados elástico usa apenas os recursos necessários em um determinado momento, deixando recursos livres para outros bancos de dados flexíveis quando eles forem necessários. Em vez de provisionar bancos de dados demais e pagar por recursos que não são utilizados, você aloca e paga um preço previsível pelos recursos agregados do pool elástico. Isso estende o custo para que você adquira um modelo de negócios e cada banco de dados elástico obtém adaptabilidade de desempenho.

Os bancos de dados que são bons candidatos para pools de bancos de dados elásticos normalmente estão ativos menos de 50% do tempo. Um padrão típico de atividade é que os bancos de dados passem algum tempo inativos, ativos com baixa demanda de recursos e ativo com alta demanda de recursos.

Nem todos os bancos de dados se encaixam nesse padrão. Há bancos de dados com uma demanda de recursos mais constante e esses bancos de dados são mais adequados para as camadas de serviço Basic, Standard e Premium, nas quais os recursos são atribuídos individualmente.

Você pode criar um pool elástico em minutos usando o portal do Microsoft Azure ou o PowerShell. Para obter detalhes, confira [Criar e gerenciar um pool elástico](sql-database-elastic-pool-portal.md). Para saber mais sobre pools de banco de dados flexível, incluindo detalhes sobre APIs e erros, confira a [Referência de bancos de dados elásticos](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]No momento, os pools elásticos estão em versão de visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

## Gerenciar facilmente uma grande quantidade de bancos de dados com ferramentas elásticas

Além de fornecer um uso de recursos mais eficiente e um desempenho previsível, os pools elásticos facilitam o desenvolvimento de aplicativos SaaS com ferramentas que simplificam a criação e o gerenciamento de sua camada de dados. A execução de tarefas de manutenção e a implementação de alterações em um conjunto grande de bancos de dados, um processo historicamente complexo e muito demorado, foi reduzido à execução de scripts em trabalhos elásticos. A capacidade de criar e executar um trabalho elástico elimina grande parte do trabalho mais pesado associado à administração de centenas ou milhares de bancos de dados. Para saber mais sobre o serviço de trabalhos elásticos que permite a execução de scripts T-SQL em todos os bancos de dados elásticos de um pool, confira [Visão geral de trabalhos de bancos de dados elásticos](sql-database-elastic-jobs-overview.md).

Um conjunto avançado e eficaz de ferramentas de desenvolvedor para a implementação de padrões de aplicativos de bancos de dados elásticos também está disponível. Para bancos de dados fragmentados, outras ferramentas, como a ferramenta de mesclagem e divisão, oferecem a capacidade de dividir os dados de um fragmento e mesclá-los em outro. Isso reduz significativamente o trabalho do gerenciamento de bancos de dados fragmentados em larga escala. Para saber mais, confira o [Mapa de tópicos sobre ferramentas de banco de dados elásticos](sql-database-elastic-scale-documentation-map.md).

## Recursos de continuidade de negócios para bancos de dados elásticos

Atualmente, no modo de visualização, os bancos de dados elásticos (na camada padrão elástica) dão suporte à maioria dos recursos que estão disponíveis para bancos de dados da camada Standard.

### Backup e restauração de bancos de dados (Restauração Pontual)

Os bancos de dados elásticos são armazenados em backup automaticamente pelo sistema e a política de retenção de backup é a mesma que em bancos de dados da camada Standard. Durante a visualização, os bancos de dados elásticos em um pool serão restaurados como novos bancos de dados elásticos no mesmo pool. Os bancos de dados elásticos removidos sempre serão restaurados como um banco de dados autônomo fora do pool como um banco de dados S0 Standard. Você pode executar operações de restauração de bancos de dados pelo Portal do Azure ou programaticamente usando a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.

### Restauração geográfica

A Restauração Geográfica permite recuperar o banco de dados de um pool para um servidor em uma região diferente. Durante a visualização, para restaurar um banco de dados de um pool em um servidor diferente, o servidor de destino de ter um pool com o mesmo nome do pool de origem. Se necessário, crie um novo pool no servidor de destino e dê a ele o mesmo nome antes de restaurar o banco de dados. Caso o servidor de destino não contenha um pool com o mesmo nome, a operação de Restauração Geográfica falhará. Você pode executar operações de Restauração Geográfica usando o Portal do Azure ou a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.


### Replicação Geográfica

Os bancos de dados que já tiverem o recurso de Replicação Geográfica habilitado podem ser movidos para dentro e para fora de um pool elástico e a replicação continuará a funcionar da mesma maneira. Na visualização, não é possível habilitar a Replicação Geográfica em um banco de dados elástico que já esteja em um pool.

<!---HONumber=58-->