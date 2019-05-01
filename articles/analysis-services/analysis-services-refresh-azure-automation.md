---
title: Atualizar modelos do Azure Analysis Services com a automação do Azure | Microsoft Docs
description: Saiba como as atualizações de modelo de código usando a automação do Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 1897193f0ae781029a7303c42ca8eeaa51389892
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920586"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com a automação do Azure

Usando a automação do Azure e Runbooks do PowerShell, você pode executar operações de atualização de dados automatizado em seus modelos de tabela de análise do Azure.  

O exemplo neste artigo usa o [módulos do PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Um Runbook do PowerShell, que demonstra a atualização de um modelo de exemplo é fornecido neste artigo.  

## <a name="authentication"></a>Authentication

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  O exemplo neste artigo usará um serviço Principal (SPN) para se autenticar no Azure Analysis Services.

Para saber mais sobre como criar uma entidade de serviço, consulte]

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir pressupõe que o firewall do Azure Analysis Services está desabilitado. Se o firewall estiver habilitado, o endereço IP público do iniciador solicitação será necessário na lista de permissões no firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale os módulos do SQL Server da Galeria do PowerShell.

1. Em sua conta de automação do Azure, clique em **módulos**, em seguida, **procurar na galeria**.

2. Na barra de pesquisa, pesquise **SqlServer**.

    ![Pesquisar Módulos](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione o SQL Server, clique **importação**.
 
    ![Importar Módulo](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar uma entidade de serviço (SPN)

Para saber mais sobre como criar uma entidade de serviço, consulte [criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar permissões no Azure Analysis Services
 
A entidade de serviço que você cria deve ter permissões de administrador do servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Criar o Runbook de automação do Azure

1. Na conta de automação, criar uma **credenciais** recurso que será usado para armazenar com segurança a entidade de serviço.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/6.png)

2. Insira os detalhes da credencial.  Para o **nome de usuário**, insira o **SPN ClientId**, para o **senha**, digite o **SPN segredo**.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importar o Runbook de automação

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Navegue para o **Model.ps1 atualização** do arquivo, forneça um **nome** e **descrição**e, em seguida, clique em **criar**.

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o Runbook tiver sido criado, ele entrará automaticamente em modo de edição.  Selecione **Publicar**.

    ![Publicar runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso de credencial que foi criado anteriormente é recuperado pelo runbook usando o **Get-AutomationPSCredential** comando.  Esse comando é então passado para o **Invoke-ProcessASADatabase** comando do PowerShell para realizar a autenticação ao Azure Analysis Services.

6. Testar o runbook clicando **iniciar**.

    ![Iniciar o Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha a **DATABASENAME**, **ANALYSISSERVER**, e **REFRESHTYPE** parâmetros e depois clique em **Okey**. O **WEBHOOKDATA** parâmetro não é necessário quando o Runbook é executado manualmente.

    ![Iniciar o Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o Runbook é executado com êxito, você receberá uma saída semelhante à seguinte:

![Execução bem-sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usar um Runbook de automação do Azure independentes

O Runbook pode ser configurado para disparar a atualização de modelo do Azure Analysis Services de forma programada.

Isso pode ser configurado da seguinte maneira:

1. No Runbook de automação, clique em **agendas**, em seguida, **adicionar um agendamento**.
 
    ![Criar agenda](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique em **agendamento** > **criar um novo agendamento**e, em seguida, preencha os detalhes.

    ![Configurar agendamento](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para a agenda. Eles serão usados sempre que dispara o Runbook. O **WEBHOOKDATA** parâmetro deverá ser deixado em branco durante a execução por meio de uma agenda.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com o Data Factory

Para consumir o runbook usando o Azure Data Factory, primeiro crie uma **Webhook** para o runbook. O **Webhook** fornecerá uma URL que pode ser chamada por meio de uma atividade de web do Azure Data Factory.

> [!IMPORTANT]
> Para criar uma **Webhook**, o status do Runbook deve ser **publicado**.

1. Em seu Runbook de automação, clique em **Webhooks**e, em seguida, clique em **adicionar Webhook**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Atribuir ao Webhook um nome e uma expiração.  O nome só identifica o Webhook dentro do Runbook de automação, ele não fazem parte da URL.

   >[!CAUTION]
   >Certifique-se de que copiar a URL antes de fechar o assistente, pois você não pode recuperá-lo uma vez fechado.
    
   ![Configure Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o webhook podem permanecer em branco.  Ao configurar a atividade de web do Azure Data Factory, os parâmetros podem ser passados no corpo da chamada de web.

3. No Data Factory, configure um **atividade da web**

### <a name="example"></a>Exemplo

   ![Atividade da Web de exemplo](./media/analysis-services-refresh-azure-automation/19.png)

O **URL** é a URL criada a partir do Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |O nome do banco de dados do Azure Analysis Services <br/> Exemplo: AdventureWorksDB         |
|**AnalysisServicesServer**     |O nome do servidor do Azure Analysis Services. <br/> Exemplo: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |O tipo de atualização para executar. <br/> Exemplo: Completo         |

Exemplo de corpo JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Esses parâmetros são definidos no runbook de script do PowerShell.  Quando a atividade da web é executada, o conteúdo JSON passado é WEBHOOKDATA.

Isso é desserializado e armazenado como parâmetros do PowerShell, que são usados pelo comando Invoke-ProcesASDatabase PowerShell.

![Webhook desserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usar um trabalhador híbrido com o Azure Analysis Services

Uma máquina de Virtual do Azure com um endereço IP público estático pode ser usado como um Hybrid Worker de automação do Azure.  Esse endereço IP público, em seguida, pode ser adicionado ao firewall do Azure Analysis Services.

> [!IMPORTANT]
> Verifique se que o endereço IP público de máquina Virtual é configurado como estático.
>
>Para saber mais sobre como configurar Workers híbridos da automação do Azure, consulte [automatizar recursos em seu datacenter ou nuvem usando o Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Depois de configurar um Hybrid Worker, criar um Webhook conforme descrito na seção [consumir com o Data Factory](#consume-with-data-factory).  A única diferença aqui é selecionar o **executado no** > **Hybrid Worker** opção ao configurar o Webhook.

Webhook de exemplo usando o Hybrid Worker:

![Exemplo Hybrid Worker Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exemplo de Runbook do PowerShell

O trecho de código a seguir é um exemplo de como executar a atualização do modelo do Azure Analysis Services usando um Runbook do PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Próximas etapas

[Exemplos](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
