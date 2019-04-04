---
title: Gerenciar o Azure Analysis Services com o PowerShell | Microsoft Docs
description: O gerenciamento do Azure Analysis Services com o PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893836"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerenciar o Azure Analysis Services com PowerShell

Este artigo descreve os cmdlets do PowerShell usados para executar tarefas de gerenciamento de banco de dados e servidor do Azure Analysis Services. 

Tarefas de gerenciamento de servidor, como criar ou excluir um servidor, suspender ou retomar operações do servidor ou alterar o nível (camada) de serviço, usam cmdlets do Azure Resource Manager (recurso) e do Analysis Services (servidor). Outras tarefas de gerenciamento de bancos de dados, como adição ou remoção de membros da função, processamento ou particionamento, usam os cmdlets incluídos no mesmo módulo SqlServer que o SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas do PowerShell exige privilégios de administrador no servidor do Analysis Services que está sendo gerenciado. As tarefas agendadas do PowerShell são operações autônomas. A conta ou a entidade de serviço que executa o agendador precisa ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor usando cmdlets do PowerShell do Azure, sua conta ou a conta que executa o Agendador também deve pertencer à função de proprietário do recurso no [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operações de gerenciamento de recursos 

Módulo - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Cmdlet|DESCRIÇÃO| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Obtém detalhes de uma instância do servidor.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Cria uma instância de servidor.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Cria uma nova configuração de firewall do Analysis Services.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Cria uma nova regra de firewall do Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Remove uma instância do servidor.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Retoma uma instância do servidor.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Suspende uma instância do servidor.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Modifica uma instância do servidor.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Testa a existência de uma instância do servidor.| 

## <a name="server-management-operations"></a>Operações de gerenciamento de servidor

Módulo – [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|DESCRIÇÃO| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Adiciona uma conta autenticada a ser usada para solicitações de cmdlet do servidor do Azure Analysis Services.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exporta um log de uma instância do servidor do Analysis Services conectada no momento no ambiente conforme especificado no comando Add-AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Reinicia uma instância do servidor do Analysis Services no ambiente conectado no momento; especificados no comando Add-AzAnalysisServicesAccount.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Sincroniza um banco de dados especificado na instância especificada do servidor do Analysis Services para todas as instâncias de expansão de consulta na conectada no momento no ambiente conforme especificado no comando Add-AzAnalysisServicesAccount|  

## <a name="database-operations"></a>Operações de banco de dados

As operações de banco de dados do Azure Analysis Services usam o mesmo módulo [SqlServer](https://www.powershellgallery.com/packages/SqlServer) que o SQL Server Analysis Services. No entanto, nem todos os cmdlets têm suporte para o Azure Analysis Services. Para saber mais, confira [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

O módulo SqlServer fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral que aceita um script ou consulta de Linguagem de Script de Modelo Tabular (TMSL). Os cmdlets a seguir no módulo SqlServer têm suporte para o Azure Analysis Services.

  
|Cmdlet|DESCRIÇÃO|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicionar um membro a uma função de banco de dados.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Fazer backup de um banco de dados do Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Executar um script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Processar um banco de dados.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Processar uma partição.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Processar uma tabela.|  
|[Partição de mesclagem](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Mesclar uma partição.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaurar um banco de dados do Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo do SqlServer na Galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programação do modelo tabular para o nível de compatibilidade 1200 e superior](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
