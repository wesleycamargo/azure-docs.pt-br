---
title: 'Exemplo: Projeto Acústico'
titlesuffix: Azure Cognitive Services
description: Este passo a passo descreve a cena de amostra do Unity para o Project Acoustics, incluindo a implantação no desktop e no VR.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7d8ba2f25bd53b407ab6860bc57163a79b7d228a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174254"
---
# <a name="unity-sample-walkthrough"></a>Exemplo passo a passo do Unity
Este é um passo a passo da amostra Project Acoustics. Para mais informações sobre o que é o Project Acoustics, confira o [Introdução à Acústica do Projeto](what-is-acoustics.md). Para obter ajuda sobre como adicionar o pacote Project Acoustics a um projeto Unity preexistente, use o [Guia de primeiros passos](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Requisitos para executar o projeto de exemplo
* Unity 2018.2 +, usando a versão de tempo de execução .NET 4.x script
* Editor de Unity de 64 bits do Windows
* O exemplo suporta os alvos de desktop, UWP e Android do Windows, incluindo monitores montados na cabeça (HMDs)
* Assinatura de lote do Azure necessária para o processo de cozimento

## <a name="sample-project-setup"></a>Exemplo de configuração de projeto
Baixe e importe as **MicrosoftAcoustics.Sample.unitypackage**. Na importação, as configurações do projeto, incluindo **Spatializer** e **Scripting Runtime Version**, são atualizadas para atender aos requisitos do plug-in. Quando isso for concluído, você verá um erro no console do Unity **AcousticsGeometry.cs** sobre como alterar a versão de tempo de execução de scripts para **.NET 4.x equivalente**. Essa alteração de configurações é feita como parte da importação do pacote, mas requer uma reinicialização do Unity entrem em vigor. Reinicie o Unity agora.

## <a name="running-the-sample"></a>Executando o exemplo
O exemplo inclui uma cena de demonstração, **Assets / AcousticsDemo / ProjectAcousticsDemo.unity**. Essa cena tem três fontes de som. Por padrão, apenas uma fonte de som está em execução e as outras duas estão em pausa. Elas estão localizadas em **Fontes de Som** na **Hierarquia**. Para ajudar a criar um script de navegação genérico, a câmera principal é filha do objeto CameraHolder. 

![Exibição de hierarquia](media/SampleHierarchyView.png)

A cena já foi cozido, e tem um ficheiro de ACE associado com as **MicrosoftAcoustics** pré-fabricadas na **Hierarquia**. 

Ouça como a cena parece clicando no botão Reproduzir no editor do Unity. Na área de trabalho, use W, A, S, D e o mouse para mover-se. Para comparar o som da cena com e sem acústica, pressione o botão **R** até que o texto de sobreposição fique vermelho e informe "Acústica: desabilitada". Para ver os atalhos de teclado de outros controles, pressione **F1**. Todos os controles também podem ser usados clicando com o botão direito do mouse para selecionar a ação a ser executada e clicando com o botão esquerdo para executar a ação.

## <a name="targeting-other-platforms"></a>Para outras plataformas
O exemplo contém configurações para serem executadas na área de trabalho do Windows, UWP, Windows Mixed Reality, Android e Oculus Go. Por padrão, o projeto está configurado para a área de trabalho do Windows. Para segmentar uma plataforma de RV, vá para as configurações do player (**Editar> Configurações do Projeto> Player**), encontre as **Configurações de XR** e marque a caixa de seleção **Virtual Reality Supported**.

![Habilitar VR](media/VRSupport.png)  

Conecte um headset VR ao seu PC. Vá para **Arquivo> Configurações de compilação** e clique em **Criar e executar** para implantar a amostra em seu fone de ouvido de realidade virtual. Navegue por meio da cena usando os controladores de movimento para fone de ouvido ou tente usar W, A, S, D no teclado.    
Para segmentar Android e Oculus Go, escolha Android no menu **Configurações de compilação**. Clique em **alternar de destino**, em seguida, **compilar e executar**. Isso implantará a cena de exemplo em seu dispositivo Android conectado. Para obter informações sobre o desenvolvimento do Unity para Android, confira a [Documentação do Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Android de destino](media/TargetAndroid.png)  

## <a name="next-steps"></a>Próximas etapas
* [Criar uma conta do Azure](create-azure-account.md) para seu próprios prepara
* Explore o [processo de design](design-process.md)

