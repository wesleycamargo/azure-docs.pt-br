---
title: Bancos de dados fornecida pelo adaptador SQL RP na pilha do Azure | Microsoft Docs
description: Como criar e gerenciar bancos de dados SQL provisionados usando o provedor de recursos do adaptador de SQL
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 0cc08c37e879b00f8cd9a4046a4c81c55dab167c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-databases"></a>Criar bancos de dados SQL

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Autoatendimento bancos de dados são fornecidos através da experiência do portal de usuário. Um usuário precisa de uma assinatura que tenha uma oferta, que contém o serviço de banco de dados.

1. Entrar para o [Azure pilha](azure-stack-poc.md) portal do usuário (administradores de serviço também podem usar o portal de administração).

2. Clique em **+ novo** &gt; **dados + armazenamento "** &gt; **banco de dados do SQL Server (visualização)** &gt; **adicionar**.

3. Preencha o formulário com os detalhes do banco de dados, incluindo um **nome do banco de dados**, **tamanho máximo**e altere os outros parâmetros conforme necessário. Você será solicitado a escolher uma SKU para seu banco de dados. Como servidores de hospedagem são adicionados, eles serão atribuídos a uma SKU. Bancos de dados são criados nesse pool de servidores que compõem o SKU de hospedagem.

  ![Novo banco de dados](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > O tamanho do banco de dados deve ser pelo menos de 64 MB. Ele pode ser aumentado usando as configurações.

4. Preencha as configurações de logon: **logon de banco de dados**, e **senha**. Essas configurações são as credenciais de autenticação do SQL que é criada para o acesso a somente esse banco de dados. O nome de usuário de logon deve ser globalmente exclusivo. Crie uma nova configuração de logon ou selecione um existente. Você pode reutilizar as configurações de logon para outros bancos de dados usando o mesmo SKU.

    ![Criar um novo logon de banco de dados](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Enviar o formulário e aguarde até que a implantação fosse concluída.

    Na folha de resultante, observe o campo "Cadeia de caracteres de Conexão". Você pode usar essa cadeia de caracteres em qualquer aplicativo que requer acesso ao SQL Server (por exemplo, um aplicativo web) na pilha do Azure.

    ![Recuperar a cadeia de caracteres de conexão](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Excluir bancos de dados SQL
No portal,

>[!NOTE]
>
>Quando um banco de dados AlwaysOn do SQL é excluído do RP, é excluída com êxito do primário e o grupo de disponibilidade do AlwaysOn, mas por Design SQL AG coloca o banco de dados na restauração de estado em cada réplica e não descarta o banco de dados, a menos que o disparou. Se um banco de dados não for descartado, as réplicas secundárias vai para estado não sincronizando. Adicionar um novo banco de dados novamente para o grupo de disponibilidade com o mesmo por meio de RP ainda funciona. Consulte ![remover um banco de dados secundário](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Gerenciar as credenciais do banco de dados
Você pode atualizar as credenciais de banco de dados (configurações de logon).

## <a name="verify-sql-alwayson-databases"></a>Verifique se os bancos de dados SQL AlwaysOn
Bancos de dados AlwaysOn devem mostrar que sincronizadas e estão disponíveis em todas as instâncias e no grupo de disponibilidade. Após o failover, o banco de dados deve se conectar diretamente. Você pode usar o SQL Server Management Studio para verificar a sincronização de um banco de dados:

![Verifique se o AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
