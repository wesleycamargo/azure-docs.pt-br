---
title: 'Tutorial: Criar um aplicativo de vinculação de entidade – C#'
titlesuffix: Azure Cognitive Services
description: Analise texto e vincule entidades nomeadas a entradas relevantes em uma base de dados de conhecimento usando a API de Vinculação de Entidade.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 883d566fd3a6089eb9e72498089f995697a318f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216805"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Tutorial: Criar um aplicativo de vinculação de entidade com C#

A Vinculação de Entidade da Microsoft é uma ferramenta de processamento de linguagem natural para analisar o texto e vincular entidades denominadas de entradas relevantes em uma base de conhecimento. 

Este tutorial explora a vinculação de entidade usando a biblioteca de cliente de Vinculação de Entidade como um pacote do NuGet. 

### <a name="Prerequisites">Pré-requisitos</a>

- Visual Studio 2015
- Uma chave de API dos Serviços Cognitivos da Microsoft
- Obter a biblioteca de clientes e o exemplo
- Pacote de NuGet de Vinculação de Entidade da Microsoft

Você pode baixar a biblioteca de cliente da API de Serviço de Inteligência de Vinculação de Entidade por meio de [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). O arquivo zip baixado precisa ser extraído para uma pasta de sua escolha. Muitos usuários escolhem a pasta do Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Etapa 1: Assinar o Serviço de Inteligência de Vinculação de Entidade e obter sua chave</a>
Antes de usar o Serviço de Inteligência de Vinculação de Entidade, você deve se inscrever para uma chave de API. Consulte [Assinaturas](https://www.microsoft.com/cognitive-services/en-us/sign-up). Tanto a chave primária quanto a secundária podem ser usadas neste tutorial.

### <a name="step-2-create-a-new-project-in-visual-studio"> Etapa 2: Criar um novo projeto no Visual Studio</a>

Vamos começar criando um novo projeto no Visual Studio. Primeiro, inicie o Visual Studio 2015 no Menu Iniciar. Em seguida, crie um novo projeto selecionando **Instalado → Modelos → Visual C# → Windows Universal → Aplicativo em branco** para o modelo de projeto:

 ![Criar um aplicativo universal](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Etapa 3: Adicionar o pacote de NuGet de Vinculação de Entidade ao seu projeto</a>

A Vinculação de Entidade de Serviços Cognitivos é lançada como um pacote NuGet.org e deve ser instalado antes de você usá-lo.
Para adicioná-lo ao seu projeto, vá para a guia **Gerenciador de Soluções**, clique com o botão direito no seu projeto, e selecione **Gerenciar Pacotes do Nuget**.

Primeiro, na janela **Gerenciar Pacotes do NuGet**, selecione nuget.org como a **origem do Pacote** no canto superior direito. Selecione **Procurar** no canto superior esquerdo e, na caixa Pesquisar, digite "ProjectOxford.EntityLinking". Selecione o pacote do NuGet **Microsoft.ProjectOxford.EntityLinking** e clique em **Instalar**.

Em seguida, procure Newtonsoft.Json e Instalar. Se você for solicitado a revisar as alterações, clique em **OK**. Se aparecer os termos de licença EntityLinking, clique em **Aceito**.

A Vinculação de Entidade agora está instalada como parte do seu aplicativo. Você pode confirmar isso verificando que a referência **Microsoft.ProjectOxford.EntityLinking** está presente como parte do seu projeto no Gerenciador de Soluções.

 ![Biblioteca do nuget incluído no projeto](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Etapa 4: Adicionar um bloco de texto de entrada e saída para XAML do seu aplicativo</a>
Navegue até **MainPage. XAML** no **Gerenciador de Soluções**, em seguida, clique duas vezes no arquivo que será aberto em uma nova janela. Para sua conveniência, você pode clicar duas vezes no botão **XAML** da guia **Designer**, isso ocultará o **Visual Designer** e reservará todo o espaço para o modo de exibição de código.

 ![Biblioteca do nuget incluído no projeto](./Images/UWPMainPage.png)
 
Como um serviço de texto, a melhor maneira de visualizar a funcionalidade é criando um bloco de texto de entrada e saída. Para fazer isso, adicione o XAML a seguir na **Grade**. Esse código adiciona três componentes, uma caixa de texto de entrada, um bloco de texto de saída e um botão de início.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Etapa 5: Vá para adicionar Serviço de Inteligência de Vinculação de Entidade</a>
 
A interface do usuário é criada. Antes de usar o serviço de Vinculação de Entidade, é preciso adicionar o manipulador de clique do botão. Abra **MainPage. XAML** no **Gerenciador de Soluções**. Adicione um manipulador de button_Click no final do botão.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Um manipulador de clique do botão deve ser implementado no código. Abra **MainPage.xaml.cs** no **Gerenciador de Soluções** para implementar o clique do botão. O EntityLinkingServiceClient é um wrapper para recuperar as respostas de Vinculação de Entidade. O argumento construtor de EntityLinkingServiceClient é a chave de assinatura dos Serviços Cognitivos. Cole a chave de assinatura que você obteve na **Etapa 1** para chamar o serviço de Vinculação de Entidade. 

Abaixo está o código de exemplo, que adiciona "wikipediaId" à resposta usando o serviço de Vinculação de Entidade. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Agora você está pronto para executar o primeiro processo de linguagem natural do aplicativo de Vinculação de Entidade. Pressione a **tecla F5** para compilar e iniciar o aplicativo. Cole snippets de texto ou parágrafos na caixa de entrada. Pressione o botão "Obter resultados" e observe as entidades identificadas no bloco de saída.
 
 ![Resultado de exemplo UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Resumo</a>
 
Neste tutorial, você aprendeu como criar um aplicativo para potenciar a biblioteca de clientes do Serviço de Inteligência de Vinculação de Entidade com apenas algumas linhas de código XAML e C#. 

