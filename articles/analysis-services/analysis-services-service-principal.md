---
title: Automatize as tarefas do Azure Analysis Services com entidades de serviço | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2b49b85d39f55052e112fd9f4f0e28bdc6c91637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213275"
---
# <a name="automation-with-service-principals"></a>Automação com entidades de serviço

As entidades de serviço são um recurso de aplicativo do Azure Active Directory que você cria em seu locatário para executar operações de nível de serviço e recursos autônomos. Eles são um tipo exclusivo de *identidade do usuário* com uma ID de aplicativo e senha ou certificado. Uma entidade de serviço tem apenas as permissões necessárias para executar tarefas definidas pelas funções e permissões para as quais é atribuída. 

No Analysis Services, as entidades de serviço são usadas com o Automação do Azure, o modo autônomo do PowerShell, aplicativos cliente personalizados e aplicativos Web para automatizar tarefas comuns. Por exemplo, servidores de provisionamento, modelos de implantação, atualização de dados, escalar verticalmente/reduzir verticalmente e pausar/continuar podem ser automatizados usando entidades de serviço. As permissões são atribuídas a entidades de serviço por meio da associação de funções, semelhante às contas comuns de UPN do Microsoft Azure AD.

## <a name="create-service-principals"></a>Criar entidades de serviço
 
As entidades de serviço podem ser criadas no portal do Azure ou usando o PowerShell. Para obter mais informações, consulte:

[Criar entidade de serviço - Portal do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Criar entidade de serviço - PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Armazenar credenciais e ativos de certificado na Automação do Azure

Credenciais e certificados da entidade de serviço podem ser armazenados de forma segura na Automação do Azure para operações de runbook. Para obter mais informações, consulte:

[Ativos de credenciais na Automação do Azure](../automation/automation-credentials.md)   
[Ativos de certificado na Automação do Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Adicionar entidades de serviço à função de administrador do servidor

Antes de poder usar uma entidade de serviço para operações de gerenciamento de servidor do Analysis Services, você deverá adicioná-la à função de administradores do servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Entidades de serviço em cadeias de conexão

AppID de entidade de serviço e senha ou certificado podem ser usados em cadeias de conexão da mesma forma que um UPN.

### <a name="powershell"></a>PowerShell

Ao usar uma entidade de serviço para operações de gerenciamento de recursos com o módulo [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices), use o cmdlet `Login-AzureRmAccount`. Ao usar uma entidade de serviço para operações do servidor com o módulo [SQLServer](https://www.powershellgallery.com/packages/SqlServer), use o cmdlet `Add-AzureAnalysisServicesAccount`. 

No exemplo a seguir, appID e uma senha são usados para executar uma operação de atualização de modelo de banco de dados:

```PowerShell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzureAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO e ADOMD 

Ao conectar com aplicativos cliente e aplicativos Web, os pacotes instaláveis das [bibliotecas cliente AMO e ADOMD](analysis-services-data-providers.md) versão 15.0.2 e superior do NuGet dão suporte para entidades de serviço nas cadeias de conexão usando a sintaxe a seguir: `app:AppID` e senha ou `cert:thumbprint`. 

No exemplo a seguir, `appID` e `password` são usados para executar uma operação de atualização de modelo de banco de dados:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Próximas etapas
[Fazer logon com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md)   