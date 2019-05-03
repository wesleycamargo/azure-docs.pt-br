---
title: Implantar um modelo de fala personalizado - serviços de fala
titlesuffix: Azure Cognitive Services
description: Neste documento, você aprenderá a criar e implantar um ponto de extremidade usando o portal de fala personalizado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025865"
---
# <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de ter carregado e inspecionado dados, avaliado a precisão e treinar um modelo personalizado, você pode implantar um ponto de extremidade personalizado para usar com seus aplicativos, ferramentas e produtos. Neste documento, você aprenderá a criar e implantar um ponto de extremidade usando o portal de fala personalizado.

## <a name="create-a-custom-endpoint"></a>Criar um ponto de extremidade personalizado

Para criar um novo ponto de extremidade personalizado, selecione **implantação** no menu de fala personalizado na parte superior da página. Se esta for sua primeira execução, você observará que não há nenhum ponto de extremidade listados na tabela. Depois de criar um ponto de extremidade, você usará essa página para acompanhar cada ponto de extremidade implantado.

Em seguida, selecione **Adicionar ponto de extremidade** e insira um **nome** e **descrição** para seu ponto de extremidade personalizado. Em seguida, selecione o modelo personalizado que você deseja associar a esse ponto de extremidade. Nessa página, você também pode habilitar o registro em log. Registro em log permite que você monitore o tráfego de ponto de extremidade. Se desabilitada, o tráfego não será armazenado.

![Como implantar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e os detalhes de preços.

Em seguida, selecione **Criar**. Essa ação retorna você para o **implantação** página. A tabela agora inclui uma entrada que corresponde ao seu ponto de extremidade personalizado. Status do ponto de extremidade mostra o estado atual. Ele pode levar até 30 minutos para instanciar um novo ponto de extremidade usando seus modelos personalizados. Quando o status da implantação seja **concluir**, o ponto de extremidade está pronto para uso.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Clique no link para exibir informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo.

## <a name="view-logging-data"></a>Exibir dados de registro em log

Dados de log estão disponíveis para download em **ponto de extremidade > detalhes**.

## <a name="next-steps"></a>Próximas etapas

* Use o ponto de extremidade personalizado com o [SDK de fala](speech-sdk.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
