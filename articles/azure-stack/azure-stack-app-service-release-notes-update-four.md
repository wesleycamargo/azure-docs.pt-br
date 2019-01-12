---
title: Notas de versão 4 de atualização do serviço de aplicativo no Azure Stack | Microsoft Docs
description: Saiba mais sobre as novidades na atualização de quatro para o serviço de aplicativo no Azure Stack, os problemas conhecidos e onde baixar a atualização.
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
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: b721545f27135e36b2999de5acc61e77539a94b2
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247309"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Serviço de aplicativo em notas de versão de atualização 4 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e correções no serviço de aplicativo do Azure no Azure Stack Update 4 e problemas conhecidos. Problemas conhecidos são divididos em questões relacionadas diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1809 seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar 1.4 de serviço de aplicativo do Azure.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no número de build do Azure Stack atualização 4 é **78.0.13698.5**

### <a name="prerequisites"></a>Pré-requisitos

Consulte a [documentação antes de você começar a usar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

Antes de começar a atualização do serviço de aplicativo do Azure no Azure Stack para 1.4:

- Verifique se todas as funções estão prontos na administração do serviço de aplicativo do Azure no Portal de administração do Azure Stack

- Fazer backup de bancos de dados mestre e o serviço de aplicativo:
  - AppService_Hosting;
  - AppService_Metering;
  - Mestre

- Fazer backup do compartilhamento de arquivo de conteúdo do aplicativo de locatário

- Distribuir a versão 1.9 do Marketplace da extensão de Script personalizado

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure Stack atualização 4 inclui os seguintes aprimoramentos e correções:

- Resolução para [2018 CVE-8600](https://aka.ms/CVE20188600) Cross Site Scripting vulnerabilidade.

- Adicionado suporte para a versão da API do serviço de aplicativo 2018-02-01

- As atualizações **aplicativo de locatário de serviço, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações para o serviço principal para melhorar a confiabilidade e mais fáceis de diagnóstico dos problemas comuns de habilitação de mensagens de erro.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
  - Adicionado NodeJS 10.6.0
  - NPM adicionado 6.1.0
  - Adicionado o Zulu OpenJDK 8.31.0.2
  - Adicionado o Tomcat 8.5.34 e 9.0.12
  - Versões do PHP adicionado:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Atualizar para versões do Python:
    - 2.7.15
    - 3.6.6
  - Git atualizado para Windows para v 2.17.1.2
  - Kudu atualizado para 78.11022.3613
  
- **Atualizações para o sistema operacional subjacente de todas as funções**:
  - [Atualização cumulativa 10 2018 do Windows Server 2016 para sistemas baseados em x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Resolvido o problema de validação de modelo ao implantar o Wordpress; DNN; e itens de galeria do Orchard CMS

- Resolvido o problema de configuração quando o Azure Stack gira o certificado do cliente do Azure Resource Manager

- Restaurado funcionalidade nas configurações de compartilhamento de recursos de origem entre no portal de locatário de serviço de aplicativo

- Exibir mensagem de erro na experiência do portal de administração do serviço de aplicativo quando o plano de controle do provedor de recursos não pode se conectar à instância do SQL Server configurada

- Verifique se o ponto de extremidade é especificado na cadeia de conexão de armazenamento personalizado quando especificado no novo aplicativo de função

### <a name="post-update-steps-optional"></a>Etapas posteriores à atualização (opcional)

Para clientes que desejem migrar para o banco de dados independente para o serviço de aplicativo do Azure existente nas implantações do Azure Stack, execute estas etapas depois que o serviço de aplicativo do Azure no Azure Stack 1.4 atualização foi concluída:

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
