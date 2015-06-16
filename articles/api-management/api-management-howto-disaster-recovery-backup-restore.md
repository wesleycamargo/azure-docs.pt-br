<properties 
	pageTitle="Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure" 
	description="Saiba como usar o backup e restauração para executar a recuperação de desastres no Gerenciamento de API no Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure

Ao escolher publicar e gerenciar suas APIs através do Gerenciamento de API do Azure, você está tirando proveito de muitos recursos de tolerância e infraestrutura que você precisaria criar, implementar e gerenciar. A plataforma Azure atenua grande parte das falhas potenciais a uma fração do custo.

Para se recuperar de problemas de disponibilidade que afetam a região onde o serviço de Gerenciamento de API está hospedado, você deverá estar pronto para reconstituir seu serviço em uma região diferente a qualquer momento. Dependendo de suas metas de disponibilidade e objetivo de tempo de recuperação, você pode querer reservar um serviço de backup em uma ou mais regiões e tentar manter suas configurações e conteúdos na sincronização com o serviço ativo. O backup do serviço e o recurso de restauração fornece o bloco de criação necessário para implementação de sua estratégia de recuperação de desastre.

O recurso de backup e restauração do serviço está disponível através da API REST de Gerenciamento de Serviço. Consulte [Autenticando as solicitações do Gerenciador de Recursos do Azure][Autenticando as solicitações do Gerenciador de Recursos do Azure] para obter instruções sobre como obter acesso para a API.

## Neste tópico

-   [Fazer backup de um serviço de Gerenciamento de API][Fazer backup de um serviço de Gerenciamento de API]
-   [Restaurar um serviço de Gerenciamento de API][Restaurar um serviço de Gerenciamento de API]

## <a name="step1"> </a>Fazer backup de um serviço de Gerenciamento de API

Para fazer backup de um serviço de Gerenciamento de API, execute a seguinte solicitação HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

onde:

-   `subscriptionId` - ID da assinatura contendo o serviço de Gerenciamento de API que você está tentando fazer backup
-   `resourceGroupName` - uma cadeia de caracteres na forma de “Api-Default-{service-region}”, onde `service-region` identifica a região do Azure onde o serviço de Gerenciamento de API que você está tentando fazer backup é hospedado, por exemplo, `North-Central-US`
-   `serviceName` - o nome do serviço de Gerenciamento de API que você está fazendo um backup em específico, no momento de sua criação
-   `api-version` - substituir por `2014-02-14`

No corpo da solicitação, especifique a conta de armazenamento do Azure de destino, chave de acesso, nome do contêiner Blob e nome de backup:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Defina o valor do cabeçalho de solicitação `Content-Type` para `application\json`.

O backup é uma operação longa de execução que pode levar vários minutos para concluir. Se a solicitação foi bem sucedida e o processo de backup foi iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho `Location`. Faça solicitações “GET” para a URL no cabeçalho do `Location` para localizar o status da operação. Enquanto o backup está em andamento, você continuará a receber um código de status “202 Aceito”. Um código de resposta de `200 OK` indicará a conclusão com sucesso da operação de backup.

**Observação**:

-   o **contêiner** especificado no corpo solicitado **deve existir**.
-   Enquanto o backup está em andamento, você **não deve tentar quaisquer operações de gerenciamento de serviço**, assim como atualização ou downgrade, alteração do nome do domínio, etc.
-   A restauração de um **backup é garantida somente por 7 dias** desde o momento de sua criação.
-   Os **dados de uso** usados para a criação de relatórios de análise **não estão incluídos** no backup. Use o [API REST de Gerenciamento de API do Azure][API REST de Gerenciamento de API do Azure] para recuperar periodicamente os relatórios analíticos por questões de segurança.
-   A frequência com que você executa os backups de serviço afetarão seu objetivo do ponto de recuperação. Para minimizar, aconselhamos a implementação de backups regular, bem como a execução de backups sob demanda depois de fazer alterações importantes para seu serviço de Gerenciamento de API.
-   As **alterações** feitas à configuração de serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto a operação de backup está em andamento **podem não ser incluídas no backup e, portanto, serão perdidas**.

## <a name="step2"> </a>Restaurar um serviço de Gerenciamento de API

Para restaurar um serviço de Gerenciamento de API de um backup criado anteriormente faz a seguinte solicitação HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

onde:

-   `subscriptionId` - ID da assinatura contendo o serviço de Gerenciamento de API que você está restaurando um backup em
-   `resourceGroupName` - uma cadeia de caracteres na forma de “Api-Default-{service-region}”, onde `service-region` identifica a região do Azure onde o serviço de Gerenciamento de API que você está restaurando é armazenado, por exemplo, `North-Central-US`
-   `serviceName` - o nome do serviço de Gerenciamento de API que está sendo restaurado em específico no momento de sua criação
-   `api-version` - substituir por `2014-02-14`

No corpo da solicitação, especifique o local de arquivo de backup, por exemplo, conta de armazenamento do Azure, chave de acesso, nome do contêiner Blob e nome de backup:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Defina o valor do cabeçalho de solicitação `Content-Type` para `application\json`.

Restaure uma operação longa de execução que pode levar até 30 minutos ou mais para concluir. Se a solicitação foi bem sucedida e o processo de restauração foi iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho do `Location`. Faça solicitações “GET” para a URL no cabeçalho do `Location` para localizar o status da operação. Enquanto a restauração está em andamento, você continuará a receber o código de status “202 Aceito”. Um código de resposta de `200 OK` indicará a conclusão com sucesso da operação de restauração.

**Observação**:

-   **a camada** do serviço que está sendo restaurada **deve combinar** com a camada do serviço de backup sendo restaurada.
-   As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto restauram a operação que está em andamento **podem ser substituídas**.

  [Autenticando as solicitações do Gerenciador de Recursos do Azure]: http://msdn.microsoft.com/library/dn790557.aspx
  [Fazer backup de um serviço de Gerenciamento de API]: #step1
  [Restaurar um serviço de Gerenciamento de API]: #step2
  [API REST de Gerenciamento de API do Azure]: http://msdn.microsoft.com/library/azure/dn781421.aspx

<!--HONumber=46--> 
 