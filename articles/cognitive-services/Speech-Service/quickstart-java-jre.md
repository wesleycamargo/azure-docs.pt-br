---
title: 'Início rápido: reconhecer fala em Java (Windows ou Linux)'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em Java (Windows ou Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 1f0c19524ebd59dcf0377cc173cd780d656ba447
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339189"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-sdk"></a>Início Rápido: reconhecer fala em Java no Windows ou Linux usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo de console Java usando o [SDK de Fala](speech-sdk.md). Você transcreverá a fala em texto em tempo real por meio do microfone do computador. O aplicativo é compilado com o pacote do Maven do SDK de Fala e o Java IDE Eclipse (v4.8) no Windows de 64 bits ou no Ubuntu Linux 16.04. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.


## <a name="create-and-configure-project"></a>Criar e configurar o projeto

Se você estiver usando o Ubuntu 16.04, antes de iniciar o Eclipse, execute os seguintes comandos para garantir que os pacotes necessários sejam instalados.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Inicie o Eclipse.

1. No Inicializador do Eclipse, no campo **Espaço de Trabalho**, insira o nome de um novo diretório de workspace. Em seguida, selecione **Iniciar**.

   ![Captura de tela do Inicializador do Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Em alguns instantes, a janela principal do IDE do Eclipse aparece. Feche a tela de boas-vindas caso haja uma.

1. Na barra de menus do Eclipse, crie um novo projeto escolhendo **Arquivo** > **Novo** > **Projeto**.

1. A caixa de diálogo **Novo Projeto** aparecerá. Selecione **Projeto Java** e selecione **Avançar**.

   ![Captura de tela da caixa de diálogo Novo Projeto, com Projeto Java realçado](media/sdk/qs-java-jre-02-select-wizard.png)

1. O assistente de Novo Projeto Java é iniciado. No campo **Nome do projeto**, insira **quickstart** e escolha **JavaSE-1.8** como o ambiente de execução. Selecione **Concluir**.

   ![Captura de tela do assistente de Novo Projeto Java](media/sdk/qs-java-jre-03-create-java-project.png)

1. Se a janela **Abrir Perspectiva Associada?** aparecer, selecione **Abrir Perspectiva**.

1. No **Explorador de pacotes**, clique com o botão direito do mouse no projeto **quickstart**. Escolha **Configurar** > **Converter para Projeto Maven** no menu de contexto.

   ![Captura de tela do Explorador de pacotes](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. A janela **Criar novo POM** é exibida. No campo **ID do Grupo**, insira **com.microsoft.cognitiveservices.speech.samples** e, no campo **ID do Artefato**, insira **quickstart**. Em seguida, selecione **Concluir**.

   ![Captura de tela da janela Criar novo POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Abra o arquivo **pom.xml** e edite-o.

   * No final do arquivo, antes da marca de fechamento `</project>`, crie um elemento `repositories` com uma referência ao repositório Maven para o SDK de Fala, como mostrado aqui:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Adicione também um elemento `dependencies`, com o SDK de Fala versão 1.0.1 como uma dependência:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Salve as alterações.

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.
Os próximos 15 segundos de entrada de fala do microfone serão reconhecidos e registrados na janela do console.

![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/java-jre`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer as intenções da fala usando o SDK de Fala para Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
