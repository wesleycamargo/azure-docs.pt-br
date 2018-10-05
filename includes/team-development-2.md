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
ms.openlocfilehash: 8580e19f1955becb2cfaee13742054af1a9edb7f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410436"
---
### <a name="run-the-service"></a>Executar o serviço

1. Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço `default/scott` recém-selecionado. 
1. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds list-up` novamente. Primeiro, você observará que uma instância de `mywebapi` agora está em execução no espaço `default/scott` (a versão em execução no `default` ainda está em execução, mas não está listada). Se você executar `azds list-uris`, observará que a URL de ponto de acesso para `webfrontend` é prefixada com o texto "scott.s.". Essa URL é exclusiva para o espaço `default/scott`. A URL especial significa que solicitações enviadas para a "URL de scott" tentarão primeiro ser encaminhadas para os serviços do espaço `default/scott`, mas, em caso de falha, recorrerão aos serviços do espaço `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Esse recurso interno do Azure Dev Spaces permite que você teste o código em um espaço compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de `mywebapi` com `webfrontend`, abra seu navegador na URL de ponto de acesso público de `webfrontend` e acesse a página Sobre. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (com a versão `mywebapi` em execução em `default`).
