<properties 
   pageTitle="Adicionando recursos a um grupo de recursos do Azure"
	description="Saiba como adicionar recursos a um grupo de recursos do Azure usando o Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags 
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/13/2015"
	ms.author="kempb"/>

# Adicionando recursos a um grupo de recursos do Azure

Se precisar adicionar mais recursos a um grupo de recursos, você pode fazer isso na janela Estrutura de Tópicos de JSON, no Visual Studio.

## Adicionando recursos a um grupo de recursos

### Para adicionar recursos a um grupo de recursos

1. No Gerenciador de Soluções, escolha o arquivo JSON para o grupo de recursos do Azure. O arquivo JSON aparece no editor e a janela **Estrutura de Tópicos JSON** também aparece ao lado do editor. Se você fechar a janela Estrutura de Tópicos JSON, ela não abrirá automaticamente novamente até que você escolha o comando Mostrar Estrutura de Tópicos no menu de contexto de um arquivo de modelo JSON (e não em um arquivo de parâmetros).

    ![Arquivo JSON para grupo de recursos do Azure](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. Na janela **Estrutura de Tópicos JSON**, escolha o nó **Recursos** e, por fim, escolha o comando **Adicionar Novo Recurso** no menu de contexto ou escolha o botão **Adicionar Recurso** na parte superior da janela Estrutura de Tópicos JSON.

    ![Adicionar um novo recurso a um grupo de recursos](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. Na lista de recursos na caixa de diálogo **Adicionar Recurso**, escolha o recurso que você deseja adicionar, forneça as informações necessárias para o recurso e, em seguida, escolha o botão **Adicionar**. Por exemplo, se você adicionar uma conta de armazenamento, você precisa fornecer um nome de base para os parâmetros que serão criados para você.
 
    ![Caixa de diálogo Adicionar Recursos](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    O recurso é adicionado aos recursos listados na janela **Estrutura de Tópicos JSON**, então, o novo JSON que representa o recurso aparece no arquivo. Parâmetros relacionados e/ou variáveis também podem ser adicionados.


    ![Recurso adicionado ao arquivo JSON](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Implante o novo recurso para o grupo de recursos do Azure. No menu de contexto do projeto de grupo de recursos no Gerenciador de Soluções, escolha **Implantar** e, em seguida, escolha o nome do grupo de recursos.

    ![Grupo de recursos do Azure implantado](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    A caixa de diálogo **Implantar no Grupo de Recursos** é exibida.


1. Escolha o botão **Implantar**.

1. Se nenhum parâmetro precisa ser especificado por você, a caixa de diálogo **Editar Parâmetros** é exibida. Insira todos os valores necessários e, em seguida, escolha o botão **Salvar**. O novo recurso é implantado em seu grupo de recursos do Azure.

## Consulte também

[Criando e implantando projetos de implantação de Grupo de Recursos do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Cmdlets do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/dn654592.aspx)

[Usando o Windows PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager/)

[Vídeo do Channel9: Gerenciador de Recursos do Azure](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=August15_HO9-->