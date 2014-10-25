<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with TFS" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online and Git" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Entrega contínua no Azure usando o Visual Studio Online e o Git

Você pode usar os projetos de equipe do Visual Studio Online para hospedar um repositório Git para seu código-fonte e compilar e implantá-lo automaticamente em sites ou serviços de nuvem do Azure sempre que enviar por push uma confirmação ao repositório.

Você precisará do Visual Studio 2013 e do SDK do Azure instalados. Se você ainda não tiver o Visual Studio 2013, baixe-o selecionando o link **Introdução gratuita (a página pode estar em inglês)** em [www.visualstudio.com][www.visualstudio.com]. Instale o SDK do Azure [aqui][aqui].

Para configurar um serviço de nuvem para compilação e implantação automática no Azure usando o Visual Studio Online, siga estas etapas:

-   [Etapa 1: Inscrever-se no Visual Studio Online e criar um repositório Git.][Etapa 1: Inscrever-se no Visual Studio Online e criar um repositório Git.]

-   [Etapa 2: Criar um projeto e enviá-lo por push ao repositório Git.][Etapa 2: Criar um projeto e enviá-lo por push ao repositório Git.]

-   [Etapa 3: Conectar o projeto ao Azure.][Etapa 3: Conectar o projeto ao Azure.]

-   [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.][Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]

-   [Etapa 5: Reimplantar uma compilação anterior (opcional)][Etapa 5: Reimplantar uma compilação anterior (opcional)]

-   [Etapa 6: Alterar a implantação em produção][Etapa 6: Alterar a implantação em produção]

-   [Etapa 7: Implantar por meio de uma ramificação em andamento][Etapa 7: Implantar por meio de uma ramificação em andamento]

## <a name="step1"></a><span class="short-header">Etapa 1: Inscrever-se no Visual Studio Online e criar um repositório Git.</span>Etapa 1: Inscrever-se no Visual Studio Online e criar um repositório Git

1.  Se ainda não tiver uma conta do Visual Studio Online, siga as instruções disponíveis [aqui][1]. Quando criar seu projeto da equipe, escolha o Git como seu sistema de controle do código-fonte. Siga as instruções para conectar o Visual Studio ao projeto da equipe.

2.  No Team Explorer, escolha o link **Clonar este repositório**.
    ![][]

3.  Especifique o local da cópia local e clique no botão **Clonar**.

## <a name="step2"> </a><span class="short-header">Criar um projeto e confirmá-lo no repositório.</span>Etapa 2: Criar um projeto e confirmá-lo no repositório

1.  No Team Explorer, na seção Soluções, selecione o link Novo para criar um novo projeto no repositório local.<br />
    ![][2]

2.  Você pode implantar um site ou um serviço de nuvem (aplicativo do Azure) seguindo as etapas neste passo a passo.
    Crie um novo projeto do serviço em nuvem do Microsoft Azure
    ou um novo projeto ASP.NET MCV. Verifique se o projeto é direcionado para o .NET Framework 4 ou 4.5 e, se você estiver criando um projeto de serviço de nuvem, adicione uma função web e uma função de trabalho ASP.NET MVC e escolha o aplicativo da Internet para a função web.
    Se quiser criar um site, escolha o modelo de projeto Aplicativo Web ASP.NET e selecione MVC. Consulte [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET].

3.  Abra o menu de atalho da solução e escolha **Confirmar**.<br />
    ![][3]

4.  Se for a primeira vez que usa o Git no Visual Studio Online, você precisará fornecer algumas informações para se identificar no Git. Na área **Alterações Pendentes** do Team Explorer, insira seu nome de usuário e endereço de email. Digite um comentário para a confirmação e selecione **Confirmar**.<br />
    ![][4]

5.  Observe as opções para incluir ou excluir alterações específicas ao fazer check-in. Se desejar que as alterações sejam excluídas, escolha **Incluir Tudo**.

6.  Você confirmou as alterações em sua cópia local do repositório. Em seguida, sincronize as alterações com o servidor. Selecione o link **Sincronizar**.

## <a name="step3"> </a><span class="short-header">Conectar o projeto ao Azure.</span>Etapa 3: Conectar o projeto ao Azure

1.  Agora que possui um repositório Git no Visual Studio Online com algum código-fonte nele, você está pronto para conectar seu repositório Git ao Azure. No [Portal do Azure][Portal do Azure], selecione o serviço de nuvem ou site ou crie um novo selecionando o ícone + na parte inferior esquerda e escolhendo **Serviço de Nuvem** ou **Site** e, em seguida, **Criação Rápida**.<br />
    ![][5].

2.  Para serviços de nuvem, escolha o link **Configurar publicação com o Visual Studio Online**. Para sites, escolha o link **Configurar a implantação do controle do código-fonte**.<br />
    ![][6]

3.  No assistente, digite o nome de sua conta do Visual Studio Online na caixa de texto e clique no link **Autorizar Agora**. Você pode ser solicitado a entrar.<br />
    ![][7]

4.  Na caixa de diálogo pop-up OAuth, escolha **Aceitar** para autorizar o Azure a configurar seu projeto da equipe no Visual Studio Online.<br />
    ![][8]

5.  Quando a autorização for bem-sucedida, você verá uma lista suspensa contendo seus projetos de equipe do Visual Studio Online. Selecione o nome do projeto de equipe que você criou nas etapas anteriores e escolha o botão de marca de seleção do assistente.<br />
    ![][9]

Na próxima vez em que você enviar uma confirmação por push ao repositório, o Visual Studio Online vai compilar e implantar seu projeto no Azure.

## <a name="step4"> </a><span class="short-header">Disparar uma recompilação</span>Etapa 4: Disparar uma recompilação e reimplantar seu projeto

1.  No Visual Studio, abra um arquivo e altere-o. Por exemplo, altere o arquivo \_Layout.cshtml na pasta Views\\Shared em uma função web do MVC.<br />
    ![][10]

2.  Edite o texto do rodapé do site e salve o arquivo.<br />
    ![][11]

3.  No Gerenciador de Soluções, abra o menu de atalho do nó da solução, nó do projeto ou do arquivo que você alterou e escolha **Confirmar**.

4.  Digite um comentário e escolha **Confirmar**.<br />
    ![][4]

5.  Selecione o link **Sincronizar**.<br />
    ![][12]

6.  Escolha o link **Enviar por push** para enviar sua confirmação por push ao repositório no Visual Studio Online. (Você também pode usar o botão **Sincronizar** para copiar suas confirmações no repositório. A diferença é que o comando **Sincronizar** também efetua pull das alterações mais recentes do repositório.)<br />
    ![][13]

7.  Escolha o botão Início para retornar à página inicial do Team Explorer.<br />
    ![][14]

8.  Escolha **Compilações** para ver as compilações em andamento.<br />
    ![][15]

    O Team Explorer mostra que uma compilação foi disparada para seu check-in.<br />
    ![][16]

9.  Para exibir um log detalhado enquanto a compilação está em andamento, clique duas vezes no nome da compilação em andamento.

10. Enquanto a compilação estiver em andamento, examine a definição de compilação que foi criada quando você usou o assistente para vincular ao Azure. Abra o menu de atalho da definição de compilação e escolha **Editar Definição de Compilação**.<br />
    ![][17]

    Na guia **Gatilho**, você verá que a definição da compilação está definida, por padrão, para compilar em cada check-in. (Para um serviço de nuvem, o Visual Studio Online compila e implanta a ramificação mestre no ambiente de preparo automaticamente. Você ainda precisa executar uma etapa manual para implantar no site ativo. Para um site que não tem um ambiente de preparo, ele implanta a ramificação mestre diretamente no site ativo.<br />
    ![][18]

    Na guia **Processo**, você pode ver que o ambiente de implantação está definido como o nome do seu serviço de nuvem ou site.<br />
    ![][19]

    Especifique valores para as propriedades se você desejar valores diferentes dos padrões. As propriedades de publicação no Azure estão na seção Implantação, e talvez você também precise definir parâmetros MSBuild. Por exemplo, em um projeto de serviço de nuvem, para especificar uma configuração de serviço diferente de "Cloud", defina os parâmetros MSbuild como /p:TargetProfile=*SeuPerfil*, em que *SeuPerfil* corresponde ao arquivo de configuração de serviço com um nome como ConfiguraçãoDeServiço.*SeuPerfil*.cscfg.
    A tabela a seguir mostra as propriedades disponíveis na seção de Implantação:

    <table>
    <tr><td>
    <strong>Propriedade</strong>
    </td><td>
    <strong>Valor padrão</strong>
    </td></tr>
    <tr><td>
    Permitir Certificados não Confiáveis
    </td><td>
    Se for falso, os certificados SSL precisarão ser assinados por uma autoridade raiz.
    </td></tr>
    <tr><td>
    Permitir Atualização
    </td><td>
    Permite que a implantação atualize uma implantação existente em vez de criar uma nova. Preserva o endereço IP.
    </td></tr>
    <tr><td>
    Não Excluir
    </td><td>
    Se for verdadeiro, não substitui uma implantação existente não relacionada (atualizações são permitidas).
    </td></tr>
    <tr><td>
    Caminho para Configurações de Implantação
    </td><td>
    O caminho para seu arquivo .pubxml se um site, relacionado à pasta raiz do repo. Ignorado para serviços de nuvem.
    </td></tr>
    <tr><td>
    Ambiente de Implantação do SharePoint
    </td><td>
    O mesmo que o nome do serviço
    </td></tr>
    <tr><td>
    Ambiente de Implantação do Microsoft Azure
    </td><td>
    O nome do site ou serviço de nuvem
    </td></tr>
    </table>

11. A essa altura, sua compilação deve estar concluída com êxito.<br />
    ![][20]

12. Se você clicar duas vezes no nome da compilação, o Visual Studio mostrará um **Resumo da Compilação**, incluindo qualquer resultado de teste associado aos projetos de teste de unidade.<br />
    ![][21]

13. No [Portal do Azure][Portal do Azure], você poderá exibir a implantação associada na guia Implantações quando o ambiente de preparo estiver selecionado.<br />
    ![][22]

14. Navegue até a URL do site. Para um site, basta clicar no botão **Procurar** no portal. Para um serviço de nuvem, escolha o URL na seção **Visão Rápida** da página **Painel** que mostra o ambiente de preparo. Por padrão, as implantações de integração contínua para serviços de nuvem são publicadas no ambiente de preparo. Você pode alterar isso definindo a propriedade Ambiente de Serviço de Nuvem Alternativo para a produção. Esta é a localização do URL do site está na página do painel do serviço de nuvem:<br />
    ![][23]

    Uma nova guia do navegador será aberta para revelar seu site em execução.<br />
    ![][24]

15. Se fizer outras alterações em seu projeto, você disparará mais compilações e acumulará várias implantações. O mais recente é marcado como Ativo.<br />
    ![][25]

## <a name="step5"> </a><span class="short-header">Reimplantar uma compilação anterior</span>Etapa 5: Reimplantar uma compilação anterior

Esta etapa é opcional. No portal de gerenciamento, selecione uma implantação anterior e clique em **Reimplantar** para retornar seu site a um check-in anterior. Observe que isso vai disparar uma nova compilação no TFS e criará uma nova entrada em seu histórico de implantação.<br />
![][26]

## <a name="step6"> </a><span class="short-header">Alterar a implantação em produção</span>Etapa 6: Alterar a implantação em produção

Quando estiver pronto, você pode promover o ambiente de preparo para o ambiente de produção escolhendo **Permutar** no Portal de Gerenciamento. O ambiente de preparo recém-implantado é promovido para a produção e o ambiente de produção anterior, se houver, torna-se um ambiente de preparo. A implantação Ativa pode ser diferente dos ambientes de preparo e de produção, mas o histórico de implantação de compilações recentes é o mesmo, independentemente do ambiente.<br />
![][27]

## <a name="step7"> </a><span class="short-header">Implantar por meio de uma ramificação em andamento</span>Etapa 6: Implantar por meio de uma ramificação em andamento.

Quando usa o Git, normalmente você faz alterações em uma ramificação em andamento e a integra à ramificação mestre quando seu desenvolvimento estiver concluído. Durante a fase de desenvolvimento de um projeto, você compilará e implantará a ramificação em andamento no Azure.

1.  No Team Explorer, escolha o botão **Início** e escolha o botão **Ramificações**.<br />
    ![][28]

2.  Escolha o link **Nova Ramificação**.<br />
    ![][29]

3.  Insira o nome da ramificação, como “em andamento” e escolha **Criar Ramificação**. Isso cria uma nova ramificação local.<br />
    ![][30]

4.  Publique a ramificação. Escolha o nome da ramificação em **Ramificações não publicadas** e selecione **Publicar**.<br />
    ![][31]

5.  Por padrão, somente alterações na ramificação mestre disparam uma compilação contínua. Para definir a compilação contínua para uma ramificação em andamento, feche a página Compilações no Team Explorer e escolha **Editar Definição de Compilação**.

6.  Abra a guia **Configurações da Origem**. Em **Ramificações monitoradas para integração e compilação contínua**, selecione **Clique aqui para adicionar uma nova linha**.<br />
    ![][32]

7.  Especifique a ramificação que você criou, como refs/heads/working.<br />
    ![][33]

8.  Faça uma alteração no código, abra o menu de atalho do arquivo alterado e escolha **Confirmar**.<br />
    ![][4]

9.  Escolha o link **Confirmações não sincronizadas** e clique no botão **Sincronizar** ou no link **Enviar por push** para copiar as alterações para a cópia da ramificação em andamento no Visual Studio Online.<br />
    ![][12]

10. Navegue até a exibição **Compilações** e encontre a compilação que foi acionada para a ramificação em andamento.

Para obter mais informações, consulte [Visual Studio Online (a página pode estar em inglês)][Visual Studio Online (a página pode estar em inglês)]. Para obter mais dicas de como usar o Git com Visual Studio Online, consulte [Compartilhar seu código no Git][Compartilhar seu código no Git], e para obter informações de como usar um repositório Git que não é gerenciado pelo Visual Studio Online para publicar no Azure, consulte [Publicando do controle do código-fonte em sites do Azure][Publicando do controle do código-fonte em sites do Azure].

  [www.visualstudio.com]: http://www.visualstudio.com
  [aqui]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Etapa 1: Inscrever-se no Visual Studio Online e criar um repositório Git.]: #step1
  [Etapa 2: Criar um projeto e enviá-lo por push ao repositório Git.]: #step2
  [Etapa 3: Conectar o projeto ao Azure.]: #step3
  [Etapa 4: Fazer alterações e disparar uma recompilação e uma reimplantação.]: #step4
  [Etapa 5: Reimplantar uma compilação anterior (opcional)]: #step5
  [Etapa 6: Alterar a implantação em produção]: #step6
  [Etapa 7: Implantar por meio de uma ramificação em andamento]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=397665
  []: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Introdução ao Azure e ao ASP.NET]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-dotnet-get-started/
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [Portal do Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [16]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Compartilhar seu código no Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publicando do controle do código-fonte em sites do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-publish-source-control
