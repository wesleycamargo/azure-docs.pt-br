---
title: Usando bancos de dados fornecidos pelo RP de adaptador do MySQL em AzureStack | Microsoft Docs
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 3063a3f3f199a591fa94c3a665ea9ecc5457e502
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247588"
---
# <a name="create-mysql-databases"></a>Criar bancos de dados MySQL

Você pode criar e gerenciar bancos de dados de autoatendimento no portal do usuário. Um usuário do Azure Stack precisa de uma assinatura com uma oferta que inclui o serviço de banco de dados MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testar sua implantação criando um banco de dados MySQL

1. Entrar no portal do usuário do Azure Stack.
2. Selecione **+ criar um recurso** > **dados + armazenamento** > **banco de dados MySQL** > **adicionar**.
3. Sob **criar banco de dados MySQL**, insira o nome do banco de dados e configure as outras configurações conforme necessário para o seu ambiente.

    ![Criar um teste de banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sob **Create Database**, selecione **SKU**. Sob **selecionar uma SKU MySQL**, escolha o SKU do banco de dados.

    ![Selecione uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Como os servidores de hospedagem são adicionados ao Azure Stack, eles serão atribuídos a uma SKU. Bancos de dados são criados no pool de servidores em um SKU de hospedagem.

5. Sob **Login**, selecione ***definir configurações obrigatórias***.
6. Sob **selecione um logon**, você pode escolher um logon existente ou selecione **+ criar um novo logon** para configurar um novo logon.  Insira um **logon de banco de dados** nome e **senha**e, em seguida, selecione **Okey**.

    ![Criar um novo logon de banco de dados](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >O comprimento do nome de logon do banco de dados não pode exceder 32 caracteres de MySQL 5.7. Em edições anteriores, ele não pode exceder 16 caracteres.

7. Selecione **criar** para concluir a configuração de banco de dados.

Depois que o banco de dados é implantado, anote o **cadeia de caracteres de Conexão** sob **Essentials**. Você pode usar essa cadeia de caracteres em qualquer aplicativo que precisa acessar o banco de dados MySQL.

![Obtenha a cadeia de conexão para o banco de dados MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Atualizar a senha administrativa

Você pode modificar a senha alterando-o na instância do servidor MySQL.

1. Selecione **recursos ADMINISTRATIVOS** > **servidores de hospedagem MySQL**. Selecione o servidor de hospedagem.
2. Sob **as configurações**, selecione **senha**.
3. Sob **senha**, insira a nova senha e, em seguida, selecione **salvar**.

![Atualizar a senha do administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Próximas etapas

[Atualizar o provedor de recursos do MySQL](azure-stack-mysql-resource-provider-update.md)
