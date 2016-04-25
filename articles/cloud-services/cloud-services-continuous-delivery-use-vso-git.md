<properties
	pageTitle="Entrega contínua com o Git e o Visual Studio Team Services no Azure | Microsoft Azure"
	description="Saiba como configurar seus projetos de equipe do Visual Studio Team Services para usarem o Git para serem compilados e implantados automaticamente no recurso Aplicativo Web no Serviço de Aplicativo do Azure ou nos serviços de nuvem."
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
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Entrega contínua no Azure usando Visual Studio Team Services e Git

Você pode usar os projetos de equipe do Visual Studio Team Services para hospedar um repositório Git para seu código-fonte, e compilar e implantá-lo automaticamente em aplicativos Web ou serviços de nuvem do Azure sempre que enviar por push uma confirmação ao repositório.

Você precisará do Visual Studio 2013 e do SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE] Você precisa de uma conta do Visual Studio Team Services para concluir este tutorial:
> você pode [abrir uma conta do Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Team Services, siga essas etapas.

## Etapa 1: criar um repositório Git

1. Se você ainda não tiver uma conta do Visual Studio Team Services, pode obter uma [aqui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando criar seu projeto da equipe, escolha o Git como seu sistema de controle do código-fonte. Siga as instruções para conectar o Visual Studio ao projeto da equipe.

2. No **Team Explorer**, escolha o link **Clonar este repositório**.

	![][3]

3. Especifique o local da cópia local e selecione o botão **Clonar**.

## Etapa 2: criar um projeto e confirmá-lo no repositório

1. No **Team Explorer**, na seção **Soluções**, selecione o link **Novo** para criar um novo projeto no repositório local.

	![][4]

2. Você pode implantar um aplicativo Web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo. Crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. Certifique-se de que o projeto direciona-se ao .NET Framework 4 ou posterior. Se você está criando um projeto de serviço de nuvem, adicione uma função de trabalho e uma função web MVC do ASP.NET. Se você quiser criar um aplicativo Web, escolha o modelo de projeto de **Aplicativo Web ASP.NET** e escolha **MVC**. Consulte [Criar um aplicativo web ASP.NET no Serviço de Aplicativo do Azure](../app-service-web/web-sites-dotnet-get-started.md) para obter mais informações.

3. Abra o menu de atalho da solução e escolha **Confirmar**.

	![][7]

4. Se for a primeira vez que usa o Git no Visual Studio Team Services, você precisará fornecer algumas informações para se identificar no Git. Na área **Alterações Pendentes** do **Team Explorer**, insira seu nome de usuário e endereço de email. Digite um comentário para a confirmação e, em seguida, escolha o botão **Confirmar**.

	![][8]

5. Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se as alterações desejadas tiverem sido excluídas, escolha **Incluir Tudo**.

6. Você confirmou as alterações em sua cópia local do repositório. Em seguida, sincronize as alterações com o servidor escolhendo o link **Sincronizar**.

## Etapa 3: conectar o projeto ao Azure

1. Agora que possui um repositório Git no Visual Studio Team Services com algum código-fonte nele, você está pronto para conectar seu repositório Git ao Azure. No [portal clássico do Azure](http://manage.windowsazure.com), selecione o serviço de nuvem ou site ou crie um novo; para isso, selecione o ícone + na parte inferior esquerda e escolha **Serviço de Nuvem** ou **Aplicativo Web**, depois selecione **Criação Rápida**.

	![][9]

3. Para serviços de nuvem, escolha o link **Configurar a publicação com o Visual Studio Team Services**. Para aplicativos Web, escolha o link **Configurar a implantação por meio do controle do código-fonte**.

	![][10]

2. No assistente, digite o nome da sua conta do Visual Studio Team Services na caixa de texto e escolha o link **Autorizar agora**. Você pode ser solicitado a entrar.

	![][11]

3. Na caixa de diálogo pop-up **Solicitação de Conexão**, escolha **Aceitar** para autorizar o Azure a configurar seu projeto da equipe no Visual Studio Team Services.

	![][12]

4. Depois que a autorização obtiver êxito, você verá uma lista suspensa que contém os projetos de equipe do Visual Studio Team Services. Selecione o nome do projeto da equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.

	![][13]

	Na próxima vez em que você enviar uma confirmação por push ao repositório, o Visual Studio Team Services vai compilar e implantar seu projeto no Azure.

## Etapa 4: disparar uma recompilação e reimplantar seu projeto

1. No Visual Studio, abra um arquivo e altere-o. Por exemplo, altere o arquivo `_Layout.cshtml` na pasta Views\\Shared em uma função web do MVC.

	![][17]

2. Edite o texto do rodapé do site e salve o arquivo.

	![][18]

3. No **Gerenciador de Soluções**, abra o menu de atalho do nó da solução, nó do projeto ou do arquivo que você alterou e então escolha **Confirmar**.

4. Digite um comentário e escolha **Confirmar**.

	![][20]

5. Selecione o link **Sincronizar**.

	![][38]

6. Escolha o link **Enviar por push** para enviar sua confirmação por push ao repositório no Visual Studio Team Services. (Você também pode usar o botão **Sincronizar** para copiar suas confirmações no repositório. A diferença é que **Sincronizar** também efetua pull das alterações mais recentes do repositório.

	![][39]

7. Escolha o botão **Início** para retornar à home page do **Team Explorer**.

	![][21]

8. Escolha **Compilações** para exibir as compilações em andamento.

	![][22]

	O **Team Explorer** mostra que uma compilação foi disparada para seu check-in.

	![][23]

9. Para exibir um log detalhado enquanto a compilação está em andamento, clique duas vezes no nome da compilação em andamento.

10. Enquanto a compilação estiver em andamento, examine a definição de compilação que foi criada quando você usou o assistente para vincular ao Azure. Abra o menu de atalho da definição de compilação e escolha **Editar Definição de Compilação**.

	![][25]

11. Na guia **Gatilho**, você verá que a definição de compilação está definida, por padrão, para compilar em cada check-in. (Para um serviço de nuvem, o Visual Studio Team Services compila e implanta a ramificação mestre no ambiente de preparo automaticamente. Você ainda precisa executar uma etapa manual para implantar no site ativo. Para um aplicativo Web que não tem um ambiente de preparo, ele implanta a ramificação mestra diretamente no site ativo.

	![][26]

1. Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou aplicativo Web.

	![][27]

1. Especifique valores para as propriedades se você desejar valores diferentes dos padrões. As propriedades de publicação no Azure estão na seção **Implantação** e talvez você também precise definir parâmetros MSBuild. Por exemplo, em um projeto de serviço de nuvem, para especificar uma configuração de serviço diferente de "Nuvem", defina os parâmetros do MSbuild como `/p:TargetProfile=[YourProfile]`, em que *[YourProfile]* corresponde a um arquivo de configuração de serviço com um nome como ServiceConfiguration.*YourProfile*.cscfg.

	A tabela a seguir mostra as propriedades disponíveis na seção **Implantação**:

	|Propriedade|Valor Padrão|
	|---|---|
	|Permitir certificados não confiáveis|Se falso, os certificados SSL deve ser assinados por uma autoridade raiz.|
	|Permitir Atualização|Permite que a implantação atualize uma implantação existente em vez de criar uma nova. Preserve o endereço IP.|
	|Não exclua|Se verdadeiro, não substitua uma implantação não relacionada existente (a atualização é permitida).|
	|Caminho para Configurações de Implantação|O caminho para seu arquivo .pubxml para um aplicativo Web, relacionado à pasta raiz do repositório. Ignorado para serviços de nuvem.|
	|Ambiente de implantação do Sharepoint|O mesmo que o nome do serviço.|
	|Ambiente de implantação do Azure|O nome do aplicativo Web ou do serviço de nuvem.|

1. A essa altura, sua compilação deve estar concluída com êxito.

	![][28]

1. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste de projetos de teste de unidade associados.

	![][29]

1. No [portal clássico do Azure](http://manage.windowsazure.com), você poderá exibir a implantação associada na guia **Implantações** quando o ambiente de preparo estiver selecionado.

	![][30]

1.	Navegue até a URL do site. Para um aplicativo Web, basta clicar no botão **Procurar** no portal. Para um serviço de nuvem, escolha o URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo.

	Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade **Ambiente de Serviço de Nuvem Alternativo** como **Produção**. Esta é a localização do URL do site está na página do painel do serviço de nuvem:

	![][31]

	Uma nova guia do navegador será aberta para revelar seu site em execução.

	![][32]

1.	Se fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. O mais recente é marcado como Ativo.

	![][33]

## Etapa 5: reimplantar uma compilação anterior

Esta etapa é opcional. No portal clássico do Azure, selecione uma implantação anterior e escolha **Reimplantar** para que seu site retroceda até um check-in anterior. Observe que isso vai disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.

![][34]

## Etapa 6: Alterar a implantação de produção

Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo **Trocar** no portal clássico do Azure. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.

![][35]

## Etapa 6: implantar por meio de uma ramificação em andamento.

Quando usa o Git, normalmente você faz alterações em uma ramificação em andamento e a integra à ramificação mestre quando seu desenvolvimento estiver concluído. Durante a fase de desenvolvimento de um projeto, você compilará e implantará a ramificação em andamento no Azure.

1. No **Team Explorer**, escolha o botão **Início** e depois escolha o botão **Ramificações**.

	![][40]

2. Escolha o link **Nova Ramificação**.

	![][41]

3. Insira o nome da ramificação, como “em andamento” e escolha **Criar Ramificação**. Isso cria uma nova ramificação local.

	![][42]

4. Publique a ramificação. Escolha o nome da ramificação em **Ramificações não publicadas** e selecione **Publicar**.

	![][44]

6. Por padrão, somente alterações na ramificação mestre disparam uma compilação contínua. Para definir a compilação contínua para uma ramificação em andamento, feche a página **Compilações** no **Team Explorer** e escolha **Editar Definição de Compilação**.

7. Abra a guia **Configurações da Origem**. Em **Ramificações monitoradas para integração e compilação contínua**, selecione **Clique aqui para adicionar uma nova linha**.

	![][47]

8. Especifique a ramificação que você criou, como refs/heads/working.

	![][48]

9. Faça uma alteração no código, abra o menu de atalho do arquivo alterado e escolha **Confirmar**.

	![][43]

10. Escolha o link **Confirmações Não Sincronizadas** e clique no botão **Sincronizar** ou no link **Enviar por push** para copiar as alterações para a cópia da ramificação em andamento no Visual Studio Team Services.

	![][45]

11. Navegue até a exibição **Compilações** e encontre a compilação que foi acionada para a ramificação em andamento.

## Próximas etapas

Para obter mais dicas de como usar o Git com Visual Studio Team Services, consulte [Desenvolver e compartilhar seu código no Git usando Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx); para obter informações de como usar um repositório Git que não é gerenciado pelo Visual Studio Team Services para publicar no Azure, consulte [Implantação contínua usando GIT no Serviço de Aplicativo do Azure](../app-service-web/web-sites-publish-source-control.md). Para obter mais informações sobre o Visual Studio Team Services, consulte [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG

<!---HONumber=AcomDC_0413_2016-->