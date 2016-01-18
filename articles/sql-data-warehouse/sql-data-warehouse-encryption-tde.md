<properties 
   pageTitle="Introdução ao Transparent Data Encryption (TDE) em SQL Data Warehouse| Microsoft Azure" 
   description="Introdução aos dados TDE (Transparent Data Encryption) no SQL Data Warehouse" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/04/2016" 
   ms.author="twounder"/>
 
# Introdução aos dados TDE (Transparent Data Encryption) no SQL Data Warehouse
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

O Transparent Data Encryption (TDE) do SQL Data Warehouse do Azure ajuda a proteger contra atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de Banco de Dados SQL. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)].

##Habilitando a criptografia

Para habilitar a TDE para um SQL Data Warehouse, execute estas etapas:

1. Abra o banco de dados no [Portal clássico do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**	
3. Selecione a opção **Criptografia transparente de dados** ![][1] 
4. Selecione a configuração **Ativado** ![][2] 
5. Selecione **Salvar** ![][3]  

##Desabilitando a criptografia

Para desabilitar a TDE para um SQL Data Warehouse, execute estas etapas:

1. Abra o banco de dados no [Portal clássico do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**	
3. Selecione a opção **Criptografia transparente de dados** ![][1] 
4. Selecione a configuração **Desativado** ![][4] 
5. Selecione **Salvar** ![][5]  




<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_0107_2016-->