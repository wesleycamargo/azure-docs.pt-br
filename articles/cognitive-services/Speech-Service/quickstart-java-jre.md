---
title: 'Início rápido: reconhecer fala em Java (Windows ou Linux)'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em Java (Windows ou Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234247"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>Início rápido: reconhecer fala em Java (Windows ou Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este documento descreve como criar um aplicativo de console baseado em Java para JRE (Java Runtime Environment) que usa SDK de Fala.
O aplicativo é baseado no Pacote Maven de SDK dos Serviços Cognitivos da Microsoft.
Usamos o Eclipse como um IDE (Ambiente de Desenvolvimento Integrado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC (Windows x64, Ubuntu 16.04 x64) capaz de executar o Eclipse, com um microfone funcionando.
* 64-bit JRE/JDK para Java 8.
* Versão 4.8 do [Eclipse](https://www.eclipse.org), versão de 64 bits.
* No Ubuntu 16.04, execute os comandos a seguir para a instalação dos pacotes requeridos:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No Inicializador do Eclipse, insira o nome de um novo diretório no campo **Espaço de trabalho**.
   Em seguida, clique em **Inicialização**.

   ![Criar espaço de trabalho do Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Após algum tempo, a janela principal do IDE do Eclipse é exibida.
   Se houver uma tela de boas-vindas, feche-a.

1. Selecione **Arquivo** \> **Novo** \> **Projeto**.

1. No assistente **Novo Projeto** exibido, selecione **Projeto Java** e clique em **Avançar**.

   ![Selecionar um assistente](media/sdk/qs-java-jre-02-select-wizard.png)

1. Na próxima janela, insira **início rápido** como um nome de projeto e escolha **JavaSE-1.8** (ou superior) como ambiente de execução.
   Clique em **Concluir**.

   ![Selecionar um assistente](media/sdk/qs-java-jre-03-create-java-project.png)

1. Se uma janela intitulada **Abrir perspectiva associada?** aparecer, selecione **Abrir Perspectiva**.

1. No **Explorador de pacote**, clique com o botão direito do mouse no projeto **início rápido** e selecione **Configurar** \> **Converter para Projeto Maven**.

   ![Converter para projeto Maven](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Na janela que aparece, insira **com.microsoft.cognitiveservices.speech.samples** como **ID do Grupo** e **início rápido** como **ID do Artefato**. Selecione **Concluir**.

   ![Configurar Maven POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Edite o arquivo **pom.xml**.

  * No final do arquivo, antes da marca de fechamento `</project>`, crie uma seção de repositórios com uma referência ao repositório Maven para o SDK de Fala:

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Além disso, adicione depois uma seção de dependências com o SDK de Fala versão 0.6.0 como uma dependência:

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * Salve as alterações.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Selecione **Arquivo** \> **Novo** \> **Classe** para adicionar uma nova classe vazia ao projeto Java.

1. Na janela **Nova Classe Java** insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Criar uma classe Principal](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo trecho de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Pressione F11 ou selecione **Executar** \> **Depurar**.
Os próximos 15 segundos de entrada de fala do microfone serão reconhecidos e registrados na janela do console.

![Saída do console após o reconhecimento com êxito](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/java-jre`.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
