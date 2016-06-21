<properties
	pageTitle="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure"
	description="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL. Alterando o tipo de preço de um banco de dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


As camadas de serviço e níveis de desempenho descrevem as características e recursos disponíveis para o banco de dados SQL e podem ser atualizados conforme as necessidades do seu aplicativo mudam. Para obter detalhes, consulte [Camadas de serviço](sql-database-service-tiers.md).

Observe que a alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Muito raramente, especialmente se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.


Use as informações em [Atualizar banco de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-server-portal.md) e [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para determinar o nível de desempenho e a camada de serviço apropriados para o Banco de Dados SQL do Azure.

- Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
- Ao atualizar um banco de dados com a [Replicação Geográfica](sql-database-geo-replication-overview.md) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
- Ao fazer downgrade de uma camada de serviço, primeiro você deve encerrar todos os relacionamentos de Replicação Geográfica. 
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
- Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo, você precisa do seguinte:**

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL do Azure. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).


## Alterar a camada de serviço e o nível de desempenho do banco de dados


Abra a folha Banco de Dados SQL do banco de dados que você deseja escalar verticalmente:

1.	Vá para o [Portal do Azure](https://portal.azure.com).
2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Bancos de dados SQL**.
2.	Clique no banco de dados que você deseja alterar.
3.	Na folha do Banco de dados SQL, clique em **Todas as configurações** e em **Tipo de preço (DTUs de escala)**:

    ![Tipo de preços][1]


1.  Selecione uma nova camada e clique em **Selecionar**:

    Clicar em **Selecionar** envia uma solicitação de escala para alterar a camada de banco de dados. Dependendo do tamanho do banco de dados, a operação de escala poderá demorar um pouco para ser concluída. Clique na notificação para obter detalhes e o status da operação de escala.

    > [AZURE.NOTE] Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![selecionar camada de preços][2]

3.	Na faixa de opções à esquerda, clique em **Notificações**:

    ![notificações][3]

## Verifique se o banco de dados está no tipo de preço selecionado

   Após a conclusão da operação de escala, inspecione e confirme se o banco de dados está na camada desejada:

2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Bancos de dados SQL**.
2.	Clique no banco de dados atualizado.
3.	Verifique o **Tipo de preço** e confirme se ele está definido como o tipo correto.

    ![novo preço][4]


## Próximas etapas

- Altere o tamanho máximo do banco de dados usando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Escalar horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectar-se a e consultar um banco de dados SQL com SSMS](sql-database-connect-query-ssms.md)
- [Exportar um banco de dados SQL do Azure](sql-database-export.md)

## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0608_2016-->