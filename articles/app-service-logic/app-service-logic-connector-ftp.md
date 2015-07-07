<properties
	pageTitle="Conector de FTP"
	description="Introdução ao Conector de FTP"
	authors="anuragdalmia"
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

#Conector de FTP

##Visão geral
O Conector de FTP permite mover dados de/para um servidor FTP. Os principais recursos do Conector de FTP são:

- Extrair arquivos do servidor FTP sob demanda
- Executar sondagens com base em um cronograma configurável
- Executar sondagens no servidor FTP e acionar o fluxo de lógica com base em novos documentos no servidor FTP
- Especificar o servidor FTP com um endereço IP, porta, senha e nome de host
- Capacidade de fazer envios sob demanda
- Capacidade de excluir arquivos do servidor FTP sob demanda

##Criar um novo conector de FTP
Para criar um novo conector de FTP, siga as etapas abaixo. - Inicie o portal do Azure - Abra o Azure Marketplace usando +Novo (na parte inferior da página) -> Web+Móvel --> Azure Marketplace

	![Launch Azure Marketplace][1]

- Clique em Aplicativos de API
- Pesquise FTP e selecione o Conector de FTP

	![Selecionar o Conector de FTP][2]

- Clique em Criar
- Na lâmina do conector de FTP que aparece, forneça os dados a seguir.

	![Criar o Conector de FTP][3]

- **Local** - escolha a região geográfica onde você quer que o conector seja implantado
- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
- **Plano de hospedagem na Web** - selecione ou crie uma plano de hospedagem na Web
- **Camada de preços** - escolha uma camada de preços para o conector
- **Nome** - dê um nome ao Conector de FTP
- **Configurações do pacote**
	- **Endereço do Servidor** - especifique o nome ou endereço IP do servidor FTP
	- **Nome de Usuário** - especifique o nome de usuário para se conectar ao servidor FTP
	- **Senha** - especifique a senha para se conectar ao servidor FTP
	- **Pasta Raiz** - especifique um caminho da pasta raiz
	- **Usar Binário** - especifique Verdadeiro para usar o modo de transferência Binário, Falso para usar ASCII
	- **Usar SSL** - especifique Verdadeiro para usar FTP em um canal SSL/TLS seguro
	- **Porta do Servidor** - especifique o número da porta do servidor FTP
- Clique em Criar. Será criado um novo Conector de FTP.

##Usar o Conector de FTP em um aplicativo lógico
Após o conector de FTP ser criado, ele pode ser consumido do fluxo.

Crie um novo fluxo em +Novo -> Web+Móvel -> LogicApp. Forneça os metadados do fluxo, incluindo o grupo de recursos

![Criar aplicativo lógico][4]

Clique em *gatilhos e ações*. O designer de fluxo se abre

![Designer de fluxo vazio do aplicativo lógico][5]

O conector de FTP pode ser usado como gatilho e como ação.

###Gatilho
No designer de fluxo vazio, clique no conector de FTP no painel da galeria à direita.

![Escolher o Gatilho de FTP][6]

O conector de FTP tem um gatilho - 'Arquivo Disponível (Leia e Exclua)'. Esse gatilho

- Sonda se há novos arquivo no caminho de pasta
- Cria uma instância no fluxo lógico sempre que há um novo arquivo
- Exclui o arquivo do caminho de pasta após a criação da instância no fluxo lógico

Clique no gatilho 'Arquivo Disponível (Leia e Exclua)'.

![Gatilho de FTP de entradas básicas][7]

As entradas ajudam a configurar um caminho de pasta específico a ser sondado com a frequência agendada. As informações básicas são - Frequência - Especifica a frequência de sondagem de FTP - Intervalo - Especifica o intervalo para a frequência programada - Caminho da Pasta - Especifica o caminho da pasta no servidor FTP - Tipo de arquivo - Especifica se o tipo de arquivo é texto ou binário

Clicar nas reticências (...) mostra as entradas avançadas.

![Gatilho de FTP de entradas básicas][8]

As entradas avançadas são - Máscara de arquivo - Especifica a máscara de arquivo durante a sondagem - Excluir máscara de arquivo - Especifica as máscaras de arquivo a serem excluídas durante a sondagem

Forneça as entradas e clique na marca de verificação para concluir a configuração das entradas.

![Gatilho de FTP de entradas básicas][9]

Observe que o gatilho do FTP configurado exibe os parâmetros de entrada configurados e também as saídas.

####Usando a saída do gatilho do FTP em ações posteriores
A saída do conector do FTP pode ser usada como entrada de algumas outras ações no fluxo.

Você pode clicar em “...” na caixa de diálogo de entrada de ação e selecionar a saída de FTP diretamente na caixa suspensa.

Você também pode escrever uma expressão diretamente na caixa de diálogo de entrada de ação. A expressão de fluxo para se referir à saída do gatilho do FTP é indicada a seguir

	@triggers('ftpconnector').outputs.body.Content

###Ações
Clique no conector de FTP no painel à direita. O conector de FTP lista as ações com suporte.

![Lista de ações do FTP][10]

O conector de FTP dá suporte a quatro ações. São elas

- **Obter Arquivo** - Obtém o conteúdo de um arquivo específico
- **Carregar Arquivo** - Carrega o arquivo no caminho da pasta de FTP
- **Excluir Arquivo** - exclui o arquivo do caminho da pasta de FTP
- **Listar arquivos** - lista todos os arquivos no caminho da pasta de FTP

Vejamos um exemplo - Carregar arquivo. Clique em Carregar Arquivo

As entradas básicas são exibidas primeiro.

![Entradas básicas de ação Carregar Arquivo][11]


- **Conteúdo** - Especifica o conteúdo do arquivo a ser carregado
- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
- **Caminho do Arquivo** - Especifica o caminho do arquivo a ser carregado

Clique em... para as entradas avançadas.

![Entradas básicas de ação Carregar Arquivo][12]


- **Acrescentar se Existe** - “Acrescentar se existe” True ou False. Quando habilitado, os dados são acrescentados ao arquivo se ele existir. Quando desabilitado, o arquivo é substituído, se ele existir
- **Pasta Temporária** - Opcional. Se fornecido, o adaptador carregará o arquivo para o “Caminho da Pasta Temporária” e, quando o carregamento for concluído, o arquivo será movido para o “Caminho da Pasta”. O “Caminho da Pasta Temporária” deve estar no mesmo disco físico que o “Caminho da Pasta “ para garantir que a operação de movimentação seja atômica. A pasta temporária pode ser usada apenas quando a propriedade Anexar se Existir está desabilitada.

Forneça as entradas e clique na marca de verificação para concluir a configuração das entradas.

![Ação configurada Carregar Arquivo][13]

O parâmetro “Caminho do arquivo” é definido como

	@concat('/Output/',triggers().outputs.body.FileName)

Observe que a ação configurada de Carregar Arquivo em FTP mostra os parâmetros de entrada e também os parâmetros de saída.

####Usando as saídas de ações anteriores como entrada para a ação de FTP
Observe que na captura de tela configurada, o valor de Conteúdo é definido como uma expressão.

	@triggers().outputs.body.Content


Você pode defini-lo como qualquer valor desejado. Este é apenas um exemplo. A expressão usa a saída do gatilho do aplicativo lógico e a usa como conteúdo do arquivo a ser carregado. Digamos que você queira usar a saída de uma ação anterior, como a transformação. Nesse caso, a expressão seria

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=62-->