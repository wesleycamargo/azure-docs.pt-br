---
title: Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure | Microsoft Docs
description: Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: barbkess
editor: ''

ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglaslMS

---
# Habilitar TDE (Transparent Data Encryption) para Stretch Database no Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
> 
> 

O TDE (Transparent Data Encryption) ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. A chave de criptografia do banco de dados está protegida por um certificado do servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral da TDE, consulte [Transparent Data Encryption (TDE)].

## Habilitando a criptografia
Para habilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Abra o banco de dados no [Portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção **Criptografia transparente de dados** ![][1]
4. Selecione a configuração **Ativar** e, em seguida, selecione **Salvar** ![][2]

## Desabilitando a criptografia
Para desabilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Abra o banco de dados no [Portal do Azure](https://portal.azure.com)
2. Na folha do banco de dados, clique no botão **Configurações**
3. Selecione a opção **Transparent Data Encryption**
4. Selecione a configuração **Desativar** e, em seguida, selecione **Salvar**

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->

<!---HONumber=AcomDC_0810_2016-->