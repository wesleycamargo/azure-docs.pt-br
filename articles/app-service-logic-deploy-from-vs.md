<properties 
	pageTitle="Implantar com o Visual Studio" 
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
	ms.date="04/26/2015"
	ms.author="stepsic"/>
	
# Implantar com o Visual Studio

Embora o [Portal do Azure](https://portal.azure.com) lhe ofereça uma ótima maneira de criar e gerenciar seus aplicativos lógicos, você também pode desejar, em vez disso, implantar seu aplicativo lógico por meio Visual Studio. Há dois recursos-chave que essa ação habilita:

- Armazenar seu aplicativo lógico junto com os outros ativos em sua solução, de modo que ele possa conter todos os aspectos do seu aplicativo
- Manter sua definição de aplicativo lógico marcada no controle do código-fonte para que você possa usar o TFS ou Git para controlar revisões feitas a ele 

Você precisa ter o SDK do Azure 2.6 instalado para poder seguir as etapas abaixo. Encontre [o SDK mais recente para VS](http://azure.microsoft.com/downloads/) aqui.

## Criar um projeto

1. Vá até o menu **Arquivo** e selecione **Novo** \> **Projeto** \(ou então, você pode ir até **Adicionar** e, em seguida, selecionar **Novo projeto** para adicioná-lo a uma solução existente\) ![Menu Arquivo](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. Na caixa de diálogo, encontre **Nuvem** e, em seguida, selecione **Grupo de Recursos do Azure**. Digite um **Nome** e, em seguida, clique em **OK**. ![Adicionar novo projeto](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. Agora você precisa selecionar se deseja um **Aplicativo lógico** ou **Aplicativo lógico + aplicativo de API**. Selecionar **Aplicativo lógico** exige que você aponte para APIs existentes. Se você selecionar **Aplicativo lógico + aplicativo de API**, você também pode criar, simultaneamente, uma API nova e vazia. ![Selecionar modelo do Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. Depois que você tiver selecionado o **Modelo**, aperte **OK**.

Agora seu projeto de Aplicativo lógico será adicionado à sua solução. Você deve ver a implantação no Gerenciador de Soluções: ![Implantação](./media/app-service-logic-deploy-from-vs/deployment.png)

## Configurando seu aplicativo lógico

Depois que você tiver um projeto, você pode editar a definição de seu aplicativo lógico no VS. Clique no arquivo JSON no Gerenciador de Soluções. Você verá uma definição de espaço reservado que você pode preencher com a lógica do seu aplicativo.

Recomenda-se para usar **parâmetros** em toda a sua definição. Isso será útil se você deseja implantar tanto em um ambiente de desenvolvimento quanto em um ambiente de produção. Nesse caso, você deve colocar todas as configurações específicas de ambiente no arquivo `.param`, e utilizar os parâmetros em vez das cadeias de caracteres em si.

Hoje, o Visual Studio não tem um designer interno, portanto, se você quiser usar uma interface gráfica \(em vez de escrever JSON\), você precisará usar o Portal do Azure.

Se você criou um aplicativo lógico anteriormente no Portal do Azure e agora deseja verificá-lo no controle do código-fonte, você pode: - Navegar até **Visualização de código** no portal e copiar a definição. - Usar a [API REST](https://msdn.microsoft.com/library/azure/dn948510.aspx) dos aplicativos lógicos para obter a definição. - Usar o [Powershell do Gerenciador de recursos do Azure](powershell-azure-resource-manager.md), especificamente o [comando `Get-AzureResource`](https://msdn.microsoft.com/library/dn654579.aspx), para baixar a definição.

## Implantação de seu aplicativo lógico

Finalmente, depois de configurar seu aplicativo, você pode implantar diretamente do Visual Studio em apenas algumas etapas.

1. Clique com o botão direito na implantação no Gerenciador de Soluções e vá para **Implantar** \> **Nova implantação...** ![Nova implantação](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Será solicitado que você faça logon em sua\(s\) assinatura\(s\) do Azure.

3. Agora você precisa escolher os detalhes do grupo de recursos no qual você deseja implantar o aplicativo lógico. ![Implantar no grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    Certifique-se de selecionar os arquivos certos de modelo e de parâmetros para o grupo de recursos \(por exemplo, se você estiver implantando em um ambiente de produção, você preferirá escolher o arquivo de parâmetros de produção\).
    
4. O status da implantação aparecerá na janela **Saída** \(talvez você precise escolher **Provisionamento do Azure**. ![Saída](./media/app-service-logic-deploy-from-vs/output.png)

No futuro, você pode revisar seu aplicativo lógico no controle do código-fonte e usar o Visual Studio para implantar novas versões. Observe que se você modificar diretamente a definição no Portal do Azure, na próxima vez que você implantar por meio do Visual Studio essas alterações serão substituídas.

Se você não deseja usar o Visual Studio, mas ainda deseja ter ferramentas para implantar seu aplicativo lógico por meio do controle do código-fonte, você pode sempre utilizar diretamente a [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) ou o [Powershell](powershell-azure-resource-manager.md) para automatizar suas implantações.
<!--HONumber=54-->