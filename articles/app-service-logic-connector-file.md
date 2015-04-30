<properties 
	pageTitle="Conector de arquivo"
	description="Introdução ao Conector de Arquivo"
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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Usando o Conector de Arquivo em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo. 

O Conector de Arquivo permite que você carregue\baixe\exclua arquivos do sistema de arquivos em um computador host.

## Criando um Conector de Arquivo para seu aplicativo lógico ##
Para usar o Conector de Arquivo, primeiro você precisa criar uma instância do aplicativo de API do Conector de Arquivo. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção +NOVO na parte inferior esquerda do Portal do Azure.
2.	Navegue até "Web e Dispositivos Móveis > Azure Marketplace" e pesquise "Conector de Arquivo".
3.	Configure o Conector de Arquivo da seguinte maneira:
 
	![][1]
 
	- **Nome** - dê um nome ao Conector de Arquivo
	- **Configurações de Pacote**
		- **Pasta Raiz** - especifique o caminho da pasta raiz em seu computador host. Por exemplo, D:\FileConnectorTest
		- **Cadeia de Conexão do Barramento de Serviço** - forneça uma Cadeia de Conexão do Barramento de Serviço. Verifique se o namespace do barramento de serviço é do tipo Padrão e NÃO Básico
	- **Plano de Serviço de Aplicativo** - selecione ou crie um Plano de Serviço de Aplicativo
	- **Camada de preços** - escolha uma camada de preços para o conector
	- **Grupo de recursos** - selecione ou crie um grupo de recursos onde o conector deve residir
	- **Assinatura** - escolha uma assinatura na qual você deseja que esse conector seja criado
	- **Local** - escolha a região geográfica onde você quer que o conector seja implantado

4. Clique em Criar. Será criado um novo Conector de Arquivo
5. Depois que a instância de aplicativo de API for criada, navegue até seu painel. Isso pode ser feito clicando em Procurar->Aplicativos de Api->Selecione seu Aplicativo de API do Conector de Arquivo
6. Você pode ver no painel do Conector de Arquivo que a lente "Conexão Híbrida" mostra "Configuração Local Incompleta". Para concluir a Configuração Híbrida, abra esse painel no computador host a cujo sistema de arquivos é necessário conectar-se. Em seguida, clique na lente "Conexão Híbrida". Na folha "Conexão Híbrida" aberta, clique no link "Baixar e configurar" para instalar o Gerenciador de Conexões Híbridas Local

	![][2]

7. Isso iniciará um instalador que solicitará a "Cadeia de Conexão de Retransmissão de Escuta". Copie e cole a "Cadeia de Configuração Principal" da lâmina "Conexão Híbrida". Clique em Instalar.

	![][3]

8. Para verificar se a instalação híbrida foi bem-sucedida, abra novamente o painel do Conector de Arquivo. Você deve ver o Status da Conexão Híbrida como "Conectado"

	![][4]

## Usando o Conector de Arquivo em seu aplicativo lógico ##
Depois que o aplicativo de API for criado, você poderá usar o Conector de Arquivo como uma ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o Conector de Arquivo. Siga as instruções para [Criar um novo aplicativo lógico].  	
	
2.	Abra "Gatilhos e Ações" no aplicativo lógico criado para abrir o Designer de Aplicativos Lógicos e configure seu fluxo.  	
	
3.	O Conector de Arquivo será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.
 	
4.	Você pode soltar o aplicativo de API do Conector de Arquivo no editor clicando em "Conector de Arquivo". O Conector de Arquivo expõe um gatilho e quatro ações.
 
	![][5]

6.	Cada um deles expõe determinadas propriedades. A imagem a seguir lista as propriedades do gatilho e a Ação Obter Arquivo
 
	![][6]

7. Depois que eles forem configurados, o gatilho e a ação poderão ser usados no seu fluxo. Da mesma forma, outras ações também podem ser configuradas.

8. Para usar o conector fora de um aplicativo lógico, as APIs REST expostas pelo conector podem ser utilizadas. Você exibir essas definições da API usando Procurar -> Aplicativo de Api -> Conector de Arquivo. Agora clique na lente Definição de API na seção Resumo para exibir todas as APIs expostas por esse conector.

	![][7]

9. Detalhes das APIs podem ser encontrados em [Definição da API do Conector de Arquivo].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Criar um novo aplicativo lógico]: app-service-logic-create-a-logic-app.md
[Definição da API do Conector de Arquivo]: https://msdn.microsoft.com/en-US/library/dn936296.aspx

<!--HONumber=52-->