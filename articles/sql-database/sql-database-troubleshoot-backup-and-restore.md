<properties
	pageTitle="Solucionar problemas de backup e restauração com o Banco de Dados SQL do Azure"
	description="Saiba como recuperar um banco de dados de nuvem que enfrentou erros e falhas usando réplicas e backups no Banco de Dados SQL."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="daleche"/>

# Restaurar um banco de dados para um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar-se de uma interrupção do datacenter

O Banco de Dados SQL mantém réplicas de seu banco de dados para que você possa recuperar-se de interrupções e erros do usuário. As opções disponíveis dependem da camada de serviço do banco de dados e das opções escolhidas. Confira [Visão Geral de Continuidade de Negócios](sql-database-business-continuity.md) para obter detalhes e considerações sobre o design.

##Para restaurar um banco de dados em um momento anterior
1.	No [Portal do Azure](https://azure.microsoft.com/), clique em **Bancos de dados SQL**.
2.	Selecione seu banco de dados na lista e clique em **Restaurar**.
3.	Digite um novo nome para o banco de dados, escolha a data e a hora da restauração e clique em **Criar.**
4.	Faça ajustes no aplicativo conforme necessário para fazer referência ao novo banco de dados. Confira [Recuperar um banco de dados de um erro do usuário](sql-database-user-error-recovery.md).

##Para restaurar um banco de dados excluído acidentalmente
1.	No [Portal do Azure](https://azure.microsoft.com/), clique em **Servidores SQL**.
2.	Escolha na lista o servidor que hospedou o banco de dados.
3.	Na folha do Servidor, role a tela para baixo e clique em **Bancos de dados excluídos**.
4.	Escolha o banco de dados que será restaurado e clique em **Criar**.
5.	Faça ajustes no aplicativo conforme necessário para fazer referência ao novo banco de dados. Confira [Recuperar um banco de dados de um erro do usuário](sql-database-user-error-recovery.md).

##Para recuperar-se da interrupção de um datacenter regional
Com os bancos de dados Standard e Premium, se você configurar secundários replicados geograficamente, poderá recuperar-se usando esses secundários. Isso permite que você restaure um banco de dados com menos possibilidade de perda de dados. Confira [Recuperar um Banco de dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md) para obter detalhes.

O Azure também fornece backups de cada banco de dados em uma região diferente (um backup com redundância geográfica). Você pode criar um novo banco de dados a partir desses backups, o que é chamado de restauração geográfica. No entanto, é provável que você enfrente perda de dados se depender apenas desse método.

**Para recuperar um banco de dados usando a restauração geográfica:**

- No [Portal do Azure](https://azure.microsoft.com/), clique em **Novo**, clique em **Dados e Armazenamento**, clique em **Banco de Dados SQL** e selecione **Backup** como a origem do banco de dados. Confira [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md) para obter detalhes.

<!---HONumber=AcomDC_0218_2016-->