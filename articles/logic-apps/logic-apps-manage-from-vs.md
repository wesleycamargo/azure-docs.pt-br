---
title: "Gerenciar aplicativos lógicos do Azure do Gerenciador de nuvem do Visual Studio | Microsoft Docs"
description: "Gerencie aplicativos lógicos do Azure do Gerenciador de nuvem do Visual Studio."
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
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Gerenciar aplicativos lógicos com o Gerenciador de nuvem do Visual Studio
Embora o [portal do Azure](https://portal.azure.com) oferece uma ótima maneira de criar e gerenciar seus aplicativos lógicos, o Gerenciador de nuvem do Visual Studio permite que você gerencie muitos de seus ativos do Azure no Visual Studio, incluindo aplicativos lógicos.  Com o Gerenciador de nuvem, você pode procurar os Aplicativos Lógicos publicados, executar ações como habilitar, desabilitar, editar e exibir o histórico de execução. 

## <a name="installation-steps"></a>Etapas de instalação
Veja a seguir as etapas para instalação e configuração das ferramentas do Visual Studio para Aplicativos Lógicos.

### <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [Gerenciador de Nuvem do Visual Studio](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Acesso à Web ao usar o designer incorporado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar ferramentas do Visual Studio para Aplicativos Lógicos
Quando os pré-requisitos estiverem instalados, 

1. Abra o Visual Studio 2015 no menu **Ferramentas** e selecione **Extensões e Atualizações**
2. Selecione a categoria **Online** para pesquisar online
3. Procure **Aplicativos Lógicos** para exibir as **Ferramentas de Aplicativos Lógicos do Azure para Visual Studio**
4. Clique no botão **Baixar** para baixar e instalar a extensão
5. Reinicie o Visual Studio após a instalação

> [!NOTE]
> Você também pode baixar a extensão diretamente [deste link](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>Procurando Aplicativos Lógicos
Para procurar os Aplicativos Lógicos no Gerenciador de nuvem abra o Gerenciador de nuvem em modo de exibição > Gerenciador de nuvem e você pode a procurar por grupo de recursos ou tipo de recurso.  Se procurar por tipo de recurso, selecione uma assinatura e, em seguida, expanda a seção de aplicativos lógicos e selecione um aplicativo lógico.  Você pode clique em um dos seus Aplicativos Lógicos ou use o menu Ações na parte inferior do Gerenciador de nuvem para executar a ação desejada.

![Procurar](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>Editar aplicativo lógico com o designer
Para abrir o aplicativo lógico atualmente implantados no designer mesmo que seja no portal do Azure, clique no aplicativo lógico e selecione "Abrir com lógica de aplicativo Editor".  Com o designer, você pode editar o aplicativo lógico e salvá-lo na nuvem e iniciar uma nova execução com o botão de "Gatilho execute".

![Designer](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>Procure pelo aplicativo lógico histórico de execução
Para listar o histórico de execução para um aplicativo lógico, clique no aplicativo lógico e selecione "Abrir histórico de execução".  Nessa exibição, você pode solicitar por qualquer uma das propriedades mostradas selecionando o cabeçalho de coluna.  

![Execuções](media/logic-apps-manage-from-vs/runs.png)

Clique duas vezes em uma das instâncias de execução mostra o histórico de execução para a instância onde você pode ver os resultados da execução, incluindo as entradas e saídas de cada etapa.

![Histórico](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md) .  
* [Veja exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Você pode automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Aprenda a integrar seus sistemas com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


