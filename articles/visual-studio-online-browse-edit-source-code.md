<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Procurar e editar seu código-fonte | Azure"metaKeywords="Visual Studio Online, VSO, git, tfvc, editar, código, confirmação" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="kamrani" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# Procurar e editar o código-fonte

Na Visualização do Portal do Microsoft Azure, é fácil procurar e editar o código-fonte do Projeto da Equipe do Visual Studio Online.


Depois de criar um Projeto da Equipe no Portal e fazer check-in (controle de versão do Team Foundation) ou confirmar um código (git), você poderá acessar e procurar facilmente o código pelo portal.


1. Primeiro, abra a folha do projeto da equipe e, em seguida, localize o código lens.  Em seguida, na parte de origem, clique no nome do repositório que você gostaria de procurar (neste caso, ele se chama 'BrowseCode').  Observação: se tiver mais de dois repositórios, será possível clicar no número do repositório (1 neste caso) para exibir a lista completa.
![Code Lens](./media/visual-studio-online-browse-edit-source-code/Code-Lens.png)
2. Agora que a folha do repositório está aberta, é possível clicar na parte Código para iniciar a navegação no código-fonte.
![Repository Blade](./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png)
3. Uma folha de navegação do modo de exibição de árvore padrão será exibida com todo o projeto disponível.  É possível clicar nos nós para abrir a pasta ou clicar em um arquivo específico (Index.cshtml neste caso) para exibir ou editar o arquivo.
![Tree Navigation](./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png)
4. Para editar um arquivo, basta clicar no botão Editar da barra de comandos.  O conteúdo de arquivo entrará no modo de edição, e é possível fazer as alterações. Dica: Clique no botão de maximizar no canto superior direito para ter uma experiência de edição em tela inteira.
![Edit Mode](./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png)
5. Ao terminar as edições de arquivo, clique no botão de confirmação (ou de check-in) na barra de comandos.  Isso iniciará uma nova faixa onde é possível inserir a mensagem de confirmação.  Clique em OK ao terminar e o arquivo será confirmado no repositório.
![Commit Code](./media/visual-studio-online-browse-edit-source-code/Commit-Code.png)
6. A parte superior da folha mostrará uma mensagem piscante indicando o êxito da confirmação no repositório.
![Commit Success](./media/visual-studio-online-browse-edit-source-code/Commit-Success.png)







