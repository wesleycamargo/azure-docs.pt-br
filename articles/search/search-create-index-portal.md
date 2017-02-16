---
title: "Criar um índice do Azure Search usando o Portal do Azure | Microsoft Docs"
description: "Criar um índice usando o Portal do Azure."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: cc439b5da8753b41f8d65781c567b4808f385078


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Criar um índice de Pesquisa do Azure usando o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Este artigo o orientará ao longo do processo de criação de um [índice](search-what-is-an-index.md) de Pesquisa do Azure usando o Portal do Azure.

Antes de seguir este guia e criar um índice, você já deverá ter [criado um serviço de Pesquisa do Azure](search-create-service-portal.md).

## <a name="go-to-your-azure-search-blade"></a>Vá para a folha de Pesquisa do Azure
1. Clique em "Todos os recursos" no menu à esquerda do [Portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o serviço de Pesquisa do Azure

## <a name="add-and-name-your-index"></a>Adicione e nomeie seu índice
1. Clique no botão "Adicionar índice"
2. Dê um nome ao seu índice de Pesquisa do Azure Como estamos criando um índice para procurar hotéis neste guia, chamamos o nosso índice de "hotels".
   * O nome do índice deve começar com uma letra e conter somente letras minúsculas, dígitos ou traços ("-").
   * Assim como o nome do serviço, o nome do índice que você escolher também fará parte da URL do ponto de extremidade para onde você enviará suas solicitações HTTP para a API de Pesquisa do Azure
3. Clique na entrada "Campos" para abrir uma nova folha

![](./media/search-create-index-portal/add-index.png)

## <a name="create-and-define-the-fields-of-your-index"></a>Crie e defina os campos do índice
1. Ao selecionar a entrada "Campos", uma nova folha será aberta com um formulário para inserir a definição de seu índice.
2. Adicione campos ao índice usando o formulário.
   
   * Um campo *chave* do tipo Edm.String é obrigatório para todo índice de Pesquisa do Azure. Esse campo chave é criado por padrão com o nome de campo "id". Nós o mudamos para "hotelId" em nosso índice.
   * Determinadas propriedades de seu esquema de índice podem ser definidas apenas uma vez, sem possibilidade de atualização no futuro. Por isso, não há suporte às atualizações de esquema que exigem reindexação, como a alteração de tipos de campo, após a configuração inicial.
   * Escolhemos cuidadosamente os valores de propriedade para cada campo com base em como achamos que serão usados em um aplicativo. Tenha em mente as necessidades do negócio e a experiência do usuário de pesquisa ao projetar o índice, uma vez que você deve atribuir as [propriedades apropriadas](https://msdn.microsoft.com/library/azure/dn798941.aspx) a cada campo. Essas propriedades controlam quais recursos de pesquisa (filtragem, facetas, classificação, pesquisa de texto completo etc.) se aplicam a quais campos. Por exemplo, é provável que ao pesquisarem hotéis, as pessoas estejam interessadas em correspondências de palavra-chave no campo de descrição, por isso habilitamos a pesquisa de texto completo para esse campo definindo a propriedade "Searchable".
     * Você também pode definir o [analisador de linguagem](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) para cada campo clicando na guia "Analisador" na parte superior da folha. Você pode ver abaixo que selecionamos um analisador de francês para um campo em nosso índice destinado a um texto em francês.
3. Clique em **OK** na folha "Campos" para confirmar as definições do campo
4. Clique em **OK** na folha "Adicionar índice" para salvar e criar o índice que você acabou de definir.

Nas capturas de tela abaixo, você verá como nomeamos e definimos os campos para nosso índice "hotels".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Próximas etapas
Após criar um índice de Pesquisa do Azure, você estará pronto para [carregar o conteúdo no índice](search-what-is-data-import.md) para que possa começar a pesquisar os dados.




<!--HONumber=Jan17_HO2-->


