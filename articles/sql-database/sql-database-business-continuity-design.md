<properties 
   pageTitle="Projeto de banco de dados SQL para continuidade de negócios"
	description="Diretrizes para escolher nesta seção, orientações serão fornecida para como escolher quais recursos de BCDR devem ser usados e quando. Isso inclui descrições de o que você obtém automaticamente usando o banco de dados SQL."
	services="sql-database"
	documentationCenter=""
	authors="elfisher"
	manager="jeffreyg"
	editor="monicar"/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="07/14/2015"
	ms.author="elfish"/>

#Projeto para continuidade dos negócios

Para criar seu aplicativo para continuidade dos negócios, é necessário que você responda às seguintes perguntas:

1. Qual recurso de continuidade dos negócios é adequado para proteger meu aplicativo contra interrupções?
2. Qual o nível de redundância e a topologia de replicação eu devo usar?

##Quando usar a restauração geográfica

O banco de dados SQL fornece uma proteção básica interna de cada banco de dados por padrão. Isso é feito ao armazenar os backups de banco de dados no armazenamento do Azure com redundância geográfica (GRS). Se você escolher esse método, nenhuma configuração especial ou alocação adicional de recursos é necessária. Com esses backups, é possível recuperar o banco de dados em qualquer região usando o comando de restauração geográfica. Use a seção [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para obter os detalhes do uso de restauração geográfica para recuperar seu aplicativo.

Você deve usar a proteção interna caso seu aplicativo atenda aos seguintes critérios:

1. Ele não é considerado crítico. Ele não tem um SLA vinculado, portanto, o tempo de inatividade de 24 horas ou mais não resultará em responsabilidade financeira.
2. A taxa de alteração de dados é baixa (por exemplo, transações por hora). O RPO de 1 hora não resultará em uma grande perda de dados.
3. O aplicativo é de baixo custo e não é possível justificar o custo adicional de replicação geográfica 

> [AZURE.NOTE]A restauração geográfica não aloca previamente a capacidade de computação em nenhuma região específica para restaurar bancos de dados ativos do backup durante a interrupção. O serviço irá gerenciar a carga de trabalho associada às solicitações de restauração geográfica de maneira a minimizar o impacto sobre os bancos de dados existentes nessa região e suas demandas de capacidade terão prioridade. Portanto, o tempo de recuperação do banco de dados dependerá de quantos outros bancos de dados serão recuperados na mesma região ao mesmo tempo.

##Quando usar a replicação geográfica

A replicação geográfica cria um banco de dados de réplica (secundário) em uma região diferente do principal. Ele garante que seu banco de dados terá os dados e recursos de computação necessários para oferecer suporte às cargas de trabalho do aplicativo após a recuperação. Consulte a seção [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para usar o failover para recuperar seu aplicativo.

Você deve usar a replicação geográfica caso seu aplicativo atenda aos seguintes critérios:

1. Ele é de crítico. Ele tem um SLA vinculado com RTO e RPO agressivos. A perda de dados e a disponibilidade resultarão em responsabilidade financeira. 
2. A taxa de alteração de dados é alta (por exemplo, transações por minuto ou segundos). O RPO de 1 h associado à proteção padrão provavelmente resultará em perda de dados inaceitável.
3. O custo associado ao uso da replicação geográfica é significativamente menor do que a responsabilidade financeira potencial e as perdas associadas do negócio.

> [AZURE.NOTE]Se seu aplicativo usa bancos de dados de camada básica, não há suporte para replicação geográfica

##Quando escolher replicação geográfica Padrão versus Ativa

Os bancos de dados de camada padrão não têm a opção de usar a replicação geográfica ativa, de forma que, se seu aplicativo usa bancos de dados padrão e atende aos critérios acima, ele deve habilitar a replicação geográfica padrão. Por outro lado, os bancos de dados Premium podem escolher qualquer uma das opções. A replicação geográfica padrão foi projetada como uma solução de recuperação de desastres mais simples e econômica, especialmente adequada a aplicativos que a utilizam somente para se protegerem contra eventos não planejados, como interrupções. Com a replicação geográfica padrão, é possível usar somente a região pareada de DR para a recuperação e ter a capacidade de criar mais de um banco de dados secundário. Esse último recurso é essencial para o cenário de atualização de aplicativo. Portanto, se esse cenário for essencial para seu aplicativo você deve habilitar a replicação geográfica ativa em vez disso. Consulte [Atualização de aplicativo sem tempo de inatividade](sql-database-business-continuity-application-upgrade.md) para obter detalhes adicionais.

> [AZURE.NOTE]A replicação geográfica também oferece suporte a acesso somente leitura ao banco de dados secundário, proporcionando capacidade adicional para as cargas de trabalho somente leitura.

##Como habilitar a replicação geográfica

Você pode habilitar replicação geográfica usando o Portal do Azure ou chamando a API REST ou comando do PowerShell.

###Portal do Azure

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. Faça logon no [Portal do Azure](https://portal.Azure.com).
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, selecione **Bancos de Dados SQL**
3. Navegue até a folha de banco de dados, selecione o **Mapa de replicação geográfica** e clique em **Configurar a replicação geográfica**.
4. Navegue até a folha de replicação geográfica. Selecione a região de destino. 
5. Navegue até a folha Criar Secundário. Selecione um servidor existente na região de destino ou crie um novo.
6. Selecione o tipo de secundário (*Legível* ou *Não legível*)
7. Clique em **Criar** para concluir a configuração

> [AZURE.NOTE]A região pareada de DR na folha de replicação geográfica será marcada como *recomendada*. Se você usar um banco de dados de camada Premium, pode escolher uma região diferente. Se você estiver usando um banco de dados padrão, não pode alterá-lo. O banco de dados Premium terá uma opção do tipo secundário (*Legível* ou *Não legível*). O banco de dados padrão só pode selecionar um secundário *não legível*.


###PowerShell

Use o cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) do PowerShell para automatizar a configuração de replicação geográfica.

Para criar a replicação geográfica com um secundário não legível para um banco de dados premium ou padrão:
		
		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy -OfflineSecondary
Para criar a replicação geográfica com um secundário legível de um banco de dados Premium:

		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy
		 
Esse comando é assíncrono. Depois que ele retornar, use o cmdlet [Get-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720235.aspx) para verificar o status dessa operação. O campo ReplicationState do objeto retornado terá o valor CATCH\_UP quando a operação for concluída.

		Get-AzureSqlDatabaseCopy -ServerName "PrimaryServerName" -DatabaseName "PrimaryDatabaseName" -PartnerServer "SecondaryServerName"


###API REST 

Use o API [Iniciar cópia de banco de dados](https://msdn.microsoft.com/library/azure/dn509576.aspx) para criar uma configuração de replicação geográfica programaticamente.

Esse API é assíncrono. Depois que ele retornar, use o API [Obter cópia de banco de dados](https://msdn.microsoft.com/library/azure/dn509570.aspx) API para verificar o status dessa operação. O campo ReplicationState do corpo da resposta terá o valor CATCH\_UP quando a operação for concluída.


##Como escolher a configuração de failover 

Ao projetar seu aplicativo para continuidade dos negócios, você deve considerar várias opções de configuração. A escolha dependerá da topologia de implantação do aplicativo e quais partes de seus aplicativos são mais vulneráveis a uma interrupção. Consulte [Criando soluções de nuvem para recuperação de desastres usando replicação geográfica](https://msdn.microsoft.com/library/azure/dn741328.aspx) para obter orientação.


 

<!---HONumber=September15_HO1-->