---
title: 3 notas de versão de atualização do serviço de aplicativo no Azure Stack | Microsoft Docs
description: Saiba mais sobre as novidades na atualização de três para serviço de aplicativo no Azure Stack, os problemas conhecidos e onde baixar a atualização.
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
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: 3e88e0a3337eafdd25c9c0cc655912a4cdbd3b68
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079524"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Serviço de aplicativo em notas de versão de atualização 3 do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e correções no serviço de aplicativo do Azure no Azure Stack Update 3 e problemas conhecidos. Problemas conhecidos são divididos em questões relacionadas diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1807 seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar o serviço de aplicativo Azure 1.3.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no número de build do Azure Stack Update 3 é **74.0.13698.31**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de você começar a usar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

Antes de começar a atualização do serviço de aplicativo do Azure no Azure Stack para 1.3, certifique-se de todas as funções estão prontos na administração do serviço de aplicativo do Azure no Portal de administração do Azure Stack

![Status da função de serviço de aplicativo](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure Stack Update 3 inclui os seguintes aprimoramentos e correções:

- Suporte para uso do SQL Server AlwaysOn para bancos de dados do provedor de recursos do serviço de aplicativo do Azure.

- Adicionado novo parâmetro de ambiente ao script Create AADIdentityApp auxiliar para ajudá-lo de direcionamento diferentes regiões do AAD.

- As atualizações **aplicativo de locatário de serviço, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações para o serviço principal para melhorar a confiabilidade e mais fáceis de diagnóstico dos problemas comuns de habilitação de mensagens de erro.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
  - Adição do ASP.Net Core 2.1.2
  - NodeJS adicionado 10.0.0
  - Adicionado o Zulu OpenJDK 8.30.0.1
  - Adicionado o Tomcat 8.5.31 e 9.0.8
  - Versões do PHP adicionado:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Adicionado Wincache 2.0.0.8
  - Git atualizado para Windows para v 2.17.1.2
  - Kudu atualizado para 74.10611.3437
  
- **Atualizações para o sistema operacional subjacente de todas as funções**:
  - [Atualização de pilha para o Windows Server 2016 para sistemas baseados em x64 (KB4132216) de manutenção](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [07 de 2018 atualização cumulativa do Windows Server 2016 para sistemas baseados em x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Etapas posteriores à atualização (opcional)

Para clientes que desejem migrar para o banco de dados independente para o serviço de aplicativo do Azure existente nas implantações do Azure Stack, execute estas etapas depois que o serviço de aplicativo do Azure no Azure Stack 1.3 atualização foi concluída:

> [!IMPORTANT]
> Esse procedimento leva cerca de 5 a 10 minutos.  Esse procedimento envolve encerrar as sessões de logon de banco de dados existente.  Planejar o tempo de inatividade migrar e validar o serviço de aplicativo do Azure em após a migração do Azure Stack
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

1. Convertendo um banco de dados para parcialmente independente.  Esta etapa incorrerá em tempo de inatividade como todas as sessões ativas precisará ser eliminado

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

- Trabalhadores são incapazes de alcançar o servidor de arquivos quando o serviço de aplicativo é implantado em uma rede virtual existente e o servidor de arquivos só está disponível na rede privada.  Isso também é chamado no serviço de aplicativo do Azure na documentação de implantação do Azure Stack.

Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
 * Fonte: qualquer
 * Intervalo de porta de origem: *
 * Destino: Endereços IP
 * Intervalo de endereços IP de destino: intervalo de IPs para seu servidor de arquivos
 * Intervalo de porta de destino: 445
 * Protocolo: TCP
 * Ação: permitir
 * Prioridade: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para administradores de nuvem, a operação de serviço de aplicativo do Azure no Azure Stack

Consulte a documentação no [notas de versão do Azure Stack 1807](azure-stack-update-1807.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [serviço de aplicativo do Azure na visão geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como se preparar implantar o serviço de aplicativo no Azure Stack, consulte [antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md).
