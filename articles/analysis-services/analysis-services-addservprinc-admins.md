---
title: Adicionar uma entidade de serviço à função de administrador do servidor do Azure Analysis Services | Microsoft Docs
description: Saiba como adicionar uma entidade de serviço de automação à função de administrador do servidor
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
ms.assetid: ''
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Adicionar uma entidade de serviço à função de administrador do servidor 

 Para automatizar tarefas do PowerShell autônomas, uma entidade de serviço deve ter privilégios de **administrador do servidor** no servidor do Analysis Services sendo gerenciado. Este artigo descreve como adicionar uma entidade de serviço à função de administradores do servidor em um servidor do AS do Azure.

## <a name="before-you-begin"></a>Antes de começar
Antes de concluir essa tarefa, você deverá ter uma entidade de serviço registrado no Azure Active Directory.

[Criar entidade de serviço - Portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Permissões necessárias
Para concluir essa tarefa, você deve ter permissões de [administrador do servidor](analysis-services-server-admins.md) no servidor do AS do Azure. 

## <a name="add-service-principle-to-server-administrators-role"></a>Adicionar entidade de serviço à função de administradores do servidor

1. No SSMS, conecte o servidor do AS do Azure.
2. Em **	Propriedades do Servidor** > **Segurança**, clique em **Adicionar**.
3. Em **Selecionar Usuário ou Grupo**, procure o aplicativo registrado pelo nome, selecione e clique em**Adicionar**.

    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Verifique o ID da conta da entidade do serviço e clique em **OK**.
    
    ![Pesquise a conta de entidade de serviço](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Para operações do servidor utilizando cmdlets do AzureRm, o agendador executando a entidade de serviço também deverá pertencer à função **Proprietário** do recurso no [RBAC (Controle de Acesso Baseado em Função do Azure)](../active-directory/role-based-access-control-what-is.md). 

## <a name="related-information"></a>Informações relacionadas

* [Baixar o módulo do PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


