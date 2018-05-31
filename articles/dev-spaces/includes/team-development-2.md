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
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367368"
---
### <a name="run-the-service"></a>Executar o serviço

1. Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço `scott` recém-selecionado. 
1. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds resource list` novamente. Primeiro, você observará que uma instância de `mywebapi` agora está em execução no espaço `scott` (a versão em execução no `default` ainda está em execução, mas não está listada). Em segundo lugar, a URL de ponto de acesso de `webfrontend` é prefixada com o texto *scott.s.*. Essa URL é exclusiva ao espaço `scott` e significa que as solicitações enviadas para a "URL de scott" tentarão primeiro ser encaminhadas para os serviços do espaço `scott` e recorrerão aos serviços do espaço `default`.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Essa funcionalidade interna do Azure Dev Spaces permite que você teste o código de ponta a ponta em um ambiente compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

## <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de `mywebapi` em conjunto com `webfrontend`, abra o navegador na URL de ponto de acesso público de webfrontend e acesse a página About. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (exibido pela versão de `mywebapi` em execução em `default`)