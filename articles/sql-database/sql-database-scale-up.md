<properties
	pageTitle="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure"
	description="Alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure mostra como escalar verticalmente seu banco de dados SQL. Alterando o tipo de preço de um banco de dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um banco de dados SQL

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

Este artigo mostra como alterar a camada de serviço e nível de desempenho do banco de dados SQL com o [Portal do Azure](https://portal.azure.com).

Use as informações em [Atualizar banco de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-server-portal.md) e [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para determinar o nível de desempenho e a camada de serviço apropriados para o Banco de Dados SQL do Azure.

> [AZURE.IMPORTANT] A alteração do nível de desempenho e da camada de serviço de um banco de dados SQL é uma operação online. Isso significa que seu banco de dados permanecerá online e disponível durante toda a operação, sem qualquer tempo de inatividade.

- Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
- Ao atualizar um banco de dados com [Replicação geográfica padrão](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou [Replicação geográfica](https://msdn.microsoft.com/library/azure/dn741339.aspx) habilitada, é necessário atualizar primeiro seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
- Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Finalizar uma relação de cópia contínuo](https://msdn.microsoft.com/library/azure/dn741323.aspx) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Você pode fazer até quatro alterações individuais de banco de dados (camada de serviço ou níveis de desempenho) em um período de 24 horas.
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

<!---HONumber=AcomDC_0224_2016-->