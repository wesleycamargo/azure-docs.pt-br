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
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 0356f3bdbb691b9bde4e906ed30b3b235180f8ba
ms.openlocfilehash: 9851ec0771c4fec4719aec8c8f43f2e3dd2604f0
ms.lasthandoff: 02/27/2017


---
# <a name="copy-an-azure-sql-database"></a>Copiar um Banco de Dados SQL do Azure

Você pode criar a cópia do banco de dados no mesmo servidor ou em outro servidor. A cópia do banco de dados é um instantâneo do banco de dados de origem a partir do momento da solicitação de cópia. O nível de desempenho e a camada de serviço (tipo de preço) da cópia do banco de dados são iguais aos do banco de dados de origem por padrão. Ao usar a API, é possível selecionar um nível de desempenho diferente na mesma camada de serviço (edição). Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Neste ponto, é possível atualizar ou fazer o downgrade para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados independentemente.  

Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor lógico diferente, a entidade de segurança no novo servidor torna-se a proprietária do banco de dados no novo banco de dados. Se você usar [usuários de banco de dados independente](sql-database-manage-logins.md) para acesso aos dados, garanta que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário. Portanto, depois que a cópia for concluída, será possível acessá-la imediatamente com as mesmas credenciais. Se você usar o [Azure Active Directory](../active-directory/active-directory-whatis.md), será possível acabar completamente com a necessidade de gerenciar credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso baseado em logon geralmente não funciona, porque os logons não existirão no novo servidor. Consulte [How to manage Azure SQL database security after disaster recovery (Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre)](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons ao copiar um banco de dados para um servidor lógico diferente. 

Você pode copiar um banco de dados SQL usando o [Portal do Azure](sql-database-copy-portal.md), o [PowerShell](sql-database-copy-powershell.md). ou o [T-SQL](sql-database-copy-transact-sql.md). 

## <a name="next-steps"></a>Próximas etapas

* Confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons e usuários ao copiar um banco de dados para um servidor lógico diferente.
* Para saber mais sobre logons, confira [Gerenciar logons](sql-database-manage-logins.md)
* Para exportar um banco de dados, confira [Exportar o banco de dados para um BACPAC](sql-database-export.md)

