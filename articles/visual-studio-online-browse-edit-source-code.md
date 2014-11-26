<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Browse and Edit Your Source Code | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, edit, code, commit" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# Procurar e editar o código-fonte

Na Visualização do Portal do Microsoft Azure, é fácil procurar e editar o código-fonte do Projeto da Equipe do Visual Studio Online.

Depois de criar um Projeto da Equipe no Portal e fazer check-in (controle de versão do Team Foundation) ou confirmar um código (git), você poderá acessar e procurar facilmente o código pelo portal.

1.  Primeiro, abra a folha do projeto da equipe e localize o código lens. Em seguida, na parte de origem, clique no nome do repositório que você gostaria de procurar (neste caso, ele se chama 'BrowseCode'). Observação: se tiver mais de dois repositórios, você poderá clicar no número do repositório (1 neste caso) para exibir a lista completa.
    ![Code Lens][Code Lens]
2.  Agora que a faixa do repositório está aberta, você pode clicar na parte Código para iniciar a navegação no código-fonte.
    ![Repository Blade][Repository Blade]
3.  Uma faixa de navegação do modo de exibição de árvore padrão será exibida com todo o projeto disponível. É possível clicar nos nós para abrir pasta ou clicar em um arquivo específico (Index.cshtml neste caso) para exibir ou editar o arquivo.
    ![Tree Navigation][Tree Navigation]
4.  Para editar um arquivo, basta clicar no botão Editar da barra de comandos. O conteúdo de arquivo entrará no modo de edição, e é possível fazer as alterações. Dica: Clique no botão de maximizar no canto superior direito para ver a edição em tela inteira.
    ![Edit Mode][Edit Mode]
5.  Quando você terminar as edições de arquivo, clique no botão de confirmação (ou de check-in) na barra de comandos. Isso iniciará uma nova faixa onde é possível inserir a mensagem de confirmação. Clique em OK, quando terminar, e o arquivo será confirmado no repositório.
    ![Commit Code][Commit Code]
6.  A parte superior da faixa mostrará uma mensagem piscante indicando o êxito da confirmação do repositório.
    ![Commit Success][Commit Success]

  [Code Lens]: ./media/visual-studio-online-browse-edit-source-code/Code-Lens.png
  [Repository Blade]: ./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png
  [Tree Navigation]: ./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png
  [Edit Mode]: ./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png
  [Commit Code]: ./media/visual-studio-online-browse-edit-source-code/Commit-Code.png
  [Commit Success]: ./media/visual-studio-online-browse-edit-source-code/Commit-Success.png
