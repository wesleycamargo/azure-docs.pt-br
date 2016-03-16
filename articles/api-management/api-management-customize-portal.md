<properties
	pageTitle="Personalizar o portal do desenvolvedor no Gerenciamento de API do Azure | Microsoft Azure"
	description="Saiba como personalizar o portal do desenvolvedor no Gerenciamento de API do Azure."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/07/2015"
	ms.author="sdanie"/>

# Personalizar o portal do desenvolvedor no Gerenciamento de API do Azure

Este guia mostra como modificar a aparência do portal do desenvolvedor no Gerenciamento de API do Azure para manter a consistência com a sua marca.

## <a name="change-page-headers"> </a>Alterar o texto ou logotipo no cabeçalho da página

Um dos principais aspectos da personalização do portal é a substituição do texto na parte superior de todas as páginas pelo nome ou logotipo de sua empresa.

O conteúdo no portal do desenvolvedor é modificado por meio do portal do publicador, que é acessado pelo Portal Clássico do Azure. Para acessar o portal do editor de API, clique em **Gerenciar** no Portal Clássico do Azure do serviço de Gerenciamento de API.

![Portal do editor][api-management-management-console]

O portal do desenvolvedor é baseado em um CMS ou sistema de gerenciamento de conteúdo. O cabeçalho que aparece em cada página é um tipo especial de conteúdo conhecido como widget. Para editar o conteúdo do widget, clique em **Widgets** no menu **Portal do Desenvolvedor** do lado esquerdo, depois selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widget][api-management-widgets-header]

O conteúdo do cabeçalho é editável no campo **Corpo**. Altere o texto para "Fabrikam Developer Portal" e clique em **Salvar** na parte inferior da página.

Agora você poderá ver o novo cabeçalho em cada página no portal do desenvolvedor.

> Para abrir o portal do desenvolvedor enquanto estiver no portal do publicador, clique em **Portal do desenvolvedor** na barra superior.

## <a name="change-headers-styling"> </a>Alterar o estilo dos cabeçalhos

As cores, fontes, tamanhos, espaços e outros elementos relacionados ao estilo de qualquer página no portal são definidos pelas regras de status. Para editar os estilos, clique em **Aparência** no menu **Portal do desenvolvedor** no portal do publicador; então, clique em **Começar a personalização** para habilitar o editor de estilo.

Seu navegador alterna para uma página oculta no portal do desenvolvedor que contém exemplos de conteúdo, com exemplos para todas as regras de estilo usadas em qualquer parte do site. Para abrir o editor de estilo, mova o cursor sobre a linha cinza vertical fina na parte extrema esquerda da página. A barra de ferramentas do editor será exibida.

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Existem dois modos principais para editar as regras de estilo - **Editar todas as regras** exibe uma lista de todas as regras de estilo usadas em todo lugar; enquanto **Separar elemento** permite selecionar um elemento na página em que você está e exibe os estilos somente para esse elemento.

Nesta seção gostaríamos de alterar o estilo somente dos cabeçalhos. Clique na opção **Escolher elemento** na barra de ferramentas do editor de estilo e depois clique em **Selecionar um elemento para personalizar**. Os elementos agora ficam destacados à medida que você passa o ponteiro do mouse sobre eles, indicando quais estilos do elemento você começaria a editar se clicasse. Mova o ponteiro do mouse sobre o texto que representa o nome da empresa no cabeçalho ("Fabrikam Developer Portal" se você seguiu as instruções na seção anterior), então clique nele. Um conjunto de regras de estilo categorizadas e nomeadas é exibido no editor de estilos.

Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1, enquanto que o nome da fonte com alternativas está em in @headings-font-family.

> Se estiver familiarizado com [bootstrap][], essas regras são, na verdade, [variáveis LESS][] no tema do bootstrap usadas pelo portal do desenvolvedor.

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada no campo **@headings-color** e digite **#000000**. Esse é o código hexadecimal para a cor preta. Quando você fizer isso, verá que um indicador de cor quadrado aparece no final da caixa de texto. Se você clicar nesse indicador, um seletor de cor permitirá que você escolha uma cor.

![Seletor de cor][api-management-customization-toolbar-color-picker]

Ao finalizar as alterações nos estilos do elemento selecionado, clique em **Visualizar Alterações** para ver os resultados na tela. Desta vez, elas estão visíveis somente para os Administradores. Para tornar essas alterações visíveis para qualquer um, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento utilizado para o cabeçalho. Clique em **Escolher um elemento** do editor de estilos, selecione o elemento em que você está interessado e comece a modificar os valores das regras de estilo exibidos na tela.

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página

O portal do desenvolvedor consiste em páginas geradas automaticamente, como APIs, Produtos, Aplicativos, Questões e conteúdo escrito manualmente. Como ele está baseado em um sistema de gerenciamento de conteúdo, você pode criar esse conteúdo conforme necessário.

Para ver uma lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do desenvolvedor** no portal do publicador.

![Gerenciar conteúdo][api-management-customization-manage-content]

Clique na página **Bem-vindo** para editar o que é exibido na home page do portal do desenvolvedor. Faça as alterações desejadas, visualize-as se necessário e depois clique em **Publicar Agora** para torná-las visíveis para todos.

> A home page usa um layout especial que permite exibir uma faixa na parte superior. Essa faixa não é editável na seção **Conteúdo**. Para editá-la, clique em **Widgets** no menu **Portal do desenvolvedor**, depois selecione **Home page** na lista suspensa **Camada Atual** e, em seguida, abra o item **Faixa** na **seção Em destaque**. O conteúdo desse widget pode ser editado como o conteúdo de qualquer outra página.

## <a name="next-steps"> </a>Próximas etapas

-	Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][].

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Introdução à configuração avançada de API]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[variáveis LESS]: http://getbootstrap.com/css/

<!---HONumber=AcomDC_1210_2015-->