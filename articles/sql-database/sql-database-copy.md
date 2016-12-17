---
title: Copiar um banco de dados SQL do Azure | Microsoft Docs
description: "Criar uma cópia de um Banco de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e6152f6539962653a582ba4921af82e4447fd76


---
# <a name="copy-an-azure-sql-database"></a>Copiar um Banco de Dados SQL do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-copy.md)
> * [Portal do Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Você pode usar os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) do Azure para criar uma cópia do seu banco de dados SQL. A cópia do banco de dados usa a mesma tecnologia como o recurso de replicação geográfica. Mas, ao contrário da replicação geográfica, ela encerra o link de replicação após a conclusão da fase de propagação. Portanto, o banco de dados de cópia é um instantâneo do banco de dados de origem a partir do momento da solicitação de cópia.  
Você pode criar a cópia do banco de dados no mesmo servidor ou em outro servidor. O nível de desempenho e a camada de serviço (tipo de preço) da cópia do banco de dados são iguais aos do banco de dados de origem por padrão. Ao usar a API, é possível selecionar um nível de desempenho diferente na mesma camada de serviço (edição). Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Neste ponto, é possível atualizar ou fazer o downgrade para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados independentemente.  

Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor lógico diferente, a entidade de segurança no novo servidor torna-se a proprietária do banco de dados no novo banco de dados. Se você usar [usuários de banco de dados independente](sql-database-manage-logins.md) para acesso aos dados, garanta que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário. Portanto, depois que a cópia for concluída, será possível acessá-la imediatamente com as mesmas credenciais. Se você usar o [Azure Active Directory](../active-directory/active-directory-whatis.md), será possível acabar completamente com a necessidade de gerenciar credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso baseado em logon geralmente não funcionará, porque os logons não existirão no novo servidor. Consulte [How to manage Azure SQL database security after disaster recovery (Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre)](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons ao copiar um banco de dados para um servidor lógico diferente. 

Para copiar um banco de dados SQL, será necessário o seguinte:

* Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
* Um banco de dados SQL para copiar. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Próximas etapas
* Confira [Copiar um banco de dados SQL do Azure usando o Portal do Azure](sql-database-copy-portal.md) para copiar um banco de dados usando o Portal do Azure.
* Confira [Copiar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
* Confira [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados usando o Transact-SQL.
* Confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons e usuários ao copiar um banco de dados para um servidor lógico diferente.

## <a name="additional-resources"></a>Recursos adicionais
* [Gerenciar logons](sql-database-manage-logins.md)
* [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
* [Exportar o banco de dados para um BACPAC](sql-database-export.md)
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--HONumber=Nov16_HO3-->


