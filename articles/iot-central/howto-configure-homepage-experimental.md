---
title: Configurar a home page do seu aplicativo Azure IoT Central | Microsoft Docs
description: Como construtor, saiba como configurar a home page do seu aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771652"
---
# <a name="configuring-homepage"></a>Configurando a home page

A home page é a página carregada quando os usuários que têm acesso navegam até a URL do aplicativo. Se você tiver selecionado os modelos de aplicativo "Exemplo Contoso" ou "Exemplo Devkits" ao criar seu aplicativo, ele terá Home pages predefinidas. Se, por outro lado, tiver selecionado o modelo de aplicativo "Aplicativo personalizado", sua home page ficará em branco.

## <a name="add-tiles"></a>Adicionar blocos

Por exemplo, veja a home page dos aplicativos com base no modelo "Exemplo Contoso". Para personalizar a home page para seu aplicativo, primeiro selecione **Editar** no canto superior direito. 

![Home page de aplicativos com base no modelo "Exemplo Contoso"](media/howto-configure-homepage-experimental/image1.png)

Selecionar **Editar**, abrirá a biblioteca de dashboard em um painel à esquerda. Há muitos tipos de títulos e primitivos de painel que podem ser adicionados para personalizar sua home page.

![Biblioteca de painéis](media/howto-configure-homepage-experimental/image2.png)

Por exemplo, é possível adicionar um bloco **Configurações e Propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades. Para fazer isso, primeiro selecione um **Modelo de dispositivo**, em seguida, selecione uma **Instância de dispositivo**. Depois disso, dê um título ao título e selecione uma **Configuração** ou uma **Propriedade** para exibir. Nesse caso, selecionamos **Definir temperatura**. Clicar em **Pronto** fará com que essa peça apareça na home page.

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-configure-homepage-experimental/image3.png)

Agora, quando o operador exibir a home page, ele poderá ver esse título que exibe as propriedades ou configurações do dispositivo:

![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-configure-homepage-experimental/image4.png)

Familiarize-se com vários outros tipos de blocos na biblioteca para descobrir como é possível personalizar a home page do seu aplicativo ainda mais.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar a home page do Azure IoT Central, é possível:

> [!div class="nextstepaction"]
> [Saiba como preparar e carregar imagens](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
