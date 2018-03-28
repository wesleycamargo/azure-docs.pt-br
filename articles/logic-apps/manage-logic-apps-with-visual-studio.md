---
title: Gerenciar aplicativos lógicos com Visual Studio - Aplicativo Lógico do Azure | Microsoft Docs
description: Gerenciar aplicativos lógicos e outros ativos do Azure com o Visual Studio Cloud Explorer
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: db847d5fa3d5f5b2b2f0293f1756226870a8b47e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerenciar aplicativos lógicos com Visual Studio

Embora seja possível criar, editar, gerenciar e implantar aplicativos lógicos no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, você também pode utilizar o Visual Studio quando quiser adicionar aplicativos lógicos ao controle do código-fonte, publicar versões diferentes e criar modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para diferentes ambientes de implantação. Com o Microsoft Visual Studio Cloud Explorer, é possível localizar e gerenciar os aplicativos lógicos junto com outros recursos do Azure. Por exemplo, é possível abrir, baixar, editar, executar, exibir histórico de execução, desabilitar e habilitar aplicativos lógicos que já estejam implantados no Portal do Azure. Se você não tiver experiência em trabalhar com Aplicativo Lógico do Azure no Visual Studio, aprenda [como criar aplicativos lógicos com Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> A implantação ou publicação de um aplicativo lógico do Visual Studio substitui a versão desse aplicativo no Portal do Azure. Portanto, se você fizer alterações no Portal do Azure que deseja manter, certifique-se de [atualizar o aplicativo lógico no Visual Studio](#refresh) do Portal do Azure, antes da próxima implantação ou publicação pelo Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>pré-requisitos

* Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Baixe e instale essas ferramentas, caso você ainda não as tenha: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 ou Visual Studio 2015 - Community Edition ou posterior</a>. 
  Este início rápido usa o Visual Studio Community 2017, que é gratuito.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">SDK do Azure (2.9.1 ou posterior)</a> e <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Microsoft Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Ferramentas de Aplicativo Lógico do Azure para Visual Studio 2017</a> ou a <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Versão do Visual Studio 2015</a> 
  
    Você pode baixar e instalar as Ferramentas de Aplicativo Lógico do Azure diretamente do Visual Studio Marketplace ou aprender a <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank"> instalar essa extensão de dentro do Visual Studio</a>. 
    Reinicie o Visual Studio após concluir a instalação.

* Acesso à Web ao usar o Designer do Aplicativo Lógico incorporado

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Localizar aplicativos lógicos

No Visual Studio, é possível localizar todos os aplicativos lógicos associados à sua assinatura do Azure e que estejam implantados no Portal do Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Exibir**, selecione **Cloud Explorer**.

2. In Cloud Explorer, escolha **Gerenciamento de Contas**. Selecione a assinatura do Azure associada aos aplicativos lógicos e escolha **Aplicar**. Por exemplo: 

   ![Escolha "Gerenciamento de Contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Se você estiver pesquisando por **Grupos de Recursos** ou **Tipos de Recursos**, siga estas etapas:

   * **Grupos de Recursos**: na sua assinatura do Azure, o Cloud Explorer mostra todos os grupos de recursos associados a essa assinatura. 
   Expanda o grupo de recursos que contém o aplicativo lógico e selecione o aplicativo lógico.

   * **Tipos de Recursos**: na sua assinatura do Azure, expanda **Aplicativos Lógicos**. Após o Cloud Explorer mostrar todos os aplicativos lógicos implementados associados à sua assinatura, selecione o aplicativo lógico.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, é possível abrir aplicativos lógicos previamente criados e implantados diretamente através do Portal do Azure ou como projetos do Azure Resource Manager com Visual Studio.

1. Abra o Cloud Explorer e localize o aplicativo lógico. 

2. No menu de atalho do aplicativo lógico, selecione **Abrir com o Editor do Aplicativo Lógico**.

   Esse exemplo mostra aplicativos lógicos por tipo de recurso para que os aplicativos lógicos apareçam na seção **Aplicativos Lógicos**.

  ![Abrir o aplicativo lógico implantado do Portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois que o aplicativo lógico for aberto no 	Designer do Aplicativo Lógico, na parte inferior do designer será possível escolher **Exibir Código** para poder analisar a estrutura de definição de aplicativo lógico subjacente. 
   Se você quiser criar um modelo de implantação para o aplicativo lógico, saiba [como baixar um modelo do Azure Resource Manager ](#download-logic-app) para esse aplicativo lógico. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar pelo Azure

É possível baixar aplicativos lógicos pelo <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> e salvá-los como modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Em seguida, você poderá editar localmente os modelos com o Visual Studio e personalizar aplicativos lógicos para diferentes ambientes de implantação. O download de aplicativos lógicos *parametriza*  automaticamente suas definições dentro dos [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), que também usam JSON (JavaScript Object Notation).

1. No Visual Studio, abra o Cloud Explorer, localize e selecione o aplicativo lógico que você deseja baixar pelo Azure.

2. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**.

   O Designer do Aplicativo Lógico é aberto e mostra o aplicativo lógico. 
   Para analisar a definição e a estrutura subjacentes do aplicativo lógico, na parte inferior do designer, escolha **Exibir Código**. 

3. Na barra de ferramentas do designer, escolha **Baixar**.

   ![Escolher "Baixar"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Quando for solicitado um local, navegue para esse local e salve o modelo do Resource Manager para a definição de aplicativo lógico no formato de arquivo .json (JSON). 

A definição de aplicativo lógico aparece na subseção `resources` dentro do modelo do Resource Manager. Agora é possível editar a definição de aplicativo lógico e o modelo do Resource Manager com o Visual Studio. Além disso, é possível adicionar o modelo como um projeto do Azure Resource Manager a uma solução do Visual Studio. Saiba mais sobre [projetos do Resource Manager para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar pelo Azure

Se você editar o aplicativo lógico no Portal do Azure e quiser manter essas alterações, atualize a versão do aplicativo no Visual Studio com essas alterações. 

* No Visual Studio, na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Atualizar**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Atualizar**. 

![Atualizar aplicativo lógico com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicativos lógicos

Quando estiver pronto para implantar as atualizações de aplicativo lógico do Visual Studio para o Azure, na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Publicar**.

![Publicar aplicativo lógico atualizado](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente o aplicativo lógico

É possível disparar um aplicativo lógico manualmente implantado no Azure a partir do Visual Studio. Na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Executar Gatilho**.

![Executar o aplicativo lógico manualmente](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Examinar o histórico de execuções

Para verificar o status e diagnosticar problemas com execuções de aplicativos lógicos, você pode revisar os detalhes, como entradas e saídas, dessas execuções no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Abrir histórico de execução**.

   ![Abrir histórico de execução](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Para visualizar os detalhes de uma execução específica, clique duas vezes em uma execução. Por exemplo: 

   ![Histórico de execução detalhado](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, escolha o cabeçalho da coluna para essa propriedade. 

3. Expanda as etapas cujas entradas e saídas você quer revisar. Por exemplo: 

   ![Exibir entradas e saídas para cada etapa](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

Sem excluir o aplicativo lógico, será possível parar o acionamento do gatilho na próxima vez quando a condição do gatilho for atendida. Desabilitar o aplicativo lógico evita que o mecanismo dos Aplicativos Lógicos crie e execute instâncias de fluxo de trabalho futuras para o aplicativo lógico.
No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Desabilitar**.

![Desabilitar o aplicativo lógico](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Quando estiver pronto para o aplicativo lógico retomar a operação, o aplicativo lógico poderá ser reativado. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Habilitar**.

![Habilitar o aplicativo lógico](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Excluir seu aplicativo lógico

Para excluir o aplicativo lógico do Portal do Azure, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Excluir**.

![Excluir seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar aplicativos lógicos implantados com Visual Studio. Em seguida, saiba mais sobre como personalizar as definições de aplicativos lógicos para implantação:

> [!div class="nextstepaction"]
> [Criar definições de aplicativo lógico em JSON](../logic-apps/logic-apps-author-definitions.md)