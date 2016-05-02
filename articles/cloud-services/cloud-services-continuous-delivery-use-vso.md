<properties
	pageTitle="Entrega contínua com o Visual Studio Team Services no Azure | Microsoft Azure"
	description="Saiba como configurar seus projetos de equipe do Visual Studio Team Services para serem compilados e implantados automaticamente no recurso Aplicativo Web no Serviço de Aplicativo do Azure ou nos serviços de nuvem."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Entrega contínua no Azure usando Visual Studio Team Services

Você pode configurar seus projetos de equipe do Visual Studio Team Services para compilação e implantação automática em aplicativos Web ou serviços de nuvem do Azure. (Para obter informações sobre como configurar uma compilação contínua e implantar um sistema usando um Team Foundation Server *local*, consulte [Entrega contínua de serviços de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).)

Este tutorial pressupõe que você possui o Visual Studio 2013 e o SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE] Você precisa de uma conta do Visual Studio Team Services para concluir este tutorial: você pode [abrir uma conta do Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Team Services, siga essas etapas.

## 1: Criar um projeto de equipe

Siga as instruções contidas [aqui](http://go.microsoft.com/fwlink/?LinkId=512980) para criar seu projeto de equipe e vinculá-lo ao Visual Studio. Este passo a passo pressupõe que você está usando oo Controle de Versão do Team Foundation (TFVC). Se quiser usar o Git para controle de versão, consulte [a versão do Git neste passo a passo](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## 2: Faça check-in em um projeto para o controle do código-fonte

1. No Visual Studio, abra a solução que você deseja implantar ou crie uma nova. Você pode implantar um aplicativo Web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo. Se você desejar criar uma nova solução, crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC e escolha o aplicativo da Internet para a função web. Quando solicitado, escolha **Aplicativo da Internet**. Se você quiser criar um aplicativo Web, escolha o modelo de projeto de Aplicativo Web ASP.NET e escolha MVC. Consulte [Criar um aplicativo web ASP.NET no Serviço de Aplicativo do Azure](../app-service-web/web-sites-dotnet-get-started.md).

	> [AZURE.NOTE] O Visual Studio Team Services só suporta implantações de CI de aplicativos Web do Visual Studio no momento. Projetos de Site estão fora do escopo.

1. Abra o menu de contexto da solução e selecione **Adicionar solução ao controle do código-fonte**.

	![][5]

1. Aceite ou altere os padrões e escolha o botão **OK**. Quando o processo estiver concluído, os ícones do controle do código-fonte aparecerão no **Gerenciador de Soluções**.

	![][6]

1. Abra o menu de atalho da solução e escolha **Fazer Check-In**.

	![][7]

1. Na área **Alterações Pendentes** do **Team Explorer**, digite um comentário para o check-in e escolha o botão **Fazer Check-In**.

	![][8]

	Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se desejar que as alterações sejam excluídas, escolha o link **Incluir Tudo**.

	![][9]

## 3: Conectar o projeto ao Azure

1. Agora que você tem um projeto da equipe do VS Team Services com algum código-fonte nele, você está pronto para conectar seu projeto da equipe ao Azure. No [portal clássico do Azure](http://manage.windowsazure.com), selecione o serviço de nuvem ou site ou crie um novo; para isso, selecione o ícone **+** na parte inferior esquerda e escolha **Serviço de Nuvem** ou **Aplicativo Web**, depois selecione **Criação Rápida**. Escolha o link **Configurar a publicação com o Visual Studio Team Services**.

	![][10]

1. No assistente, digite o nome da sua conta do Visual Studio Team Services na caixa de texto e clique no link **Autorizar agora**. Você pode ser solicitado a entrar.

	![][11]

1. Na caixa de diálogo pop-up **Solicitação de Conexão**, clique no botão **Aceitar** para autorizar o Azure a configurar seu projeto da equipe no VS Team Services.

	![][12]

1. Quando a autorização for bem-sucedida, você verá uma lista suspensa que contém uma lista de seus projetos de equipe do Visual Studio Team Services. Escolha o nome do projeto da equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.

	![][13]

1. Quando seu projeto estiver vinculado, você verá algumas instruções para fazer check-in das alterações em seu projeto de equipe do Visual Studio Team Services. Em seu próximo check-in, o Visual Studio Team Services criará e implantará o projeto no Azure. Tente isso agora clicando no link **Fazer Check-In no Visual Studio** e no link **Iniciar o Visual Studio** (ou o botão **Visual Studio** equivalente na parte inferior da tela do portal).

	![][14]

## 4: Disparar uma recompilação e reimplantar seu projeto

1. No **Team Explorer** do Visual Studio, escolha o link **Gerenciador do Controle do Código-fonte**.

	![][15]

1. Navegue para seu arquivo de solução e abra-o.

	![][16]

1. No **Gerenciador de Soluções**, abra um arquivo e altere-o. Por exemplo, altere o arquivo `_Layout.cshtml` na pasta Views\\Shared em uma função Web do MVC.

	![][17]

1. Edite o logotipo do site e pressione **Ctrl+S** para salvar o arquivo.

	![][18]

1. No **Team Explorer**, escolha o link **Alterações Pendentes**.

	![][19]

1. Digite um comentário e escolha o botão **Fazer Check-In**.

	![][20]

1. Escolha o botão **Início** para retornar à home page do **Team Explorer**.

	![][21]

1. Escolha o link **Compilações** para exibir as compilações em andamento.

	![][22]

	O **Team Explorer** mostra que uma compilação foi disparada para seu check-in.

	![][23]

1. Clique duas vezes no nome da compilação em andamento para exibir um log detalhado enquanto a compilação está em andamento.

	![][24]

1. Enquanto a compilação estiver em andamento, examine a definição da compilação que foi criada quando você vinculou o TFS no Azure usando o assistente. Abra o menu de atalho da definição de compilação e escolha **Editar Definição de Compilação**.

	![][25]

	Na guia **Gatilho**, você verá que a definição de compilação está definida, por padrão, para compilar em cada check-in.

	![][26]

	Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou aplicativo Web. Se estiver trabalhando com aplicativos Web, as propriedades que você verá serão diferentes daquelas mostradas aqui.

	![][27]

1. Especifique valores para as propriedades se você desejar valores diferentes dos padrões. As propriedades de publicação no Azure ficam na seção **Implantação**.

	A tabela a seguir mostra as propriedades disponíveis na seção **Implantação**:

	|Propriedade|Valor Padrão|
	|---|---|
	|Permitir certificados não confiáveis|Se falso, os certificados SSL deve ser assinados por uma autoridade raiz.|
	|Permitir Atualização|Permite que a implantação atualize uma implantação existente em vez de criar uma nova. Preserve o endereço IP.|
	|Não exclua|Se verdadeiro, não substitua uma implantação não relacionada existente (a atualização é permitida).|
	|Caminho para Configurações de Implantação|O caminho para seu arquivo .pubxml para um aplicativo Web, relacionado à pasta raiz do repositório. Ignorado para serviços de nuvem.|
	|Ambiente de implantação do Sharepoint|O mesmo que o nome do serviço.|
	|Ambiente de implantação do Azure|O nome do aplicativo Web ou do serviço de nuvem.|

1. Se está usando configurações de serviço múltipla (arquivos .cscfg), você pode especificar a configuração do serviço desejado na configuração **Compilar, Avançado argumentos MSBuild**. Por exemplo, para usar ServiceConfiguration.Test.cscfg, defina opções de linha de argumentos MSBuild `/p:TargetProfile=Test`.

	![][38]

	A essa altura, sua compilação deve estar concluída com êxito.

	![][28]

1. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste de projetos de teste de unidade associados.

	![][29]

1. No [portal clássico do Azure](http://manage.windowsazure.com), você poderá exibir a implantação associada na guia **Implantações** quando o ambiente de preparo estiver selecionado.

	![][30]

1.	Navegue até a URL do site. Para um aplicativo Web, basta clicar no botão **Procurar** na barra de comandos. Para um serviço de nuvem, escolha a URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo de um serviço de nuvem. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade **Ambiente de Serviço de Nuvem Alternativo** para a **Produção**. Esta captura de tela mostra onde a URL do site está na página do painel do serviço de nuvem.

	![][31]

	Uma nova guia do navegador será aberta para revelar seu site em execução.

	![][32]

	Para serviços de nuvem, se você fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. A última marcada como Ativa.

	![][33]

## 5: Reimplantar um build anterior

Esta etapa se aplica aos serviços de nuvem e é opcional. No portal clássico do Azure, selecione uma implantação anterior e clique no botão **Reimplantar** para retroceder seu site para um check-in anterior. Observe que isso vai disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.

![][34]

## 6: Alterar a implantação de Produção

Esta etapa se aplica somente aos serviços de nuvem, não aos aplicativo Web. Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo o botão **Permutar** no portal clássico do Azure. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.

![][35]

## 7: Executar testes de unidade

Esta etapa se aplica somente aos aplicativo Web, não serviços de nuvem. Para colocar um portão de qualidade em suas implantações, você pode executar testes da unidade e se eles falhares, você pode parar a implantação.

1.  No Visual Studio, adicione um projeto de teste de unidade.

	![][39]

1.  Adicione as referências do projeto aos projetos que deseja testar.

	![][40]

1.  Adicione alguns testes de unidade. Para começar, tente um teste fictício que sempre passará.

		```
		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;

		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}
		```

1.  Edite a definição da compilação, escolha a guia **Processar** e expanda o nó **Teste**.

1.  Defina o **Compilação falha na falha de teste** para Verdadeiro. Isso significa que a implantação não ocorre, a menos que passe no teste.

	![][41]

1.  Fila de uma nova compilação.

	![][42]

	![][43]

1. Enquanto a compilação está em processamento, verifique seu progresso.

	![][44]

	![][45]

1. Quando a compilação está concluída, verifique os resultados de teste.

	![][46]

	![][47]

1.  Tente criar um teste que falhará. Adicione um novo teste copiando o primeiro, renomeando-o e comentando a linha de código que afirma que NotImplementedException é uma exceção esperada.

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. Faça check-in das alterações para a fila de uma nova compilação.

	![][48]

1. Exina os resultados de teste para ver detalhes sobre a falha.

	![][49]

	![][50]

## Próximas etapas
Para obter mais informações sobre o teste de unidade no Visual Studio Team Services, consulte [Executar testes de unidade em sua compilação](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se você estiver usando o Git, consulte [Compartilhar seu código no Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Implantação contínua usando GIT no Serviço de Aplicativo do Azure](../app-service-web/web-sites-publish-source-control.md). Para obter mais informações sobre o Visual Studio Team Services, consulte [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

<!---HONumber=AcomDC_0420_2016-->