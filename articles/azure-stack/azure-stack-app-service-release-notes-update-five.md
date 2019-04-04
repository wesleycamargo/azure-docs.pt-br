---
title: 5 notas de versão de atualização do serviço de aplicativo no Azure Stack | Microsoft Docs
description: Saiba mais sobre as novidades na atualização de cinco para serviço de aplicativo no Azure Stack, os problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 851673de4a711f5eb64228233ea7703ef86bfa7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500195"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Serviço de aplicativo em notas de versão de atualização 5 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e correções no serviço de aplicativo do Azure no Azure Stack Update 5 e problemas conhecidos. Problemas conhecidos são divididos em questões relacionadas diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização de 1901 ao seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar 1.5 de serviço de aplicativo do Azure.


## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no número de build do Azure Stack com atualização 5 é **80.0.2.15**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de você começar a usar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

Antes de começar a atualização do serviço de aplicativo do Azure no Azure Stack para 1.5:

- Verifique se todas as funções estão prontos na administração do serviço de aplicativo do Azure no Portal de administração do Azure Stack

- Fazer backup de bancos de dados mestre e o serviço de aplicativo:
  - AppService_Hosting;
  - AppService_Metering;
  - Mestre

- Fazer backup do compartilhamento de arquivo de conteúdo do aplicativo de locatário

- Distribuir o **extensão de Script personalizado** versão **1.9.1** do Marketplace

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure Stack com atualização 5 inclui os seguintes aprimoramentos e correções:

- As atualizações **aplicativo de locatário de serviço, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- As atualizações **tempo de execução do Azure Functions** à **v1.0.12205**.

- As atualizações **ferramentas do Kudu** para resolver problemas com o estilo e a funcionalidade para clientes operando **desconectado** Azure Stack. 

- Atualizações para o serviço principal para melhorar a confiabilidade e mais fáceis de diagnóstico dos problemas comuns de habilitação de mensagens de erro.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
  - Adicionado o ASP.NET Core 2.1.6 e 2.2.0
  - Adicionado NodeJS 10.14.1
  - Adicionado NPM 6.4.1
  - Kudu atualizado para 79.20129.3767
  
- **Atualizações para o sistema operacional subjacente de todas as funções**:
  - [02 de 2019 atualização cumulativa do Windows Server 2016 para sistemas baseados em x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Etapas de pós-implantação

> [!IMPORTANT]  
> Se você tiver fornecido a RP de serviço de aplicativo com uma sempre na instância do SQL, você deve [adicionar os bancos de dados appservice_hosting e appservice_metering a um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizar os bancos de dados para evitar a perda de serviço no evento de um failover de banco de dados.

### <a name="post-update-steps"></a>Etapas de pós-atualização

Para clientes que desejem migrar para o banco de dados independente para o serviço de aplicativo do Azure existente nas implantações do Azure Stack, execute essas etapas após o serviço de aplicativo do Azure na atualização 1.5 do Azure Stack:

> [!IMPORTANT]
> O procedimento de migração leva aproximadamente 5 a 10 minutos.  O procedimento envolve encerrar as sessões de logon de banco de dados existente.  Planeje o tempo de inatividade migrar e validar o serviço de aplicativo do Azure em após a migração do Azure Stack.  Se você concluir essas etapas após a atualização para o serviço de aplicativo do Azure no Azure Stack 1.3 essas etapas não são necessárias.
>
>

1. Adicionar [AppService bancos de dados (appservice_hosting e appservice_metering) para um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Habilitar contido banco de dados
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Convertendo um banco de dados para parcialmente independente, a conversão incorrerá em tempo de inatividade como todas as sessões ativas precisará ser eliminado

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Validar

1. Verifique se o SQL Server tem contenção habilitada

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Verifique o comportamento independente existente
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Trabalhadores são incapazes de alcançar o servidor de arquivos quando o serviço de aplicativo é implantado em uma rede virtual existente e o servidor de arquivos só está disponível na rede privada, conforme destacado no serviço de aplicativo do Azure na documentação de implantação do Azure Stack.

Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB. Vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
 * Origem: Qualquer
 * Intervalo de porta de origem: *
 * Destino: Endereços IP
 * Intervalo de endereços IP de destino: Intervalo de IPs para seu servidor de arquivos
 * Intervalo de porta de destino: 445
 * Protocolo: TCP
 * Ação: PERMITIR
 * Prioridade: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para administradores de nuvem, a operação de serviço de aplicativo do Azure no Azure Stack

Consulte a documentação no [notas de versão do Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [serviço de aplicativo do Azure na visão geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como se preparar implantar o serviço de aplicativo no Azure Stack, consulte [antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md).
