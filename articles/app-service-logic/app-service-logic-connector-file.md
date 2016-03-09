<properties
	pageTitle="Usando o Conector de Arquivo em Aplicativos Lógicos | Serviço de Aplicativo do Microsoft Azure"
	description="Como criar e configurar o Conector de Arquivo ou o aplicativo de API e usá-lo em um aplicativo lógico no Serviço de Aplicativo do Azure"
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="rajram"/>

# Introdução ao Conector de Arquivo e à adição dele ao seu Aplicativo Lógico
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos.

Conecte-se a um sistema de arquivos para carregar, baixar e realizar outras operações com seus arquivos em um computador host. Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados. Você pode adicionar o conector de Arquivo a seu fluxo de trabalho de negócios e processar dados como parte desse fluxo de trabalho dentro de um Aplicativo Lógico.

O Conector de Arquivo usa o Gerenciador de Conexões Híbridas para conectividade híbrida com o sistema de arquivos do host.

## Criando um conector de Arquivo para seu aplicativo lógico ##
Para usar o conector de Arquivo, primeiro você precisa criar uma instância do aplicativo de API do Conector de Arquivo. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção + NEW no lado esquerdo do Portal do Azure.
2.	Navegue até “Marketplace > Aplicativos de API” e pesquise por “Conector de Arquivo”.
3.	Configure o conector de Arquivo da seguinte maneira:![][1]

	- **Nome** - dê um nome para o seu Conector de Arquivo
	- **Configurações do pacote**
		- **Pasta Raiz** - especifique o caminho da pasta raiz em seu computador host. Por exemplo, D:\\FileConnectorTest
		- **Cadeia de Conexão do Barramento de Serviço** - forneça uma Cadeia de Conexão do Barramento de Serviço. Verifique se o namespace do barramento de serviço é do tipo Padrão e NÃO Básico, para permitir o uso de Retransmissões do Barramento de Serviço. A Retransmissão do Barramento de Serviço é usada para conectar-se ao Gerenciador de Conexões Híbridas.
	- **Plano de Serviço de Aplicativo** - selecione ou crie um Plano de Serviço de Aplicativo
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado

4. Clique em Criar. Será criado um novo Conector de Arquivo

## Configurar o Gerenciador de Conexão Híbrida ##
Depois que a instância de Aplicativo de API for criada, navegue até seu painel. Isso pode ser feito clicando em Procurar > Aplicativos de Api > Selecione seu Aplicativo de API do Conector de Arquivo. Aqui, o Gerenciador de Conexões Híbridas precisa ser configurado. Para obter mais informações sobre configuração e resolução de problemas do Gerenciador de Conexão Híbrida, consulte [Uso do Gerenciador de Conexão Híbrida].

## Usando o Conector de Arquivo em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector de Arquivo como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o Conector de Arquivo. Siga as instruções para [Criar um novo aplicativo lógico].

2.	Abra "Gatilhos e Ações" no aplicativo lógico criado para abrir o Designer de Aplicativos Lógicos e configure seu fluxo.

3.	O Conector de Arquivo será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.

4.	Você pode soltar o aplicativo de API do Conector de Arquivo no editor clicando em "Conector de Arquivo". O Conector de Arquivo expõe um gatilho e quatro ações: ![][5]

6.	Cada um deles expõe determinadas propriedades. A imagem a seguir lista as propriedades do gatilho e a Ação Obter Arquivo: ![][6]

7. Depois que eles forem configurados, o Gatilho e a Ação poderão ser usados no seu fluxo. Da mesma forma, outras ações também podem ser configuradas.

> [AZURE.NOTE] O gatilho do arquivo excluirá o arquivo após este ser lido com êxito na pasta.

## Conector de arquivo de APIs REST ##
Para usar o conector fora de um aplicativo lógico, as APIs REST expostas pelo conector podem ser utilizadas. Você exibir essas definições da API usando Procurar -> Aplicativo de Api -> Conector de Arquivo. Agora clique na lente Definição de API na seção Resumo para exibir todas as APIs expostas por esse conector: ![][7]

Detalhes das APIs podem ser encontrados em [Definição da API do Conector de Arquivo].

## Faça mais com seu Conector
Agora que o conector foi criado, você pode adicioná-lo a um fluxo de trabalho comercial usando um Aplicativo Lógico. Consulte [O que são Aplicativos Lógicos?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se você deseja começar com os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar os Aplicativos Lógicos](https://tryappservice.azure.com/?appservice=logic), em que você pode criar imediatamente um aplicativo lógico inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

Exibir a referência da API REST de Swagger em [Conectores e referência de aplicativos de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode examinar estatísticas de desempenho e controlar a segurança do conector. Consulte [Gerenciar e monitorar Aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md
[Definição da API do Conector de Arquivo]: https://msdn.microsoft.com/library/dn936296.aspx
[Uso do Gerenciador de Conexão Híbrida]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0224_2016-->