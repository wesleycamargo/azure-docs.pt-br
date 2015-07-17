<properties 
	pageTitle="Fornecimento contínuo com o Visual Studio Online no Azure" 
	description="Saiba como configurar seus projetos de equipe do Visual Studio Online para serem compilados e implantados automaticamente no recurso Aplicativo Web nos Serviços de Aplicativo do Azure ou serviços de nuvem." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="kempb"/>




# Fornecimento contínuo no Azure usando o Visual Studio Online e o Git

Você pode usar os projetos de equipe do Visual Studio Online para hospedar um repositório Git para seu código-fonte, e compilar e implantá-lo automaticamente em aplicativos Web ou serviços de nuvem do Azure sempre que enviar por push uma confirmação ao repositório.

Você precisará do Visual Studio 2013 e do SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com](http://www.visualstudio.com). Instale o SDK do Azure [aqui](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Você precisa de uma conta do Visual Studio online para concluir este tutorial:
> você pode [abrir uma conta do Visual Studio Online gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Online, siga estas etapas:

-   [Etapa 1: Criar um repositório Git.][]

-   [Etapa 2: criar um projeto e enviá-lo por push ao repositório Git.][]

-   [Etapa 3: Conectar o projeto ao Azure.][]

-   [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.][]

-   [Etapa 5: Reimplantar uma compilação anterior (opcional)][]

-   [Etapa 6: Alterar a implantação de produção][]

-	[Etapa 7: implantar por meio de uma ramificação em andamento][]

## <a name="step1"></a>Etapa 1: criar um repositório Git


1. Se ainda não tiver uma conta do Visual Studio Online, siga as instruções disponíveis [aqui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando criar seu projeto da equipe, escolha o Git como seu sistema de controle do código-fonte. Siga as instruções para conectar o Visual Studio ao projeto da equipe.

2. No Team Explorer, escolha o link **Clonar este repositório**. 
![][3]

3. Especifique o local da cópia local e clique no botão **Clonar**.
 
## <a name="step2"></a>Etapa 2: criar um projeto e confirmá-lo no repositório

1. No Team Explorer, na seção Soluções, selecione o link Novo para criar um novo projeto no repositório local.<br/>
![][4]

2. Você pode implantar um aplicativo Web ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo. Crie um novo projeto de Serviço de Nuvem do Azure ou um novo projeto ASP.NET MVC. Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC. Se você quiser criar um aplicativo Web, escolha o modelo de projeto de Aplicativo Web ASP.NET e escolha MVC. Consulte [Criar um aplicativo web ASP.NET no Serviço de Aplicativo do Azure](../web-sites-dotnet-get-started.md).

3. Abra o menu de atalho da solução e escolha **Confirmar**.<br/>
![][7]

4. Se for a primeira vez que usa o Git no Visual Studio Online, você precisará fornecer algumas informações para se identificar no Git. Na área **Alterações Pendentes** do Team Explorer, insira seu nome de usuário e endereço de email. Digite um comentário para a confirmação e selecione **Confirmar**.<br/>
![][8]

5. Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se as alterações desejadas tiverem sido excluídas, escolha **Incluir Tudo**.<br/>

6. Você confirmou as alterações em sua cópia local do repositório. Em seguida, sincronize as alterações com o servidor. Selecione o link **Sincronizar**.

## <a name="step3"></a>Etapa 3: Conectar o projeto ao Azure.

1. Agora que possui um repositório Git no Visual Studio Online com algum código-fonte nele, você está pronto para conectar seu repositório Git ao Azure. No [Portal do Azure](http://manage.windowsazure.com), selecione o serviço de nuvem ou aplicativo Web, ou crie um novo selecionando o ícone + na parte inferior esquerda e escolhendo **Serviço de Nuvem** ou **Aplicativo Web** e, em seguida, **Criação Rápida**.<br.>
![][9]

3. Para serviços de nuvem, escolha o link **Configurar publicação com o Visual Studio Online**. Para aplicativos Web, escolha o link **Configurar a implantação do controle do código-fonte**.<br/>
![][10]

2. No assistente, digite o nome de sua conta do Visual Studio Online na caixa de texto e clique no link **Autorizar Agora**. Você pode ser solicitado a entrar.<br/>
![][11]

3. Na caixa de diálogo pop-up OAuth, escolha **Aceitar** para autorizar o Azure a configurar seu projeto da equipe no Visual Studio Online.<br/>
![][12]

4. Quando a autorização for bem-sucedida, você verá uma lista suspensa contendo seus projetos de equipe do Visual Studio Online. Selecione o nome do projeto da equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.<br/>
![][13]

Na próxima vez em que você enviar uma confirmação por push ao repositório, o Visual Studio Online vai compilar e implantar seu projeto no Azure.<br/>


## <a name="step4"></a>Etapa 4: Acionar uma recompilação e reimplantar seu projeto

1. No Visual Studio, abra um arquivo e altere-o. Por exemplo, mude a pasta do arquivo _Layout.cshtml na pasta Views\Shared em uma função web do MVC.<br/>
![][17]

2. Edite o texto do rodapé do site e salve o arquivo.<br/>
![][18]

3. No Gerenciador de Soluções, abra o menu de atalho do nó da solução, nó do projeto ou do arquivo que você alterou e escolha **Confirmar**.<br/>

4. Digite um comentário e escolha **Confirmar**.<br/>
![][20]

5. Selecione o link **Sincronizar**.<br/>
![][38]

6. Escolha o link **Enviar por push** para enviar sua confirmação por push ao repositório no Visual Studio Online. (Você também pode usar o botão **Sincronizar** para copiar suas confirmações no repositório. A diferença é que **Sincronizar** também efetua pull das alterações mais recentes do repositório.<br/>
![][39]

7. Escolha o botão Início para retornar à home page do Team Explorer.<br/>
![][21]

8. Escolha **Compilações** para exibir as compilações em andamento.<br/>
![][22]
<br/>
O Team Explorer mostra que uma compilação foi disparada para seu check-in.<br/>
![][23]

9. Para exibir um log detalhado enquanto a compilação está em andamento, clique duas vezes no nome da compilação em andamento.<br/>

10. Enquanto a compilação estiver em andamento, examine a definição de compilação que foi criada quando você usou o assistente para vincular ao Azure. Abra o menu de atalho da definição de compilação e escolha **Editar Definição de Compilação**.<br/>
![][25]
<br/>
Na guia **Gatilho**, você verá que a definição de compilação está definida, por padrão, para compilar em cada check-in. (Para um serviço de nuvem, o Visual Studio Online compila e implanta a ramificação mestre no ambiente de preparo automaticamente. Você ainda precisa executar uma etapa manual para implantar no site ativo. Para aplicativo Web que não tem um ambiente de preparo, ele implanta a ramificação mestra diretamente no site ativo.<br/>
![][26]
<br/>
Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou aplicativo Web.<br/>
![][27]
<br/>
Especifique valores para as propriedades se você desejar valores diferentes dos padrões. As propriedades de publicação no Azure estão na seção Implantação, e talvez você também precise definir parâmetros MSBuild. Por exemplo, em um projeto de serviço de nuvem, para especificar uma configuração de serviço diferente de "Cloud", defina os parâmetros MSbuild como /p:TargetProfile=*SeuPerfil*, em que *SeuPerfil* corresponde ao arquivo de configuração de serviço com um nome como ServiceConfiguration.SeuPerfil.cscfg. A tabela a seguir mostra as propriedades disponíveis na seção de Implantação:
	<table>
<tr><td><b>Propriedade</b></td><td><b>Valor padrão</b></td></tr>
><tr><td>Permitir Certificados não Confiáveis</td><td>Se falso, os certificados SSL deve ser assinados por uma autoridade raiz.</td></tr>
<tr><td>Permitir Atualização</td><td>Permite que a implantação atualize uma implantação existente em vez de criar uma nova. Preserve o endereço IP.</td></tr><tr><td>Não exclua</td><td>Se verdadeiro, não substitua uma implantação não relacionada existente (a atualização é permitida).</td></tr>
<tr><td>Caminho para Configurações de Implantação</td><td>O caminho para seu arquivo .pubxml para um aplicativo Web, relacionado à pasta raiz do repositório. Ignorado para serviços de nuvem.</td></tr>
<tr><td>Ambiente de implantação do Sharepoint</td><td>O mesmo que o nome do serviço</td></tr>
<tr><td>Ambiente de implantação do Azure</td><td>O nome do aplicativo Web ou do serviço de nuvem</td></tr>
</table> <br/>

11. A essa altura, sua compilação deve estar concluída com êxito.<br/>
![][28]

12. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste associado aos projetos de teste de unidade.<br/>
![][29]

13. No [Portal do Azure](http://manage.windowsazure.com), você poderá exibir a implantação associada na guia Implantações quando o ambiente de preparo estiver selecionado.<br/>
![][30]

14.	Navegue até a URL do site. Para um aplicativo Web, basta clicar no botão **Procurar** no portal. Para um serviço de nuvem, escolha o URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade Ambiente de Serviço de Nuvem Alternativo para a produção. É aqui que a URL do site está na página do painel do serviço de nuvem: <br/>
![][31]
<br/>
uma nova guia do navegador será aberta para revelar seu site em execução.<br/>
![][32]

15.	Se fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. O mais recente é marcado como Ativo.<br/>
![][33]

## <a name="step5"></a>Etapa 5: Reimplantar uma compilação anterior

Esta etapa é opcional. No Portal de Gerenciamento, selecione uma implantação anterior e clique em **Reimplantar** para retroceder seu site para um check-in anterior. Observe que isso vai disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.<br/>
![][34]

## <a name="step6"></a>Etapa 6: Alterar a implantação de produção

 Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo **Trocar** no Portal de Gerenciamento. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.<br/>
![][35]

## <a name="step7"></a>Etapa 6: implantar por meio de uma ramificação em andamento.

Quando usa o Git, normalmente você faz alterações em uma ramificação em andamento e a integra à ramificação mestre quando seu desenvolvimento estiver concluído. Durante a fase de desenvolvimento de um projeto, você compilará e implantará a ramificação em andamento no Azure.

1. No Team Explorer, escolha o botão **Início** e escolha o botão **Ramificações**.<br/>
![][40]

2. Escolha o link **Nova Ramificação**.<br/>
![][41]

3. Insira o nome da ramificação, como “em andamento” e escolha **Criar Ramificação**. Isso cria uma nova ramificação local.<br/>
![][42]

4. Publique a ramificação. Escolha o nome da ramificação em **Ramificações não publicadas** e selecione **Publicar**.<br/>
![][44]

6. Por padrão, somente alterações na ramificação mestre disparam uma compilação contínua. Para definir a compilação contínua para uma ramificação em andamento, feche a página Compilações no Team Explorer e escolha **Editar Definição de Compilação**.

7. Abra a guia **Configurações da Origem**. Em **Ramificações monitoradas para integração e compilação contínua**, selecione **Clique aqui para adicionar uma nova linha**.<br/>
![][47]

8. Especifique a ramificação que você criou, como refs/heads/working.
![][48]

9. Faça uma alteração no código, abra o menu de atalho do arquivo alterado e escolha **Confirmar**.<br/>
![][43]

10. Escolha o link **Confirmações Não Sincronizadas** e clique no botão **Sincronizar** ou no link **Enviar por push** para copiar as alterações para a cópia da ramificação em andamento no Visual Studio Online.
![][45]

11. Navegue até a exibição **Compilações** e encontre a compilação que foi acionada para a ramificação em andamento.

Para saber mais, consulte [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Para obter mais dicas de como usar o Git com Visual Studio Online, consulte [Compartilhar seu código no Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx), e para obter informações de como usar um repositório Git que não é gerenciado pelo Visual Studio Online para publicar no Azure, consulte [Publicando do controle do código-fonte em sites do Azure](../web-sites-publish-source-control.md).

[Etapa 1: Criar um repositório Git.]: #step1
[Etapa 2: criar um projeto e enviá-lo por push ao repositório Git.]: #step2
[Etapa 3: Conectar o projeto ao Azure.]: #step3
[Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]: #step4
[Etapa 5: Reimplantar uma compilação anterior (opcional)]: #step5
[Etapa 6: Alterar a implantação de produção]: #step6
[Etapa 7: implantar por meio de uma ramificação em andamento]: #step7
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
 

<!----HONumber=62-->