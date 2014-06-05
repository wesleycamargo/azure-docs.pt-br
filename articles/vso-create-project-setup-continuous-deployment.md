<properties linkid="create-vso-project-setup-continuous-deployment" urlDisplayName="Como criar um projeto VSO e configurar implantação contínua" pageTitle="Como criar um projeto de equipe do Visual Studio Online e configurar implantação contínua - Windows Azure" metaKeywords="O Visual Studio Online cria o projeto de equipe, implantação contínua no Azure" description="Saiba como criar um projeto de equipe do Visual Studio Online e configurá-lo para implantação contínua no Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="Como criar e implantar um serviço de nuvem" authors="jimlamb" solutions="" writer="jimlamb" manager="" editor=""  />

#Criar um projeto do Visual Studio Online e configurar implantação contínua no Windows Azure 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

O Portal de Gerenciamento do Windows Azure permite criar um Projeto de Equipe no Visual Studio Online e configurar seu aplicativo web para implantação contínua em um site.

##Sumário##

* [Como criar um projeto de equipe](#create_team_project)
* [Como criar um novo aplicativo web e adicioná-lo ao controle de versão Git](#create_web_app)
* [Como configurar a implantação contínua](#continuous_deployment)

## <a name="create_team_project"></a>Como criar um projeto de equipe

1. Entre no Portal de Gerenciamento.
2. Clique em **novo** no canto inferior esquerdo.
3. Clique em **Projeto de Equipe**.
4. Dê um nome a seu projeto de equipe. Observe que você não pode alterar o nome de seu projeto de equipe depois de ele ter sido criado.
5. Escolha o tipo de controle de versão que você deseja usar para o seu projeto. Você pode escolher Git (um sistema controle de versão distribuído) ou o Controle de Versão do Team Foundation (um sistema controle de versão centralizado). Não tem certeza sobre qual sistema usar? Saiba mais [aqui](http://msdn.microsoft.com/pt-br/library/ms181368.aspx).
6. Escolha o modelo do processo. Para obter uma comparação dos modelos de processo, consulte [Trabalhar com artefatos de projetos de equipe (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/ms400752.aspx).
7. Escolha a conta do Visual Studio Online a ser usada para criar o projeto de equipe, adicionar os usuários e monitorar o uso de recursos.
8. Deixe a caixa de seleção **Adicionar à Startboard** marcada para que seu novo projeto de equipe seja exibido automaticamente em sua Startboard.
9. Clique em **Criar**.

## <a name="create_web_app"></a>Como criar um novo aplicativo web e adicioná-lo ao controle de versão Git

1. Na Startboard, clique em seu novo projeto de equipe.
2. Na lente **Código**, na parte **Repositórios**, clique no repositório Git que tem o mesmo nome que seu projeto de equipe.
3. Na lâmina repositório/ramificação, clique no comando em nível de lâmina do **Visual Studio** para abrir o novo repositório no Visual Studio. Seu navegador da web pode solicitar que você autorize a inicialização do Visual Studio.
4.  Na janela da ferramenta Team Explorer do Visual Studio, clique em **Faça a clonagem deste repositório** para configurar um clone local de seu novo repositório em seu disco local.
5.  Na seção **Soluções** da página de início do Team Explorer, clique em **Novo...** para criar um novo projeto no repositório que acabou de ser clonado.
6.  Na caixa de diálogo Novo Projeto, expanda o nó Visual Basic ou Visual C#, dependendo da sua preferência de linguagem de programação e selecione **Web**.
7.  Clique em **Aplicativo Web ASP.NET** na lista de modelos de projeto disponíveis e digite um nome para seu aplicativo web.
8.  Clique em **OK**.
9.  Alterne para a janela da ferramenta Team Explorer, navegue até a página Alterações e digite uma mensagem de confirmação.
10.  Clique na seta suspensa no botão **Confirmar** e clique em **Confirmar e Sincronizar** para confirmar as alterações e enviar essa confirmação por push ao repositório remoto clonado anteriormente.

## <a name="continuous_deployment"></a>Como configurar a implantação contínua

1. Entre no Portal de Gerenciamento.
2. Na Startboard, clique no projeto de equipe criado anteriormente.
3. Na lente **Compilar**, clique na parte **Configurar implantação contínua**.
4. Selecione o site no qual você deseja implantar o aplicativo web.
5. Selecione o repositório onde seu código-fonte reside (você deve ter apenas um repositório em seu projeto de equipe neste momento).
6. Selecione a ramificação a ser compilada. O Visual Studio Online verificará o conteúdo da confirmação mais recente nessa ramificação para obter um único arquivo de solução do Visual Studio (*.sln). Se encontrar um, ele irá configurar uma nova definição de compilação (com um nome terminando em "_CD") para compilar essa solução. Se não encontrar um arquivo de solução ou se encontrar mais de um, ele ainda irá configurar uma nova definição de compilação, mas irá desabilitá-la, e você precisará editá-la no Visual Studio para especificar a solução a ser compilada. 
7. Clique em **Criar**.
8. O Visual Studio Online criará uma nova definição de compilação para criar e implantar seu projeto de aplicativo web e tentará enfileirar uma nova compilação dessa definição.

###Para verificar se sua implantação foi concluída com êxito###

1. Na Startboard, clique no projeto de equipe criado anteriormente.
2. Na lente **Compilar**, clique na parte **Compilação mais recente** para abrir a lâmina de compilação.
3. Na lâmina de compilação, clique no primeiro item na parte **Implantações** para abrir o site associado.
4. Na lâmina do site, clique no comando em nível de lâmina **Procurar** para procurar o site e verificar a implantação do seu aplicativo web.

