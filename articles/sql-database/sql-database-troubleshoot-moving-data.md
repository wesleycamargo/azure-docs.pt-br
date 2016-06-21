<properties
	pageTitle="Mova bancos de dados entre servidores, entre as assinaturas e para dentro e para fora do Azure."
	description="Passos rápidos para copiar, mover e migrar dados e bancos de dados no Banco de Dados SQL."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="v-shysun"/>

# Mover bancos de dados entre servidores, entre as assinaturas e para dentro e para fora do Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##Para mover um banco de dados para um servidor diferente na mesma assinatura
- No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, selecione um banco de dados na lista e, em seguida, clique em **Copiar**. Para obter mais detalhes, consulte [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md).

## Para mover um banco de dados entre assinaturas
- No [Portal do Azure](https://portal.azure.com), clique em **servidores SQL** e, em seguida, selecione o servidor que hospeda seu banco de dados da lista. Clique em **Mover**, então selecione os recursos a mover e a assinatura para a qual movê-los.

## Para migrar um Banco de Dados SQL para o Azure
- Determine a compatibilidade do banco de dados e, em seguida, escolha o método de migração correto com base em suas necessidades. Siga as diretrizes e as opções em [Migração de banco de dados do SQL Server](sql-database-cloud-migrate.md).

## Para criar uma cópia de um banco de dados para uso fora do Azure
- [Exporte um arquivo BACPAC.](sql-database-export.md)

<!---HONumber=AcomDC_0608_2016-->