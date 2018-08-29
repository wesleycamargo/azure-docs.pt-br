---
title: Primeiros passos com o Project Acoustics - Cognitive Services
description: Este guia de início rápido mostrará como integrar o plug-in em seu projeto Unity, assar sua cena e aplicar a acústica a fontes de som.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b80543b199d4b766c1a8800d2dff4cf5ed81f8cc
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181084"
---
# <a name="getting-started-with-project-acoustics"></a>Introdução ao projeto acústica
Este guia de início rápido mostrará como integrar o plug-in em seu projeto Unity, assar sua cena e aplicar a acústica a fontes de som. Para este início rápido, você precisará primeiro criar uma [conta do lote do Azure](create-azure-account.md). Este guia pressupõe alguma familiaridade com o Unity.

## <a name="download-the-plugin"></a>Baixe o plug-in
Registre [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) para participar da visualização do designer.

## <a name="supported-platforms-for-quickstart"></a>Plataformas com suporte para o guia de início rápido
* [Unity 2018.2 +](http://www.unity3d.com)
  * Requer a configuração do seu projeto para a versão de tempo de execução do script **.NET 4.x Equivalent** 
  * Requer que o editor do Unity com base em Windows

## <a name="import-the-plugin"></a>O plug-in de importação
Importe a acústica UnityPackage para o seu projeto. 
* No Unity, vá para **ativos > Importar pacote > pacote personalizado...**

![Importar pacote](media/ImportPackage.png)  

* Escolha **MicrosoftAcoustics.unitypackage**

Se você estiver importando o plug-in para um projeto existente, seu projeto já pode ter um arquivo **mcs.rsp** na raiz do projeto, que especifica opções para o compilador C#. Você precisará mesclar o conteúdo desse arquivo com o arquivo de MCS que vem com o plug-in do projeto acústica.

## <a name="enable-the-plugin"></a>Habilitar o plug-in
A parte de assar do kit de ferramentas de acústica requer a versão de tempo de execução de script do .NET 4.x. A importação de pacotes atualizará as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito.

![Configurações do Player](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
Use o fluxo de trabalho [padrão do Unity ](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) para criar uma malha de navegação para o seu projeto. Para obter informações sobre como usar seus próprios malhas de navegação, consulte a [tortas percorrer de interface do usuário](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Marcar malhas estáticas para acústica
Abra a janela de acústica usando **Window> Acoustics** no Unity. Esta janela pode ser encaixada ao lado do Inspetor.

![Janela aberta acústica](media/WindowAcoustics.png)

Na janela Unity hierarchy, desmarque todos os itens selecionados. Na aba acústica **Objeto**, clique na caixa de seleção "Acústica Geométrica" para marcar todas as malhas e terrenos em sua cena como geometria acústica.

Na guia **Materiais**, atribua os materiais acústicos aos materiais usados na sua cena. O **padrão** material tem absorção equivalente a concreto. Para obter mais informações sobre como especificar suas próprias propriedades de materiais, consulte a [página do processo de design](design-process.md).

![Guia de materiais](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>As investigações de visualização
Na guia **Sondas**, clique em **Calcular**. Esse cálculo pode levar alguns minutos, dependendo do tamanho da cena. Quando o cálculo estiver concluído, você verá esferas flutuantes na visualização de cena, que marcam os locais da simulação acústica, chamados "pontos de teste". Se você receber perto o suficiente para um objeto na janela de cena, você também pode ver a cena voxelization. O verde voxels deve alinhar com os objetos marcados como geometria. Os pontos da sonda e os displays do voxel podem ser alternados no menu Gizmos no canto superior direito da visualização da cena.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>A cena de tortas
Na guia **Bake**, insira suas credenciais do Azure e clique em **Bake**. Se você não tiver uma conta do Azure Batch, consulte [este passo a passo para nossa configuração de conta recomendada](create-azure-account.md).
Quando o cozimento estiver concluído, o arquivo de dados será automaticamente baixado para o diretório **Assets / AcousticsData** em seu projeto.

## <a name="set-up-audio-runtime-dsp"></a>Configurar DSP de tempo de execução de áudio
Incorporamos o DSP de tempo de execução de áudio para acústica na estrutura de spatializer do Unity e o integramos com a espacialização baseada em HRTF. Para habilitar o processamento de acústica, alterne para o **Microsoft Acoustics** spatializer acessando **Editar > configurações do projeto > áudio**e selecione **Acoustics Microsoft** como o **plug-in do Spatializer** para seu projeto. Além disso, verifique se o **tamanho do buffer DSP** está definido como Melhor desempenho.

![Configurações do projeto](media/ProjectSettings.png)  

![Projeto acústica Spatializer](media/ChooseSpatializer.png)

Abra o mixer de áudio (**Window> Audio Mixer**). Certifique-se de ter pelo menos um Mixer, com um grupo. Se você não fizer isso, clique no botão "+" à direita de **Mixers**. Clique com o botão direito do mouse na parte inferior da faixa de canais na seção de efeitos e adicione o efeito **Microsoft Acoustics Mixer**. Observe que apenas um Mixer acústica de projeto é suportado por vez.

![Áudio Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acústica
Arraste e solte o **pré-fabricado Microsoft Acoustics** do painel do projeto em sua cena:

![Pré-fabricado acústica](media/AcousticsPrefab.png)

Clique no **ProjectAcoustics** Game Object e vá até o painel de inspetores. Especifique a localização do seu resultado de cozimento (o arquivo .ACE, em **Assets / AcousticsData**) arrastando-o e soltando-o no script do Acoustics Manager, ou clicando no botão de círculo ao lado da caixa de texto.

![Gerenciador de acústica](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Aplique acústica a fontes sonoras
Crie uma fonte de áudio. Clique na caixa de seleção na parte inferior do painel de controle do AudioSource que diz **Spatialize**. Certifique-se **Blend espacial** é definido como completo 3D.  

![Fonte de áudio](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Aplicar o design de pós-tortas
Você pode anexar o script **AcousticsSourceCustomization** a uma fonte de som em sua cena para ativar parâmetros adicionais de design de origem, clicando em **Add Component** e escolhendo **Scripts> Acoustics Source Customization**:

![Personalização de fonte](media/SourceCustomization.png)

Também há parâmetros sobre o **Microsoft acústica Mixer**. Para obter mais informações sobre o design de pós-tortas, consulte [criar parâmetros](design-process.md).

## <a name="next-steps"></a>Próximas etapas
* Experimente o [cena de exemplo](sample-walkthrough.md)
* Saiba mais sobre o conjunto completo de [recursos de tortas](bake-ui-walkthrough.md)
* Explore mais detalhada [criar parâmetros](design-process.md)

