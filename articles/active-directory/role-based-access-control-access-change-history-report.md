<properties
	pageTitle="Criar um relatório de histórico de alterações de acesso | Microsoft Azure"
	description="Gere um relatório que lista todas as alterações no acesso a suas assinaturas do Azure com Controle de Acesso Baseado em Função nos últimos 90 dias."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/23/2016"
	ms.author="kgremban"/>

# Criar relatório de histórico de alterações de acesso

Quando você não é o proprietário exclusivo de suas assinaturas do Azure, ou dos recursos e dos grupos de recursos nelas, precisa ser capaz de controlar todas as alterações de acesso. Sempre que alguém concede ou revoga acesso em suas assinaturas, as alterações são registradas em log em eventos do Azure. Você pode criar relatórios de histórico de alterações de acesso para ver todas as alterações nos últimos 90 dias.

## Criar um relatório com o Azure PowerShell
Para criar um relatório de histórico de alterações de acesso no PowerShell, use o seguinte comando:

```
Get-AzureRMAuthorizationChangeLog
```

Você pode especificar quais propriedades das atribuições deseja listar, incluindo o seguinte:

| Propriedade | Descrição |
| -------- | ----------- |
| **Ação** | Se o acesso foi concedido ou revogado |
| **Chamador** | O proprietário responsável por alterar o acesso |
| **Data** | A data e a hora em que o acesso foi alterado |
| **DirectoryName** | O diretório do Azure Active Directory |
| **PrincipalName** | O nome do usuário, do grupo ou do aplicativo |
| **PrincipalType** | Se a atribuição foi para um usuário, um grupo ou um aplicativo |
| **RoleId** | O GUID da função que foi concedida ou revogada |
| **RoleName** | A função que foi concedida ou revogada |
| **ScopeName** | O nome da assinatura, do grupo de recursos ou do recurso |
| **ScopeType** | Se a atribuição foi no escopo do recurso, no grupo de recursos ou na assinatura |
| **SubscriptionId** | O GUID da assinatura do Azure |
| **SubscriptionName** | O nome da assinatura do Azure |

O exemplo abaixo lista todas as alterações de acesso na assinatura nos os últimos sete dias:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - captura de tela](./media/role-based-access-control-configure/access-change-history.png)

## Criar um relatório com a CLI do Azure
Para criar um relatório de histórico de alterações de acesso na CLI (Interface de Linha de Comando) do Azure, use o seguinte comando:
```
azure authorization changelog
```

## Exportar para uma planilha
Para salvar o relatório ou manipular os dados, exporte as alterações de acesso para um arquivo .csv. Você pode exibir o relatório em uma planilha para análise.

![Log de alteração exibido como planilha - captura de tela](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Consulte também
- Introdução ao [Controle de Acesso Baseado em Função do Azure](role-based-access-control-configure.md)
- Trabalhe com [Funções personalizadas no RBAC do Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->