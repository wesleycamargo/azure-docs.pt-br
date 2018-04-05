---
title: Recuperar uma mensagem do Twitter com o Azure Functions | Microsoft Docs
description: Use o sensor de movimento para detectar agitando e usar o Azure Functions para localizar um tweet aleatório com um hashtag que você especificar.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: a84393c5c53b8f8e4a8b688a462f433b2d611b0e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake para um tweet -- Recuperar uma mensagem do Twitter com o Azure Functions!

Neste projeto, você aprenderá como usar o sensor de movimento para disparar um evento usando o Azure Functions. O aplicativo recupera um tweet aleatório com um #hashtag que você configura no seu esboço de Arduino. O tweet exibe na tela Kit de Desenvolvimento.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu Kit de Desenvolvimento conectado ao Wi-Fi.
* Preparar o ambiente de desenvolvimento.

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses métodos:

* Ative uma [conta do Microsoft Azure de avaliação por 30 dias](https://azure.microsoft.com/en-us/free/)
* Declare seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou o Visual Studio

## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

### <a name="start-vs-code"></a>Iniciar o VS Code

- Verifique se o Kit de Desenvolvimento **não** está conectado ao computador.
- Iniciar o VS Code.
- Conecte o DevKit ao computador.

O VS Code localiza o seu Kit de Desenvolvimento automaticamente e exibe uma página de introdução:

![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> Ao iniciar o VS Code, você receberá uma mensagem de erro que o Arduino IDE ou o pacote de quadro relacionado não pode ser encontrado. Se esse erro ocorrer, feche o VS Code e reinicie o Arduino IDE. O VS Code agora deve localizar o caminho do Arduino IDE corretamente.

### <a name="open-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **ShakeShake**. Abre uma nova janela do VS Code com a pasta de projeto nela.

![Exemplos de minisolução](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Se você fechar o painel, você poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa por meio de `Ctrl+P` (macOS: `Cmd+P`) digitando `task cloud-provision`:.

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários:

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Se a página trava no status de carregamento ao tentar entrar no Azure, consulte este [etapa de Perguntas Frequentes] ({{"/docs/faq/#page-hangs-when-log-in-azure" | 
 
## <a name="modify-the-hashtag"></a>Modificar o #hashtag

Abra `ShakeShake.ino` e procure por esta linha de código:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Substitua a cadeia de caracteres `iot` dentro de chaves com seu hashtag preferencial. O Kit de Desenvolvimento posteriormente recupera um tweet aleatório que inclui o hashtag que você especificar nesta etapa.

## <a name="deploy-azure-functions"></a>Implantar o Azure Functions

Use `Ctrl+P` (macOS: `Cmd+P`) para executar `task cloud-deploy` para começar a implantar o código do Azure Functions:

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ocasionalmente, o Azure Function pode não funcionar corretamente. Para resolver esse problema quando ele ocorre, verifique esta [etapa das Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

### <a name="windows"></a>Windows

1. Use `Ctrl+P` para executar `task device-upload`.
2. O terminal solicita que você entre no modo de configuração. Para fazer isso:

   * Mantenha pressionado o botão A
   * Pressione e solte o botão Reiniciar.

3. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.
4. Isso define a cadeia de conexão que é recuperada da etapa `task cloud-provision`.
5. O VS Code então inicia verificando e carregando o esboço Arduino para o seu Kit de Desenvolvimento: ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Você pode obter uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse problema, verifique esta [etapa das Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Coloque o Kit de Desenvolvimento no modo de configuração: mantenha o botão A pressionado e, em seguida, pressione e solte o botão Reiniciar. A tela exibe “Configuração”.
2. Use `Cmd+P` para executar `task device-upload` para definir a cadeia de conexão que é recuperada da etapa `task cloud-provision`.
3. O VS Code então inicia verificando e carregando o esboço Arduino para o seu Kit de Desenvolvimento: ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Você pode obter uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse problema, verifique esta [etapa das Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Teste do projeto

Após a inicialização do aplicativo, clique e solte o botão A e, em seguida, cuidadosamente agite o quadro Kit de Desenvolvimento. Essa ação recupera um tweet aleatório, que contém o hashtag especificado anteriormente. Em alguns segundos, um tweet é exibe na tela do Kit de Desenvolvimento:

### <a name="arduino-application-initializing"></a>Inicialização do aplicativo Arduino...
![Inicialização-do-aplicativo-Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Pressione A para agitar...
![Pressione-A-para-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Pronto para agitar...
![Pronto-para-agitar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Processando...
![Processando](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Pressione B para leitura...
![Pressione-B-para-leitura](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Exiba um tweet aleatório...
![Exiba-um-tweet-aleatório](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Pressione o botão A novamente e agite para um novo tweet.
- Pressione o botão B para rolar pelo restante do tweet.

## <a name="how-it-works"></a>Como ele funciona

![diagrama](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

O esboço de Arduino envia um evento para o Hub IoT do Azure. Este evento dispara o aplicativo Azure Functions. O aplicativo Azure Functions contém a lógica para se conectar à API do Twitter e recuperar um tweet. Ele envolve, em seguida, o texto escrito em uma mensagem C2D (nuvem para dispositivo) e envia-o novamente para o dispositivo.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcional: Usar seu próprio token de portador do Twitter

Para fins de teste, este projeto de exemplo usa um token de portador pré-configurado do Twitter. No entanto, há um [limite de taxa](https://dev.twitter.com/rest/reference/get/search/tweets) para cada conta do Twitter. Se você quiser usar seu próprio token, siga estas etapas:

1. Vá para [portal do Desenvolvedor do Twitter](https://dev.twitter.com/) para registrar um novo aplicativo do Twitter.

2. [Obtenha a Chave do consumidor e Segredos do consumidor](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) do seu aplicativo.

3. Use [algum utilitário](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) para gerar um token de portador do Twitter dessas duas chaves.

4. No [portal do Azure](https://portal.azure.com/){: destino = blank"}, vá para o **Grupo de Recursos** e localize o Azure Function (tipo: serviço de aplicativo) para o projeto"Shake, Shake". O nome contém sempre a cadeia de caracteres 'shake...'.
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Atualize o código para `run.csx` em **Funções > shakeshake-cs** com seu próprio token:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Salve o arquivo e clique em **Executar**.


## <a name="problems-and-feedback"></a>Comentários e problemas

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>A tela exibe 'Nenhum Tweet', enquanto cada etapa foi executada com êxito

Normalmente, essa condição ocorre pela primeira vez que você implanta e executa o exemplo porque o aplicativo de função requer em qualquer lugar de alguns segundos até um minuto para inicialização a frio do aplicativo. Ou, ao executar o código, há alguns blips que causam uma reinicialização do aplicativo. Quando essa condição ocorre, o aplicativo de dispositivo pode obter um tempo limite para buscar o tweet. Nesse caso, você pode tentar um ou ambos os métodos para resolver esse problema:

1. Clique no botão Reiniciar no Kit de Desenvolvimento para executar o aplicativo de dispositivo novamente.

2. No [portal do Azure](https://portal.azure.com/), localize o aplicativo Azure Functions que você criou e reinicie-o: ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Comentários

Se você tiver outros problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco pelos canais a seguir:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar a um dispositivo do Kit de Desenvolvimento para o Azure IoT Suite e recuperar um tweet, aqui estão as próximas etapas sugeridas:

* [Visão geral do Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)