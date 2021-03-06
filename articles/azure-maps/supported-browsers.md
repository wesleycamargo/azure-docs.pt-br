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
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577332"
---
# <a name="web-sdk-supported-browsers"></a>Navegadores com suporte do SDK da Web

SDK da Web de mapas do Azure fornece uma função auxiliar [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) detectar se um navegador da web tem o mínimo necessário WebGL recursos para dar suporte ao carregamento e renderizar o controle de mapa. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

SDK da Web de mapas do Azure suporta os seguintes navegadores da área de trabalho.

- A versão atual e anterior do Microsoft Edge 
- A versão atual e anterior do Chrome 
- A versão atual e anterior do Firefox 
- A versão atual e anterior do Safari (Mac OS X) 

Consulte também [direcionar os navegadores herdados](#Target-Legacy-Browsers).

## <a name="mobile"></a>Móvel

SDK da Web de mapas do Azure suporta os seguintes navegadores móveis.

-  Android
    * Versão atual do Chrome no Android 6.0 +
    * Chrome WebView no Android 6.0 +
- iOS
    * Mobile Safari versão principal atual e anterior do iOS
    * UIWebView e WKWebView na versão principal atual e anterior do iOS
    * Versão atual do Chrome para iOS

> [!TIP]
> Se você estiver inserindo um mapa de dentro de um aplicativo móvel usando um controle WebView, talvez você prefira usar o [pacote npm do SDK do Azure mapas Web](https://www.npmjs.com/package/azure-maps-control) em vez de referenciar a versão hospedada em CDN do SDK. Isso reduzirá o tempo de carregamento, o SDK será já no dispositivo do usuário e não precisa ser baixado em tempo de execução.

## <a name="nodejs"></a>Node.js

Também há suporte para os seguintes módulos do SDK para Web em Node. js:

- Módulo de serviços ([documentação](how-to-use-services-module.md) | [módulo npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Navegadores herdados de destino

Se você precisar direcionar os navegadores mais antigos que podem não dar suporte ou tem suporte limitado para WebGL, é recomendável usar os serviços de mapas do Azure em combinação com um controle de mapa de código-fonte aberto, como [Encarte](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas do Azure + Encarte" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>mapas do Azure + Encarte</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SDK da Web de mapas do Azure.

> [!div class="nextstepaction"]
> [Controle de mapa](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)
