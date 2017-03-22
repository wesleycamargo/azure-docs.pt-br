---
title: "Gerenciar aplicativos lógicos no Visual Studio – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Gerenciar aplicativos lógicos e outros ativos do Azure com o Visual Studio Cloud Explorer"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gerenciar os aplicativos lógicos com o Visual Studio Cloud Explorer

Embora o [portal do Azure](https://portal.azure.com/) ofereça uma excelente maneira para criar e gerenciar Aplicativo Lógico do Azure, é possível gerenciar vários ativos do Azure, incluindo aplicativos lógicos, no Visual Studio, ao usar o Visual Studio Cloud Explorer. É possível procurar os aplicativos lógicos publicados e realizar tarefas como habilitar e desabilitar os aplicativos lógicos ou editar e exibir os históricos de execução. 

## <a name="installation-steps"></a>Etapas de instalação

Para instalar e configurar as ferramentas do Visual Studio para Aplicativos Lógicos do Azure, siga estas etapas.

### <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Gerenciador de Nuvem do Visual Studio](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acesso à Web ao usar o designer incorporado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar ferramentas do Visual Studio para Aplicativos Lógicos

Após instalar os pré-requisitos:

1. Abra o Visual Studio. No menu **Ferramentas**, selecione **Extensões e Atualizações**.
2. Expanda a categoria **Online** para que você possa pesquisar online.
3. Procure por **Aplicativos Lógicos** até encontrar as **Ferramentas de Aplicativos Lógicos do Azure para Visual Studio**.
4. Clique em **Baixar** para baixar e instalar a extensão.
5. Reinicie o Visual Studio após a instalação.

> [!NOTE]
> Você também pode baixar Ferramentas de Aplicativos Lógicos do Azure para Visual Studio diretamente no [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Procurar aplicativos lógicos no Cloud Explorer

1.    Para abrir o Cloud Explorer, no menu **Exibir**, escolha **Cloud Explorer**.
2.    Navegue para o aplicativo lógico, por grupo de recursos ou tipo de recurso. 

    Se você procurar por tipo de recurso, selecione sua assinatura do Azure e, em seguida, expanda a seção Aplicativos Lógicos e selecione um Aplicativo Lógico. 
    É possível clicar com o botão direito do mouse em um aplicativo lógico ou escolher no menu **Ações** na parte inferior do Cloud Explorer.

    ![Navegar para o aplicativo lógico](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>Editar o aplicativo lógico com o Designer de Aplicativos Lógicos

Para abrir um aplicativo lógico atualmente implantado no mesmo designer usado no portal do Azure, clique com o botão direito do mouse no aplicativo lógico e selecione **Abrir com Editor de Aplicativo Lógico**. 

No designer, é possível editar o aplicativo lógico, salvar as atualizações na nuvem e iniciar uma nova execução escolhendo **Executar Gatilho**.

![Designer de Aplicativos Lógicos](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>Procurar o histórico de execução do aplicativo lógico

Para exibir o histórico de execução do aplicativo lógico, clique com o botão direito do mouse no aplicativo lógico e selecione **Abrir histórico de execução**. Para reordenar o histórico de execução de acordo com uma das propriedades mostradas, selecione o cabeçalho de coluna.

![Histórico da execução](media/logic-apps-manage-from-vs/runs.png)

Para mostrar o histórico de execução de uma instância para poder ver os resultados da execução, incluindo as entradas e saídas de cada etapa, clique duas vezes em uma das instâncias de execução.

![Resultados do histórico de execução, entradas e saídas de etapas](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Próximas etapas

*    Para começar a usar os Aplicativo Lógico do Azure, saiba [como criar seu primeiro aplicativo lógico no portal do Azure](logic-apps-create-a-logic-app.md)
* [Criar, compilar e implantar aplicativos lógicos no Visual Studio](logic-apps-deploy-from-vs.md)
* [Veja exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Saiba como automatizar processos de negócios com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/T694)
* [Saiba como integrar seus sistemas com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/P462)

