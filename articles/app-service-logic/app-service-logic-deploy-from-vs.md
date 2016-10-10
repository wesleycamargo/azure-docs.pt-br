<properties 
	pageTitle="Compilar Aplicativos Lógicos no Visual Studio | Microsoft Azure" 
	description="Crie um projeto no Visual Studio para criar e implantar seu aplicativo lógico." 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="jehollan"/>
	
# Compilar e implantar Aplicativos Lógicos no Visual Studio

Embora o [Portal do Azure](https://portal.azure.com/) lhe ofereça uma ótima maneira de criar e gerenciar seus aplicativos lógicos, talvez você também queira projetar e implantar seu aplicativo lógico por meio do Visual Studio. Os Aplicativos Lógicos vêm com um conjunto de ferramentas sofisticadas do Visual Studio que permite que você crie um aplicativo lógico usando o designer, configure os modelos de implantação e automação e implante em qualquer ambiente.

## Etapas de instalação

Veja a seguir as etapas para instalação e configuração das ferramentas do Visual Studio para Aplicativos Lógicos.

### Pré-requisitos

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
- [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)
- Acesso à Web ao usar o designer incorporado

### Instalar ferramentas do Visual Studio para Aplicativos Lógicos

Quando os pré-requisitos estiverem instalados,

1. Abra o Visual Studio 2015 no menu **Ferramentas** e selecione **Extensões e Atualizações**
1. Selecione a categoria **Online** para pesquisar online
1. Procure **Aplicativos Lógicos** para exibir as **Ferramentas de Aplicativos Lógicos do Azure para Visual Studio**
1. Clique no botão **Baixar** para baixar e instalar a extensão
1. Reinicie o Visual Studio após a instalação

> [AZURE.NOTE] Você também pode baixar a extensão diretamente [deste link](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Depois de instalado, você poderá usar o projeto do Grupo de Recursos do Azure com o Designer do Aplicativo Lógico.

## Criar um projeto

1. Vá até o menu **Arquivo** e selecione **Novo** > **Projeto** (ou então, você pode ir até **Adicionar** e selecionar **Novo projeto** para adicioná-lo a uma solução existente): ![Menu Arquivo](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. Na caixa de diálogo, encontre **Nuvem** e selecione **Grupo de Recursos do Azure**. Digite um **Nome** e, em seguida, clique em **OK**. ![Adicionar novo projeto](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Escolha o modelo **Aplicativo lógico**. Isso criará um modelo de implantação do aplicativo lógico em branco para começar. ![Selecionar modelo do Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Depois de selecionar o **Modelo**, aperte **OK**.

	Agora, seu projeto de aplicativo lógico foi adicionado à sua solução. Você deve ver o arquivo de implantação no Gerenciador de Soluções:

	![Implantação](./media/app-service-logic-deploy-from-vs/deployment.png)

## Como usar o Designer de Aplicativo Lógico

Quando você tiver um projeto do Grupo de Recursos do Azure que contenha um aplicativo lógico, você poderá abrir o designer no Visual Studio para ajudá-lo a criar o fluxo de trabalho. O designer exige uma conexão com a internet para consultar os conectores em busca de dados e propriedades disponíveis (por exemplo, se você usar o conector do Dynamics CRM Online, o designer consultará sua instância do CRM para listar as propriedades padrão e personalizadas disponíveis).

1. Clique com o botão direito no arquivo `<template>.json` e selecione **Abrir com o Designer de Aplicativo Lógico** (ou `Ctrl+L`)
1. Escolha a assinatura, grupo de recursos e local para o modelo de implantação
	- É importante observar que a criação de um aplicativo lógico criará recursos de **Conexão da API** para consultar propriedades durante o design. O grupo de recursos selecionado será o grupo de recursos usado para criar as conexões durante o design. Você pode exibir ou modificar as Conexões da API acessando o Portal do Azure e navegando até **Conexões de API**. ![Seletor de Assinatura](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. O designer deve ser processado com base na definição no arquivo `<template>.json`.
1. Agora você pode criar e projetar seu aplicativo lógico, e as alterações serão atualizadas no modelo de implantação. ![Designer no Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Você também verá os recursos de `Microsoft.Web/connections` sendo adicionados ao seu arquivo de recursos para todas as conexões necessárias ao funcionamento do aplicativo lógico. Essas propriedades de conexão podem ser definidas quando você implanta, e gerenciadas após a implantação nas **Conexões de API** no Portal do Azure.

### Alternar para a exibição de código do JSON

Você pode selecionar a guia **Exibição de Código** na parte inferior do designer para alternar para a representação JSON do aplicativo lógico. Para voltar ao JSON e seus recursos completos, clique com o botão direito no arquivo `<template>.json` e selecione **Abrir**.

### Como salvar o aplicativo lógico

Você pode salvar o aplicativo lógico quando quiser usando o botão **Salvar** ou `Ctrl+S`. Se houver erros no aplicativo lógico no momento em que você salvar, eles serão exibidos na janela **Saída** do Visual Studio.

## Implantação de seu aplicativo lógico

Finalmente, depois de configurar seu aplicativo, você pode implantar diretamente do Visual Studio em apenas algumas etapas.

1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e vá para **Implantar** > **Nova Implantação...** ![Nova implantação](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Você é solicitado a entrar em suas assinaturas do Azure.

3. Agora você precisa escolher os detalhes do grupo de recursos no qual você deseja implantar o aplicativo lógico. ![Implantar no grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Certifique-se de selecionar os arquivos certos de modelo e de parâmetros para o grupo de recursos (por exemplo, se você estiver implantando em um ambiente de produção, você preferirá escolher o arquivo de parâmetros de produção).
4. Escolha o botão Implantar
 
    
6. O status da implantação aparece na janela **Saída** (talvez você precise escolher **Provisionamento do Azure**). ![Saída](./media/app-service-logic-deploy-from-vs/output.png)

No futuro, você pode revisar seu aplicativo lógico no controle do código-fonte e usar o Visual Studio para implantar novas versões.

> [AZURE.NOTE] Se você modificar a definição diretamente no Portal do Azure, essas alterações serão substituídas na próxima vez em que você fizer uma implantação por meio do Visual Studio.

## Próximas etapas

- Para começar com aplicativos lógicos, siga o tutorial [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md).
- [Veja exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Você pode automatizar processos de negócios com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/T694)
- [Aprenda a integrar seus sistemas com aplicativos lógicos](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0928_2016-->