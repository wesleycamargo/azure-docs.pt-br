<properties 
	pageTitle="Implantação do Visual Studio | Microsoft Azure" 
	description="Crie um projeto no Visual Studio para gerenciar seu aplicativo lógico." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="stepsic"/>
	
# Implantar com o Visual Studio

Embora o [Portal do Azure](https://portal.azure.com/) lhe ofereça uma ótima maneira de criar e gerenciar seus aplicativos lógicos, você também pode desejar, em vez disso, implantar seu aplicativo lógico por meio Visual Studio. Há dois recursos-chave que essa ação habilita:

- Armazenar seu aplicativo lógico junto com os outros ativos em sua solução, de modo que ele possa conter todos os aspectos do seu aplicativo
- Manter sua definição de aplicativo lógico marcada no controle do código-fonte para que você possa usar o TFS ou Git para controlar revisões feitas a ele 

Você deve ter o Azure SDK 2.7 ou posterior instalado para que você siga as etapas abaixo. Encontre [o SDK mais recente para VS](https://azure.microsoft.com/downloads/) aqui.

## Criar um projeto

1. Vá até o menu **Arquivo** e selecione **Novo** > **Projeto** (ou então, você pode ir até **Adicionar** e selecionar **Novo projeto** para adicioná-lo a uma solução existente): ![Menu Arquivo](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. Na caixa de diálogo, encontre **Nuvem** e selecione **Grupo de Recursos do Azure**. Digite um **Nome** e, em seguida, clique em **OK**. ![Adicionar novo projeto](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. Agora você precisa selecionar se deseja um **Aplicativo lógico** ou **Aplicativo lógico e Aplicativo de API**. Selecionar **Aplicativo lógico** exige que você aponte para APIs existentes. Caso selecione **Aplicativo lógico e Aplicativo de API**, você também poderá criar, simultaneamente, um Aplicativo de API novo e vazio. Neste documento, selecionei o Aplicativo lógico. ![Selecionar modelo do Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. Depois de selecionar o **Modelo**, aperte **OK**.

Agora, seu projeto de aplicativo lógico foi adicionado à sua solução. Você deve ver a implantação no Gerenciador de Soluções: ![Implantação](./media/app-service-logic-deploy-from-vs/deployment.png)

## Configurando seu aplicativo lógico

Depois que você tiver um projeto, poderá editar a definição de seu aplicativo lógico no Visual Studio. Clique no arquivo JSON no Gerenciador de Soluções. Você vê uma definição de espaço reservado que pode preencher com a lógica do seu aplicativo.

Recomenda-se para usar **parâmetros** em toda a sua definição. Isso é útil se você deseja implantar tanto em um ambiente de desenvolvimento quanto em um ambiente de produção. Nesse caso, você deve colocar todas as configurações específicas de ambiente no arquivo `*.parameters.json`, e utilizar os parâmetros em vez das cadeias de caracteres em si.

Hoje, o Visual Studio não tem um designer interno de JSON; portanto, se você quer usar uma interface gráfica (em vez de escrever em JSON), use o Portal do Azure.

Se você criou um Aplicativo lógico no Portal do Azure e agora deseja fazer check-in dele para fazer o controle do código-fonte, há três maneiras de fazer isso:

- Vá até **Modo de Exibição de Código** no portal e copie a definição.
- Use a [API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) dos Aplicativos Lógicos para obter a definição.
- Use o [PowerShell do Azure Resource Manager](../powershell-azure-resource-manager.md), especificamente o [comando `Get-AzureResource`](https://msdn.microsoft.com/library/dn654579.aspx), para baixar a definição.

## Implantação de seu aplicativo lógico

Finalmente, depois de configurar seu aplicativo, você pode implantar diretamente do Visual Studio em apenas algumas etapas.

1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e vá para **Implantar** > **Nova Implantação...** ![Nova implantação](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Você é solicitado a entrar em suas assinaturas do Azure.

3. Agora você precisa escolher os detalhes do grupo de recursos no qual você deseja implantar o aplicativo lógico. ![Implantar no grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Certifique-se de selecionar os arquivos certos de modelo e de parâmetros para o grupo de recursos (por exemplo, se você estiver implantando em um ambiente de produção, você preferirá escolher o arquivo de parâmetros de produção). 
4.  Escolha o botão Implantar
    
5. O status da implantação aparece na janela **Saída** (talvez você precise escolher **Provisionamento do Azure**). ![Saída](./media/app-service-logic-deploy-from-vs/output.png)

No futuro, você pode revisar seu aplicativo lógico no controle do código-fonte e usar o Visual Studio para implantar novas versões.

> [AZURE.NOTE] Se você modificar a definição diretamente no Portal do Azure, essas alterações serão substituídas na próxima vez em que você fizer uma implantação por meio do Visual Studio.

> [AZURE.TIP] Se você não quiser usar o Visual Studio, mas ainda quiser usar ferramentas para implantar seu aplicativo lógico por meio do controle do código-fonte, use diretamente a [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) ou o [Powershell](../powershell-azure-resource-manager.md) para automatizar suas implantações.

<!---HONumber=AcomDC_0413_2016-->