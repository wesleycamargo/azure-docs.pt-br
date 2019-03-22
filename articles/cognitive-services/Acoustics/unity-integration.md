---
title: Projeto de implantação e integração do Unity acústica
titlesuffix: Azure Cognitive Services
description: Esse manual explica a integração do plug-in do projeto acústica Unity ao seu projeto do Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137722"
---
# <a name="project-acoustics-unity-integration"></a>Integração do Project acústica Unity
Esse manual explica a integração do plug-in do projeto acústica Unity ao seu projeto do Unity.

Requisitos de software:
* [Unity 2018.2 +](http://unity3d.com) para Windows
* [Pacote do projeto Unity acústica](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>O plug-in de importação
Importe a acústica UnityPackage para o seu projeto. 
* No Unity, vá para **ativos > Importar pacote > pacote personalizado...**

    ![Importar pacote](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se você estiver importando o plug-in para um projeto existente, seu projeto já pode ter um arquivo **mcs.rsp** na raiz do projeto, que especifica opções para o compilador C#. Você precisará mesclar o conteúdo desse arquivo com o arquivo de MCS que vem com o plug-in do projeto acústica.

## <a name="enable-the-plugin"></a>Habilitar o plug-in
A parte de assar do kit de ferramentas de acústica requer a versão de tempo de execução de script do .NET 4.x. A importação de pacotes atualizará as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito.

![Configurações do Player](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar DSP de áudio
Projeto acústica inclui o tempo de execução áudio DSP que se integra a estrutura de spatializer de mecanismo de áudio do Unity. Ele inclui o funcionamento em com base em HRTF e panorâmica. Habilitar o projeto acústica DSP abrindo as configurações de áudio do Unity usando **Editar > configurações do projeto > áudio**, selecionando **projeto acústica** como o **Spatializer plug-in** para seu projeto. Verifique se o **DSP o tamanho do Buffer** é definido como o melhor desempenho.

![Configurações do projeto](media/project-settings.png)  

![Projeto acústica Spatializer](media/choose-spatializer.png)

Em seguida, abra o Mixer de áudio (**Janela > áudio Mixer**). Certifique-se de ter pelo menos um Mixer, com um grupo. Se você não fizer isso, clique no botão "+" à direita de **Mixers**. A parte inferior da faixa de canal na seção efeitos com o botão direito e, em seguida, adicione a **projeto acústica Mixer** efeito. Observe que apenas um Mixer acústica de projeto é suportado por vez.

![Áudio Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Habilitar acústica em fontes de som
Crie uma fonte de áudio. Clique na caixa de seleção na parte inferior do painel de controle do AudioSource que diz **Spatialize**. Certifique-se **Blend espacial** é definido como completo 3D.  

![Fonte de áudio](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Habilitar design acústico
Anexar o script **AcousticsAdjust** a uma fonte de som na sua cena para habilitar os parâmetros de design de origem adicionais, clicando em **Add Component** e escolhendo **Scripts > Ajustar acústica** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Próximas etapas
* [Inserir sua cena com acústica do projeto para Unity](unity-baking.md)
* [Criar uma conta do lote do Azure](create-azure-account.md) que inserir sua cena na nuvem
* Explore os [processo de design de projeto acústica Unity](unity-workflow.md).

