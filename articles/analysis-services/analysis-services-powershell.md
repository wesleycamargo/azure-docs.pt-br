---
title: Gerenciar o Azure Analysis Services com o PowerShell | Microsoft Docs
description: O gerenciamento do Azure Analysis Services com o PowerShell.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: ef3f31c633eeba92f343e2126626bd029aebbf64
ms.openlocfilehash: 170657601a0ea6b0c0ebabfd34befdce290cebd8


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Gerenciar o Azure Analysis Services com PowerShell

Este artigo descreve os cmdlets do PowerShell usados para executar tarefas de gerenciamento de banco de dados e servidor do Azure Analysis Services. 

As tarefas de gerenciamento de servidor, como criar ou excluir um servidor, suspender ou retomar operações de servidor ou alterar o nível de serviço (camada), usam cmdlets do AzureRM (Azure Resource Manager). Outras tarefas de gerenciamento de bancos de dados, como adição ou remoção de membros da função, processamento ou particionamento, usam os mesmos cmdlets no módulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que o SQL Server Analysis Services.

## <a name="permissions"></a>Permissões
A maioria das tarefas do PowerShell exige privilégios de administrador no servidor do Analysis Services que está sendo gerenciado. As tarefas agendadas do PowerShell são operações autônomas. A conta que executa o agendador deve ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor usando os cmdlets AzureRm, sua conta ou a conta executando o Agendador também deve pertencer à função de Proprietário do recurso no [Controle de Acesso Baseado em Função do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Operações do servidor 
Os cmdlets do Azure Analysis Services são incluídos no módulo de componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Para instalar os módulos de cmdlet do AzureRM, confira [Cmdlets do Azure Resource Manager](https://docs.microsoft.com/powershell/resourcemanager/) na Galeria do PowerShell.

|Cmdlet|Descrição| 
|------------|-----------------| 
|Get-AzureRmAnalysisServicesServer|Obtém detalhes de uma instância do servidor.|  
|New-AzureRmAnalysisServicesServer|Cria uma nova instância do servidor.|
|Remove-AzureRmAnalysisServicesServer|Remove uma instância do servidor.|  
|Suspend-AzureRmAnalysisServicesServe|Suspende uma instância do servidor.| 
|Resume-AzureRmAnalysisServicesServer|Retoma uma instância do servidor.|  
|Set-AzureRmAnalysisServicesServer|Modifica uma instância do servidor.|   
|Test-AzureRmAnalysisServicesServer|Testa a existência de uma instância do servidor.| 

## <a name="database-operations"></a>Operações de banco de dados
As operações de banco de dados do Azure Analysis Services usam o mesmo módulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que o SQL Server Analysis Services. No entanto, nem todos os cmdlets têm suporte para visualização do Azure Analysis Services. 

O módulo SQLASCMDLETS fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral, que aceita um script ou consulta de TMSL (linguagem de script de modelo Tabular). Os seguintes cmdlets no módulo SQLASCMDLETS têm suporte para a visualização do Azure Analysis Services.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Adicionar um membro a uma função de banco de dados.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Executar um script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Processar um banco de dados.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Processar uma partição.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Processar uma tabela.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Mesclar uma partição.|  
  

## <a name="related-information"></a>Informações relacionadas
* [Script do PowerShell no Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx).
* [Programação de Modelo de Tabela para o Nível de Compatibilidade 1200](https://msdn.microsoft.com/library/mt712541.aspx)


<!--HONumber=Jan17_HO5-->


