---
title: Exemplo de projeto Acoustics - Serviços Cognitivos
description: Este passo a passo descreve a cena de amostra do Unity para o Project Acoustics, incluindo a implantação no desktop e no VR.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b738cc2fc7db6987b8f4ad54a2c53cc9e69989b3
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181054"
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
O exemplo inclui uma cena de demonstração, **Assets / AcousticsDemo / ProjectAcousticsDemo.unity**. Esta cena tem uma única fonte de áudio spatialized reproduzindo a partir de um cubo flutuante (denominada **AudioHolder** na **hierarquia**). Para ajudar a criar um script de navegação genérico, a câmera principal é filha do objeto CameraHolder. 

![Exibição de hierarquia](media/SampleHierarchyView.png)

A cena já foi cozido, e tem um ficheiro de ACE associado com as **MicrosoftAcoustics** pré-fabricadas na **Hierarquia**. 

Ouça como a cena parece clicando no botão Reproduzir no editor do Unity. Use W, A, S, D e o mouse para mover-se. Para comparar como a cena soa com e sem acústica, clique no botão esquerdo do mouse ou no botão do controlador primário. Para percorrer as várias fontes de som, clique no botão direito do mouse ou no botão Voltar no seu controle.

## <a name="targeting-other-platforms"></a>Para outras plataformas
O exemplo contém configurações para serem executadas na área de trabalho do Windows, UWP, Windows Mixed Reality, Android e Oculus Go. Por padrão, o projeto está configurado para a área de trabalho do Windows. Para segmentar uma plataforma de RV, vá para as configurações do player (**Editar> Configurações do Projeto> Player**), encontre as **Configurações de XR** e marque a caixa de seleção **Virtual Reality Supported**.

![Habilitar VR](media/VRSupport.png)  

Conecte um headset VR ao seu PC. Vá para **Arquivo> Configurações de compilação** e clique em **Criar e executar** para implantar a amostra em seu fone de ouvido de realidade virtual. Navegue por meio da cena usando os controladores de movimento para fone de ouvido ou tente usar W, A, S, D no teclado.    
Para segmentar Android e Oculus Go, escolha Android no menu **Configurações de compilação**. Clique em **alternar de destino**, em seguida, **compilar e executar**. Isso implantará a cena de exemplo em seu dispositivo Android conectado. Para obter informações sobre o desenvolvimento do Unity para Android, consulte [documentação do Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Android de destino](media/TargetAndroid.png)  

## <a name="next-steps"></a>Próximas etapas
* [Criar uma conta do Azure](create-azure-account.md) para seu próprios prepara
* Explore o [processo de design](design-process.md)

