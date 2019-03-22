---
title: Nome do emissor e chave do emissor nos Serviços BizTalk | Microsoft Docs
description: Saiba como recuperar o nome do emissor e chave do emissor para barramento de serviço ou controle de acesso (ACS) nos serviços BizTalk. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 5eac98ec88b960956c9a0931673e67f530aef8da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542175"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Serviços BizTalk: Nome do emissor e chave do emissor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure usam o nome e a chave do emissor do Barramento de Serviço e o nome e a chave do emissor do Controle de Acesso. Especificamente:

| Tarefa | Qual nome e chave do emissor |
| --- | --- |
| Implantando seu aplicativo do Visual Studio |Nome e chave do emissor do Controle de Acesso |
| Configurando o Portal dos Serviços BizTalk do Azure |Nome e chave do emissor do Controle de Acesso |
| Criando retransmissões de LOB com os Serviços do Adaptador do BizTalk no Visual Studio |Nome e chave do emissor do Service Bus |

Este tópico lista as etapas para recuperar o Nome e a Chave do Emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome e chave do emissor do Controle de Acesso
O nome e a chave do emissor do Controle de Acesso são usados pelo seguinte:

* Seu aplicativo de serviço BizTalk do Azure criado no Visual Studio: Para implantar com êxito seu aplicativo do BizTalk Service no Visual Studio para o Azure, você pode inserir o nome do emissor de controle de acesso e a chave do emissor. 
* O Portal de Serviços BizTalk do Azure: Quando você cria um BizTalk Service e abre o Portal de Serviços BizTalk, seu nome de emissor do controle de acesso e a chave do emissor são automaticamente registrados para implantações com os mesmos valores de controle de acesso.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Obtenha o Nome e a Chave do Emissor do Controle de Acesso

Para usar o ACS para autenticação e obter os valores de Nome do Emissor e Chave do Emissor, as etapas gerais incluem:

1. Instalar os [cmdlets do Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adicionar sua conta do Azure: `Add-AzureAccount`
3. Retornar o nome da sua assinatura: `get-azuresubscription`
4. Selecionar sua assinatura: `select-azuresubscription <name of your subscription>` 
5. Criar um novo namespace: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exemplo:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Quando o novo namespace do ACS for criado (o que pode levar vários minutos), os valores de Nome do Emissor e Chave do Emissor serão listados na cadeia de conexão: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Resumidamente:  
Nome do Emissor = SharedSecretIssuer  
Chave do Emissor = SharedSecretKey

Mais no cmdlet [New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome e chave do emissor do Service Bus
O nome e a chave do emissor do Barramento de Serviço são usados pelos Serviços do Adaptador do BizTalk. Em seu projeto de Serviços BizTalk no Visual Studio, você usa os Serviços do Adaptador BizTalk para se conectar a um sistema de Linha de Negócios (LOB) local. Para se conectar, você cria a retransmissão de LOB e insere os detalhes do seu sistema de LOB. Ao fazer isso, você também insere o nome e a chave do emissor do Barramento de Serviço.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para recuperar o nome e a chave do emissor do Barramento de Serviço
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Procure por **Barramento de Serviço**e selecione seu namespace. 
3. Abra as propriedades **políticas de acesso compartilhado**, selecione a sua política e veja a **Cadeia de caracteres de Conexão** para o nome e os valores de chave.  

## <a name="next"></a>Avançar
Tópicos adicionais dos Serviços BizTalk do Azure:

* [Instalando o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Veja também
* [Como: Usar o serviço de gerenciamento do ACS para configurar identidades de serviço](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Serviços BizTalk: Developer, Basic, Standard e Premium gráfico de edições](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Serviços BizTalk: O provisionamento](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Serviços BizTalk: Gráfico de Status de provisionamento](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: Guias painel, Monitor e escala](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: Backup e restauração](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: Throttling](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

