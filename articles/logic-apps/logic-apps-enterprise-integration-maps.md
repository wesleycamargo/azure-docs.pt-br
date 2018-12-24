---
title: Transformar XML com mapas XSLT – Aplicativos Lógicos do Azure | Microsoft Docs
description: Adicionar mapas XSLT que transformam XML nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123549"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Adicionar mapas para transformação de XML nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

A integração do Enterprise usa mapas para transformar dados XML entre formatos. Um mapa é um documento XML que define os dados em um documento que devem ser transformados em outro formato. 

## <a name="why-use-maps"></a>Por que usar mapas?

Vamos supor que você receba regularmente pedidos ou faturas B2B de clientes que usam o formato AAAAMMDD para datas. No entanto, em sua organização, você armazena datas no formato MMDDAAAA. Você pode usar um mapa para *transformar* o formato de data AAAAMMDD em MMDDAAAA antes de armazenar os detalhes do pedido ou da fatura em seu banco de dados de atividade do cliente.


## <a name="how-do-i-create-a-map"></a>Como faço para criar um mapa?

É possível criar projetos do BizTalk Integration com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") para o Visual Studio 2015. Então, será possível criar um arquivo de mapa de integração que permitirá visualizar itens de mapa entre dois arquivos de esquema XML. Após criar esse projeto, você terá um documento XSLT.

Se o mapa tiver uma referência a um assembly externo, ambos deverão ser carregados para a conta de integração. Eles deverão ser carregados em uma ordem específica, primeiro o assembly e, em seguida, o mapa que faz referência ao assembly.


## <a name="how-do-i-add-a-map"></a>Como adicionar um mapa?

1. No portal do Azure, selecione **Procurar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Insira **integração** na caixa de pesquisa do filtro e, em seguida, selecione **Contas de Integração** na lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Escolha a conta de integração à qual você deseja adicionar o mapa.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Escolha o bloco **Mapas**.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Após a página Mapas ser aberta, escolha **Adicionar**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Insira um **Nome** para o mapa. Para carregar o arquivo de mapa, clique no ícone de pasta à direita da caixa de texto **Mapa**. Após a conclusão do processo de upload, clique em **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Após o Azure adicionar o mapa à sua conta de integração, você receberá uma mensagem na tela informando se o arquivo de mapa foi adicionado ou não. Depois de receber essa mensagem, clique no bloco **Mapas** para exibir o mapa recém-adicionado.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Como adiciono um assembly?
Escolha a conta de integração à qual você deseja carregar o assembly.

1. Escolha o bloco **Assemblies**.

    ![integrationaccount-assembly-tile](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Após a página Assemblies ser aberta, escolha **Adicionar**. Digite um **Nome** para o seu assembly. Para carregar o arquivo de assembly, clique no ícone de pasta à direita da caixa de texto **Assembly**. Após a conclusão do processo de upload, clique em **OK**.

    ![add-assembly](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Como editar um mapa?

É necessário carregar um novo arquivo de mapa com as alterações desejadas. Primeiro, você pode baixar o mapa e editá-lo.

Para carregar um novo mapa que substitui um mapa existente, siga estas etapas.

1. Escolha o bloco **Mapas**.

2. Após a página Mapas ser aberta, selecione o mapa que deseja editar.

3. Na página **Mapas**, clique em **Atualizar**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. No seletor de arquivos, selecione o arquivo de mapa que você deseja carregar e, em seguida, selecione **Abrir**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Como excluir um mapa?

1. Escolha o bloco **Mapas**.

2. Após a página Mapas ser aberta, selecione o mapa que deseja excluir.

3. Clique em **Excluir**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Confirme que você realmente deseja excluir o mapa.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  
* [Saiba mais sobre transformações](logic-apps-enterprise-integration-transform.md "Saiba mais sobre as transformações de integração corporativa")  

