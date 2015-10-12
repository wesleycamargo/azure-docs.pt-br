<properties 
	pageTitle="Galerias de runbook e de módulos para a Automação do Azure"
	description="Os runbooks e os módulos da Microsoft e da comunidade estão disponíveis para instalação e uso em seu ambiente da Automação do Azure. Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a Galeria."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />


# Galerias de runbook e de módulos para a Automação do Azure

Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar uma variedade de soluções que já foram desenvolvidas pela Microsoft e pela comunidade. É possível usar essas soluções sem modificação ou usá-las como um ponto de partida e editá-las para seus requisitos específicos.

Você pode obter runbooks da [Galeria de Runbook](#runbooks-in-runbook-gallery) e módulos da [Galeria do PowerShell](#modules-in-powerShell-gallery). Você também pode contribuir com a comunidade compartilhando as soluções que desenvolve.

## Runbooks na Galeria de Runbook

A [Galeria de Runbook](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) fornece uma variedade de runbooks da Microsoft e da comunidade que você pode importar para a Automação do Azure. Você pode baixar um runbook da galeria hospedada no [Script Center do TechNet](http://gallery.technet.microsoft.com/) ou pode importar runbooks diretamente da galeria do portal do Azure e do portal de visualização do Azure.

Você só pode importar diretamente da Galeria de Runbook usando o portal do Azure ou o portal de visualização do Azure. Não é possível executar essa função usando o Windows PowerShell.

>[AZURE.NOTE]Você deverá validar o conteúdo de todos os runbooks obtidos da Galeria de Runbook e tomar muito cuidado ao instalá-los e executá-los em um ambiente de produção.|

### Para importar um runbook da Galeria de Runbook com o portal do Azure

1. No portal de Gerenciamento do Azure, clique em **Novo**, **Serviços de Aplicativos**, **Automação**, **Runbook**, **Da Galeria**.
2. Selecione uma categoria para exibir os runbooks relacionados e selecione um runbook para exibir seus detalhes. Quando você selecionar o runbook desejado, clique no botão de seta para a direita.<br> ![Galeria de Runbook](media/automation-runbook-gallery/runbook-gallery.png)
3. Examine o conteúdo do runbook e observe todos os requisitos na descrição. Clique no botão com a seta para a direita quando concluir.
4. Insira os detalhes do runbook e clique no botão com a marca de seleção. O nome do runbook já estará preenchido.
5. O runbook aparecerá na guia **Runbooks** para a Conta de Automação.

### Para importar um runbook da Galeria de Runbook com o portal de visualização do Azure

1. No Portal de Visualização do Azure, abra sua conta de Automação. 
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Procurar na galeria**. <br> ![Botão Procurar na galeria](media/automation-runbook-gallery/browse-gallery-button.png)
4. Localize o item da galeria desejado e selecione-o para exibir seus detalhes. <br> ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)
4. Clique em **Exibir projeto de origem** para exibir o item no [Script Center do TechNet](http://gallery.technet.microsoft.com/).
5. Para importar um item, clique nele para exibir seus detalhes e clique no botão **Importar**.<br> ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)
6. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
5. O runbook aparecerá na guia **Runbooks** para a Conta de Automação.


### Adicionando um runbook à galeria de runbook

A Microsoft incentiva você a adicionar à Galeria de Runbook os runbooks que, em sua opinião, seriam úteis para outros clientes. Você pode adicionar um runbook [carregando-o no Script Center](http://gallery.technet.microsoft.com/site/upload) levando em consideração os detalhes a seguir.

- Você deve especificar o *Microsoft Azure* para a **Categoria** e *Automação* para a **Subcategoria** do runbook a ser exibida no assistente.  

- O carregamento deve ser um único arquivo .ps1 ou .graphrunbook. Se o runbook exigir módulos, runbooks filhos ou ativos, você deverá listá-los na descrição do envio e na seção de comentários do runbook. Se você tiver uma solução que exija vários runbooks, carregue cada um separadamente e liste os nomes dos runbooks relacionados em cada uma das descrições. Use as mesmas marcas de forma que elas sejam mostradas na mesma categoria. Um usuário terá de ler a descrição para saber quais outros runbooks serão necessários para que a solução funcione.

- O Resumo do carregamento será exibido nos resultados da Galeria de Runbook e, portanto, você deverá fornecer informações detalhadas que ajudarão um usuário a identificar a funcionalidade do runbook.

- Você deve atribuir ao carregamento de uma a três das Marcas a seguir. O runbook será listado no assistente em categorias que correspondam às suas marcas. As marcas que não estiverem nessa lista serão ignoradas pelo assistente. Se você não especificar nenhuma marca correspondente, o runbook será listado em Outra categoria.

 - Backup
 - Gerenciamento de Capacidade
 - Controle de Alterações
 - Conformidade
 - Ambientes de Desenvolvimento/Teste
 - Recuperação de desastre
 - Monitoramento
 - Aplicação de patch
 - Provisionamento
 - Correção
 - Gerenciamento do ciclo de vida de VMs


- A automação atualiza a Galeria uma vez por hora e, portanto, você não verá suas contribuições imediatamente. Se você não vir seu runbook na galeria após uma hora, verifique os requisitos na seção [Adicionando um runbook à Galeria de Runbook](#AddRunbook).

## Módulos na Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](http://www.powershellgallery.com). Você pode iniciar esta galeria no portal de visualização do Azure e instalá-los diretamente na Automação do Azure ou pode baixá-los e instalá-los manualmente. Não é possível instalar os módulos diretamente do portal do Azure, mas você pode baixá-los e instalá-los como o faria com qualquer módulo.

### Para importar um módulo da Galeria do PowerShell com o portal de visualização do Azure

1. No Portal de Visualização do Azure, abra sua conta de Automação. 
2. Clique no bloco **Ativos** para abrir a lista de ativos.
3. Clique no bloco **Módulos** para abrir a lista de módulos.
3. Clique no botão **Galeria do PowerShell** para iniciar a Galeria do PowerShell em outra janela do navegador. <br> ![Galeria do PowerShell](media/automation-runbook-gallery/powershell-gallery-button.png)
4. Clique no menu **Módulos** para acessar a lista de módulos disponíveis.<br> ![Botão Galeria do PowerShell](media/automation-runbook-gallery/powershell-gallery.png)
4. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.
5. Para instalar o módulo diretamente na Automação do Azure, clique no botão **Implantar na Automação do Azure**.<br> ![Botão Galeria do PowerShell](media/automation-runbook-gallery/powershell-gallery-detail.png)
6. Você voltará para o portal de visualização do Azure em um painel **Implantação personalizada**. Especifique se você instalará o módulo em uma **Conta de Automação Nova e Existente** e o **Nome da Conta de Automação**. O **Local da Conta de Automação** será ignorado se você usar uma conta existente. 
7. Selecione **Grupo de recursos** e especifique um grupo de recursos existente ou crie um novo para o módulo.
6. Selecione **Termos legais** e clique em **Comprar**. Observe que, apesar do nome desse botão, você não será realmente cobrado pela instalação de um módulo.
7. Clique em **Criar** para importar o módulo.  
8. Você receberá uma notificação de que o módulo está sendo implantado e uma notificação quando a implantação for concluída. 


## Solicitando um runbook ou um módulo

Você pode enviar solicitações para [User Voice] (http://feedback.azure.com/[forum](http://social.msdn.microsoft.com/Forums/windowsazure/pt-BR/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)s/246290-azure-automation). Se precisar de ajuda para escrever um runbook ou se tiver uma pergunta sobre o PowerShell, poste uma pergunta em nosso [fórum](http://social.msdn.microsoft.com/Forums/windowsazure/pt-BR/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Artigos relacionados

- [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
- [Aprendendo o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)

<!---HONumber=Oct15_HO1-->