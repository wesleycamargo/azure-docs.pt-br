---
title: Construir aplicativos sem servidor com o Visual Studio | Microsoft Docs
description: Construir, implantar e gerenciar seu primeiro aplicativo sem servidor com os Aplicativos Lógicos do Azure e o Azure Functions no Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.openlocfilehash: f5555d9a60934529bf8fed6db6a18dd783f46075
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297461"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Construir seu primeiro aplicativo sem servidor com os Aplicativos Lógicos do Azure e o Azure Functions - Visual Studio

É possível rapidamente desenvolver e implantar aplicativos de nuvem usando as ferramentas sem servidor e os recursos no Azure, como [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Azure Functions](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir um aplicativo sem servidor, o qual usa um aplicativo lógico que chama uma função do Azure, no Visual Studio. Para saber mais sobre as soluções sem servidor no Azure, consulte [Azure sem servidor com funções e aplicativos lógicos](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para construir um aplicativo sem servidor no Visual Studio, serão necessários estes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* [Visual Studio 2017](https://www.visualstudio.com/vs/) ou Visual Studio 2015 – Community, Professional ou Enterprise

* [SDK do Microsoft Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou posterior)

* [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)

* [Ferramentas de Aplicativo Lógico do Azure para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) ou a [Versão do Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)

  Você pode baixar e instalar as Ferramentas dos Aplicativos Lógicos do Azure diretamente do Visual Studio Marketplace ou aprender como [instalar essa extensão de dentro do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
  Reinicie o Visual Studio após concluir a instalação. 

* [Ferramentas Básicas do Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) para depurar Funções localmente

* Acesso à Web ao usar o Designer do Aplicativo Lógico incorporado no Visual Studio

  O Designer requer uma conexão de Internet para criar recursos no Azure e ler as propriedades e os dados dos conectores em seu aplicativo lógico. 
  Por exemplo, se você utilizar o conector do Dynamics CRM Online, o Designer verifica sua instância CRM para propriedades padrão e personalizadas disponíveis.

## <a name="create-resource-group-project"></a>Criar um projeto do grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) para o aplicativo sem servidor. No Azure, você cria recursos dentro de um grupo de recursos, que é uma coleção lógica utilizada para organizar, gerenciar e implementar recursos em um aplicativo inteiro como um único ativo. Para um aplicativo sem servidor no Azure, o grupo de recursos contém recursos para os Aplicativos Lógicos do Azure e Azure Functions. Saiba mais sobre [Recursos e grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Inicie o Visual Studio e entre com sua conta do Azure. 

1. No menu **Arquivo**, selecione **Novo** > **Projeto**. 

   ![Criar um novo projeto no Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Selecione **Nuvem** > **Grupo de Recursos do Azure**.

   Se a categoria **Nuvem** ou o projeto **Grupo de Recursos do Azure** não existir, certifique-se de ter instalado o SDK do Azure para Visual Studio.

1. Dê ao projeto um nome e um local e, em seguida, escolha **OK**. 

   O Visual Studio solicita que você selecione um modelo. 
   É possível iniciar com um modelo em branco, o Aplicativo Lógico ou outro modelo, mas este exemplo usa um Modelo de Início Rápido do Azure para construir um aplicativo sem servidor que inclui um aplicativo lógico e uma chamada para uma função do Azure.

   Para criar apenas um aplicativo lógico no Visual Studio, selecione o modelo **Aplicativo Lógico**. Este modelo cria um aplicativo lógico vazio que é aberto no Designer do Aplicativo Lógico sem precisar pré-implantar a solução em um grupo de recursos do Azure.

1. Em **Mostrar modelos deste local**, selecione **Início Rápido do Azure (github/Azure/azure-quickstart-templates)**. 

1. Na caixa de pesquisa, digite "aplicativo lógico" como filtro, selecione este modelo de início rápido sem servidor e escolha **OK**: **101-logic-app-and-function-app**

   ![Selecionar modelo de início rápido do Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o projeto do grupo de recursos. 
   O modelo de início rápido selecionado cria um modelo de implantação nomeado `azuredeploy.json` dentro do projeto do grupo de recursos. 
   Esse modelo de implantação inclui a definição para um aplicativo lógico simples que dispara em uma solicitação HTTP, chama uma função do Azure e retorna o resultado como uma resposta HTTP. 
   
   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, é necessário implantar a solução no Azure antes de abrir o modelo de implantação e examinar os recursos para o aplicativo sem servidor. 

## <a name="deploy-your-solution"></a>Implantar sua solução

Antes de abrir o aplicativo lógico com o Designer do Aplicativo Lógico no Visual Studio, é necessário ter um grupo de recursos do Azure que já esteja implantado no Azure. O designer então pode criar conexões com recursos e serviços no aplicativo lógico. Para essa tarefa, implante a solução do Visual Studio para o portal do Azure.

1. No Gerenciador de Soluções, abra o menu de atalho do projeto de recurso e, em seguida, selecione **Implantar** > **Novo**.

   ![Criar nova implantação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se ainda não estiver selecionado, selecione sua assinatura do Azure e o grupo de recursos para o qual você quer implantar. Escolha **Implantar**.

   ![Configurações de implantação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se a caixa **Editar Parâmetros** for exibida, forneça o nome do recurso a ser usado para o aplicativo lógico e o aplicativo de funções do Azure e depois salve as configurações. Use um nome globalmente exclusivo para o aplicativo de funções.

   ![Fornecer nomes para o aplicativo lógico e o aplicativo de funções](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio inicia a implantação para o grupo de recursos especificado, o status da implantação da solução aparece na janela **Saída** do Visual Studio. 
   Depois que a implantação for concluída, o aplicativo lógico ficará ativo no portal do Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Editar o aplicativo lógico no Visual Studio

Agora que a solução está implantada no grupo de recursos, abra o aplicativo lógico com o Designer do Aplicativo Lógico para que você possa editar e alterar o aplicativo lógico.

1. No Gerenciador de Soluções, abra o menu de atalho do arquivo `azuredeploy.json` e selecione **Abrir com Designer do Aplicativo Lógico**.

   ![Abrir "azuredeploy.json" no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Depois que a caixa **Propriedades do Aplicativo Lógico** for exibida e, se ainda não estiver selecionada, em **Assinatura**, selecione a sua assinatura do Azure. Em **Grupo de Recursos**, selecione o grupo de recursos e o local onde você implantou a solução e, em seguida, escolha **OK**.

   ![Propriedades do aplicativo lógico](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Quando o Designer do Aplicativo Lógico for aberto, é possível continuar a adicionar etapas ou alterar o fluxo de trabalho e salvar as atualizações.

   ![Aplicativo lógico aberto no Designer do Aplicativo Lógico](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Criar um projeto do Azure Functions

Para criar o projeto do Functions e a função com JavaScript, Python, F#, PowerShell, Lote ou Bash, siga as etapas no artigo [Trabalhar com as Ferramentas Básicas do Azure Functions](../azure-functions/functions-run-local.md). Para desenvolver a função do Azure com C# dentro da solução, é possível usar uma biblioteca de classes do C#, seguindo as etapas no artigo [Publicar uma biblioteca de classes do .NET como um Aplicativo de funções](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Implantar funções a partir do Visual Studio

O modelo de implantação implanta todas as funções do Azure que você tem em sua solução do repositório Git que é especificado pelas variáveis no arquivo `azuredeploy.json`. Se você criar e for autor do projeto do Functions na solução, será possível verificar o projeto no controle do código-fonte Git, por exemplo, GitHub ou Azure DevOps e, em seguida, atualizar a variável `repo` para que o modelo implante a função do Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerenciar os aplicativos lógicos exibir o histórico de execuções

Para aplicativos lógicos já implantados no Azure, ainda é possível editar, gerenciar, exibir o histórico de execuções e desabilitar esses aplicativos a partir do Visual Studio. 

1. No menu **Exibir** no Visual Studio, abra **Cloud Explorer**. 

1. Em **Todas as assinaturas**, selecione a assinatura do Azure associada aos aplicativos lógicos que você quer gerenciar e, em seguida, escolha **Aplicar**.

1. Em **Aplicativos Lógicos**, selecione o aplicativo lógico. No menu de atalho desse aplicativo, selecione **Abrir com o Editor do Aplicativo Lógico**. 

Agora você pode baixar o aplicativo lógico já publicado no projeto do grupo de recursos. Assim, mesmo que você tenha iniciado um aplicativo lógico no portal do Azure, ainda é possível importar e gerenciar esse aplicativo no Visual Studio. Para obter mais informações, consulte [Gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Próximas etapas

* [Construir um painel social sem servidor](logic-apps-scenario-social-serverless.md)
* [Gerenciar aplicativos lógicos no Visual Studio](manage-logic-apps-with-visual-studio.md)
* [Linguagem de definição de fluxo de trabalho de aplicativo lógico](logic-apps-workflow-definition-language.md)