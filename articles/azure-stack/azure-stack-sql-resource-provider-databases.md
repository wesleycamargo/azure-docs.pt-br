---
title: Usando bancos de dados fornecidos pelo provedor de recursos do adaptador de SQL no Azure Stack | Microsoft Docs
description: Como criar e gerenciar bancos de dados SQL provisionados usando o provedor de recursos do adaptador de SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 6f56cdccd8f4cd690eb149c8f84a562a580325dc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164338"
---
# <a name="create-sql-databases"></a>Criar bancos de dados SQL

Você pode criar e gerenciar bancos de dados de autoatendimento no portal do usuário. Um usuário do Azure Stack precisa de uma assinatura com uma oferta que inclui o serviço de banco de dados SQL.

1. Entrar para o [do Azure Stack](azure-stack-poc.md) portal do usuário.

2. Selecione **+ novo** &gt; **dados + armazenamento** &gt; **banco de dados do SQL Server** &gt; **adicionar**.

3. Sob **Create Database**, insira as informações necessárias, como **nome do banco de dados** e **Size máximo em MB**.

   >[!NOTE]
   >O tamanho do banco de dados deve ser pelo menos 64 MB, que você pode aumentar depois de implantar o banco de dados.

   Defina as outras configurações conforme necessário para o seu ambiente.

4. Sob **Create Database**, selecione **SKU**. Sob **selecionar uma SKU**, selecione a SKU do banco de dados.

   ![Criar banco de dados](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Como os servidores de hospedagem são adicionados ao Azure Stack, eles serão atribuídos a uma SKU. Bancos de dados são criados no pool de servidores em um SKU de hospedagem.

5. Selecione **Login**.
6. Sob **selecione um logon**, escolha um logon existente ou selecione **+ criar um novo logon**.
7. Sob **novo logon**, insira um nome para **logon de banco de dados** e um **senha**.

   >[!NOTE]
   >Essas configurações são a credencial de autenticação do SQL que é criada para o seu acesso a somente esse banco de dados. O nome de usuário de logon deve ser globalmente exclusivo. Você pode reutilizar as configurações de logon para outros bancos de dados que usam a mesma SKU.

   ![Criar um novo logon de banco de dados](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Selecione **Okey** para concluir a implantação de banco de dados.

Sob **Essentials**, que é mostrado depois que o banco de dados é implantado, anote as **cadeia de caracteres de Conexão**. Você pode usar essa cadeia de caracteres em qualquer aplicativo que precisa acessar o banco de dados do SQL Server.

![Recuperar a cadeia de conexão](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bancos de dados SQL Always On

Por design, bancos de dados AlwaysOn são tratados de maneira diferente em um ambiente de servidor autônomo. Para obter mais informações, consulte [apresentando o SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Verifique se os bancos de dados SQL Always On

A captura de tela a seguir mostra como você pode usar o SQL Server Management Studio para examinar o status do banco de dados no SQL Always On.

![Status do banco de dados AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Bancos de dados AlwaysOn devem mostrar como Synchronized e está disponível para todas as instâncias do SQL e são exibidos em grupos de disponibilidade. Na captura de tela anterior, o exemplo de banco de dados é newdb1 e seu status será **newdb1 (sincronizado)**.

### <a name="delete-an-alwayson-database"></a>Excluir um banco de dados do AlwaysOn

Quando você exclui um banco de dados AlwaysOn do SQL do provedor de recursos, o SQL exclui o banco de dados da réplica primária e do grupo de disponibilidade.

SQL, em seguida, coloca o banco de dados no estado Restoring nas outras réplicas e não descartar o banco de dados, a menos que o disparou. Se o banco de dados não é descartado, as réplicas secundárias entram em um estado não sincronizando.

## <a name="next-steps"></a>Próximas etapas

[Manter o provedor de recursos do SQL Server](azure-stack-sql-resource-provider-maintain.md)
