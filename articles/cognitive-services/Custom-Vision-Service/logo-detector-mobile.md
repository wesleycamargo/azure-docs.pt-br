---
title: 'Tutorial: Usar o detector de logotipo personalizado para reconhecer serviços do Azure – Visão Personalizada'
titlesuffix: Azure Cognitive Services
description: Neste tutorial, você acompanhará as etapas de um aplicativo de exemplo que usa a Visão Personalizada do Azure como parte de um cenário de detecção de logotipo. Saiba como a Visão Personalizada é usada com outros componentes para entregar um aplicativo completo.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771403"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutorial: Reconhecer logotipos de serviços do Azure em imagens da câmera

Neste tutorial, você acompanhará as etapas de um aplicativo de exemplo que usa a Visão Personalizada do Azure como parte de um cenário maior. O aplicativo de Provisionamento Visual e IA, um aplicativo Xamarin.Forms para plataformas móveis, analisa imagens da câmera de logotipos de serviços do Azure e, em seguida, implanta os serviços reais na conta do Azure do usuário. Aqui você aprenderá como ele usa a Visão Personalizada em coordenação com outros componentes para entregar um aplicativo útil completo. Talvez você queira executar todo o aplicativo por conta própria ou apenas concluir a parte da Visão Personalizada da instalação e explorar como o aplicativo a utiliza.

Este tutorial mostrará como:

> [!div class="checklist"]
> - Criar um detector de objeto personalizado para reconhecer logotipos de serviços do Azure
> - Conectar seu aplicativo à Pesquisa Visual Computacional e à Visão Personalizada do Azure
> - Criar uma conta de entidade de serviço do Azure para implantar os serviços do Azure por meio do aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Carga de trabalho do Xamarin para Visual Studio (confira [Instalando o Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Um emulador do iOS ou o Android Emulator para Visual Studio
- [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (opcional)

## <a name="get-the-source-code"></a>Obter o código-fonte

Caso deseje usar o aplicativo Web fornecido, clone ou baixe o código-fonte do aplicativo no repositório [Provisionamento Visual de IA](https://github.com/Microsoft/AIVisualProvision) no GitHub. Abra o arquivo *Source/VisualProvision.sln* no Visual Studio. Em breve, você fará as edições necessárias em alguns dos arquivos de projeto para que seja possível executar o aplicativo.

## <a name="create-an-object-detector"></a>Criar um detector de objeto

Entre no [site da Visão Personalizada](https://customvision.ai/) e crie um projeto. Especifique um projeto de Detecção de Objetos e use o domínio do Logotipo; isso permitirá que o serviço use um algoritmo otimizado para a detecção de logotipo. 

![janela da caixa de diálogo Novo projeto no site da Visão Personalizada no navegador Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Em seguida, você precisará treinar o algoritmo de detecção de logotipo carregando imagens de logotipos de serviços do Azure e marcando-os manualmente. O repositório de AIVisualProvision inclui um conjunto de imagens de treinamento que pode ser usado. No site, selecione o botão **Adicionar imagens** na guia **Imagens de Treinamento** e, em seguida, navegue para a pasta **Documents/Images/Training_DataSet** do repositório. Você precisará marcar manualmente os logotipos em cada imagem e, portanto, se estiver apenas testando este projeto, você poderá carregar apenas um subconjunto das imagens. No mínimo, você precisará carregar 15 instâncias de cada tag que pretender usar.

Depois de carregar as imagens de treinamento, selecione a primeira na exibição. Isso abrirá a janela de marcação. Desenhe caixas e atribua marcas a cada logotipo em cada imagem. 

![imagem de logotipos com as marcas sendo aplicadas no site da Visão Personalizada](media/azure-logo-tutorial/tag-logos.png)

O aplicativo está configurado para trabalhar com cadeias de caracteres de tag específicas; confira as definições no arquivo *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Quando você marcar uma imagem, navegue para a direita para marcar a próxima. Saia da janela de marcação quando terminar.

## <a name="train-the-object-detector"></a>Treinar o detector de objeto

No painel esquerdo, defina a opção **Marcas** como **Marcado** e você deverá ver todas as suas imagens. Em seguida, clique no botão verde na parte superior da página para treinar o modelo. Isso ensinará o algoritmo a reconhecer as mesmas marcas nas novas imagens. Também testará o modelo em algumas das imagens existentes para gerar pontuações de precisão.

![o site da Visão Personalizada, na guia Imagens de Treinamento; o botão Treinar está contornado](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obter a URL de previsão

Depois que o modelo for treinado, você estará pronto para integrá-lo ao aplicativo. Para fazer isso, você precisará obter a URL do ponto de extremidade (o endereço do modelo que será consultado pelo aplicativo) e a chave de previsão (para permitir acesso ao aplicativo às solicitações de previsão). Na guia **Desempenho**, clique no botão **URL de Previsão** na parte superior da página.

![o site da Visão Personalizada com uma tela da API de Previsão mostrando um endereço de URL e a chave de API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copie a URL do arquivo de imagem e o valor `Prediction-key` para os campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Examinar o uso da Visão Personalizada

Abra o arquivo *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* para ver como a chave e a URL do ponto de extremidade da Visão Personalizada são usadas no aplicativo. O método **PredictImageContentsAsync** usa um fluxo de bytes de um arquivo de imagem, juntamente com um token de cancelamento (para o gerenciamento de tarefas assíncronas), chama a API de previsão da Visão Personalizada e retorna o resultado da previsão. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Esse resultado assume a forma de uma instância de **PredictionResult**, que por si só contém uma lista de instâncias de **Prediction**. Uma **Previsão** contém uma tag detectada e sua localização de caixa delimitadora na imagem.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Caso deseje saber mais sobre como o aplicativo manipula esses dados, comece no método **GetResourcesAsync**, definido no arquivo *Source/VisualProvision/Services/Recognition/RecognitionService.cs*. 

## <a name="add-computer-vision"></a>Adicionar a Pesquisa Visual Computacional

A parte da Visão Personalizada do tutorial está completa, mas caso você deseje executar o aplicativo, precisará integrar o serviço de Pesquisa Visual Computacional também. O aplicativo usa o recurso de reconhecimento de texto da Pesquisa Visual Computacional para complementar o processo de detecção de logotipo; um logotipo do Azure pode ser reconhecido por sua aparência _ou_ pelo texto impresso próximo a ele. Ao contrário dos modelos da Visão Personalizada, a Pesquisa Visual Computacional é pré-treinada para executar determinadas operações em imagens ou vídeos.

Basta assinar o serviço de Pesquisa Visual Computacional para obter uma chave e uma URL do ponto de extremidade. Confira [Como obter chaves de assinatura](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) caso precise de ajuda com esta etapa.

![o serviço de Pesquisa Visual Computacional no portal do Azure, com o menu Início Rápido selecionado; um link para as chaves é realçado, assim como a URL do ponto de extremidade de API](media/azure-logo-tutorial/comvis-keys.png)

Em seguida, abra o arquivo *Source\VisualProvision\AppSettings.cs* e popule as variáveis `ComputerVisionEndpoint` e `ComputerVisionKey` com os valores corretos.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

O aplicativo exige uma conta de entidade de serviço do Azure para implantar serviços em sua assinatura do Azure. Uma entidade de serviço permite que você delegue permissões específicas a um aplicativo usando o controle de acesso baseado em função. Confira o [guia de entidades de serviço](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) caso deseje saber mais.

Crie uma entidade de serviço usando o Azure Cloud Shell ou a CLI do Azure (conforme mostrado a seguir). Primeiro, faça logon e selecione a assinatura que deseja usar.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Em seguida, crie a entidade de serviço (observe que pode levar alguns instantes para ela ser concluída).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Após a conclusão bem-sucedida, você deverá ver a saída JSON a seguir contendo as credenciais necessárias.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Anote os valores `clientId` e `tenantId`. Adicione-os aos campos apropriados no arquivo *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Execute o aplicativo

Nesta altura, você permitiu acesso ao aplicativo ao seguinte:
* um modelo treinado da Visão Personalizada
* o serviço de Pesquisa Visual Computacional
* uma conta de entidade de serviço 

Siga estas etapas para executar o aplicativo:

1. No Gerenciador de Soluções do Visual Studio, selecione o projeto VisualProvision.Android ou VisualProvision.iOS e escolha um emulador ou um dispositivo móvel conectado correspondente no menu suspenso na barra de ferramentas principal (observe que é necessário ter um dispositivo macOS para executar um emulador do iOS). Em seguida, execute o arquivo.

1. Na primeira tela carregada, insira a ID do cliente da entidade de serviço, a ID de locatário e a senha. Clique no botão **Logon**.

    > [!NOTE]
    > Em alguns emuladores, o botão **Logon** poderá não ser ativado nesta etapa. Se isso acontecer, interrompa o aplicativo, abra o arquivo _Source/VisualProvision/Pages/LoginPage.xaml_, localize o elemento `Button` rotulado como LOGIN BUTTON e remova a linha:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Em seguida, execute o aplicativo novamente.

    ![a tela do aplicativo com campos para as credenciais da entidade de serviço](media/azure-logo-tutorial/app-credentials.png)

1. Na próxima tela, selecione sua assinatura do Azure no menu suspenso (isto deverá conter todas as assinaturas às quais a entidade de serviço tem acesso). Clique no botão **Continuar**.

    ![a tela do aplicativo com um campo suspenso para a assinatura de destino do Azure](media/azure-logo-tutorial/app-az-subscription.png)

    Nesta altura, o aplicativo poderá solicitar que você permita acesso aos dispositivos à câmera e ao armazenamento de fotos. Aceite essas permissões.

1. Em seguida, a câmera do dispositivo será ativada. Tire uma foto de um dos logotipos de serviços do Azure treinados. Uma caixa de diálogo de implantação solicitará que você selecione uma região e um grupo de recursos para os novos serviços (como você faria se os estivesse implantando no portal do Azure). 

    ![uma tela da câmera do smartphone com dois recortes de papel de logotipos do Azure em exibição](media/azure-logo-tutorial/app-camera-capture.png)

    ![uma tela do aplicativo com campos para entrada do grupo de recursos e da região de implantação](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Limpar recursos 

Se você seguiu todas as etapas deste cenário e usou o aplicativo para implantar serviços do Azure em sua conta, acesse o [portal do Azure](https://ms.portal.azure.com/) quando terminar e cancele os serviços que não deseja usar.

Além disso, se você pretende criar seu próprio projeto de detecção de objetos com a Visão Personalizada no futuro, talvez você deseje excluir o projeto de detecção de logotipo criado neste tutorial. Uma avaliação gratuita da Visão Personalizada permite dois projetos. No [site da Visão Personalizada](https://customvision.ai), navegue para **Projetos** e selecione a lixeira em **Meu Novo Projeto**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e explorou um aplicativo Xamarin.Forms completo que utiliza o serviço de Visão Personalizada para detectar logotipos em imagens da câmera de dispositivos móveis. Em seguida, conheça as melhores práticas para a criação de um modelo da Visão Personalizada, de modo que quando você criar um para seu próprio aplicativo, você possa torná-lo eficiente e preciso.

> [!div class="nextstepaction"]
> [Como melhorar o classificador](getting-started-improving-your-classifier.md)