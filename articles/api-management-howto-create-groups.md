<properties pageTitle="How to create and use groups to manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to manage developer accounts using groups in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How to create and use groups to manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento da API do Azure

No Gerenciamento de API (visualização), os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos ficam visíveis primeiro para os grupos, e depois os desenvolvedores nesses grupos podem ver e assinar os produtos associados aos grupos.

O gerenciamento de API tem os seguintes grupos internos.

-   **Administradores** - Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
-   **Desenvolvedores** - Desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criam aplicativos que chamam as operações de uma API.
-   **Convidados** - Os usuários não autenticados, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos internos, os administradores podem criar grupos personalizados. Os grupos personalizados têm os mesmos privilégios que o grupo de desenvolvedores internos e podem ser usados para gerenciar vários grupos de desenvolvedores. Por exemplo, você pode criar um grupo personalizado para desenvolvedores que usará as APIs de um produto e um outro grupo de desenvolvedores que usará as APIs de um produto diferente.

Este guia mostra como administradores de uma instância do Gerenciamento de API podem adicionar novos grupos e associá-los a produtos e desenvolvedores.

## Neste tópico

-   [Criar um grupo][Criar um grupo]
-   [Associar um grupo a um produto][Associar um grupo a um produto]
-   [Associar grupos a desenvolvedores][Associar grupos a desenvolvedores]
-   [Próximas etapas][Próximas etapas]

## <a name="create-group"> </a>Criar um grupo

Para criar um novo grupo, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de Gerenciamento de API][Console de Gerenciamento de API]

Clique em **Grupos** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar grupo**.

![Adicione o novo grupo][Adicione o novo grupo]

Insira um nome exclusivo e uma descrição opcional para o grupo e clique em **Salvar**.

![Adicione o novo grupo][1]

O novo grupo é exibido na guia de grupos. Para editar o **Nome** ou a **Descrição** do grupo, clique no nome do grupo na lista. Para excluir o grupo, clique em **Excluir**.

![Grupo adicionado][Grupo adicionado]

Agora que foi criado, o grupo pode ser associado a produtos e desenvolvedores.

## <a name="associate-group-product"> </a>Associar um grupo a um produto

Para associar um grupo a um produto, clique em **Produtos** no menu **Gerenciamento de API** à esquerda e depois clique no nome do produto desejado.

![Configurar visibilidade][Configurar visibilidade]

Selecione a guia **Visibilidade** para adicionar e remover grupos e para ver os grupos atuais do produto. Para adicionar ou remover grupos, marque ou desmarque as caixas de seleção correspondentes aos grupos desejados e clique em **Salvar**.

![Configurar visibilidade][2]

> Para configurar grupos a partir da guia **Visibilidade** de um produto, clique em **Gerenciar grupos**.

Após um produto ser associado a um grupo, os desenvolvedores nesse grupo poderão ver e assinar o produto.

## <a name="associate-group-developer"> </a>Associar grupos a desenvolvedores

Para associar grupos a desenvolvedores, clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda e marque a caixa de seleção ao lado do desenvolvedor que desejar associar a um grupo.

![Adicione o desenvolvedor a um grupo][Adicione o desenvolvedor a um grupo]

Após os desenvolvedores desejados serem selecionados, clique no grupo desejado no menu suspenso **Adicionar ao grupo**. Os desenvolvedores podem ser removidos dos grupos usando o menu suspenso **Remover do grupo**.

![Desenvolvedores][Desenvolvedores]

Após fazer a associação entre o desenvolvedor e o grupo, você poderá vê-la na guia **Desenvolvedores**.

## <a name="next-steps"> </a>Próximas etapas

Após um desenvolvedor ser associado a um grupo, ele poderá ver e assinar produtos associados ao grupo em questão. Para obter mais informações, consulte [Como criar e publicar um produto no Gerenciamento de API do Azure][Como criar e publicar um produto no Gerenciamento de API do Azure].

  [Criar um grupo]: #create-group
  [Associar um grupo a um produto]: #associate-group-product
  [Associar grupos a desenvolvedores]: #associate-group-developer
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-create-groups/api-management-management-console.png
  [Adicione o novo grupo]: ./media/api-management-howto-create-groups/api-management-add-group.png
  [1]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
  [Grupo adicionado]: ./media/api-management-howto-create-groups/api-management-new-group.png
  [Configurar visibilidade]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
  [2]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
  [Adicione o desenvolvedor a um grupo]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
  [Desenvolvedores]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png
  [Como criar e publicar um produto no Gerenciamento de API do Azure]: ../api-management-howto-add-products
