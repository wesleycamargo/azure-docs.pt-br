---
title: "Relatórios de acesso – RBAC do Azure | Microsoft Docs"
description: "Gere um relatório que lista todas as alterações no acesso a suas assinaturas do Azure com Controle de Acesso Baseado em Função nos últimos 90 dias."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 015cc28903bfd366c653a51b0f73512bf8b578ea
ms.openlocfilehash: 433dc731c342924d962e2f08e392556558a0168d
ms.lasthandoff: 02/28/2017

---
# <a name="create-an-access-report-for-role-based-access-control"></a>Criar um relatório de acesso para o Controle de Acesso Baseado em Função
Sempre que alguém concede ou revoga acesso em suas assinaturas, as alterações são registradas em log em eventos do Azure. Você pode criar relatórios de histórico de alterações de acesso para ver todas as alterações nos últimos 90 dias.

## <a name="create-a-report-with-azure-powershell"></a>Criar um relatório com o Azure PowerShell
Para criar um relatório de histórico de alterações de acesso no PowerShell, use o comando `Get-AzureRMAuthorizationChangeLog` . Mais detalhes sobre esse cmdlet estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Ao chamar esse comando, você pode especificar quais propriedades das atribuições deseja listar, incluindo o seguinte:

| Propriedade | Descrição |
| --- | --- |
| **Ação** |Se o acesso foi concedido ou revogado |
| **Chamador** |O proprietário responsável por alterar o acesso |
| **Data** |A data e a hora em que o acesso foi alterado |
| **DirectoryName** |O diretório do Azure Active Directory |
| **PrincipalName** |O nome do usuário, do grupo ou do aplicativo |
| **PrincipalType** |Se a atribuição foi para um usuário, um grupo ou um aplicativo |
| **RoleId** |O GUID da função que foi concedida ou revogada |
| **RoleName** |A função que foi concedida ou revogada |
| **ScopeName** |O nome da assinatura, do grupo de recursos ou do recurso |
| **ScopeType** |Se a atribuição foi no escopo do recurso, no grupo de recursos ou na assinatura |
| **SubscriptionId** |O GUID da assinatura do Azure |
| **SubscriptionName** |O nome da assinatura do Azure |

O exemplo abaixo lista todas as alterações de acesso na assinatura nos os últimos sete dias:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - captura de tela](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Criar um relatório com a CLI do Azure
Para criar um relatório de histórico de alterações de acesso na CLI (Interface de Linha de Comando) do Azure, use o comando `azure role assignment changelog list` .

## <a name="export-to-a-spreadsheet"></a>Exportar para uma planilha
Para salvar o relatório ou manipular os dados, exporte as alterações de acesso para um arquivo .csv. Você pode exibir o relatório em uma planilha para análise.

![Log de alteração exibido como planilha - captura de tela](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>Próximas etapas
* Trabalhe com [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md)
* Saiba como gerenciar o [RBAC do Azure com o PowerShell](role-based-access-control-manage-access-powershell.md)


