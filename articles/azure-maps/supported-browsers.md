---
title: Compatível com navegadores da web - mapas do Azure | Microsoft Docs
description: Saiba mais sobre os navegadores da web com suporte para o SDK Web do Azure mapas
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501265"
---
# <a name="supported-web-browsers"></a>Navegadores da Web com suporte

SDK da Web de mapas do Azure fornece uma função auxiliar [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) detectar se um navegador da web tem o mínimo necessário WebGL recursos para dar suporte ao carregamento e renderizar o controle de mapa. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

SDK da Web de mapas do Azure suporta os seguintes navegadores da web.

## <a name="desktop"></a>Desktop

- A versão atual e anterior do Microsoft Edge 
- A versão atual e anterior do Chrome 
- A versão atual e anterior do Firefox 
- A versão atual e anterior do Safari (Mac OS X) 

Consulte também [direcionar os navegadores herdados](#Target-Legacy-Browsers).

## <a name="mobile"></a>Móvel

-  Android
    * Versão atual do Chrome no Android 6.0 +
    * Chrome WebView no Android 6.0 +
- iOS
    * Mobile Safari versão principal atual e anterior do iOS
    * UIWebView e WKWebView na versão principal atual e anterior do iOS
    * Versão atual do Chrome para iOS

> [!TIP]
> Se você estiver inserindo um mapa em uma exibição um controle WebView do aplicativo móvel, você pode encontrar usando o [pacote npm do SDK da Web de mapas do Azure](https://www.npmjs.com/package/azure-maps-control) preferível referenciando a versão hospedada em CDN do SDK. Isso reduzirá o tempo de carregamento, o SDK será já no dispositivo do usuário e não precisa ser baixado em tempo de execução.

## <a name="nodejs"></a>Node.js

Também há suporte para os seguintes módulos do SDK para Web em Node. js:

- Módulo de serviços ([documentação](how-to-use-services-module.md) | [módulo npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Navegadores herdados de destino

Se você precisar direcionar os navegadores mais antigos que podem não dar suporte ou tem suporte limitado para WebGL, é recomendável usar os serviços de mapas do Azure em combinação com um controle de mapa de código-fonte aberto, como [Encarte](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapas do Azure + Encarte" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>mapas do Azure + Encarte</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>