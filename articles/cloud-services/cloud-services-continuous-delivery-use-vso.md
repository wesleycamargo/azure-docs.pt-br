<properties
	pageTitle="Fornecimento contínuo com o Visual Studio Online no Azure"
	description="Saiba como configurar seus projetos de equipe do Visual Studio Online para serem compilados e implantados automaticamente no recurso Aplicativo Web nos Serviços de Aplicativo do Azure ou serviços de nuvem."
	services="cloud-services"
	documentationCenter=".net"
	authors="kempb"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/07/2015" 
	ms.author="kempb"/>


# Entrega contínua no Azure usando o Visual Studio Online

  Você pode configurar seus projetos de equipe do Visual Studio Online para compilação e implantação automática em aplicativos Web ou serviços de nuvem do Azure. (Para obter informações sobre como configurar uma compilação contínua e implantar um sistema usando um Team Foundation Server *local*, consulte [Entrega contínua de serviços de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).)

Este tutorial pressupõe que você possui o Visual Studio 2013 e o SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Você precisa de uma conta do Visual Studio online para concluir este tutorial:
> Você pode [abrir uma conta do Visual Studio Online gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Online, siga estas etapas:

-   [Etapa 1: criar um projeto da equipe.][]

-   [Etapa 2: fazer check-in em um projeto para o controle do código-fonte.][]

-   [Etapa 3: Conectar o projeto ao Azure.][]

-   [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.][]

-   [Etapa 5: Reimplantar uma compilação anterior (opcional)][]

-   [Etapa 6: Alterar a implantação da produção (somente serviços de nuvem)][]

-	[Etapa 7: Executar testes de unidade (opcional)][]

## <a name="step1"></a>Etapa 1: criar um projeto da equipe

Siga as instruções contidas [aqui](http://go.microsoft.com/fwlink/?LinkId=512980) para criar seu projeto de equipe e vinculá-lo ao Visual Studio. Este passo a passo pressupõe que você está usando oo Controle de Versão do Team Foundation (TFVC). Se quiser usar o Git para controle de versão, consulte [a versão do Git neste passo a passo](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="step2">Etapa 2: Fazer check-in em um projeto para o controle do código-fonte</a>.

1. No Visual Studio, abra a solução que você deseja implantar ou crie uma nova.
Você pode implantar um aplicativo Web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo.
Se você desejar criar uma nova solução,crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. 
Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC e escolha o aplicativo da Internet para a função web. Quando solicitado, escolha **Aplicativo da Internet**.
Se você quiser criar um aplicativo Web, escolha o modelo de projeto de Aplicativo Web ASP.NET e escolha MVC. Consulte [Criar um aplicativo web ASP.NET no Serviço de Aplicativo do Azure](../web-sites-dotnet-get-started.md).

> [AZURE.NOTE]Visual Studio Online só tem suporte para implantações CI de aplicativos Web do Visual Studio no momento. Projetos de Site estão fora do escopo.


2. Abra o menu de contexto da solução e selecione **Adicionar solução ao controle do código-fonte**.<br/>
![][5]

3. Aceite ou altere os padrões e escolha o botão **OK**. Quando o processo estiver concluído, os ícones do controle do código-fonte aparecerão no Gerenciador de Soluções.<br/>
![][6]

4. Abra o menu de atalho da solução e escolha **Fazer Check-In**.<br/>
![][7]

5. Na área Alterações Pendentes do Team Explorer, digite um comentário para o check-in e escolha o botão **Fazer Check-In**.<br/>
![][8]

<br/>
Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se desejar que as alterações sejam excluídas, escolha o link **Incluir Tudo**.<br/>
![][9]

## <a name="step3"></a>Etapa 3: Conectar o projeto ao Azure.

1. Agora que você possui um projeto da equipe do VSO com algum código-fonte nele, você está pronto para conectar seu projeto da equipe ao Azure. No [Portal do Azure](http://manage.windowsazure.com), selecione o serviço de nuvem ou site ou crie um novo selecionando o ícone + na parte inferior esquerda e escolhendo **Serviço de Nuvem** ou **Aplicativo Web** e, em seguida, **Criação Rápida**. Escolha o link **Configurar publicação com o Visual Studio Online**.<br/>
![][10]

2. No assistente, digite o nome de sua conta do Visual Studio Online na caixa de texto e clique no link **Autorizar Agora**. Você pode ser solicitado a entrar.<br/>
![][11]

3. Na caixa de diálogo pop-up OAuth, escolha **Aceitar** para autorizar o Azure a configurar seu projeto da equipe no VSO.<br/>
![][12]

4. Quando a autorização for bem-sucedida, você verá uma caixa suspensa contendo uma lista de seus projetos de equipe do Visual Studio Online. Selecione o nome do projeto da equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.<br/>
![][13]

5. Quando seu projeto estiver vinculado, você verá algumas instruções para fazer check-in das alterações em seu projeto de equipe do Visual Studio Online. Em seu próximo check-in, o Visual Studio Online irá compilar e implantar seu projeto no Azure. Tente isso agora clicando no link **Fazer Check-In no Visual Studio** e no link **Iniciar o Visual Studio** (ou o botão **Visual Studio** equivalente na parte inferior da tela do portal).<br/>
![][14]

## <a name="step4"></a>Etapa 4: Acionar uma recompilação e reimplantar seu projeto

1. No Team Explorer do Visual Studio, clique no link **Gerenciador do controle do código-fonte**.<br/>
![][15]

2. Navegue para seu arquivo de solução e abra-o.<br/>
![][16]

3. No Gerenciador de Soluções, abra um arquivo e altere-o. Por exemplo, altere o arquivo \_Layout.cshtml na pasta Views\\Shared em uma função Web do MVC.<br/>
![][17]

4. Edite o logotipo do site e pressione Ctrl+S para salvar o arquivo.<br/>
![][18]

5. No Team Explorer, escolha o link **Alterações Pendentes**.<br/>
![][19]

6. Digite um comentário e escolha o botão **Fazer Check-In**.<br/>
![][20]

7. Escolha o botão Início para retornar à home page do Team Explorer.<br/>
![][21]

8. Escolha o link **Compilações** para exibir as compilações em andamento.<br/>
![][22]
<br/>
O Team Explorer mostra que uma compilação foi disparada para seu check-in.<br/>
![][23]

9. Clique duas vezes no nome da compilação em andamento para exibir um log detalhado enquanto a compilação está em andamento.<br/>
![][24]

10. Enquanto a compilação estiver em andamento, examine a definição da compilação que foi criada quando você vinculou o TFS no Azure usando o assistente. Abra o menu de atalho da definição de compilação e escolha **Editar Definição de Compilação**.<br/>
![][25]
<br/>
Na guia **Gatilho**, você verá que a definição de compilação está definida, por padrão, para compilar em cada check-in.<br/>
![][26]
<br/>
Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou aplicativo Web. Se estiver trabalhando com sites, as propriedades que você verá serão diferentes daquelas mostradas aqui.<br/>
![][27]
<br/>
Especifique valores para as propriedades se você desejar valores diferentes dos padrões. As propriedades de publicação no Azure estão na seção Implantação.
A tabela a seguir mostra as propriedades disponíveis na seção de Implantação:
	<table>
<tr><td><b>Propriedade</b></td><td><b>Valor padrão</b></td></tr>
><tr><td>Permitir Certificados não Confiáveis</td><td>Se falso, os certificados SSL deve ser assinados por uma autoridade raiz.</td></tr>
<tr><td>Permitir Atualização</td><td>Permite que uma implantação atualize uma implantação existente, ao invés de criar uma nova. Preserve o endereço IP.</td></tr>
><tr><td>Não exclua</td><td>Se verdadeiro, não substitua uma implantação não relacionada existente (a atualização é permitida).</td></tr>
<tr><td>Caminho para Configurações de Implantação</td><td>O caminho para seu arquivo .pubxml para um aplicativo Web, relacionado à pasta raiz do repositório. Ignorado para serviços de nuvem.</td></tr>
<tr><td>Ambiente de implantação do Sharepoint</td><td>O mesmo que o nome do serviço</td></tr>
<tr><td>Ambiente de implantação do Azure</td><td>O nome do aplicativo Web ou do serviço de nuvem</td></tr>
</table>
<br/>

Se está usando configurações de serviço múltipla (arquivos .cscfg), você pode especificar a configuração do serviço desejado na configuração **Compilar, Avançado argumentos MSBuild**. Por exemplo, para usar ServiceConfiguration.Test.cscfg, defina opções de linha de argumentos MSBuild /p:TargetProfile=Test.<br/>
![][38]

11. A essa altura, sua compilação deve estar concluída com êxito.<br/>
![][28]

12. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste associado aos projetos de teste de unidade.<br/>
![][29]

13. No [Portal do Azure](http://manage.windowsazure.com), você poderá exibir a implantação associada na guia Implantações quando o ambiente de preparo estiver selecionado.<br/>
![][30]

14.	Navegue até a URL do site. Para um aplicativo Web, basta clicar no botão Procurar na barra de comandos. Para um serviço de nuvem, escolha a URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo de um serviço de nuvem. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade Ambiente de Serviço de Nuvem Alternativo para a produção. Essa captura de tela mostra onde a URL do site está na página do painel do serviço de nuvem: <br/>
![][31]
<br/>
Uma nova guia do navegador será aberta para revelar seu site em execução.<br/>
![][32]

15.	Para serviços de nuvem, se você fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. O mais recente marcado como Ativo.<br/>
![][33]

## <a name="step5"></a>Etapa 5: Reimplantar uma compilação anterior

Esta etapa se aplica aos serviços de nuvem e é opcional. No Portal de Gerenciamento, selecione uma implantação anterior e clique no botão **Reimplantar** para retroceder seu site para um check-in anterior. Observe que isso vai disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.<br/>
![][34]

## <a name="step6"></a>Etapa 6: Alterar a implantação de produção

Esta etapa se aplica somente aos serviços de nuvem, não aos aplicativo Web. Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo o botão Permutar no Portal de Gerenciamento. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.<br/>
![][35]

## <a name="step7"></a>Etapa 7: executar testes de unidade

Esta etapa se aplica somente aos aplicativo Web, não serviços de nuvem. Para colocar um portão de qualidade em suas implantações, você pode executar testes da unidade e se eles falhares, você pode parar a implantação.

1.  No Visual Studio, adicione um projeto de teste de unidade.<br/>
![][39]

2.  Adicione as referências do projeto aos projetos que deseja testar.<br/>
![][40]

3.  Adicione alguns testes de unidade. Para começar, tente um teste fictício que sempre passará.

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

4.  Edite a definição da compilação, escolha a guia Processar e expanda o nó de Teste.


5.  Defina o **Compilação falha na falha de teste** para Verdadeiro. Isso significa que a implantação não ocorre, a menos que passe no teste.<br/>
![][41]

6.  Colocar uma nova compilação na fila.<br/>
![][42]
<br/>
![][43]

7. Enquanto a compilação estiver em processamento, verifique seu progresso.<br/>
![][44]
<br/>
![][45]

7. Quando a compilação estiver concluída, verifique os resultados do teste.<br/>
![][46]
<br/>
![][47]

8.  Tente criar um teste que falhará. Adicione um novo teste copiando o primeiro, renomeando-o e comentando a linha de código que afirma que NotImplementedException é uma exceção esperada.

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. Faça check-in das alterações para colocar uma nova compilação na fila.<br/>
![][48]

10. Exiba os resultados de teste para ver detalhes sobre a falha.<br/>
![][49]
<br/>
![][50]

Para saber mais sobre o teste de unidade no Visual Studio Online, consulte [Executar testes de unidade em sua compilação](http://go.microsoft.com/fwlink/p/?LinkId=510474).

Para saber mais, consulte [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Se você estiver usando o Git, consulte [Compartilhar seu código no Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Implantação contínua usando GIT no Serviço de Aplicativo do Azure](../web-sites-publish-source-control.md).

[Etapa 1: criar um projeto da equipe.]: #step1
[Etapa 2: fazer check-in em um projeto para o controle do código-fonte.]: #step2
[Etapa 3: Conectar o projeto ao Azure.]: #step3
[Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]: #step4
[Etapa 5: Reimplantar uma compilação anterior (opcional)]: #step5
[Etapa 6: Alterar a implantação da produção (somente serviços de nuvem)]: #step6
[Etapa 7: Executar testes de unidade (opcional)]: #step7
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

<!---HONumber=August15_HO7-->