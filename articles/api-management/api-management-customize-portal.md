---
title: Personalizar o portal do desenvolvedor no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o portal do desenvolvedor no Gerenciamento de API do Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 13431648e87d085161ad278dc991d49f7872be34
ms.openlocfilehash: 60213f885020a5ba36d6ada0812f755e06b3c48b


---
# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalizar o portal do desenvolvedor no Gerenciamento de API do Azure
Este guia mostra como modificar a aparência do portal do desenvolvedor no Gerenciamento de API do Azure para manter a consistência com a sua marca.

## <a name="change-page-headers"> </a>Alterar o texto ou logotipo no cabeçalho da página
Um dos principais aspectos da personalização do portal é a substituição do texto na parte superior de todas as páginas pelo nome ou logotipo de sua empresa.

O conteúdo no portal do desenvolvedor é modificado por meio do portal do editor, que pode ser acessado do Portal do Azure. Para alcançá-lo, clique em **Portal do editor** na barra de ferramentas do serviço da sua instância do Gerenciamento de API.

![Portal do editor][api-management-management-console]

O portal do desenvolvedor é baseado em um CMS ou sistema de gerenciamento de conteúdo. O cabeçalho que aparece em cada página é um tipo especial de conteúdo conhecido como widget. Para editar o conteúdo do widget, clique em **Widgets** no menu **Portal do Desenvolvedor** do lado esquerdo, depois selecione o widget **Cabeçalho** na lista.

![Cabeçalho de widget][api-management-widgets-header]

O conteúdo do cabeçalho é editável no campo **Corpo** . Altere o texto para "Fabrikam Developer Portal" e clique em **Salvar** na parte inferior da página.

Agora você poderá ver o novo cabeçalho em cada página no portal do desenvolvedor.

> Para abrir o portal do desenvolvedor enquanto estiver no portal do publicador, clique em **Portal do desenvolvedor** na barra superior.
> 
> 

## <a name="change-headers-styling"> </a>Alterar o estilo dos cabeçalhos
As cores, fontes, tamanhos, espaços e outros elementos relacionados ao estilo de qualquer página no portal são definidos pelas regras de status. Para editar os estilos, durante o **portal do desenvolvedor**, abra a barra de ferramentas de personalização no lado esquerdo, movendo sobre o ícone de personalização e, em seguida, selecione "estilos" na barra de ferramentas.

![Botão da barra de personalização][api-management-customization-toolbar-button]

Há duas maneiras principais de editar as regras de estilo - você pode examinar a lista de todas as regras de estilo usadas em qualquer lugar que é exibida por padrão e modificar um estilo conforme necessário, ou você pode escolher **selecionar um elemento na página** e, em seguida, clique em qualquer lugar na página para ver somente os estilos para esse elemento.

Nesta seção gostaríamos de alterar o estilo somente dos cabeçalhos. Clique o **selecionar um elemento na página** opção da barra de ferramentas de editor de estilo. 

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Os elementos agora ficam destacados à medida que você passa o ponteiro do mouse sobre eles, indicando quais estilos do elemento você começaria a editar se clicasse. Mova o ponteiro do mouse sobre o texto que representa o nome da empresa no cabeçalho ("Fabrikam Developer Portal" se você seguiu as instruções na seção anterior), então clique nele. Um conjunto de regras de estilo categorizadas e nomeadas é exibido no editor de estilos. Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto do cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1, enquanto que o nome da fonte com alternativas está em @headings-font-family.

> Se você está familiarizado com [bootstrap][bootstrap], essas regras são, na verdade, [variáveis LESS][LESS variables] no tema do bootstrap usadas pelo portal do desenvolvedor.
> 
> 

Vamos alterar a cor do texto do cabeçalho. Selecione a entrada no campo **@headings-color** e digite **#000000**. Esse é o código hexadecimal para a cor preta. Quando você fizer isso, verá que um indicador de cor quadrado aparece no final da caixa de texto. Se você clicar nesse indicador, um seletor de cor permitirá que você escolha uma cor.

![Seletor de cor][api-management-customization-toolbar-color-picker]

As alterações são visualizadas em tempo real como torná-los, mas ficam visíveis somente para administradores. Para tornar essas alterações visíveis para qualquer um, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento utilizado para o cabeçalho. Clique em **Escolher um elemento** do editor de estilos, selecione o elemento em que você está interessado e comece a modificar os valores das regras de estilo exibidos na tela.
> 
> 

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página
O portal do desenvolvedor consiste em páginas geradas automaticamente, como APIs, Produtos, Aplicativos, Questões e conteúdo escrito manualmente. Como ele está baseado em um sistema de gerenciamento de conteúdo, você pode criar esse conteúdo conforme necessário.

Para ver uma lista de todas as páginas de conteúdo existentes, clique em **Conteúdo** no menu **Portal do desenvolvedor** no portal do publicador.

![Gerenciar conteúdo][api-management-customization-manage-content]

Clique na página **Bem-vindo** para editar o que é exibido na home page do portal do desenvolvedor. Faça as alterações desejadas, visualize-as se necessário e depois clique em **Publicar Agora** para torná-las visíveis para todos.

> A home page usa um layout especial que permite exibir uma faixa na parte superior. Essa faixa não é editável na seção **Conteúdo** . Para editá-la, clique em **Widgets** no menu **Portal do desenvolvedor**, selecione **Home page** na lista suspensa **Camada Atual** e abra o item **Faixa** na seção **Em destaque**. O conteúdo desse widget pode ser editado como o conteúdo de qualquer outra página.
> 
> 

## <a name="next-steps"> </a>Próximas etapas
* Saiba como personalizar o conteúdo das páginas do portal do desenvolvedor usando [Modelos de portal do desenvolvedor](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-portal/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/



<!--HONumber=Feb17_HO1-->


