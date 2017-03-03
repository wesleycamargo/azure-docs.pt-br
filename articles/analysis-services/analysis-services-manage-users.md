---
title: "Gerenciar usuários nos Azure Analysis Services | Microsoft Docs"
description: "Saiba como gerenciar usuários em um servidor do Analysis Services no Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Gerenciar usuários nos serviços de análise do Azure
Nos Azure Analysis Services, há dois tipos de usuários, administradores de servidor e usuários de banco de dados. 

## <a name="server-administrators"></a>Administradores de servidor
Você pode usar os **Administradores do Analysis Services** na folha de controle de seu servidor no portal do Azure ou nas Propriedades do Servidor no SSMS para gerenciar os administradores de servidor. Os Administradores do Analysis Services são os administradores de servidor de banco de dados com direitos para execução de tarefas comuns de administração de banco de dados, como adicionar e remover bancos de dados e gerenciar usuários. Por padrão, o usuário que cria o servidor no Portal do Azure é adicionado automaticamente como Administrador do Analysis Services.

![Administradores de servidor no portal do Azure](./media/analysis-services-manage-users/aas-manage-users-admins.png)

Você também deve saber:

* O Windows Live ID não é um tipo de identidade com suporte para os Azure Analysis Services.  
* Os Administradores do Analysis Services deve ser usuários válidos do Azure Active Directory.
* Se você criar um servidor do Azure Analysis Services por meio de modelos do Azure Resource Manager, os Administradores do Analysis Services usarão uma matriz JSON de usuários que devem ser adicionados como administradores.

Os Administradores do Analysis Services podem ser diferentes dos administradores de recursos do Azure, que podem gerenciar os recursos de assinaturas do Azure. Isso mantém a compatibilidade com o comportamentos de gerenciamento existente de XMLA e TMSL no Analysis Services e serve para que você possa separar as tarefas entre o gerenciamento de recursos do Azure e o gerenciamento de banco de dados do Analysis Services. Para exibir todas as funções e tipos de acesso para o recurso do Azure Analysis Services, use o IAM (Controle de acesso) na folha de controle.

### <a name="to-add-administrators-using-azure-portal"></a>Adicionar administradores usando o portal do Azure
1. Na folha de controle do seu servidor, clique em **os administradores de serviços de análise de**.
2. Na folha ** \<nomedoservidor >- Administradores de Serviços de Análise**, clique em **Adicionar**.
3. Na folha **Adicionar Administradores de Servidor**, selecione as contas de usuários a adicionar.

## <a name="database-users"></a>Usuários de banco de dados
Usuários de banco de dados devem ser adicionados às funções de banco de dados. Funções definem os usuários e grupos que têm as mesmas permissões para um banco de dados. Por padrão, os bancos de dados de modelo de tabela têm uma função de usuários padrão com permissões de leitura. Para saber mais, veja [Funções em modelos de tabela](https://msdn.microsoft.com/library/hh213165.aspx).

Os usuários de banco de dados de modelo do Azure Analysis Services *devem estar em seu Azure Active Directory*. Nomes de usuário especificado devem ser pelo endereço de email organizacionais ou UPN. Isso é diferente dos bancos de dados de modelo tabular locais que oferecem suporte a usuários de acordo com os nomes de usuário de domínio do Windows. 

Você pode criar funções de banco de dados, adicionar usuários e grupos a funções e configurar a segurança de nível de linha no SQL Server Data Tools (SSDT) ou no SQL Server Management Studio (SSMS). Você também pode adicionar ou remover usuários das funções usando [cmdlets do PowerShell do Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx) ou usando [linguagem de script do modelo Tabular](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL).

**Exemplo de script TMSL**

Neste exemplo, um usuário e um grupo são adicionados à função de usuários do banco de dados SalesBI.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)

Os administradores de assinaturas podem usar o **Controle de acesso** na folha de controle para configurar as funções. Isso não é o mesmo que ocorre para administradores de servidor ou usuários de banco de dados que, conforme descrito acima, são configurados no nível do servidor ou do banco de dados. 

![Controle de acesso no Portal do Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Funções se aplicam a usuários ou contas que precisam executar tarefas que podem ser realizadas no Portal ou usando modelos do Azure Resource Manager. Para saber mais, consulte [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md).

## <a name="next-steps"></a>Próximas etapas
Se você ainda não tiver implantado um modelo de tabela em seu servidor, agora é um bom momento. Para saber mais, confira [Implantar no Azure Analysis Services](analysis-services-deploy.md).

Se você tiver implantado um modelo de tabela para seu servidor, você estará pronto para se conectar usando um cliente ou navegador. Para saber mais, confira [Obter dados do servidor do Azure Analysis Services](analysis-services-connect.md).


