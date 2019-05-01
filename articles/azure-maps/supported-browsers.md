---
title: SDK da Web com suporte a navegadores - mapas do Azure | Microsoft Docs
description: Saiba mais sobre navegadores com suporte para o SDK Web do Azure mapas
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686638"
---
# <a name="web-sdk-supported-browsers"></a>Navegadores compatíveis com o SDK Web

SDK da Web de mapas do Azure fornece uma função auxiliar chamada [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Essa função detecta se um navegador da web tem o conjunto mínimo de recursos de WebGL necessários para dar suporte ao carregamento e renderizar o controle de mapa. Aqui está um exemplo de como usar a função:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

SDK da Web de mapas do Azure suporta os seguintes navegadores da área de trabalho:

- Microsoft Edge (versão atual e anterior)
- Google Chrome (versão atual e anterior)
- Mozilla Firefox (versão atual e anterior)
- Apple Safari (Mac OS X) (versão atual e anterior)

Consulte também [direcionar os navegadores herdados](#Target-Legacy-Browsers) mais adiante neste artigo.

## <a name="mobile"></a>Móvel

SDK da Web de mapas do Azure dá suporte a navegadores móveis a seguir:

- Android
  - Versão atual do Chrome no Android 6.0 e versões posteriores
  - Chrome WebView no Android 6.0 e versões posteriores
- iOS
  - Mobile Safari versão principal atual e anterior do iOS
  - UIWebView e WKWebView na versão principal atual e anterior do iOS
  - Versão atual do Chrome para iOS

> [!TIP]
> Se você estiver inserindo um mapa de dentro de um aplicativo móvel usando um controle WebView, talvez você prefira usar o [pacote npm do SDK do Azure mapas Web](https://www.npmjs.com/package/azure-maps-control) em vez de referenciar a versão do SDK que esteja hospedada no fornecimento de conteúdo do Azure Rede. Essa abordagem reduz o tempo de carregamento porque o SDK é já estar no dispositivo do usuário e não precisa ser baixado em tempo de execução.

## <a name="nodejs"></a>Node.js

Também há suporte para os seguintes módulos do SDK para Web em Node. js:

- Módulo de serviços ([documentação](how-to-use-services-module.md) | [módulo npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Navegadores herdados de destino

Você poderá direcionar os navegadores mais antigos que não suportam WebGL ou que têm apenas suporte limitado para ele. Nesses casos, é recomendável que você use serviços de mapas do Azure junto com um controle de mapa de código-fonte aberto semelhante [Encarte](https://leafletjs.com/). Aqui está um exemplo:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas do Azure + Encarte" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>mapas do Azure + Encarte</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SDK da Web de mapas do Azure:

> [!div class="nextstepaction"]
> [Controle de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
