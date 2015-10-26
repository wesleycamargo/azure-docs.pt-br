<properties
   pageTitle="Usando o Conector do Box em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
   description="Como criar e configurar o conector do Box ou aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
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
   ms.date="08/23/2015"
   ms.author="andalmia"/>

# Comece a usar o Conector do Box e adicione-o a seu Aplicativo Lógico 
Conecte-se ao Box para obter, carregar, excluir. e realizar outras operações em seus arquivos. Os conectores são usados em Aplicativos Lógicos como parte de um "fluxo de trabalho".

Você pode ter situações em que talvez precise trabalhar com o Box, que permite compartilhar dados com qualquer pessoa de maneira segura – mesmo que a pessoa esteja fora do seu firewall. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.


## Gatilhos e Ações
O aplicativo de galeria do Box fornece a você Ações como mecanismos para interagir com o Box:

**Ações**: as ações permitem que você execute ações predefinidas na conta do Box, configurada com o aplicativo lógico. A seguir, ações que podem ser executadas na conta do Box usando o conector do Box:

a. *Listar Arquivos:* esta operação retorna as informações de todos os arquivos em uma pasta. Lista de parâmetros necessários para a ação:

Nome do Parâmetro | Descrição | Obrigatório
--- | --- | ---
Caminho da pasta | Caminho da pasta para a lista. | Sim

> [AZURE.NOTE]Ela não retorna nenhum conteúdo de arquivo.

b. *Obter Arquivo:* essa operação recupera um arquivo, incluindo conteúdo e propriedades. Lista de parâmetros necessários para a ação:

Nome do Parâmetro | Descrição | Obrigatório
--- | --- | ---
Caminho do arquivo | Caminho da pasta na qual o arquivo reside. | Sim
Tipo de arquivo | Especifica se o arquivo é de Texto ou Binário. | Não

> [AZURE.NOTE]Esta operação não exclui o arquivo depois de lê-lo.


c. *Carregar Arquivo*: como o nome sugere, a ação carrega o arquivo na conta do Box. Se o arquivo já existir, ele não será substituído e um erro será gerado. Lista de parâmetros necessários para a ação:

Nome do Parâmetro | Descrição | Obrigatório
--- | --- | ---
Caminho do arquivo | Caminho para o arquivo. | Sim
Conteúdo do arquivo | Conteúdo do arquivo a ser carregado. | Sim
Codificação de transferência de conteúdo | Tipo de codificação do conteúdo: Base64 ou Nenhum. | 

d. *Excluir Arquivo*: a ação exclui o arquivo especificado de uma pasta. Se o arquivo/pasta não for encontrado, uma exceção será gerada. Lista de parâmetros necessários para a ação:

Nome do Parâmetro | Descrição | Obrigatório
--- | --- | ---
Caminho do arquivo | Caminho Completo do Arquivo, incluindo Pastas. | Sim


## Criar um Conector do Box para o aplicativo lógico

Um conector pode ser criado em um aplicativo lógico ou diretamente no Azure Marketplace. Para criar um conector no Marketplace:

1. No quadro inicial do Azure, selecione **Marketplace**.
2. Pesquise "Conector do Box", selecione-o e selecione **Criar**.
3. Digite o Nome, o Plano do Serviço de Aplicativo e outras propriedades: ![][1]
4. Selecione **Criar**.


## Usando o Conector do Box em seu Aplicativo Lógico

Depois de criar o aplicativo de API, você pode usar o Conector do Box como uma ação em seu aplicativo lógico. Para fazer isso:

1. No aplicativo lógico, abra **Gatilhos e Ações** para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo. O Conector do Box está listado na galeria. Selecione-o para adicioná-lo automaticamente ao seu designer de aplicativos lógicos.

	> [AZURE.NOTE]Se o conector do Box é selecionado no início do aplicativo lógico, ele atua como gatilho. Caso contrário, as ações podem ser executadas na conta do Box usando o conector. O Conector do Box não tinha nenhum gatilho na data de criação deste artigo.

2. Autentique e autorize os aplicativos lógicos a executarem operações em seu nome. Selecione **Autorizar** no Conector do Box: ![][2]

3. Insira os detalhes de conexão da conta do Box na qual você deseja executar as operações: ![][3]

4. Conceda aos aplicativos lógicos acesso à sua conta para executar operações em seu nome: ![][4]

5. A lista de ações é exibida e você pode escolher a operação apropriada que deseja executar: ![][5]

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST do Swagger em [Conectores e referência dos Aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=Oct15_HO3-->