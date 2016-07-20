<properties
   pageTitle="Introdução ao Transparent Data Encryption (TDE) em SQL Data Warehouse| Microsoft Azure"
   description="Introdução aos dados TDE (Transparent Data Encryption) no SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/07/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# Introdução aos dados TDE (Transparent Data Encryption) no SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão Geral da Segurança](sql-data-warehouse-overview-manage-security.md)
- [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)
- [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Visão Geral da Auditoria](sql-data-warehouse-auditing-overview.md)
- [Clientes de nível inferior da auditoria](sql-data-warehouse-auditing-downlevel-clients.md)


O Transparent Data Encryption (TDE) do SQL Data Warehouse do Azure ajuda a proteger contra atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de Banco de Dados SQL. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é o AES-256. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)].

##Habilitando a criptografia

Para habilitar a TDE para um SQL Data Warehouse, siga estas etapas:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção **Criptografia transparente de dados** ![][1]
4. Selecione a configuração **Ativado** ![][2]
5. Selecione **Salvar** ![][3]

##Desabilitando a criptografia

Para desabilitar a TDE para um SQL Data Warehouse, siga estas etapas:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção **Criptografia transparente de dados** ![][1]
4. Selecione a configuração **Desativado** ![][4]
5. Selecione **Salvar** ![][5]

##DMVs de criptografia

A criptografia pode ser confirmada com as DMVs a seguir:

- [sys.databases]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0706_2016-->