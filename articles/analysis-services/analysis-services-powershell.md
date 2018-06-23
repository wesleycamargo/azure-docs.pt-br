---
title: Gerenciar o Azure Analysis Services com o PowerShell | Microsoft Docs
description: O gerenciamento do Azure Analysis Services com o PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b4e819bdce971e92e4b2d99e68f51ddbf8a22182
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597460"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerenciar o Azure Analysis Services com PowerShell

Este artigo descreve os cmdlets do PowerShell usados para executar tarefas de gerenciamento de banco de dados e servidor do Azure Analysis Services. 

Tarefas de gerenciamento de servidor, como criar ou excluir um servidor, suspender ou retomar operações do servidor ou alterar o nível (camada) de serviço, usam cmdlets do Azure Resource Manager (recurso) e do Analysis Services (servidor). Outras tarefas de gerenciamento de bancos de dados, como adição ou remoção de membros da função, processamento ou particionamento, usam os cmdlets incluídos no mesmo módulo SqlServer que o SQL Server Analysis Services.

## <a name="permissions"></a>Permissões
A maioria das tarefas do PowerShell exige privilégios de administrador no servidor do Analysis Services que está sendo gerenciado. As tarefas agendadas do PowerShell são operações autônomas. A conta ou a entidade de serviço que executa o agendador precisa ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor usando os cmdlets AzureRm, sua conta ou a conta executando o Agendador também deve pertencer à função de Proprietário do recurso no [Controle de Acesso Baseado em Função do Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operações de gerenciamento de recursos 
Módulo – [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices)

|Cmdlet|DESCRIÇÃO| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtém detalhes de uma instância do servidor.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Cria uma instância de servidor.|   
|[New-AzureRmAnalysisServicesFirewallConfig](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallconfig)|Cria uma nova configuração de firewall do Analysis Services.|   
|[New-AzureRmAnalysisServicesFirewallRule](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesfirewallrule)|Cria uma nova regra de firewall do Analysis Services.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Remove uma instância do servidor.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Retoma uma instância do servidor.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Suspende uma instância do servidor.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifica uma instância do servidor.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Testa a existência de uma instância do servidor.| 

## <a name="server-management-operations"></a>Operações de gerenciamento de servidor

Módulo – [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|DESCRIÇÃO| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azure.analysisservices/add-azureanalysisservicesaccount)|Adiciona uma conta autenticada a ser usada para solicitações de cmdlet do servidor do Azure Analysis Services.| 
|[Export-AzureAnalysisServicesInstance]()|Exporta um log de uma instância do servidor do Analysis Services no ambiente conectado no momento, conforme especificado no comando Add-AzureAnalysisServicesAccount|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Reinicia uma instância do servidor do Analysis Services no ambiente conectado no momento; especificada no comando Add-AzureAnalysisServicesAccount.|  
|[Sync-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Sincroniza um banco de dados especificado na instância especificada do servidor do Analysis Services com todas as instâncias de scale out de consulta no ambiente conectado no momento, conforme especificado no comando Add-AzureAnalysisServicesAccount|  

## <a name="database-operations"></a>Operações de banco de dados

As operações de banco de dados do Azure Analysis Services usam o mesmo módulo [SqlServer](https://www.powershellgallery.com/packages/SqlServer) que o SQL Server Analysis Services. No entanto, nem todos os cmdlets têm suporte para o Azure Analysis Services. 

O módulo SqlServer fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral que aceita um script ou consulta de Linguagem de Script de Modelo Tabular (TMSL). Os cmdlets a seguir no módulo SqlServer têm suporte para o Azure Analysis Services.

  
|Cmdlet|DESCRIÇÃO|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Adicionar um membro a uma função de banco de dados.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Fazer backup de um banco de dados do Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Executar um script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Processar um banco de dados.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Processar uma partição.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Processar uma tabela.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Mesclar uma partição.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restaurar um banco de dados do Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na Galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://msdn.microsoft.com/library/mt712541.aspx) (Programação de Modelo Tabular para o Nível de Compatibilidade 1200 e superior)
