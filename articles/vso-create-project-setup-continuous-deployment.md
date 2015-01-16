<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="Como criar um projeto da equipe do Visual Studio Online e configurar a implantação contínua - Microsoft Azure" metaKeywords="Visual Studio Online, criar projeto da equipe, implantação contínua no Azure" description="Saiba como criar um projeto de equipe do Visual Studio Online e configurá-lo para implantação contínua no Microsoft Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#Criar um projeto do Visual Studio Online e configurar implantação contínua no Windows Azure 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

O Portal de Gerenciamento do Windows Azure permite criar um Projeto de Equipe no Visual Studio Online e configurar seu aplicativo web para implantação contínua em um site.

##Sumário##

* [Como criar um projeto de equipe](#create_team_project)
* [Como criar um novo aplicativo web e adicioná-lo ao controle de versão Git](#create_web_app)
* [Como configurar a implantação contínua](#continuous_deployment)

## <a name="create_team_project"></a>Como criar um projeto de equipe

1. Entre no Portal de gerenciamento.
2. Clique em **Novo** no canto inferior esquerdo.
3. Clique em **Projeto da Equipe**
4. Dê um nome ao seu projeto da equipe. Observe que não é possível alterar o nome de seu projeto da equipe depois de ele ter sido criado.
5. Escolha o tipo de controle de versão que você deseja utilizar para o seu projeto. Você pode escolher Git (um sistema controle de versão distribuído) ou o Controle de Versão do Team Foundation (um sistema controle de versão centralizado). Não tem certeza sobre qual sistema usar? Saiba mais [aqui](http://msdn.microsoft.com/pt-br/library/ms181368.aspx).
6. Escolha o modelo de processo. Para obter uma comparação dos modelos de processo, consulte [Trabalhar com artefatos do projeto da equipe](http://msdn.microsoft.com/pt-br/library/ms400752.aspx).
7. Escolha a conta do Visual Studio Online a ser utilizada para criar o projeto da equipe, adicione os usuários e monitore o uso de recursos.
8. Deixe a caixa de seleção **Adicionar ao quadro inicial** marcada para que seu novo projeto da equipe seja exibido automaticamente em sua Startboard.
9. Clique em **Criar**.

## <a name="create_web_app"></a>Como criar um novo aplicativo web e adicioná-lo ao controle de versão Git

1. No quadro inicial, clique no novo projeto da equipe.
2. Na lente **código**, na parte **Repositórios**, clique no repositório Git que tem o mesmo nome que seu projeto da equipe.
3. Na lâmina repositório/ramificação, clique no comando em nível de lâmina do **Visual Studio** para abrir o novo repositório no Visual Studio. Seu navegador da Web pode solicitar que você autorize a inicialização do Visual Studio.
4. Na janela de ferramentas Team Explorer do Visual Studio, clique em **Faça a clonagem deste repositório** para configurar um clone local de seu novo repositório no disco local.
5. Na seção **Soluções** da Home page do Team Explorer, clique em **Novo...** para criar um novo projeto no repositório que acabou de ser clonado.
6. Na caixa de diálogo Novo Projeto, expanda o nó do Visual Basic ou do Visual C#, dependendo da sua preferência de linguagem de programação e, em seguida, selecione **Web**.
7. Clique em **Aplicativo Web ASP.NET** na lista de modelos de projeto disponíveis e digite um nome para seu aplicativo Web.
8. Clique em **OK**.
9. Alterne para a janela de ferramentas Team Explorer, navegue até a página Alterações e digite uma mensagem de confirmação.
10. Clique na seta suspensa no botão **Confirmar** e clique em **Confirmar e Sincronizar** para confirmar as alterações e enviar essa confirmação por push ao repositório remoto clonado anteriormente.

## <a name="continuous_deployment"></a>Como configurar a implantação contínua

1. Entre no Portal de gerenciamento.
2. No quadro inicial, clique no projeto da equipe criado anteriormente.
3. Na lente **Compilar**, clique na parte **Configurar implantação contínua**.
4. Selecione o site no qual você deseja implantar o aplicativo Web.
5. Selecione o repositório onde seu código-fonte reside (você deve ter apenas um repositório em seu projeto da equipe neste momento).
6. Selecione a ramificação a ser compilada. O Visual Studio Online verificará o conteúdo da confirmação mais recente nessa ramificação para obter um único arquivo de solução do Visual Studio (*.sln). Se encontrar um, ele irá configurar uma nova definição de compilação (com um nome terminando em "_CD") para compilar essa solução. Se não encontrar um arquivo de solução ou se encontrar mais de um, ele ainda irá configurar uma nova definição de compilação, mas irá desabilitá-la, e você precisará editá-la no Visual Studio para especificar a solução a ser compilada. 
7. Clique em **Criar**.
8. O Visual Studio Online criará uma nova definição de compilação para criar e implantar seu projeto de aplicativo Web e tentará colocar na fila uma nova compilação dessa definição.

###Para verificar se sua implantação foi concluída com êxito###

1. No quadro inicial, clique no projeto da equipe criado anteriormente.
2. Na lente **Compilar**, clique na parte **Compilação mais recente** para abrir a lâmina de compilação.
3. Na lâmina de compilação, clique no primeiro item na parte **Implantações** para abrir o site associado.
4. Na lâmina do site, clique no comando em nível de lâmina **Procurar** para procurar o site e verificar a implantação do seu aplicativo Web.
