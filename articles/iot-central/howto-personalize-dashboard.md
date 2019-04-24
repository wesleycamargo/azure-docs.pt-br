---
title: Criar dashboards pessoais Azure IoT Central | Microsoft Docs
description: Como um usuário, saiba como criar e gerenciar seus painéis pessoais.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: fb74669dcaa802ad06a9c4dff3ffdf25726f518c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518609"
---
# <a name="create-and-manage-personal-dashboards"></a>Criar e gerenciar painéis pessoais

O **Dashboard** é a página que carrega quando você navega pela primeira vez ao seu aplicativo. Um **construtor** em seu aplicativo define o painel do aplicativo padrão para todos os usuários. Você pode substituir esse painel padrão com seu próprio, painel de aplicativos personalizados. Você pode ter vários painéis que exibam dados diferentes e alternar entre elas.

## <a name="create-dashboard"></a>Criar painel

Captura de tela a seguir mostra o painel em um aplicativo criado a partir de **Contoso exemplo** modelo. Você pode substituir o painel do aplicativo padrão com um painel pessoal. Para fazer isso, selecione **+ novo** na parte superior direita da página.

![Painel para aplicativos baseados no modelo "Exemplo Contoso"](media/howto-personalize-dashboard/defaultdashboard.png)

Selecionando **+ novo**, abre o editor de dashboard. No editor, você pode dar um nome de seu dashboard e escolher itens da biblioteca. A biblioteca contém os blocos e os primitivos de painel, que você pode usar para personalizar o painel.

![Biblioteca de painéis](media/howto-personalize-dashboard/dashboardeditor.png)

Por exemplo, você pode adicionar um **configurações do dispositivo e propriedades** lado a lado para mostrar os valores de propriedades e configurações para um dos dispositivos gerenciados. Para fazer isso, primeiro selecione um **Modelo de dispositivo**, em seguida, selecione uma **Instância de dispositivo**. Em seguida, dê o bloco um título e selecione uma **configuração** ou um **propriedade** para exibir. A captura de tela a seguir mostra a **velocidade** configuração selecionada para adicionar ao bloco. Selecione **feito** para salvar as alterações ao seu painel.

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-personalize-dashboard/dashboardsetting.png)

Agora quando você exibir seu painel pessoal, você vê o novo bloco com o **velocidade** configuração para o dispositivo:

![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-personalize-dashboard/personaldashboard.png)

Você pode explorar os outros tipos de bloco na biblioteca para descobrir como personalizar ainda mais seus painéis pessoais.

## <a name="manage-dashboards"></a>Gerenciar painéis

Você pode ter vários painéis pessoais e alternar entre eles, ou escolha o painel do aplicativo padrão:

![Painel de switch](media/howto-personalize-dashboard/switchdashboards.png)

Você pode editar seus painéis pessoais e excluir os que não são mais necessários:

![Excluir painel](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como criar e gerenciar painéis pessoais, você pode:

> [!div class="nextstepaction"]
> [Saiba como gerenciar suas preferências de aplicativo](howto-manage-preferences.md)
