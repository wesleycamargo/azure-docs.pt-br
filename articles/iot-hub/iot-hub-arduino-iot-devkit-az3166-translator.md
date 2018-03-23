---
title: Tradutor do Kit de Desenvolvimento da IoT usando um Azure Function e os Serviços Cognitivos | Microsoft Docs
description: Use o microfone no Kit de Desenvolvimento da IoT para receber uma mensagem de voz e os Serviços Cognitivos do Azure para processá-la em um texto traduzido em inglês.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 5f74e43d20b5954ae4808f82a34372d1e2190481
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Usar o Kit de Desenvolvimento da IoT AZ3166 com um Azure Function e os Serviços Cognitivos para criar um tradutor de idiomas

Neste artigo, você aprende a tornar o Kit de Desenvolvimento da IoT um tradutor de idiomas usando os [Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/). Ele registra sua voz e a traduz para o texto em inglês mostrado na tela do Kit de Desenvolvimento.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando a [extensão do Visual Studio Code para Arduino](https://aka.ms/arduino). E vem com um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções de IoT (Internet das Coisas) que aproveitam os serviços do Microsoft Azure.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses dois métodos:

* Ative uma [conta do Microsoft Azure de avaliação por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html)
* Declare seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou o Visual Studio

## <a name="step-1-open-the-project-folder"></a>Etapa 1. Abrir a pasta do projeto

### <a name="a-start-vs-code"></a>a. Iniciar o VS Code

- Verifique se o Kit de Desenvolvimento não está conectado ao computador.
- Iniciar o VS Code
- Conecte o DevKit ao computador.

O VS Code localiza o Visual Studio Code automaticamente e abre uma página de introdução:

![Página Introdução](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. Abra a pasta de exemplos de Arduino

Expanda os **EXEMPLOS DO ARDUINO > Exemplos de MXCHIP AZ3166 > AzureIoT** no lado esquerdo e selecione **DevKitTranslator**. Ele abre uma nova janela do VS Code com a pasta de projeto DEVKITTRANSLATOR.

![Amostras do Kit de Desenvolvimento da IoT](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Se você fechar o painel, você poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="step-2-provision-azure-services"></a>Etapa 2. Provisionamento dos serviços do Azure

Na janela da solução, digite `Ctrl+P` (macOS: `Cmd+P`) e insira `task cloud-provision`.

No terminal do VS Code, uma linha de comando interativa orientará você pelo provisionamento de todos os serviços do Azure necessários:

![Tarefa de provisionamento de nuvem](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Etapa 3. Implantar o Azure Functions

Use `Ctrl+P` (macOS: `Cmd+P`) para executar `task cloud-deploy` para implantar o código do Azure Functions. Esse processo geralmente leva de 2 a 5 minutos para ser concluído:

![Tarefa de implantação de nuvem](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Depois que o Azure Function for implantado com êxito, preencha o arquivo azure_config.h com o nome do aplicativo de funções. Navegue para o [portal do Azure](https://portal.azure.com/) para encontrá-lo:

![Encontrar o nome do aplicativo do Azure Function](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Se o Azure Function não funcionar corretamente, confira esta seção de [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolver o problema.

## <a name="step-4-build-and-upload-the-device-code"></a>Etapa 4. Compilação e carregamento do código de dispositivo

1. Use `Ctrl+P` (macOS: `Cmd+P`) para executar `task config-device-connection`.

2. O terminal perguntará se você deseja usar a cadeia de conexão recuperada da etapa `task cloud-provision`. Você também pode inserir sua própria cadeia de conexão do dispositivo selecionando **'Criar Nova...'**

3. O terminal solicita que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.
  ![Verificação e upload do esboço do Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Depois de `task config-device-connection` terminar, clique em `F1` para carregar os comandos de VS Code e selecione `Arduino: Upload`, o VS Code inicia verificando e carregando o sketch do Arduino: ![verificação e carregamento do esboço sketch Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

## <a name="test-the-project"></a>Teste do projeto

Após a inicialização do aplicativo, siga as instruções da tela do Kit de Desenvolvimento. O idioma de origem padrão é o chinês.

Para selecionar outro idioma para tradução:

1. Pressione o botão A para entrar no modo de instalação.
2. Pressione o botão B para percorrer todos os idiomas de origem compatíveis.
3. Pressione o botão A para confirmar sua escolha de idioma de origem.
4. Pressione e mantenha o botão B pressionado enquanto fala e, em seguida, solte o botão B para iniciar a tradução.
5. O texto traduzido em inglês é mostrado na tela.

![Percorrer a lista para selecionar o idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado da tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na tela de resultados da tradução, você pode:

- Pressionar os botões A e B para percorrer a lista e selecionar o idioma de origem.
- Pressionar o botão B para falar, soltá-lo para enviar a voz e obter o texto de tradução

## <a name="how-it-works"></a>Como ele funciona

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O esboço do Arduino grava sua voz e, em seguida, envia uma solicitação HTTP para disparar o Azure Functions. O Azure Functions chama a API de tradução de fala do serviço cognitivo para fazer a tradução. Depois que o Azure Functions obtém o texto de tradução, ele envia uma mensagem C2D para o dispositivo. Em seguida, a tradução é exibida na tela.

## <a name="change-device-id"></a>Alterar ID do dispositivo

A ID do dispositivo padrão registrada no Hub IoT do Azure é **AZ3166**. Se você quiser modificá-lo, siga as [instruções aqui](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco pelos canais abaixo:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora você pode tornar o Kit de Desenvolvimento da IoT um tradutor usando o Azure Function e os Serviços Cognitivos. Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Usar a tarefa do Visual Studio Code para automatizar os provisionamentos de nuvem
> * Configurar a cadeia de conexão do dispositivo IoT do Azure
> * Implantar o Azure Function
> * Testar a tradução da mensagem de voz

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Conectar o IoT DevKit AZ3166 ao Azure IoT Suite para monitoramento remoto](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)