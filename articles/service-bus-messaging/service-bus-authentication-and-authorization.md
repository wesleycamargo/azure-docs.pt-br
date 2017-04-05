---
title: "Autenticação e Autorização do Barramento de Serviço do Azure | Microsoft Docs"
description: "Visão geral da autenticação SAS."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 8e113a78519444fd1e0a9da89ec95aa0dccebb2b
ms.lasthandoff: 03/27/2017


---
# <a name="service-bus-authentication-and-authorization"></a>Autenticação e autorização do Barramento de Serviço
Os aplicativos podem se autenticar no Barramento de Serviço do Azure usando a autenticação SAS (assinatura de acesso compartilhado) ou por meio do Controle de Acesso do Active Directory do Azure (também conhecido como Serviço de Controle de Acesso ou ACS). A autenticação SAS permite que os aplicativos se autentiquem no Barramento de Serviço usando uma tecla de acesso configurada no namespace ou em uma entidade com a qual tenha direitos específicos associados. Em seguida, você pode usar essa tecla para gerar um token SAS que os clientes podem usar para se autenticar no Barramento de Serviço.

> [!IMPORTANT]
> É recomendável o SAS em vez do ACS, pois ele fornece um esquema de autenticação simples, flexível e fácil de usar no Barramento de Serviço. Os aplicativos podem usar SAS em cenários em que eles não precisam gerenciar a noção de um "usuário" autorizado. 

## <a name="shared-access-signature-authentication"></a>Autenticação SAS
[autenticação SAS](service-bus-sas.md) permite que você conceda a um usuário acesso a recursos de Barramento de Serviço com direitos específicos. A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace do Barramento de Serviço. A chave se aplica a todas as entidades de mensagens nesse namespace. Também é possível configurar chaves em tópicos e filas do Barramento de Serviço. Também há suporte para SAS na [Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md).

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace, fila ou tópico que consista no seguinte:

* *KeyName* , que identifica a regra.
* *PrimaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *SecondaryKey* , que é uma chave de criptografia usada para assinar/validar tokens SAS.
* *Direitos* , que representa a coleção de direitos de Escuta, Envio ou Gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a tecla correspondente. Até 12 regras de autorização podem ser configuradas em um namespace, fila ou tópico do Barramento de Serviço. Por padrão, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## <a name="acs-authentication"></a>Autenticação ACS
A autenticação do Barramento de Serviço por meio do ACS é gerenciada através de um namespace de complemento "-sb" do ACS. Se quiser que um namespace do ACS complementar seja criado para um namespace do Barramento de Serviço, você não pode criar o seu namespace do Barramento de Serviço usando o portal clássico do Azure. Você deve criar o namespace usando o cmdlet do PowerShell [New-AzureSBNamespace](/powershell/servicemanagement/azure.compute/v1.6.1/New-AzureSBNamespace). Por exemplo:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar a criação de um namespace do ACS, emita o seguinte comando:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por exemplo, se você criar um namespace de Barramento de Serviço chamado **contoso.servicebus.windows.net**, um namespace complementar ACS chamado **contoso-sb.accesscontrol.windows.net** será provisionado automaticamente. Para todos os namespaces que foram criados antes de agosto de 2014, um namespace do ACS de acompanhamento também foi criado.

Um "proprietário" de identidade de serviço padrão, com todos os direitos, é provisionado por padrão neste namespace de complemento do ACS. Você pode obter controle refinado de qualquer entidade de Barramento de Serviço por meio do ACS configurando as relações de confiança apropriadas. Você pode configurar identidades de serviço adicionais para gerenciar o acesso a entidades do Barramento de Serviço.

Para acessar uma entidade, o cliente solicita um token SWT do ACS com as declarações adequadas apresentando suas credenciais. O token SWT deve ser enviado como parte da solicitação ao Barramento de Serviço para habilitar a autorização do cliente para o acesso à entidade.

O suporte à autenticação ACS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. Essa autenticação inclui suporte para um [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão do ACS.

## <a name="next-steps"></a>Próximas etapas
Continue lendo [Autenticação de Barramento de Serviço com Assinaturas de Acesso Compartilhado](service-bus-sas.md) para obter mais detalhes sobre SAS.

Para informações correspondentes sobre a autenticação e autorização da Retransmissão do Azure, confira [Autenticação e autorização da Retransmissão do Azure](../service-bus-relay/relay-authentication-and-authorization.md). 


