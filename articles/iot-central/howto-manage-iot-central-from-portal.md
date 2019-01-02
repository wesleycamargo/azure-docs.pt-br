---
title: Gerenciar IoT Central do portal do Azure | Microsoft Docs
description: Gerenciar IoT Central do portal do Azure.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963820"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gerenciar IoT Central do portal do Azure 
Além de criar e gerenciar aplicativos do IoT Central no site do IoT Central, também é possível gerenciar o IoT Central no portal do Azure. Este artigo irá explicar o que é possível e como fazê-lo.

## <a name="create-iot-central-applications"></a>Criar aplicativos do IoT Central
Para criar um novo aplicativo, navegue até o [portal do Azure](https://ms.portal.azure.com) e clique em "Criar um recurso" no menu de navegação principal esquerdo. 

![Portal de gerenciamento: menu de navegação](media/howto-manage-iot-central-from-portal/image0.png)

Na barra de pesquisa, digite o termo "IoT Central".

![Portal de gerenciamento: pesquisar](media/howto-manage-iot-central-from-portal/image0a.png)

Clique no item de linha do aplicativo do IoT Central nos resultados da pesquisa.

![Portal de gerenciamento: resultados da pesquisa](media/howto-manage-iot-central-from-portal/image0b.png)

Agora, clique no botão "Criar" para ver o formulário que será necessário preencher.

![Portal de gerenciamento: Recursos de IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Preencha todos os campos no formulário. Este formulário é semelhante ao formulário que será necessário preencher para criar aplicativos no site do IoT Central. Para saber mais sobre como preencher cada campo, confira o início rápido do [Criar um aplicativo IoT Central](quick-deploy-iot-central.md). 

![Portal de gerenciamento: criar recurso do IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Após preencher todos os campos, clique no botão "Criar".

## <a name="manage-existing-iot-central-applications"></a>Gerenciar aplicativos do IoT Central existentes
Se você já tiver um aplicativo do Azure IoT Central, poderá excluí-lo e movê-lo para uma assinatura ou grupo de recursos diferente no portal do Azure. Você não pode ver aplicativos de avaliação no portal do Azure, pois nenhuma assinatura faz essas avaliações.

Para começar, clique em "Todos os recursos" no menu de navegação principal esquerdo. Use a caixa de pesquisa para digitar o nome do aplicativo e localize-o na lista de recursos. Em seguida, clique no aplicativo do IoT Central que você quer gerenciar.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image2.png)

Para navegar até o aplicativo, clique na URL do aplicativo do IoT Central.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover o aplicativo para um grupo de recursos diferente, clique no link **alterar** ao lado do grupo de recursos. Escolha o grupo de recursos para o qual você deseja migrar esse aplicativo na caixa de diálogo exibida.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image4.png)

Para mover o aplicativo para uma Assinatura diferente, clique no link **alterar** ao lado da Assinatura. Escolha a Assinatura para a qual você deseja migrar esse aplicativo na caixa de diálogo exibida.

![Portal de gerenciamento: gerenciamento de recursos](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no portal do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)