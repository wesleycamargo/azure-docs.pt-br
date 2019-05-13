---
title: Introdução aos Aplicativos Móveis usando o Xamarin.Forms
description: Siga este tutorial para começar a usar os Aplicativos Móveis para desenvolvimento do Xamarin.Forms
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: b99513cad34bba1b050a24795ecb21d0357d19c1
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416090"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Criar um aplicativo Xamarin.Forms com Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel do Xamarin.Forms usando o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure como back-end. Você cria um novo back-end do Aplicativo Móvel e um aplicativo de lista de tarefas pendentes Xamarin.Forms que armazena dados do aplicativo no Azure.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para o Xamarin.Forms.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para saber mais, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Ferramentas do Visual Studio para Xamarin, no Visual Studio 2017 ou posterior, ou o Visual Studio para Mac. Consulte a [página de instalação do Xamarin][Install Xamarin] para obter instruções.

* (opcional) Para compilar um aplicativo para iOS é necessário um Mac com Xcode 9.0 ou posterior. O Visual Studio para Mac pode ser usado para desenvolver aplicativos iOS ou o Visual Studio 2017 ou posteriormente, pode ser usado (desde que o Mac está disponível na rede).

## <a name="create-a-new-mobile-apps-back-end"></a>Criar um novo back-end de Aplicativos Móveis

Para criar um novo back-end de Aplicativos Móveis, faça o seguinte:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Agora, você configurou um back-end de Aplicativo Móvel que os aplicativos móveis poderão utilizar. Em seguida, você baixa um projeto do servidor para um back-end simples da "lista de tarefas" e o publica no Azure.

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor

Para configurar o projeto de servidor para usar o back-end Node.js ou .NET, faça o seguinte:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Baixar e executar a solução de Xamarin.Forms

As Ferramentas do Visual Studio para Xamarin são necessárias para abrir a solução, consulte as [instruções de instalação do Xamarin][Install Xamarin]. Se as ferramentas já estiverem instaladas, execute estas etapas para baixar e abrir a solução:

### <a name="visual-studio"></a>Visual Studio

1. Vá para o [Portal do Azure].

2. Na folha configurações do seu Aplicativo Móvel, clique em **Início Rápido** (em Implantação) > **Xamarin.Forms**. Na etapa 3, clique em **Criar um novo aplicativo** se essa opção ainda não tiver sido selecionada.  Em seguida, clique no botão **Baixar** .

   Essa ação baixa um projeto que contém um aplicativo cliente conectado ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

3. Extraia o projeto que você baixou e abra-o no Visual Studio.

   ![Projeto extraído no Visual Studio][8]

4. Siga as instruções abaixo para executar os projetos Android ou Windows e, se houver um computador Mac em rede disponível, o projeto iOS.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Vá para o [Portal do Azure].

2. Na folha configurações do seu Aplicativo Móvel, clique em **Início Rápido** (em Implantação) > **Xamarin.Forms**. Na etapa 3, clique em **Criar um novo aplicativo** se essa opção ainda não tiver sido selecionada.  Em seguida, clique no botão **Baixar** .

   Essa ação baixa um projeto que contém um aplicativo cliente conectado ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

3. Extraia o projeto que você baixou e, em seguida, abra-o no Visual Studio para Mac.

   ![Projeto extraído no Visual Studio para Mac][9]

4. Siga as instruções abaixo para executar os projetos Android ou iOS.



## <a name="optional-run-the-android-project"></a>(Opcional) Executar o projeto do Android

Nesta seção, será executado o projeto Xamarin.Android. Você poderá ignorá-la se não estiver trabalhando com dispositivos Android.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto do Android (Droid) e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto Android e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo em um emulador Android, pressione a tecla **F5** ou clique no botão **Iniciar**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android e selecione **Definir Como Projeto de Inicialização**.

2. Para compilar o projeto e iniciar o aplicativo em um emulador Android, selecione o menu **Executar** e, em seguida, **Iniciar Depuração**.



No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

![Aplicativo de tarefa pendente do Android][11]

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

> [!NOTE]
> O código que acessa o back-end dos Aplicativos Móveis está no arquivo C# **TodoItemManager.cs** do projeto de código compartilhado na solução.
>

## <a name="optional-run-the-ios-project"></a>(Opcional) Executar o projeto do iOS

Nesta seção, você executará o projeto Xamarin.iOS para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto iOS e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo no emulador do iPhone, selecione a tecla **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir Como Projeto de Inicialização**.

2. No menu **Executar**, selecione **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.



No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

![Aplicativo de tarefa pendente do iOS][10]

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

> [!NOTE]
> Você localizará o código que acessa o back-end do Aplicativo Móvel no arquivo C# **TodoItemManager.cs** do projeto de código compartilhado na solução.
>

## <a name="optional-run-the-windows-project"></a>(Opcional) Executar o projeto do Windows

Nesta seção, você executará o projeto Xamarin.Forms da UWP (Plataforma Universal do Windows) para dispositivos Windows. Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.

### <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse em qualquer projeto UWP e selecione **Definir como Projeto de Inicialização**.

2. No menu **Build**, selecione **Gerenciador de Configurações**.

3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Compilar** e **Implantar** ao lado do projeto Windows que você escolheu e verifique se o projeto de código compartilhado está com a caixa **Compilar** selecionada.

4. Para compilar o projeto e iniciar o aplicativo em um emulador do Windows, pressione a tecla **F5** ou clique no botão **Iniciar** (que deverá ler **Computador Local**).

> [!NOTE]
> O projeto do Windows não pode ser executado no macOS.



No aplicativo, digite um texto significativo, como *Saiba mais sobre o Xamarin* e selecione o sinal de adição (**+**).

Essa ação envia uma solicitação POST para o novo back-end dos Aplicativos Móveis hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os Itens armazenados na tabela são retornados pelo back-end dos Aplicativos Móveis e os dados são exibidos na lista.

![Aplicativo de tarefa pendente da UWP][12]

> [!NOTE]
> Você encontrará o código que acessa o back-end dos Aplicativos Móveis no arquivo C# **TodoItemManager.cs** do projeto de biblioteca de classes portátil da sua solução.
>

## <a name="troubleshooting"></a>solução de problemas

Se você tiver problemas para compilar a solução, execute o gerenciador de pacotes NuGet e atualize para a última versão do `Xamarin.Forms` e, no projeto Android, atualize os pacotes de suporte `Xamarin.Android`. Os projetos de Início Rápido nem sempre incluem as últimas versões.

Observe que todos os pacotes de suporte referenciados em seu projeto Android devem ter a mesma versão. O [pacote NuGet de Aplicativos Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) depende de `Xamarin.Android.Support.CustomTabs` para a plataforma Android e, portanto, se o projeto usar pacotes de suporte mais recentes, será necessário instalar diretamente esse pacote com a versão necessária para evitar conflitos.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar autenticação ao aplicativo](app-service-mobile-xamarin-forms-get-started-users.md) Saiba como autenticar os usuários do aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao aplicativo](app-service-mobile-xamarin-forms-get-started-push.md) Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end de Aplicativos Móveis para usar Hubs de Notificação do Azure para enviar as notificações por push.

* [Habilitar a sincronização offline para o aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md) Saiba como adicionar suporte offline ao aplicativo usando um back-end de Aplicativos Móveis. Com a sincronização offline, você pode exibir, adicionar ou modificar os dados do aplicativo móvel mesmo quando não há nenhuma conexão de rede.

* [Usar o cliente gerenciado para Aplicativos Móveis](app-service-mobile-dotnet-how-to-use-client-library.md) Saiba como trabalhar com o SDK do cliente gerenciado no aplicativo Xamarin.

* [Usar outros serviços do Azure com o Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Adicionar recursos adicionais do Azure como serviços cognitivos, armazenamento e pesquisa aos aplicativos Xamarin.Forms.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/
