<properties 
   pageTitle="Conector do Blob de Armazenamento do Azure" 
   description="Introdução ao Conector do Blob de Armazenamento do Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
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
   
# Conector do Blob de Armazenamento do Azure

## Visão geral
O conector do Blob de Armazenamento do Azure permite carregar, baixar e excluir blobs de um contêiner de blob.

## Criar um novo conector do Blob de Armazenamento do Azure
Para criar um novo conector de Armazenamento do Azure, siga as etapas abaixo.
- Inicie o portal do Azure
- Abra o Azure Marketplace usando +Novo (na parte inferior da página) -> Web+Móvel --> Azure Marketplace

![Launch Azure Marketplace][1]
- Clique em Aplicativos de API
- Pesquise _Blob_ e selecione o Conector do Blob de Armazenamento do Azure

![Select Azure Storage Blob Connector][2]
- Clique em Criar
- Na lâmina do conector do Blob de Armazenamento do Azure que aparece, forneça os dados a seguir.

![Create Azure Storage Blob Connector][3]

- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
- **Camada de preços** - escolha uma camada de preços para o conector
- **Nome** - dê um nome para o Conector de FTP
- **Configurações do pacote** 
	- **URI do contêiner/SAS** - especifique o URI do Contêiner de Blob.  O URI também pode incluir o token SAS .  Exemplo http://storageaccountname.blob.core.windows.net/containername ou http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Chave de acesso** - especifique uma chave de acesso válida para a conta de armazenamento primária/secundária.  Deixe este campo em branco se você estiver usando um token SAS para autenticação.
- Clique em Criar.  Será criado um novo Conector do Blob de Armazenamento do Azure.

## Usar o Conector do Blob de Armazenamento do Azure no Aplicativo Lógico
Após ser criado, o conector do Blob de Armazenamento do Azure pode ser consumido no fluxo.

Criar um novo fluxo em +Novo -> Web+Móvel -> LogicApp.  Forneça os metadados do fluxo, incluindo o grupo de recursos

![Create Logic App][4]

Clique em *triggers and actions*.  O designer de fluxo abre

![Logic App empty flow designer][5]

O Conector do Blob de Armazenamento do Azure pode ser usado como gatilho e ação. 

### Gatilho
No designer de fluxo vazio, clique no conector do Blob de Armazenamento do Azure do painel da galeria à direita.

![Choose Blob Available Trigger][6]

O Conector do Blob de Armazenamento do Azure tem um gatilho - _BlobAvailable_.  Esse gatilho sonda se há blobs presentes no Contêiner especificado.  Também há suporte para sondagem e filtragem de Blobs no nível de diretório.  Ele exclui blobs do Contêiner após eles serem recebidos

Clique no gatilho _BlobAvailable_.

![Basic inputs Blob Available Trigger][7]

As entradas ajudam a configurar um caminho de pasta específico a ser sondado com a frequência agendada.  As entradas básicas são
- Frequência - especifica a frequência da sondagem de FTP
- Intervalo - especifica o intervalo da frequência agendada
- Caminho da pasta - especifica um caminho de pasta virtual a ser sondado.  Use '.' para designar a pasta do contêiner raiz
- Tipo de Arquivo - especifica uma máscara de arquivo para comparar com os nomes dos arquivos de Blob.  Somente Blobs com nomes de arquivo correspondentes a essa máscara de arquivo serão incluídos na sondagem.  Por padrão, todos os blobs serão incluídos.

Clicar em ... mostra as entradas avançadas. 

![Advanced inputs Blob Available Trigger][8]

As entradas avançadas são

- Máscara de arquivo - especifica uma máscara de arquivo para comparar com os nomes dos arquivos de Blob.  Somente Blobs com nomes de arquivo correspondentes a essa máscara de arquivo serão incluídos na sondagem.  Por padrão, todos os blobs serão incluídos.
- Excluir máscara de arquivo - especifica uma máscara de arquivo para comparar com os nomes dos arquivos de Blob.  Blobs correspondentes a essa máscara de arquivo serão excluídos.  Por padrão, nenhum blob é excluído.

Forneça as entradas e clique na marca de escala para concluir a configuração das entradas.

![Configured Blob Available Trigger][9]

Observe que o gatilho configurado _Blob Available_ mostra os parâmetros de entrada configurados e também os parâmetros de saída. 

Após a criação do aplicativo lógico, o gatilho _Blob Available_ 


- Sonda se há novos arquivo no caminho de pasta
- Cria uma instância no fluxo lógico sempre que há um novo arquivo
- Exclui o arquivo do caminho de pasta após a criação da instância no fluxo lógico

#### Usando a saída do gatilho de Blob Disponível em ações subsequentes
A saída do gatilho _Blob Available_ pode ser usada como entrada de algumas outras ações no fluxo. 

Você pode clicar em + na caixa de diálogo de entrada de ação e selecionar a saída de FTP diretamente na caixa suspensa.

Você também pode escrever uma expressão diretamente na caixa de diálogo de entrada de ação.  A expressão de fluxo para referir-se à saída do gatilho é indicada a seguir

	@triggers().outputs.body.Content

### Ações
Clique no Conector do Blob de Armazenamento do Azure no painel à direita.  O conector lista as ações com suporte.

![List of Azure Storage Blob Actions][10]

O Conector do Blob de Armazenamento do Azure dá suporte a quatro ações.  Elas são

- **Obter Blob** - obtenha um blob específico do contêiner
- **Carregar Blob** - carregue um blob novo ou atualize um existente
- **Excluir Blob** - exclua um blob específico de um contêiner
- **Listar Blobs** -liste todos os blobs de um diretório
- **Instantâneo do Blob** - crie um instantâneo somente leitura de um blob específico
- **Copiar Blob** - crie um novo blob por meio da cópia de outro blob.  O blob de origem pode estar na mesma conta ou em outra conta.

Vejamos um exemplo - Carregar Blob.  Clique em Carregar Blob

![Inputs of Upload Blob action][11]


- **Caminho do Blob** - especifique o caminho do blob a ser carregado.  O caminho é interpretado com relação ao caminho do contêiner configurado.
- **Gravar Conteúdo do Blob** - especifique o conteúdo e as propriedades do blob a ser carregado.
- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
- **Substituir** - se for definido como verdadeiro, o blob existente será substituído.  Caso contrário, retornará um erro se já existir um blob no mesmo caminho.

Forneça as entradas e clique na marca de escala para concluir a configuração das entradas.


Observe que a ação configurada de Carregar Blob do Blob de Armazenamento do Azure mostra os parâmetros de entrada e também os parâmetros de saída.

#### Usando as saídas de ações anteriores como entrada para a ação de FTP
Observe que na captura de tela configurada, o valor de Conteúdo é definido como uma expressão.

	@triggers().outputs.body.Content


Você pode defini-lo como qualquer valor desejado.  Este é apenas um exemplo.  A expressão leva a saída do gatilho do aplicativo lógico e a usa como conteúdo do arquivo a ser carregado.  Digamos que você queira usar a saída de uma ação anterior, como a transformação.  Nesse caso, a expressão seria

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

<!--HONumber=49-->