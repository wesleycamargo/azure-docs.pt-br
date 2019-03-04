---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 7dddf7e48913aea9d84e0f8b66c2c9d29449ee6f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825538"
---
### <a name="run-the-service"></a>Executar o serviço

1. Pressione F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no espaço _dev/scott_ recém-selecionado. 
1. Confirme se o serviço está sendo executado em seu próprio espaço executando `azds list-up` novamente. Você observará que uma instância de *mywebapi* agora está em execução no espaço _dev/scott_ (a versão em execução em _dev_ ainda está em execução, mas não está listada).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Execute `azds list-uris` e observe a URL de ponto de acesso para *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Use a URL com o *scott.s* prefixo para navegar até seu aplicativo. Observe que a URL atualizada ainda é resolvida. Essa URL é exclusiva para o espaço _dev/scott_. A URL especial significa que solicitações enviadas para a "URL de scott" tentarão primeiro ser encaminhadas para os serviços do espaço _dev/scott_, mas, em caso de falha, recorrerão aos serviços do espaço _dev_.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Esse recurso interno do Azure Dev Spaces permite que você teste o código em um espaço compartilhado, sem a necessidade de cada desenvolvedor recriar a pilha completa de serviços em seus espaços. Esse roteamento exige o código do aplicativo para encaminhar cabeçalhos de propagação, conforme ilustrado na etapa anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar o código em um espaço
Para testar a nova versão de *mywebapi* com *webfrontend*, abra o navegador na URL de ponto de acesso público de *webfrontend* e acesse a página Sobre. Você deverá ver a nova mensagem exibida.

Agora, remova a parte "scott.s." da URL e atualize o navegador. Você deverá ver o comportamento antigo (com a versão *mywebapi* em execução em _dev_).

Quando tiver um espaço _dev_, que sempre contém suas alterações mais recentes, e supondo que seu aplicativo tenha sido projetado para aproveitar o roteamento baseado em espaço do Dev Space, conforme descrito nesta seção do tutorial, deverá ser fácil ver como o Azure Dev Spaces podem ajudar bastante com o teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos_ os serviços em seu espaço privado, você poderá criar um espaço privado derivado de _dev_ e apenas "aumentar" os serviços em que de fato está trabalhando. A infraestrutura de roteamento do Azure Dev Spaces cuidará do restante utilizando tantos serviços de seu espaço privado quantos puder encontrar, e usando como padrão a versão mais recente em execução no espaço _dev_. E ainda melhor, _vários_ desenvolvedores podem desenvolver ativamente serviços diferentes ao mesmo tempo em seu próprio espaço sem interromper uns aos outros.

### <a name="well-done"></a>Muito bem!
Você concluiu o guia de introdução! Você aprendeu como:

> [!div class="checklist"]
> * Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
> * Desenvolva iterativamente o código em contêineres.
> * Desenvolva independentemente dois serviços separados e use a descoberta de serviço de DNS do Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolva e teste o código produtivamente em um ambiente de equipe.
> * Estabelecer uma linha de base de funcionalidade usando o Azure Dev Spaces para testar facilmente alterações isoladas dentro do contexto de um aplicativo de microsserviço maior

Agora que você explorou o Azure Dev Spaces, [compartilhe seu espaço de desenvolvimento com um membro da equipe](../articles/dev-spaces/how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpar
Para excluir completamente uma instância do Azure Dev Spaces em um cluster, incluindo todos os espaços de desenvolvimento e execução de serviços dentro dele, use o comando `az aks remove-dev-spaces`. Tenha em mente que essa ação é irreversível. Você pode adicionar suporte para Azure Dev Spaces novamente no cluster, mas isso será como se você estiver iniciando novamente. Os serviços e os espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores de Azure Dev Spaces em sua assinatura ativa e então exclui o controlador de Azure Dev Spaces associado ao cluster AKS ‘myaks’ do grupo de recursos 'myaks-rg'.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```