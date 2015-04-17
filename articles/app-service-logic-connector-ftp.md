<properties 
	pageTitle="Conector de FTP"
	description="Introdução ao Conector de FTP"
	authors="rajeshramabathiran" 
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
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

# Conector de FTP

## Visão geral
O Conector de FTP permite mover dados de/para um servidor FTP.  Os principais recursos do Conector de FTP são:

- Extrair arquivos do servidor FTP sob demanda
- Executar sondagens com base em um cronograma configurável
- Executar sondagens no servidor FTP e acionar o fluxo de lógica com base em novos documentos no servidor
- Especificar o servidor FTP com um endereço IP, porta, senha e nome de host
- Capacidade de fazer envios sob demanda
- Capacidade de excluir arquivos do servidor FTP sob demanda

## Criar um novo conector de FTP
Para criar um novo conector de FTP, siga as etapas abaixo.
- Inicie o portal do Azure
- Abra o Azure Marketplace usando +Novo (na parte inferior da página) -> Web+Móvel --> Azure Marketplace

![Launch Azure Marketplace][1]
- Clique em Aplicativos de API
- Pesquise FTP e selecione o Conector de FTP

![Select FTP Connector][2]
- Clique em Criar
- Na lâmina do conector de FTP que aparece, forneça os dados a seguir.

![Create FTP Connector][3]

- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
- **Camada de preços** - escolha uma camada de preços para o conector
- **Nome** - dê um nome para o Conector de FTP
- **Configurações do pacote** 
	- **Endereço do servidor** - especifique o nome ou endereço IP do servidor FTP
	- **Nome de usuário** - especifique o nome de usuário para se conectar ao servidor FTP
	- **Senha** - especifique a senha para se conectar ao servidor FTP
	- **Pasta raiz** - especifique um caminho de pasta raiz
	- **Usar tipo binário** - especifique true para usar o modo de transferência binário, false para usar ASCII
	- **Usar SSL** - especifique true para usar FTP em um canal SSL/TLS seguro
	- **Porta do servidor** - especifique o número da porta do servidor FTP
- Clique em Criar.  Será criado um novo Conector de FTP.

## Usar o Conector de FTP em um Aplicativo Lógico
Após o conector de FTP ser criado, ele pode ser consumido do fluxo.

Criar um novo fluxo em +Novo -> Web+Móvel -> LogicApp.  Forneça os metadados do fluxo, incluindo o grupo de recursos

![Create Logic App][4]

Clique em *triggers and actions*.  O designer de fluxo abre

![Logic App empty flow designer][5]

O conector de FTP pode ser usado como gatilho e como ação. 

### Gatilho
No designer de fluxo vazio, clique no conector de FTP no painel da galeria à direita.

![Choose FTP Trigger][6]

O conector de FTP tem um disparador - TriggerOnFileAvailable.  Ele será acionado sempre que um novo arquivo for carregado em uma pasta específica no servidor FTP.

Clique no 'TriggerOnFileAvailable' gatilho.

![Basic inputs FTP Trigger][7]

As entradas ajudam a configurar um caminho de pasta específico a ser sondado com a frequência agendada.  As entradas básicas são
- Frequência - especifica a frequência da sondagem de FTP
- Intervalo - especifica o intervalo da frequência agendada
- Caminho da pasta - especifica o caminho da pasta no servidor FTP
- Tipo de arquivo - especifica se o tipo de arquivo é texto ou binário

Clicar em ... mostra as entradas avançadas. 

![Basic inputs FTP Trigger][8]

As entradas avançadas são
- Máscara de arquivo - especifica a máscara de arquivo durante a sondagem
- Excluir máscara de arquivo - especifica as máscaras de arquivo a serem excluídas durante a sondagem

Forneça as entradas e clique na marca de escala para concluir a configuração das entradas.

![Basic inputs FTP Trigger][9]

Observe que o gatilho do FTP configurado exibe os parâmetros de entrada configurados e também a saída do gatilho de FTP. 

Após a criação do aplicativo lógico, o gatilho do FTP 


- Sonda se há novos arquivo no caminho de pasta
- Cria uma instância no fluxo lógico sempre que há um novo arquivo
- Exclui o arquivo do caminho de pasta após a criação da instância no fluxo lógico

#### Usando a saída do gatilho do FTP em ações posteriores
A saída do conector do FTP pode ser usada como entrada de algumas outras ações no fluxo. 

Você pode clicar em + na caixa de diálogo de entrada de ação e selecionar a saída de FTP diretamente na caixa suspensa.

Você também pode escrever uma expressão diretamente na caixa de diálogo de entrada de ação.  A expressão de fluxo para referir-se à saída do gatilho do FTP é indicada a seguir

	@triggers('ftpconnector').outputs.body.Content

### Ações
Clique no conector de FTP no painel à direita.  O conector de FTP lista as ações com suporte.

![List of FTP Actions][10]

O conector de FTP dá suporte a quatro ações.  Elas são

- **Obter arquivo** - obtém o conteúdo de um arquivo específico
- **Carregar arquivo** - carrega o arquivo no caminho da pasta de FTP
- **Excluir arquivo** - exclui o arquivo do caminho da pasta de FTP
- **Listar arquivo** - lista todos os arquivos do caminho da pasta de FTP

Vejamos um exemplo - Carregar arquivo.  Clique em Carregar Arquivo

As entradas básicas são exibidas primeiro.

![Basic inputs of Upload File action][11]


- **Conteúdo** - especifica o conteúdo do arquivo a ser carregado
- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
- **Caminho do Arquivo** - especifica o caminho do arquivo a ser carregado

Clique em... para as entradas avançadas.

![Basic inputs of Upload File action][12]


- **Acrescente se Existir** - habilite ou desabilite 'Append If Exist'.  Quando habilitado, os dados são acrescentados ao arquivo se ele existir.  Quando desabilitado, o arquivo é substituído se ele existir
- **Pasta Temporária** - opcional.  Se fornecida, o adaptador carregará o arquivo no 'Temporary Folder Path' e, após a conclusão do carregamento, o arquivo será movido para 'Folder Path'.  O 'Temporary Folder Path' deve estar no mesmo disco físico que o 'Folder Path' para garantir que a operação de movimentação seja atômica.  A pasta temporária pode ser usada apenas quando a propriedade 'Append If Exist' está desabilitada.

Forneça as entradas e clique na marca de escala para concluir a configuração das entradas.

![Configured Upload File action][13]

Observe que a ação configurada de Carregar Arquivo em FTP mostra os parâmetros de entrada e também os parâmetros de saída.

#### Usando as saídas de ações anteriores como entrada para a ação de FTP
Observe que na captura de tela configurada, o valor de Conteúdo é definido como uma expressão.

	@triggers().outputs.body.Content


Você pode defini-lo como qualquer valor desejado.  Este é apenas um exemplo.  A expressão leva a saída do gatilho do aplicativo lógico e a usa como conteúdo do arquivo a ser carregado.  Digamos que você queira usar a saída de uma ação anterior, como a transformação.  Nesse caso, a expressão seria

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-FTP/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-FTP/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-FTP/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-FTP/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-FTP/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-FTP/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-FTP/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-FTP/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-FTP/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-FTP/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-FTP/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-FTP/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-FTP/ConfiguredUploadFile.PNG

<!--HONumber=49-->