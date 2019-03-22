---
title: Fazer backup de serviço de aplicativo no Azure Stack | Microsoft Docs
description: Orientação detalhada para o backup do serviço de aplicativo do Azure Stack.
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
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340545"
---
# <a name="back-up-app-service-on-azure-stack"></a>Fazer backup do serviço de aplicativo no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre como fazer backup do serviço de aplicativo no Azure Stack.

> [!IMPORTANT]
> Serviço de aplicativo no Azure Stack não é feito como parte da [backup de infraestrutura do Azure Stack](azure-stack-backup-infrastructure-backup.md). Como um operador de pilha do Azure, você deve tomar medidas para garantir que o serviço de aplicativo podem ser recuperado com êxito se necessário.

Serviço de aplicativo do Azure no Azure Stack tem quatro componentes principais a serem considerados no planejamento para recuperação de desastres:
1. A infraestrutura do provedor de recursos; funções de servidor, camadas de trabalhador, etc. 
2. Os segredos do serviço de aplicativo
3. O aplicativo de serviço SQL Server que hospeda e bancos de dados de medição
4. A serviço de aplicativo usuário carga de trabalho de conteúdo armazenada no compartilhamento de arquivo de serviço de aplicativo   

## <a name="back-up-app-service-secrets"></a>Fazer backup de segredos do serviço de aplicativo
Ao recuperar o serviço de aplicativo de backup, você precisa fornecer as chaves de serviço de aplicativo usadas pela implantação inicial. Essas informações devem ser salvos, assim que o serviço de aplicativo é implantado e armazenado em um local seguro com êxito. A configuração de infraestrutura do provedor de recursos será recriada de backup durante a recuperação usando cmdlets do PowerShell de recuperação de serviço de aplicativo.

Use o portal de administração para fazer backup de segredos do serviço de aplicativo seguindo estas etapas: 

1. Entre no portal de administração do Azure Stack como administrador de serviços.

2. Navegue até **serviço de aplicativo** -> **segredos**. 

3. Selecione **baixar segredos**.

   ![Baixar segredos](./media/app-service-back-up/download-secrets.png)

4. Quando segredos prontos para download, clique em **salve** e armazenar os segredos do serviço de aplicativo (**SystemSecrets.JSON**) o arquivo em um local seguro. 

   ![Salvar segredos](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Repita essas etapas sempre que os segredos do serviço de aplicativo são girados.

## <a name="back-up-the-app-service-databases"></a>Fazer backup de bancos de dados do serviço de aplicativo
Para restaurar o serviço de aplicativo, você terá a **Appservice_hosting** e **Appservice_metering** backups de banco de dados. É recomendável usar planos de manutenção do SQL Server ou o servidor de Backup do Azure para garantir que esses bancos de dados são submetidos a backup e salvos com segurança com regularidade. No entanto, qualquer método de garantir backups regulares do SQL são criados podem ser usados.

Para fazer backup desses bancos de dados enquanto estiver conectado no SQL Server manualmente, você pode usar os seguintes comandos do PowerShell:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Se você precisar fazer backup de bancos de dados AlwaysOn do SQL, siga [estas instruções](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Depois que todos os bancos de dados tiveram sido backup realizados com êxito, copie os arquivos. bak para um local seguro, juntamente com as informações de segredos do serviço de aplicativo.

## <a name="back-up-the-app-service-file-share"></a>Fazer backup do compartilhamento de arquivo do serviço de aplicativo
Informações do aplicativo serviço de aplicativo armazenamentos de locatário no compartilhamento de arquivos. Isso deve ser feito com regularidade, juntamente com os bancos de dados do serviço de aplicativo para que o mínimo possível de dados será perdido se uma restauração seja necessária. 

Para fazer backup de compartilhamento de arquivos do serviço de aplicativo, conteúdo, que você pode usar o servidor de Backup do Azure ou outro método para copiar regularmente o compartilhamento de arquivos para o local de conteúdo salvar todas as informações de recuperação anterior. 

Por exemplo, você pode usar estas etapas para usar o robocopy de uma sessão de console do Windows PowerShell (não o PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Próximas etapas
[Restaurar o serviço de aplicativo no Azure Stack](app-service-recover.md)