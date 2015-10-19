<properties
	pageTitle="Introdução aos serviços móveis para aplicativos JavaScript da Windows Store | Serviços Móveis do Azure"
	description="Siga este tutorial para começar a usar os Serviços Móveis do Azure para desenvolvimento da Windows Store em JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="10/06/2015"
	ms.author="glenga"/>

# Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo JavaScript da Windows Store usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows]

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo da Windows Store

Após a criação do seu serviço móvel, você poderá seguir um início rápido no Portal de Gerenciamento para criar um novo aplicativo JavaScript da Windows Store 8.1 que se conecte ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.


2. Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo da Windows Store**.

3. Se ainda não o fez, baixe e instale o [Visual Studio 2013][Visual Studio 2013 Express for Windows] no computador local ou na máquina virtual.

4. Clique em **Criar tabela TodoItem** para criar uma tabela para armazenar os dados do aplicativo.

5. Em **Baixe e execute seu aplicativo**, selecione um idioma para seu aplicativo e clique em **Baixar**.

  	Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo do Windows

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução no Visual Studio.

2. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um texto com sentido, como *Concluir o tutorial*, em **Inserir um TodoItem** e clique em **Salvar**.

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

4. (Opcional) Execute o aplicativo novamente e observe que os dados salvos da etapa anterior são carregados a partir do serviço móvel após a inicialização do aplicativo.
 
4. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

>[AZURE.NOTE]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo default.js.

## Próximas etapas
Agora que você concluiu o início rápido, saiba como trabalhar com o [Cliente dos Serviços Móveis para HTML/JavaScript](mobile-services-html-how-to-use-client-library.md).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO2-->