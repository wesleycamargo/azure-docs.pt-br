<properties 
	pageTitle="Introdução aos Serviços Móveis do Azure para o Appcelerator Titanium" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento Appcelerator." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="hero-article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como aproveitar os Serviços Móveis do Azure nos aplicativos criados no Appcelerator.

Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa JavaScript para a lógica de negócios do lado do servidor.

Para concluir este tutorial, é necessário ter o [Appcelerator Titanium].

Se estiver criando para iOS, você também precisará do Xcode 5.1 e iOS 7.1 SDK ou versões posteriores. 

Para criação para Android, você também precisará do Android 4.3 ou SDK superior.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo no Appcelerator

1. No portal dos Serviços Móveis, escolha a guia **Dados** para seu serviço móvel.

2. Clique em **Adicionar uma Tabela** e crie uma nova tabela denominada **TodoItem**.

3. Baixe o novo aplicativo [Todolist do Appcelerator] e descompacte o projeto.

4. Se ainda não tiver feito isso, baixe e instale o [Appcelerator Titanium Studio][Appcelerator Titanium] e o SDK v3.2.1 ou posterior. Você também precisará do Xcode (v 5.0 +) e/ou Android SDK (v 4.3+) para executar o projeto.

5. De volta no portal dos Serviços Móveis, em **Painel**, escolha **Gerenciar Chaves** e copie a **Chave do Aplicativo**.

5. No index.js do aplicativo, substitua `<---App Name---->` e `<------------APP KEY------------>` pelos valores de seu serviço móvel.

## Execute seu novo aplicativo do Appcelerator ##

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Abra o Titanium Studio e acesse **Arquivo -> Importar** para importar o projeto que você baixou anteriormente.

    ![][0]

2.	Na tela seguinte, selecione **Projetos existentes** no espaço de trabalho e clique em **Próximo**.

    ![][1]

3.	Na tela de seleção do projeto, use a opção Procurar e localize o projeto do Azure Titanium, que você baixou do Portal de Gerenciamento do Azure.

    ![][2]

4.	Por fim, o projeto que você selecionou na seção de projetos é exibido. Opcionalmente, você pode marcar a opção &quot;Copiar projetos para o espaço de trabalho&quot;, que copiará o projeto baixado para o espaço de trabalho. Por fim, clique em Finalizar para abrir o projeto no Titanium Studio.

    ![][3]

5.	Selecione em qual plataforma (iOS/Android) você está interessado em executar o projeto.

    ![][4]

6.	Pressione o botão Executar para criar o projeto e iniciar o aplicativo no simulador do iPhone ou no emulador do Android.

7.	Faça sua escolha com base nas configurações do Portal de Gerenciamento do Azure.

    ![][5]

8.	Na tela seguinte, clique no ícone de mais (+) e digite um texto com sentido, como &quot;Concluir este tutorial&quot; e clique no botão Salvar.<br />

    ![][6]

    ![][7]

Isso envia uma solicitação POST para o novo serviço móvel hospedado no Microsoft Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

![][8]


>[AZURE.NOTE] Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo TodoService.m.

4. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![][9]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][10]


## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Todolist do Appcelerator]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[Introdução à autenticação]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users.md
[Introdução às notificações por push]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push.md

<!--HONumber=52-->