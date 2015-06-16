<properties 
   pageTitle="Conector do Blob de Armazenamento do Azure" 
   description="Introdução ao Conector do Blob de Armazenamento do Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
#Conector do Blob de Armazenamento do Azure

##Visão geral
O conector do Blob de Armazenamento do Azure permite carregar, baixar e excluir blobs de um contêiner de blob.

##Criar um novo conector do Blob de Armazenamento do Azure
Para criar um novo conector de Armazenamento do Azure, siga as etapas abaixo. <ul> <li>Inicie o portal do Azure <li>Abra o Azure Marketplace usando +Novo (na parte inferior da página) -> Web+Móvel --> Azure Marketplace </ul>

![Iniciar o Azure Marketplace][1]
<br>
<ul>
<li>Clique em aplicativos de API
<li>Procure por <i>Blob</i> e selecione o Conector do Blob de Armazenamento do Azure
</ul>

![Selecionar o conector de Blob de Armazenamento do Azure][2]
<br>
<ul> 
<li>Clique em Criar
<li>Na folha do conector do Blob de Armazenamento do Azure que aparece e forneça os dados a seguir.
</ul>

![Criar o conector de Blob de Armazenamento do Azure][3]

- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
- **Camada de preços** - escolha uma camada de preços para o conector
- **Nome** - dê um nome ao Conector do Armazenamento de Blob
- **Configurações do pacote** 
	- **URI do contêiner/SAS** - especifique o URI do Contêiner de Blob. O URI também pode incluir o token SAS. Exemplo http://storageaccountname.blob.core.windows.net/containername ou http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Chave de acesso** - Especifique uma chave de acesso válida para a conta de armazenamento primária/secundária. Deixe este campo em branco se você estiver usando um token SAS para autenticação.
- Clique em Criar. Será criado um novo Conector do Blob de Armazenamento do Azure.

##Usar o Conector do Blob de Armazenamento do Azure no Aplicativo Lógico
Após ser criado, o conector do Blob de Armazenamento do Azure pode ser consumido no fluxo.

Crie um novo fluxo em +Novo -> Web+Móvel -> LogicApp. Forneça os metadados do fluxo, incluindo o grupo de recursos

![Criar aplicativo lógico][4]

Clique em *gatilhos e ações*. O designer de fluxo se abre

![Designer de fluxo vazio do aplicativo lógico][5]

O Conector do Blob de Armazenamento do Azure pode ser usado como uma ação.

###Ações
Clique no Conector do Blob de Armazenamento do Azure no painel à direita. O conector lista as ações com suporte.

![Lista de ações de Blob de armazenamento do Azure][10]

O Conector do Blob de Armazenamento do Azure dá suporte a quatro ações. São elas

- **Obter Blob** - Obtenha um blob específico do contêiner
- **Carregar Blob** - Carregue um novo blob ou atualize um existente
- **Excluir Blob** - Exclua um blob específico de um contêiner
- **Listar Blobs** - Liste todos os blobs de um diretório
- **Criar Instantâneo do Blob** - Crie um instantâneo somente leitura de um Blob específico
- **Copiar Blob** - Crie um novo Blob por meio da cópia de outro Blob. O Blob de origem pode estar na mesma conta ou em outra conta.

Vejamos um exemplo - Carregar Blob. Clique em Carregar Blob

![Entradas da ação Carregar Blob][11]


- **Caminho do Blob** - Especifica o caminho do Blob a ser carregado. O caminho é interpretado com relação ao caminho do Contêiner configurado.
- **Gravar Conteúdo do Blob** - Especifique o conteúdo e as propriedades do Blob a ser carregado.
- **Codificação de Transferência de Conteúdo** - Especifique nenhuma ou Base64
- **Substituir**- Se for definido como verdadeiro, o Blob existente será substituído. Caso contrário, retornará um erro se já existir um Blob no mesmo caminho.

Forneça as entradas e clique na marca de escala para concluir a configuração da entrada.


Observe que a ação configurada Carregar Blob do Blob de Armazenamento do Azure mostra os parâmetros de entrada e também os parâmetros de saída.

####Usando as saídas de ações anteriores como entradas para ações de Blob de Armazenamento do Azure
Observe que na captura de tela configurada, o valor de Conteúdo é definido como uma expressão.

	@triggers().outputs.body.Content


Você pode defini-lo como qualquer valor desejado. Este é apenas um exemplo. A expressão usa a saída do gatilho do aplicativo lógico e a usa como conteúdo do arquivo a ser carregado. Digamos que você queira usar a saída de uma ação anterior, como a transformação. Nesse caso, a expressão seria

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!---HONumber=54--> 