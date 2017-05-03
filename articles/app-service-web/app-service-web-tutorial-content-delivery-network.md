---
title: "Adicionar uma Rede de Entrega de Conteúdo em um Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Adicione uma Rede de Entrega de Conteúdo em um Serviço de Aplicativo do Azure para entregar os arquivos estáticos dos nós de borda."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Adicionar uma Rede de Entrega de Conteúdo em um Serviço de Aplicativo do Azure

Neste tutorial, você adicionará uma Rede de Distribuição de Conteúdo (CDN) para o Serviço de Aplicativo do Azure para expor o conteúdo estático em um servidor de borda. Você criará um Perfil de CDN, que é uma coleção de até 10 pontos de extremidade de CDN.

A Rede de Distribuição de Conteúdo armazena em cache o conteúdo Web estático em locais estrategicamente posicionados para fornecer taxa de transferência máxima para a entrega de conteúdo aos usuários. As vantagens do uso da CDN para armazenar ativos de site da Web em cache incluem:

* Melhor desempenho e experiência de usuário para usuários finais, especialmente ao se usar aplicativos em que várias viagens de ida e volta são necessárias para carregar o conteúdo.
* Grande dimensionamento para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto.
* Distribuindo-se solicitações de usuário e fornecendo-se conteúdo de servidores de borda, menos tráfego é enviado à origem.

> [!TIP]
> Revise a lista atualizada dos [Locais POP da CDN do Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Implantar o exemplo

Para concluir este tutorial, você precisará de um aplicativo implantado no aplicativo Web. Siga o [início rápido do HTML estático](app-service-web-get-started-html.md) para ter uma base para este tutorial.

## <a name="step-1---login-to-azure-portal"></a>Etapa 1: Fazer logon no Portal do Azure

Primeiro, abra seu navegador favorito e navegue até o [Portal](https://portal.azure.com) do Azure.

## <a name="step-2---create-a-cdn-profile"></a>Etapa 2: Criar um perfil de CDN

Clique no botão `+ New` no painel de navegação esquerdo, clique em **Web + Móvel**. Na categoria Web + Móvel selecione **CDN**.

Especifique os seguintes campos:

| Campo | Valor de exemplo | Descrição |
|---|---|---|
| Nome | myCDNProfile | Um nome para o perfil da CDN. |
| Local padrão | Europa Ocidental | Esse é o local do Azure onde suas informações de perfil CDN serão armazenadas. Ele não tem impacto sobre os locais de ponto de extremidade CDN. |
| Grupo de recursos | myResourceGroup | Para saber mais sobre Grupos de Recursos, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups) |
| Tipo de preço  | Akamai Standard | Confira a [Visão geral da CDN](../cdn/cdn-overview.md#azure-cdn-features) para uma comparação de tipos de preço. |

Clique em **Criar**.

Abra o hub de grupos de recursos do painel de navegação esquerdo e selecione **myResourceGroup**. Na lista de recursos, selecione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Etapa 3: Criar um novo ponto de extremidade de CDN

Clique no **ponto de extremidade +** nos comandos ao lado da caixa de pesquisa para iniciar a folha de Criação do ponto de extremidade.

Especifique os seguintes campos:

| Campo | Valor de exemplo | Descrição |
|---|---|
| Nome |  | Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net` |
| Tipo de origem | Aplicativo Web | Selecionar um tipo de origem fornece menus contextuais para os campos restantes. Selecionar origem personalizada, fornecerá um campo de texto para o nome de host de origem. |
| Nome do host de origem | |  O menu suspenso listará todas as origens disponíveis do tipo especificado. Se você selecionou origem Personalizada como o tipo de Origem, você digitará o domínio de sua origem personalizada  |

Clique em **Adicionar**.

O Ponto de extremidade será criado e, após a criação do ponto de extremidade da Rede de distribuição de conteúdo, o status será atualizado para **executando**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Etapa 4 - Fornecer a partir da CDN do Azure

Agora que o Ponto de Extremidade da CDN está **em execução**, você deve ser capaz de acessar o conteúdo a partir do ponto de extremidade da CDN.

Considerando que usamos o [início rápido do HTML estático](app-service-web-get-started-html.md) como base para este tutorial, devemos ter as seguintes pastas disponíveis em nossa CDN: `css`, `img`, `js`.

Os caminhos de conteúdo entre a URL do aplicativo Web `http://<app_name>.azurewebsites.net/img/` e a URL de Ponto de Extremidade da CDN `http://<endpointname>.azureedge.net/img/` são iguais, isso significa que você pode simplesmente substituir o domínio do Ponto de Extremidade da CDN para qualquer conteúdo estático ser atendido pela CDN.

Vamos obter nossa primeira imagem do Ponto de Extremidade da CDN, navegando até a seguinte url no seu navegador favorito:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Agora que o conteúdo estático está disponível na CDN, você pode atualizar seu aplicativo para usar o ponto de extremidade da CDN para entregar o conteúdo para o usuário final.

Dependendo da linguagem usada para criar o seu site, podem haver muitas estruturas para ajudá-lo com o fallback da CDN. Por exemplo, o ASP.NET oferece suporte para [agrupamento e minificação](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn) que também habilita recursos de fallback de CDN.

Se sua linguagem não possui suporte para failback de CDN integrado ou uma biblioteca para tal, você pode usar uma estrutura javascript como [FallbackJS](http://fallback.io/), que oferece suporte para carregamento de [scripts](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [folhas de estilo](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) e [imagens](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>Etapa 5 - Limpar a CDN

Pode ser necessário, algumas vezes, forçar uma limpeza da CDN, se você deseja a expiração do conteúdo antes do tempo de vida útil (TTL) expirar.

É possível limpar manualmente da CDN do Azure, a partir da folha Perfil de CDN ou da folha Ponto de Extremidade da CDN. Se você selecionar a limpeza a partir da página Perfil, será necessário selecionar qual ponto de extremidade você deseja limpar.

Para limpar o conteúdo, digite os caminhos de conteúdo que você deseja limpar. Você pode passar um caminho de arquivo completo para limpar um arquivo individual ou um segmento de caminho para limpar e atualizar o conteúdo de uma pasta específica.

Depois que você tiver fornecido todos os caminhos de conteúdo que deseja excluir, clique em **Limpar**.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Etapa 6 - Mapear um domínio personalizado

Mapear um domínio personalizado para o seu ponto de extremidade da CDN fornece um domínio uniforme para o seu aplicativo Web.

Para mapear o domínio para o Ponto de Extremidade da CDN, você deve criar um registro CNAME com seu registrador de domínios.

> [!NOTE]
> Um registro CNAME é um recurso DNS que mapeia um domínio de origem, como `www.contosocdn.com` ou `static.contosocdn.com`, para um domínio de destino.

Nesse caso, adicionaremos um domínio de origem `static.contosocdn.com`, que irá apontar o domínio de destino, que é o Ponto de Extremidade da CDN.

| domínio de origem | domínio de destino |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

Na folha de visão geral do Ponto de Extremidade da CDN, clique no botão `+ Custom domain`.

Na folha Adicionar um domínio personalizado, insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira o nome de domínio no formato `static.contosocdn.com`.

Clique em **Adicionar**.

## <a name="step-7---version-content"></a>Etapa 7 - Conteúdo da versão

No painel de navegação do Ponto de Extremidade CDN esquerdo, selecione **Cache** nas configurações de título.

A folha **Cache** permite que você configure como a CDN lida com cadeias de caracteres de consulta na solicitação.

> [!NOTE]
> Para obter uma descrição das opções de comportamento da cadeia de caracteres de cache leia o tópico [Controle do comportamento de armazenamento em cache da CDN do Azure com cadeias de caracteres de consulta](../cdn/cdn-query-string.md).

Selecione **Armazenar em cache todas as URL exclusivas** no menu suspenso para saber o comportamento de armazenamento em cache da cadeia de caracteres de Consulta.

Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* [O que é a CDN do Azure](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [Como ativar HTTPS em um domínio personalizado da CDN do Azure](../cdn/cdn-custom-ssl.md)
* [Melhorar o desempenho compactando os arquivos na CDN do Azure](../cdn/cdn-improve-performance.md)
* [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](../cdn/cdn-preload-endpoint.md)

