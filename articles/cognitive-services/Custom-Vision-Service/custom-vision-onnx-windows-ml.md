---
title: Modelo de Serviço de Visão Personalizada ONNX com Windows ML – Serviços Cognitivos | Microsoft Docs
description: Saiba como criar um aplicativo UWP do Windows que usa um modelo ONNX exportado dos Serviços Cognitivos.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939381"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Tutorial: Usar um modelo ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia)

Saiba como usar o modelo exportado ONNX do Serviço de Visão Personalizada com Windows ML (versão prévia).

As informações neste documento demonstram como usar um arquivo ONNX exportado do Serviço de Visão Personalizada com Windows ML. Um aplicativo UWP do Windows de exemplo é fornecido. Um modelo treinado que pode reconhecer cachorros e gatos é incluído com o exemplo. Também são fornecidas etapas sobre como você pode usar seu próprio modelo com esse exemplo.

> [!div class="checklist"]
> * Sobre o aplicativo de exemplo
> * Obter o código de exemplo
> * Executar o exemplo
> * Usar seu próprio modelo

## <a name="prerequisites"></a>pré-requisitos

* Um dispositivo Windows 10 com:

    * Uma câmera.

    * Visual Studio 2017 versão 15.7 ou posterior com a carga de trabalho __Desenvolvimento com a Plataforma Universal do Windows__ habilitada.

    * Modo de desenvolvedor habilitado. Para obter mais informações, confira o documento [Habilitar seu dispositivo para desenvolvimento](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

* (Opcional) Um arquivo ONNX exportado do Serviço de Visão Personalizada. Para obter mais informações, confira o documento [Exportar seu modelo para uso com dispositivos móveis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

    > [!NOTE]
    > Para usar seu próprio modelo, siga as etapas na seção [Usar seu próprio modelo](#use-your-own-model).

## <a name="about-the-example-app"></a>Sobre o aplicativo de exemplo

O aplicativo é um aplicativo UWP do Windows genérico. Ele usa a câmera no dispositivo Windows 10 para fornecer imagens para o modelo. As marcações e as pontuações retornadas pelo modelo são exibidas abaixo da visualização do vídeo.

* Como os dados vêm em da câmera, embora [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) é usado para extrair quadros individuais. Os quadros são enviados para o modelo para pontuação.

* O modelo retorna as marcações nas quais ele foi treinado e um valor float que indica o quão confiante ele está de que a imagem contém tal item.

### <a name="the-ui"></a>A interface do usuário

A interface do usuário do aplicativo de exemplo é criada usando os controles __CaptureElement__ e __TextBlock__. O CaptureElement mostra uma visualização do vídeo da câmera e o TextBlock mostra os resultados retornados do modelo. 

### <a name="the-model"></a>O modelo

O modelo (`cat-or-dog.onnx`) fornecido com o exemplo foi criado e treinado usando o Serviço de Visão Personalizada dos Serviços Cognitivos. O modelo treinado foi então exportado como um modelo ONNX. Para obter mais informações sobre o uso desse serviço, confira os documentos [Como criar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) e [Exportar seu modelo para uso com dispositivos móveis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

> [!IMPORTANT]
> O modelo fornecido com o exemplo foi treinado com um pequeno conjunto de imagens de cachorros e de gatos. Portanto, ele pode não ser o melhor do mundo no reconhecimento de cachorros e gatos.

### <a name="the-model-class-file"></a>O arquivo de classe do modelo

Quando você adiciona um arquivo ONNX a um aplicativo UWP do Windows, ele cria um arquivo .cs. Esse arquivo tem o mesmo nome que o arquivo `.onnx` (`cat-or-dog` nesse exemplo) e contém as classes usadas para trabalhar com o modelo de C#. No entanto, as entidades na classe gerada podem ter nomes como `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Você pode renomear essas entradas (clique com o botão direito do mouse e escolha a opção de renomear) com segurança para um nome amigável.

> [!NOTE]
> O código de exemplo refatorou os nomes de método e de classe para os a seguir:
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Acesso à câmera

A guia __Recursos__ no arquivo `Package.appxmanifest` está configurada para permitir o acesso à webcam e ao microfone.

> [!NOTE]
> Embora esse exemplo não use o áudio, habilitei o microfone antes de conseguir acessar a câmera no meu dispositivo.

O aplicativo tenta obter a câmera na parte traseira do seu dispositivo caso haja uma disponível. Ele usa a classe [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) para iniciar a captura de vídeo da câmera. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) é usado para capturar os quadros de vídeo e enviá-los para o modelo.

## <a name="get-the-example-code"></a>Obter o código de exemplo

O aplicativo de exemplo fica disponível em [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Executar o exemplo

1. Use a tecla `F5` para iniciar o aplicativo do Visual Studio. É possível que seja solicitado que você habilite o Modo de Desenvolvedor. Para obter mais informações, confira o documento [Habilitar seu dispositivo para desenvolvimento](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. Quando solicitado, permita que o aplicativo acesse a câmera e o microfone do seu dispositivo.

3. Aponte a câmera para um cachorro ou para um gato. A pontuação para se a imagem contém um cachorro ou um gato é mostrada abaixo da visualização no aplicativo.

    > [!TIP]
    > Se você não tiver um cachorro ou um gato acessível, poderá usar uma foto de um cachorro ou de um gato.

## <a name="use-your-own-model"></a>Usar seu próprio modelo

Para usar seu próprio modelo, use as seguintes etapas:

> [!IMPORTANT]
> As etapas nesta seção renomeiam o modelo atual (cat-or-dog.cs) e refatoram os nomes de método e classe do novo modelo. Isso tem como objetivo evitar colisões de nomes com o modelo de exemplo.

1. Treine um modelo usando o Serviço de Visão Personalizada. Para obter informações sobre como treinar um modelo, confira [Como criar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exporte o modelo treinado como um modelo ONNX. Para obter informações sobre como exportar um modelo, confira o documento [Exportar seu modelo para uso com dispositivos móveis](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

3. No Gerenciador de Soluções, clique com o botão direito do mouse em __cat-or-dog.cs__ e renomeie-o como __cat-or-dog.txt__. A renomeação impede a colisão do nome com o novo modelo.

    > [!TIP]
    > Você também pode usar nomes diferentes para os nomes de classe no novo modelo, mas reutilizar os nomes existentes é mais fácil.

4. No Gerenciador de Soluções, clique com o botão direito do mouse na entrada __VisionApp__ e selecione __Adicionar__ > __Item existente...__.

5. Para gerar uma classe para o modelo, selecione o arquivo ONNX a ser importado e selecione o botão __Adicionar__. Uma nova classe com o mesmo nome que o arquivo ONNX (mas uma extensão `.cs`) é adicionada no Gerenciador de Soluções.

6. Abra o arquivo .cs gerado e encontre os nomes dos seguintes itens:

    > [!IMPORTANT]
    > Use o arquivo `cat-or-dog.txt` de exemplo como guia para reconhecer as classes e funções.

    * A classe que define a entrada do modelo. O nome gerado pode ser semelhante a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Renomeie esta classe como __ModelInput__.
    * A classe que define a saída do modelo. O nome gerado pode ser semelhante a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Renomeie esta classe como __ModelOutput__.
    * A classe que define o modelo. O nome gerado pode ser semelhante a `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Renomeie esta classe como __Model__.
    * O método que cria o modelo. O nome gerado pode ser semelhante a `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Renomeie este método como __CreateModel__.

7. No Gerenciador de Soluções, mova o arquivo `.onnx` para a pasta __Ativos__. 

8. Para incluir o arquivo ONNX no pacote de aplicativos, selecione o arquivo `.onnx` e defina __Ação de Build__ como __Conteúdo__ nas propriedades.

9. Abra o arquivo __MainPage.xaml.cs__. Localize a seguinte linha e altere o nome do arquivo para o novo arquivo `.onnx`:

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Essa alteração carrega o novo modelo no tempo de execução.

10. Compile e execute o aplicativo. Agora ele usa o novo modelo para atribuir a pontuação às imagens.

## <a name="next-steps"></a>Próximas etapas

Para descobrir outras maneiras de exportar e usar um modelo do Serviço de Visão Personalizada, confira os seguintes documentos:

* [Exportar seu modelo](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Usar o modelo Tensorflow exportado em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Usar o modelo CoreML exportado em um aplicativo iOS Swift](https://go.microsoft.com/fwlink/?linkid=857726)
* [Usar o modelo CoreML exportado em um aplicativo iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Para obter mais informações sobre como usar modelos ONNX com Windows ML, confira o documento [Integrar um modelo ao aplicativo no Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
