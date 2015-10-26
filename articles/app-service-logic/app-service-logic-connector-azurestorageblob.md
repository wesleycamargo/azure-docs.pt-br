<properties 
   pageTitle="Usando o Conector de Blob de Armazenamento do Azure em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure" 
   description="Como criar e configurar o Conector de Blob de Armazenamento do Azure ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure" 
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
   ms.date="08/23/2015"
   ms.author="rajram"/>
   
# Introdução ao Conector de Blob de Armazenamento do Azure e à adição dele a seu Aplicativo Lógico 
Conecte-se ao blob de armazenamento do Azure para carregar, baixar e excluir blobs de um contêiner de blob. Os conectores são usados em Aplicativos Lógicos como parte de um "fluxo de trabalho".

## Gatilhos e Ações
*Gatilhos* são eventos que ocorrem. Por exemplo, quando um pedido é atualizado ou quando um novo cliente é adicionado. Uma *Ação* é o resultado do gatilho. Por exemplo, quando uma ordem é atualizada, enviar um alerta para o vendedor. Ou, quando um novo cliente for adicionado, enviar um email de boas-vindas para ele.

O conector de blob de armazenamento pode ser usado como uma ação em um aplicativo lógico e dá suporte a dados nos formatos JSON e XML. Atualmente, não há gatilhos disponíveis para o conector de blob de armazenamento.

O conector de blob de armazenamento tem os seguintes gatilhos e ações disponíveis:

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Obter blob: obter um blob específico do contêiner</li><li>Carregar blob: carregar um blob novo ou atualizar um blob existente</li><li>Excluir blob: excluir um blob específico de um contêiner</li><li>Listar blobs: listar todos os blobs em um diretório</li><li>Instantâneo do blob: criar um instantâneo somente leitura de um blob específico</li><li>Copiar blob: criar um novo blob por meio da cópia de outro blob. O blob de origem pode estar na mesma conta ou em outra conta.</li></ul>


## Criar o conector de blob de armazenamento do Azure

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Blob": ![Selecionar o conector de Blob de Armazenamento do Azure][2]

3. Selecione-o e selecione **Criar**.
4. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades.
5. Insira as seguintes configurações de pacote:

	Nome | Obrigatório | Descrição
--- | --- | ---
URI do contêiner/SAS | Sim | Digite o URI do contêiner do blob. O URI também pode incluir o token SAS. Por exemplo, insira http://*storageaccountname*.blob.core.windows.net/containername ou http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
Chave de acesso | Não | Insira uma chave de acesso da conta de armazenamento primária ou secundária válida. Deixe esse campo em branco se estiver usando token SAS para autenticação.

	![Criar o conector de Blob de Armazenamento do Azure][3]

6. Clique em **Criar**.

## Usar o Conector do Blob de Armazenamento do Azure no Aplicativo Lógico
Após a criação do conector de blob de armazenamento do Azure, ele poderá ser adicionado ao fluxo de trabalho.

1. Crie um novo aplicativo lógico: Novo -> Web+Móvel -> LogicApp. Insira as propriedades do aplicativo lógico: ![Criar aplicativo lógico][4]

2. Clique em **Gatilhos e ações**. O designer de fluxo de trabalho é aberto: ![Designer de fluxo vazio do aplicativo lógico][5]

3. Selecione o conector de blob de armazenamento do Azure no painel à direita. O conector lista as ações disponíveis: ![Lista de ações de Blob de armazenamento do Azure][10]

4. Neste cenário, usaremos a ação **Carregar Blob**: ![Entradas da ação Carregar Blob][11]

5. Insira os valores de entrada e selecione a marca de seleção para concluir a configuração:

	Entrada | Descrição
--- | ---
Caminho do Blob | Determina o caminho do blob a ser carregado. O caminho é interpretado com relação ao caminho do Contêiner configurado.
Gravar conteúdo do blob | Digite o conteúdo e as propriedades do blob a ser carregado.
Codificação de transferência de conteúdo | Digite none ou Base64.
Substituir | Quando definido como true, o blob existente é substituído. Quando definido como false, retornará um erro se já existir um blob no mesmo caminho.

Observe que a ação configurada Carregar Blob do Blob de Armazenamento do Azure mostra os parâmetros de entrada e também os parâmetros de saída.

#### Usando as saídas de ações anteriores como entradas para ações de Blob de Armazenamento do Azure
Na captura de tela anterior, o valor de **Conteúdo** pode ser uma expressão:

	@triggers().outputs.body.Content

Você pode defini-lo como qualquer valor desejado. A expressão usa a saída do gatilho do aplicativo lógico e a usa como conteúdo do arquivo a ser carregado. Por exemplo: você deseja usar a saída de uma transformação. Neste cenário, a expressão seria:

	@actions('transformservice').outputs.body.OutputXML

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
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
 

<!---HONumber=Oct15_HO3-->