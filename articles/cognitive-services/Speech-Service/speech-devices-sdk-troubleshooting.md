---
title: Solucionar problemas de dispositivos de fala do SDK - serviços de fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudar a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026151"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Solucionar problemas de dispositivos de fala do SDK

Este artigo fornece informações para ajudar a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.

## <a name="certificate-failures"></a>Falhas de certificado

Se você receber falhas de certificado ao usar os serviços de fala, certifique-se de que seu dispositivo tenha a data e hora corretas:

1. Vá para **Configurações**. Em **Sistema**, selecione **Data e hora**.

    ![Em Configurações, selecione a Data e hora](media/speech-devices-sdk/qsg-12.png)

1. Mantenha a opção selecionada **Data e hora automática**. Em **Selecionar fuso horário**, selecione seu fuso horário atual.

    ![Selecione as opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando você vir que o tempo do kit de desenvolvimento corresponde o tempo em seu computador, o kit de desenvolvimento estará conectado à internet.

## <a name="next-steps"></a>Próximas etapas

* [Examinar as notas de versão](devices-sdk-release-notes.md)
