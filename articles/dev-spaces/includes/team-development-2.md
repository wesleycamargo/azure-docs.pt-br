---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936390"
---
### <a name="run-the-service"></a>Executar o serviço

1. Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço `default/scott` recém-selecionado. 
1. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds list` novamente. Primeiro, você observará que uma instância de `mywebapi` agora está em execução no espaço `default/scott` (a versão em execução no `default` ainda está em execução, mas não está listada). Em segundo lugar, a URL de ponto de acesso de `webfrontend` é prefixada com o texto "scott.s.". Essa URL é exclusiva para o espaço `default/scott`. A URL especial significa que solicitações enviadas para a "URL de scott" tentarão primeiro ser encaminhadas para os serviços do espaço `default/scott`, mas, em caso de falha, recorrerão aos serviços do espaço `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Esse recurso interno do Azure Dev Spaces permite que você teste o código em um espaço compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de `mywebapi` com `webfrontend`, abra seu navegador na URL de ponto de acesso público de `webfrontend` e acesse a página Sobre. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (com a versão `mywebapi` em execução em `default`)
