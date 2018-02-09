---
title: "Habilitar o ajuste automático para o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Habilite o ajuste automático do Banco de Dados SQL do Azure com facilidade."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: b599273874a4b5a3bbcb78284d69b4c8d02b5f2c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="enable-automatic-tuning"></a>Habilitar o ajuste automático

O Banco de Dados SQL do Azure é um serviço de dados gerenciados automaticamente que monitora as consultas constantemente e identifica a ação que pode ser executada para melhorar o desempenho da carga de trabalho. Examine as recomendações e aplique-as manualmente ou permita que o Banco de Dados SQL do Azure aplique as ações corretivas automaticamente – isso é conhecido como **modo de ajuste automático**. O ajuste automático pode ser habilitado no nível do servidor ou do banco de dados.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar o ajuste automático no servidor
No nível do servidor, você pode escolher entre herdar ou não a configuração de ajuste automático de "Padrões do Azure". Os Padrões do Azure são: FORCE_LAST_GOOD_PLAN habilitado, CREATE_INDEX habilitado e DROP_INDEX desabilitado.

## <a name="configure-automiatic-tuning-e-mail-notifications"></a>Configurar o ajuste automático das notificações por email

Consulte [Ajuste automático das notificações por email](sql-database-automatic-tuning-email-notifications.md)

### <a name="azure-portal"></a>Portal do Azure
Para habilitar o ajuste automático no servidor de Banco de Dados SQL do Azure, navegue para o servidor no portal do Azure e, em seguida, selecione **Ajuste automático** no menu. Selecione as opções de ajuste automático que você deseja habilitar e selecione **Aplicar**:

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

As opções de ajuste automático no servidor são aplicadas a todos os bancos de dados do servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de dados individualmente.

### <a name="rest-api"></a>API REST
[Clique aqui para saber mais sobre como habilitar o ajuste automático em nível de servidor por meio da API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Habilitar o ajuste automático em um banco de dados individual

O Banco de Dados SQL do Azure permite especificar a configuração de ajuste automático individualmente em cada banco de dados. No nível do banco de dados, você pode escolher entre herdar ou não a configuração de ajuste automático do servidor pai, "Padrões do Azure". Os Padrões do Azure são: FORCE_LAST_GOOD_PLAN habilitado, CREATE_INDEX habilitado e DROP_INDEX desabilitado.

> [!NOTE]
> A recomendação geral é gerenciar a configuração de ajuste automático no nível de servidor, de forma que as mesmas definições de configuração possam ser aplicadas em cada banco de dados automaticamente. Configure o ajuste automático em um banco de dados individual caso ele seja diferente dos outros no mesmo servidor.
>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um banco de dados individual, navegue para o banco de dados no portal do Azure e, em seguida, selecione **Ajuste automático**. Configure um banco de dados individual para herdar as configurações do servidor selecionando a opção ou especifique a configuração de um banco de dados individualmente.

![Banco de dados](./media/sql-database-automatic-tuning-enable/database.png)

Depois de selecionar a configuração apropriada, clique em **Aplicar**.

### <a name="rest-api"></a>API Rest
[Clique aqui para saber mais sobre como habilitar o ajuste automático em um único banco de dados por meio da API REST](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Para habilitar o ajuste automático em um único banco de dados por meio de T-SQL, conecte-se ao banco de dados e execute a seguinte consulta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Definir o ajuste automático como AUTO aplicará os Padrões do Azure. Definindo-o como INHERIT, a configuração de ajuste automático será herdada do servidor pai. Se escolher CUSTOM, você precisará configurar manualmente o ajuste automático.

Para configurar opções de ajuste automático individuais por meio do T-SQL, conecte-se ao banco de dados e execute a consulta de modo similar a esta:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Definir a opção de ajuste individual como ON substituirá qualquer configuração de banco de dados herdada e habilitará a opção de ajuste. Defini-la a como OFF substituirá qualquer configuração herdada por esse banco de dados e desabilitará a opção de ajuste. Definir a opção de ajuste automático como DEFAULT fará com que a configuração seja herdada da configuração de ajuste automático do nível de banco de dados.  

## <a name="disabled-by-the-system"></a>Desabilitado pelo sistema
O ajuste automático está monitorando todas as ações que ele realiza no banco de dados e, em alguns casos, ele pode determinar que o ajuste automático não pode funcionar adequadamente no banco de dados. Nessa situação, a opção de ajuste será desabilitada pelo sistema. Na maioria dos casos, isso ocorre porque o Repositório de Consultas não está habilitado ou está em estado somente leitura em um banco de dados específico.

## <a name="next-steps"></a>Próximas etapas
* Leia o [artigo Ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático e como ele pode ajudar você a melhorar o desempenho.
* Consulte [Recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.
