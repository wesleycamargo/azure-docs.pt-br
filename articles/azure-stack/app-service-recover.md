---
title: Recuperar o serviço de aplicativo no Azure Stack | Microsoft Docs
description: Orientações detalhadas para a recuperação de desastres do serviço de aplicativo do Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340238"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Recuperação do serviço de aplicativo no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre as ações para levar o serviço de aplicativo para recuperação de desastres.

As ações a seguir devem ser executadas para recuperar o serviço de aplicativo no Azure Stack de backup:
1.  Restaurar os bancos de dados do serviço de aplicativo
2.  Restaurar o conteúdo de compartilhamento do servidor de arquivo
3.  Restaurar serviços e funções de serviço de aplicativo

Se o armazenamento do Azure Stack foi usado para armazenamento de aplicativos de funções, em seguida, você também precisa levar as etapas para restaurar aplicativos de funções.

## <a name="restore-the-app-service-databases"></a>Restaurar os bancos de dados do serviço de aplicativo
Os bancos de dados do aplicativo de serviço SQL Server devem ser restaurados em uma instância do SQL Server pronta de produção. 

Após [Preparando a instância do SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) para hospedar os bancos de dados do serviço de aplicativo, use estas etapas para restaurar os bancos de dados de backup:

1. Entrar para o SQL Server que hospedará os bancos de dados recuperados do serviço de aplicativo com permissões de administrador.
2. Use os comandos a seguir para restaurar os bancos de dados do serviço de aplicativo em um prompt de comando executando com permissões de administrador:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Verifique se ambos os bancos de dados do serviço de aplicativo foi restaurados com êxito e sair do SQL Server Management Studio.

> [!NOTE]
> Para se recuperar de uma falha de instância de cluster de failover, [estas instruções](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Restaurar o conteúdo de compartilhamento de arquivo do serviço de aplicativo
Após [Preparando o servidor de arquivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) para hospedar o compartilhamento de arquivos do serviço de aplicativo, você precisa restaurar o conteúdo do compartilhamento de arquivo locatário de backup. Você pode usar qualquer método que você tem disponível para copiar os arquivos para o local de compartilhamento de arquivo de serviço de aplicativo recém-criado. Executar esse exemplo no servidor de arquivos de usará o PowerShell e robocopy para se conectar a um compartilhamento remoto e copie os arquivos para o compartilhamento:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Além de copiar o conteúdo do compartilhamento de arquivo, você também deve redefinir permissões no compartilhamento de arquivos em si. Para fazer isso, abra um prompt de comando administrativo no computador do servidor de arquivo e execute o **ReACL.cmd** arquivo. O **ReACL.cmd** arquivo está localizado nos arquivos de instalação do serviço de aplicativo na **BCDR** directory.

## <a name="restore-app-service-roles-and-services"></a>Restaurar serviços e funções de serviço de aplicativo
Depois que os bancos de dados do serviço de aplicativo e o conteúdo do compartilhamento de arquivo foram restaurados, em seguida você precisa usar o PowerShell para restaurar as funções de serviço de aplicativo e serviços. Essas etapas irá restaurar os segredos do serviço de aplicativo e configurações de serviço.  

1. Faça logon no controlador de serviço de aplicativo **CN0-VM** VM conforme **roleadmin** usando a senha fornecida durante a instalação do serviço de aplicativo. 
    > [!TIP]
    > Você precisará modificar o grupo de segurança de rede da VM para permitir conexões de RDP. 
2. Cópia de **SystemSecrets.JSON** arquivo localmente para a VM do controlador. Você precisará fornecer o caminho para esse arquivo como o `$pathToExportedSecretFile` parâmetro na próxima etapa. 
3. Use os seguintes comandos em uma janela de console do PowerShell com privilégios elevados para restaurar serviços e funções de serviço de aplicativo:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> É altamente recomendável para fechar esta sessão do PowerShell quando o comando for concluído.

## <a name="restore-function-apps"></a>Restaurar aplicativos de funções 
O serviço de aplicativo para o Azure Stack não dá suporte à restauração de dados que não seja o conteúdo do compartilhamento de arquivos ou aplicativos de usuário do locatário. Portanto, eles devem ser submetidos a backup e recuperados fora do serviço de aplicativo de backup e restauração de operações. Se o armazenamento do Azure Stack foi usado para armazenamento de aplicativos de funções, as etapas a seguir devem ser tomadas para recuperar dados perdidos:

1. Crie uma nova conta de armazenamento a ser usado pelo aplicativo de funções. Esse armazenamento pode ser qualquer armazenamento compatível, o armazenamento do Azure ou armazenamento do Azure Stack.
2. Recupere a cadeia de conexão para o armazenamento.
3. Abra o portal de função, navegue até o aplicativo de funções.
4. Navegue até a **recursos da plataforma** guia e clique em **configurações do aplicativo**.
5. Alteração **AzureWebJobsDashboard** e **AzureWebJobsStorage** para a nova cadeia de caracteres de conexão e clique em **salvar**.
6. Alterne para **visão geral**.
7. Reinicie o aplicativo. Pode levar várias tentativas para limpar todos os erros.

## <a name="next-steps"></a>Próximas etapas
[Serviço de aplicativo na visão geral do Azure Stack](azure-stack-app-service-overview.md)