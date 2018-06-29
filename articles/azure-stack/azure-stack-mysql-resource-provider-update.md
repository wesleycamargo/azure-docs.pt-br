---
title: Bancos de dados fornecida pelo adaptador RP MySQL em AzureStack | Microsoft Docs
description: Como criar e gerenciar bancos de dados MySQL provisionados usando o provedor de recursos de adaptador do MySQL
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031815"
---
# <a name="create-mysql-databases"></a>Criar bancos de dados MySQL

Você pode criar e gerenciar bancos de dados de autoatendimento no portal do usuário. Um usuário de pilha do Azure precisa de uma assinatura com uma oferta que inclui o serviço de banco de dados MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testar a implantação por meio da criação de um banco de dados MySQL

1. Entrar no portal do usuário de pilha do Azure.
2. Selecione **+ novo** > **dados + armazenamento** > **banco de dados MySQL** > **adicionar**.
3. Em **criar banco de dados MySQL**, digite o nome do banco de dados e defina as outras configurações conforme necessário para seu ambiente.

    ![Criar um teste de banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Em **criar banco de dados**, selecione **SKU**. Em **selecionar uma SKU MySQL**, escolha a SKU do seu banco de dados.

    ![Selecione uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >Como os servidores de hospedagem são adicionados a pilha do Azure, que estão atribuídas a uma SKU. Bancos de dados são criados no pool de servidores em um SKU de hospedagem.

5. Em **Login**, selecione ***definir as configurações necessárias***.
6. Em **selecione um logon**, você pode escolher um logon existente ou selecione **+ criar um novo logon** para configurar um novo logon.  Insira um **logon de banco de dados** nome e **senha**e, em seguida, selecione **Okey**.

    ![Criar um novo logon de banco de dados](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >O comprimento do nome de logon do banco de dados não pode exceder 32 caracteres 5.7 do MySQL. Em edições anteriores, ele não pode exceder 16 caracteres.

7. Selecione **criar** para concluir a configuração do banco de dados.

Depois que o banco de dados é implantado, anote o **cadeia de caracteres de Conexão** em **Essentials**. Você pode usar essa cadeia de caracteres em qualquer aplicativo que precisa acessar o banco de dados MySQL.

![Obter a cadeia de caracteres de conexão para o banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Atualizar a senha administrativa

Você pode modificar a senha alterando-o na instância do servidor MySQL.

1. Selecione **recursos ADMINISTRATIVOS** > **servidores de hospedagem MySQL**. Selecione o servidor de hospedagem.
2. Em **configurações**, selecione **senha**.
3. Em **senha**, digite a nova senha e, em seguida, selecione **salvar**.

![Atualize a senha de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Próximas etapas

[Manter o provedor de recursos MySQL](azure-stack-mysql-resource-provider-maintain.md)