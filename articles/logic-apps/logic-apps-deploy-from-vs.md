---
title: "Compilar Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Crie um projeto no Visual Studio para criar e implantar seu aplicativo lógico."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: db818b2b76d3a8d8c3324c9556237139d319efb9


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Compilar e implantar Aplicativos Lógicos no Visual Studio
Embora o [portal do Azure](https://portal.azure.com/) lhe ofereça uma ótima maneira de criar e gerenciar seus aplicativos lógicos, talvez você também queira projetar e implantar seu aplicativo lógico por meio do Visual Studio.  Os Aplicativos Lógicos vêm com um conjunto de ferramentas sofisticadas do Visual Studio que permite que você crie um aplicativo lógico usando o designer, configure os modelos de implantação e automação e implante em qualquer ambiente.  

## <a name="installation-steps"></a>Etapas de instalação
Veja a seguir as etapas para instalação e configuração das ferramentas do Visual Studio para Aplicativos Lógicos.

### <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)
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

Depois de instalado, você poderá usar o projeto do Grupo de Recursos do Azure com o Designer do Aplicativo Lógico.

## <a name="create-a-project"></a>Criar um projeto
1. Vá até o menu **Arquivo** e selecione **Novo** >  **Projeto** (ou então, você pode ir até **Adicionar** e selecionar **Novo projeto** para adicioná-lo a uma solução existente):  ![menu Arquivo](./media/logic-apps-deploy-from-vs/filemenu.png)
2. Na caixa de diálogo, encontre **Nuvem** e selecione **Grupo de Recursos do Azure**. Digite um **Nome** e, em seguida, clique em **OK**.
    ![Adicionar novo projeto](./media/logic-apps-deploy-from-vs/addnewproject.png)
3. Escolha o modelo **Aplicativo lógico** . Isso cria um modelo de implantação do aplicativo lógico em branco para começar.
    ![Selecionar modelo do Azure](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)
4. Depois de selecionar o **Modelo**, aperte **OK**.
   
    Agora, seu projeto de aplicativo lógico foi adicionado à sua solução. Você deve ver o arquivo de implantação no Gerenciador de Soluções:  
   
    ![Implantação](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Como usar o Designer de Aplicativo Lógico
Quando você tiver um projeto do Grupo de Recursos do Azure que contenha um aplicativo lógico, você poderá abrir o designer no Visual Studio para ajudá-lo a criar o fluxo de trabalho.  O designer exige uma conexão com a internet para consultar os conectores em busca de dados e propriedades disponíveis (por exemplo, se você usar o conector do Dynamics CRM Online, o designer consultará sua instância do CRM para listar as propriedades padrão e personalizadas disponíveis).

1. Clique com o botão direito no arquivo `<template>.json` e selecione **Abrir com o Designer de Aplicativo Lógico** (ou `Ctrl+L`)
2. Escolha a assinatura, grupo de recursos e local para o modelo de implantação
   * É importante observar que a criação de um aplicativo lógico criará recursos de **Conexão da API** para consultar propriedades durante o design.  O grupo de recursos selecionado é usado para criar as conexões durante o design.  Você pode exibir ou modificar as Conexões da API acessando o portal do Azure e navegando até **Conexões de API**.
   
     ![Seletor de Assinatura](./media/logic-apps-deploy-from-vs/designer_picker.png)
3. O designer deve ser processado com base na definição no arquivo `<template>.json` .
4. Agora você pode criar e projetar seu aplicativo lógico, e as alterações são atualizadas no modelo de implantação.
    ![Designer no Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Os recursos do `Microsoft.Web/connections` são adicionados ao seu arquivo de recursos para todas as conexões necessárias ao funcionamento do aplicativo lógico.  Essas propriedades de conexão podem ser definidas quando você implanta, e gerenciadas após a implantação nas **Conexões de API** no portal do Azure.

### <a name="switching-to-the-json-code-view"></a>Alternar para a exibição de código do JSON
Você pode selecionar a guia **Exibição de Código** na parte inferior do designer para alternar para a representação JSON do aplicativo lógico.  Para voltar ao JSON e seus recursos completos, clique com o botão direito no arquivo `<template>.json` e selecione **Abrir**.

### <a name="saving-the-logic-app"></a>Como salvar o aplicativo lógico
Você pode salvar o aplicativo lógico quando quiser usando o botão **Salvar** ou `Ctrl+S`.  Se houver erros no aplicativo lógico no momento em que você salvar, eles serão exibidos na janela **Saída** do Visual Studio.

## <a name="deploying-your-logic-app"></a>Implantação de seu aplicativo lógico
Finalmente, depois de configurar seu aplicativo, você pode implantar diretamente do Visual Studio em apenas algumas etapas. 

1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e vá para **Implantar** > **Nova Implantação...**
    ![Nova implantação](./media/logic-apps-deploy-from-vs/newdeployment.png)
2. Você é solicitado a entrar em suas assinaturas do Azure. 
3. Agora você precisa escolher os detalhes do grupo de recursos no qual você deseja implantar o aplicativo lógico. 
    ![Implantar no grupo de recursos](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > Certifique-se de selecionar os arquivos certos de modelo e de parâmetros para o grupo de recursos (por exemplo, se você estiver implantando em um ambiente de produção, você preferirá escolher o arquivo de parâmetros de produção). 
   > 
   > 
4. Escolha o botão Implantar
5. O status da implantação aparece na janela **Saída** (talvez você precise escolher **Provisionamento do Azure**. 
    ![Saída](./media/logic-apps-deploy-from-vs/output.png)

No futuro, você pode revisar seu aplicativo lógico no controle do código-fonte e usar o Visual Studio para implantar novas versões. 

> [!NOTE]
> Se você modificar a definição diretamente no portal do Azure, essas alterações serão substituídas na próxima vez em que você fizer uma implantação por meio do Visual Studio.
> 
> 

## <a name="adding-a-logic-app-to-an-existing-resource-group-project"></a>Adicionar um Aplicativo Lógico a um projeto existente do Grupo de Recursos
Se você tiver um projeto existente do Grupo de Recursos, adicione um aplicativo lógico para ele ou adicionar outro aplicativo lógico junto com aquele que você criou anteriormente, pode ser feito através da janela de estrutura de tópicos JSON.
1. Abra o arquivo `<template>.json` .
2. Abra a janela Estrutura de Tópicos JSON.  A janela de estrutura de tópicos JSON pode ser encontrada em **Exibir** > **Outras Janelas** > **Estrutura de Tópicos JSON**.
3. Para adicionar um recurso ao arquivo de modelo, clique no botão Adicionar Recurso na parte superior da janela de Estrutura de Tópicos JSON ou com o botão direito do mouse em **recursos** e selecione **Adicionar Novo Recurso**.

    ![Estrutura de tópicos Json](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Na caixa de diálogo **Adicionar Recurso**, procure e selecione **Aplicativo Lógico**, atribua um nome e selecione **Adicionar**.

    ![Adicionar recurso](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](logic-apps-create-a-logic-app.md) .  
* [Veja exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Você pode automatizar processos de negócios com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Aprenda a integrar seus sistemas com Aplicativos Lógicos](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


