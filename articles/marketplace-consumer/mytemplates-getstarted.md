---
title: "Introdução aos modelos privados | Microsoft Docs"
description: Adicione, gerencie e compartilhe seus modelos privados usando o portal do Azure, a CLI do Azure ou o PowerShell.
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: c890339ba7677b23717a6e0437b5e936fdf8ab03
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introdução aos modelos privados no portal do Azure
Um modelo do [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) é um modelo declarativo usado para definir a sua implantação. Você define os recursos a serem implantados em uma solução e especifica os parâmetros e variáveis que lhe permitem inserir valores para diferentes ambientes. O modelo consiste em JSON e expressões, que podem ser usados na construção de valores para sua implantação.

Você pode usar o novo recurso **Modelos** no [portal do Azure](https://portal.azure.com), juntamente com o provedor de recursos **Microsoft.Gallery** como uma extensão do [Azure Marketplace](https://azure.microsoft.com/marketplace/) para permitir aos usuários criar, gerenciar e implantar modelos privados de uma biblioteca pessoal.

> [!NOTE]
> Em vez de usar modelos privados no portal, a Microsoft recomenda que você crie um aplicativo de catálogo de serviço por meio de [Aplicativos Gerenciados](../managed-applications/overview.md). Você pode disponibilizar o aplicativo de catálogo de serviço aos usuários da sua organização.

Este documento explica como adicionar, gerenciar e compartilhar um **Modelo** privado usando o portal do Azure.

## <a name="guidance"></a>Diretrizes
As sugestões a seguir ajudarão a aproveitar os **Modelos** ao máximo quando estiver trabalhando com suas soluções:

* Um **Modelo** é um recurso de encapsulamento que contém um modelo do Resource Manager e metadados adicionais. Ele se comporta de modo semelhante a um item no Marketplace. A principal diferença é que se trata de um item privado, e não itens públicos do Marketplace.
* A biblioteca **Modelos** funciona bem com usuários que precisam personalizar suas implantações.
* **Modelos** funcionam bem com usuários que precisam de um repositório simples no Azure.
* Comece com um modelo existente do Resource Manager. Encontre modelos no [github](https://github.com/Azure/azure-quickstart-templates) ou [exporte o modelo](../azure-resource-manager/resource-manager-export-template.md) de um grupo de recursos existente.
* **Modelos** ficam vinculados ao usuário que os publicar. O nome do publicador fica visível para qualquer pessoa que tenha acesso de leitura.
* **Modelos** são recursos do Resource Manager e não podem ser renomeados depois de publicados.

## <a name="add-a-template-resource"></a>Adicionar um recurso de modelo
Há duas maneiras de criar um recurso de **modelo** no portal do Azure.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: criar um novo recurso de Modelo a partir de um grupo de recursos em execução
1. Navegue até um grupo de recursos existente no portal do Azure. Selecione **Exportar modelo** em **Configurações**.
2. Depois que o modelo do Resource Manager é exportado, use o botão **Salvar Modelo** para salvá-lo no repositório **Modelos**. Encontre todos os detalhes sobre a exportação de modelos [aqui](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Exportação do grupo de recursos](media/rg-export-portal1.PNG)
3. Selecione o botão de comando **Salvar no Modelo** .
   <br /><br />
4. Insira as seguintes informações:
   
   * Nome – nome do objeto de modelo (OBSERVAÇÃO: esse campo é um nome baseado no Azure Resource Manager. Todas as restrições de nomenclatura são aplicáveis e ele não pode ser alterado depois de criado).
   * Descrição – resumo rápido do modelo.
     
     ![Salvar Modelo](media/save-template-portal1.PNG)
5. Clique em **Salvar**.
   
   > [!NOTE]
   > O portal mostra notificações quando o modelo do Resource Manager exportado tem erros, mas você ainda poderá salvar esse modelo do Resource Manager em Modelos. Verifique e corrija problemas no modelo do Resource Manager antes de reimplantar o modelo do Resource Manager exportado.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>Método 2: adicionar um novo recurso de Modelo por meio de navegação
Você também pode adicionar um novo **Modelo** do zero usando o botão de comando +Adicionar em **Procurar > Modelos**. Forneça um Nome, uma Descrição e o JSON do modelo do Resource Manager.

![Adicionar Modelo](media/add-template-portal1.PNG)

> [!NOTE]
> O Microsoft.Gallery é um provedor de recursos do Azure baseado em locatário. O recurso de modelo está vinculado ao usuário que o criou. Ele não está associado a nenhuma assinatura específica. Escolha uma assinatura ao implantar um modelo.
> 
> 

## <a name="view-template-resources"></a>Exibir recursos de modelo
Todos os **modelos disponíveis** podem ser exibidos em **Procurar > Modelos**. Os modelos disponíveis incluem aqueles criados por pessoas e os que foram compartilhados com você com vários níveis de permissões. Para obter mais informações, consulte [controle de acesso](#access-control-for-a-tenant-resource-provider).

![Exibir Modelo](media/view-template-portal1.PNG)

Você pode exibir os detalhes de um **modelo** clicando em um item na lista.

![Exibir Modelo](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Editar um recurso de modelo
Você pode iniciar o fluxo de edição de um **Modelo** clicando com o botão direito do mouse no item na lista Procurar ou escolhendo o botão de comando Editar.

![Editar modelo](media/edit-template-portal1a.PNG)

Edite a descrição ou o texto do modelo do Resource Manager. Você não pode editar o nome, já que ele é um nome de recurso do Resource Manager. Quando você editar o JSON do modelo do Resource Manager, nós o validaremos para garantir que se trata de um JSON válido. Escolha **OK** e **Salvar** para salvar o modelo atualizado.

![Editar modelo](media/edit-template-portal2a.PNG)

Depois que o Modelo for salvo, você verá uma notificação de confirmação.

![Editar modelo](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Implantar um recurso de modelo
Você pode implantar qualquer **Modelo** para o qual tiver permissões de **Leitura**. Preencha os valores dos parâmetros de modelo do Resource Manager para prosseguir com a implantação.

![Implantar o modelo](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Compartilhar um recurso de modelo
Um recurso de **modelo** pode ser compartilhados com seus colegas. O compartilhamento se comporta da mesma forma que a [atribuição de função de qualquer recurso no Azure](../active-directory/role-based-access-control-configure.md). O proprietário do **modelo** fornece permissões para outros usuários que podem interagir com um recurso de modelo. A pessoa ou o grupo de pessoas com quem você compartilhar o **Modelo** será capaz de ver o modelo do Resource Manager e suas propriedades de galeria.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controle de acesso aos recursos da Microsoft.Gallery
| Função | Permissões |
| --- | --- |
| Proprietário |Permite o controle total sobre o recurso de modelo, incluindo compartilhamento |
| Leitor |Permite leitura e execução (Implantação) no recurso de modelo |
| Colaborador |Concede permissão de edição e exclusão no recurso de modelo. O usuário não pode compartilhar o modelo com outras pessoas |

Selecione **Compartilhar** no item de navegação clicando com o botão direito do mouse ou ao exibir um item específico.

![Compartilhar modelo](media/share-template-portal1a.png)

 Agora você pode escolher uma função e um usuário ou grupo para fornecer acesso a determinado **modelo**. As funções disponíveis são Proprietário, Leitor e Colaborador.

![Compartilhar modelo](media/share-template-portal2b.png)

![Compartilhar modelo](media/share-template-portal3b.png)

Clique em **Selecionar** e em **Ok**. Agora você pode ver os usuários ou grupos adicionados ao recurso.

![Compartilhar modelo](media/share-template-portal4b.png)

> [!NOTE]
> Um modelo só pode ser compartilhado com usuários e grupos no mesmo locatário do Azure Active Directory. Se você compartilhar um Modelo com um endereço de email que não está em seu locatário, um convite é enviado com um pedido ao usuário para ingressar no locatário como convidado.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Resource Manager, confira [Criando modelos](../azure-resource-manager/resource-group-authoring-templates.md)
* Para conhecer as funções que podem ser usadas em um modelo do Resource Manager, consulte [Funções do modelo](../azure-resource-manager/resource-group-template-functions.md)

