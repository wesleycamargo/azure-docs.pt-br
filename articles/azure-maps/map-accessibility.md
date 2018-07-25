---
title: Criar um aplicativo acessível com o Azure Mapas | Microsoft Docs
description: Como criar um aplicativo acessível usando o Azure Mapas
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867045"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Este artigo mostra como criar um aplicativo de mapa que pode ser usado por leitores de tela.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Criar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Criar um aplicativo acessível</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O mapa é pré-compilado com alguns recursos de acessibilidade. Um usuário pode navegar no mapa usando o teclado e se um leitor de tela está em execução, o mapa notificará o usuário de alterações em seu estado. Por exemplo, o usuário será notificado de novas latitude, longitude, zoom e localidade novas do mapa quando ele é deslocado ou ampliado. Qualquer informação adicional é colocada no mapa de base deve ter informações textuais correspondentes para os usuários de leitores de tela. Usar o [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) é uma maneira de fazer isso. No exemplo acima de pesquisa, um pop-up com informações textuais é adicionado ao mapa para cada marcador colocado no mapa. Usar o método anexar do [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) anexar permite que ele seja visto por leitores de tela sem exibir visualmente o pop-up no mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos de pop-up usados neste artigo:

* [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [abrir](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Confira nossa [página de exemplo de código](http://aka.ms/AzureMapsSamples) para obter mais cenários de mapeamento.
