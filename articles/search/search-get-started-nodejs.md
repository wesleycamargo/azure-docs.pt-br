<properties 
	pageTitle="Introdução à Pesquisa do Azure no NodeJS" 
	description="Percorra a criação de um aplicativo de Pesquisa do Azure personalizado usando NodeJS como sua linguagem de programação." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/25/2015" 
	ms.author="heidist"/>

#Introdução à Pesquisa do Azure no NodeJS#

Aprenda a criar um aplicativo de pesquisa NodeJS personalizado que usa a Pesquisa do Azure para a sua experiência de pesquisa. O tutorial usa a [AAPI REST do Serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e operações usados neste exercício.

Usamos [NodeJS](https://nodejs.org) e NPM, [Sublime Text 3](http://www.sublimetext.com/3) e o Windows PowerShell no Windows 8.1 para desenvolver e testar esse código.

Para executar esse exemplo, você deve ter um serviço de Pesquisa do Azure, o qual você pode assinar no [Portal do Azure](https://portal.azure.com).

> [AZURE.TIP]Baixe o código-fonte deste tutorial em [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).

##Sobre os dados##

Este exemplo de aplicativo usa dados do [Serviço Geológico dos Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Rhode Island, para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, por exemplo, hospitais e escolas, e características geológicas, como rios, lagos e picos.

Neste aplicativo, o programa **DataIndexer** cria e carrega o índice usando um constructo [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando o conjunto de dados filtrado do USGS de um Banco de Dados SQL do Azure público. As informações de credenciais e de conexão para a fonte de dados online são fornecidas no código do programa. Nenhuma configuração adicional é necessária.

> [AZURE.NOTE]Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar a camada padrão, esse limite não se aplicará. Para obter detalhes sobre a capacidade de cada camada de preços, confira [Limites e restrições](https://msdn.microsoft.com/library/azure/dn798934.aspx).

##Criar o serviço##

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na barra de navegação, clique em **Novo** | **Dados + armazenamento** | **Pesquisa**.
 
     ![][1]

3. Configure o nome do serviço, a camada de preços, o grupo de recursos, a assinatura e o local. Essas configurações são necessárias e não podem ser alteradas após o fornecimento do serviço.

     ![][2]

	- O **nome do serviço** deve ser exclusivo, com letras minúsculas, abaixo de 15 caracteres, sem espaços. Esse nome se torna parte do ponto de extremidade de seu serviço de Pesquisa do Azure. Confira [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber mais sobre as convenções de nomenclatura. 
	
	- A **Camada de preços** determina a capacidade e a cobrança. Ambas as camadas fornecem os mesmos recursos, mas em diferentes níveis de recursos.
	
		- Um serviço na camada **Gratuito** é executado em clusters compartilhados com outros assinantes. Ela oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção. A implantação de um serviço gratuito geralmente demora apenas alguns minutos.
		- Um serviço na camada **Padrão** é executado em recursos dedicados e é altamente escalonável. Inicialmente, um serviço padrão é configurado com uma réplica e uma partição, mas você pode ajustar a capacidade quando o serviço for criado. A implantação de um serviço padrão demora mais tempo, normalmente cerca de quinze minutos.
	
	- **Grupos de recursos** são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, Sites do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.
	
	- **Assinatura** permite que você escolha entre várias assinaturas se tiver mais de uma.
	
	- **Local** é a região do data center. Atualmente, todos os recursos devem ser executados no mesmo data center. Não há suporte para a distribuição de recursos em vários data centers.

4. Clique em **Criar** para provisionar o serviço.

Observe as notificações na barra de navegação. Um aviso é exibido quando o serviço fica pronto para uso.

<a id="sub-2"></a>
##Localizar o nome do serviço e a chave de api do serviço de Pesquisa do Azure

Depois que o serviço for criado, você poderá retornar ao portal para obter a URL ou `api-key`. As conexões com o serviço de Pesquisa exigem que você tenha a URL e a `api-key` para autenticar a chamada.

1. Na barra de navegação rápida, clique em **Home** e clique no serviço de Pesquisa para abrir o painel do serviço. 

2. No painel de serviço, você verá blocos com as informações essenciais e o ícone de chave para acessar as chaves de administrador.

  	![][3]

3. Copie a URL do serviço, uma chave de administrador e uma chave de consulta. Você precisará dos três posteriormente, ao adicioná-los ao arquivo config.js.

##Baixe os arquivos do exemplo.

Use qualquer um dos procedimentos a seguir para baixar o exemplo.

1. Acesse [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Clique em **Baixar ZIP**, salve o arquivo .zip e, em seguida, extraia todos os arquivos que ele contém.

Todas as modificações de arquivos subsequentes e instruções de execução serão feitas nos arquivos nessa pasta.

Como alternativa, se você tiver o GIT em sua declaração de caminho, abra uma janela do PowerShell e digite `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`

##Atualize o config.js. com a URL do serviço de Pesquisa e a chave de api

Usando a URL e as chaves de api que você copiou anteriormente, especifique a URL, a chave de administrador e a chave de consulta no arquivo de configuração.

As chaves de administrador oferecem controle total sobre as operações do serviço, incluindo a criação ou exclusão de um índice e carregamento de documentos. Em contraste, as chaves de consulta servem para operações somente leitura, normalmente usadas por aplicativos cliente que se conectam à Pesquisa do Azure.

Neste exemplo, incluímos a chave de consulta para ajudar a reforçar a prática recomendada de usar a chave de consulta em aplicativos cliente.

A captura de tela a seguir mostra **config.js** aberto em um editor de texto, com as entradas relevantes demarcadas para que você possa ver onde deve atualizá-lo com os valores válidos para o serviço de pesquisa.

![][5]


##Hospedar um ambiente de tempo de execução para o exemplo

O exemplo exige um servidor HTTP, que pode ser instalado globalmente usando npm.

Use uma janela do PowerShell para os comandos a seguir.

1. Navegue até a pasta que contém o arquivo **package.json**.
2. Digite `npm install`.
2. Digite `npm install -g http-server`.

##Crie o índice e executar o aplicativo.

1. Digite `npm run indexDocuments`. 
2. Digite `npm run build`.
3. Digite `npm run start_server`.
4. Direcione seu navegador para `http://localhost:8080/index.html`

##Pesquisar dados do USGS##

O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisar** em uma caixa de pesquisa vazia, obterá as 50 entradas principais, que é o valor padrão.

A inserção de um termo de pesquisa fornecerá ao mecanismo de pesquisa algo para seguir. Tente inserir um nome regional. "Roger Williams" foi o primeiro administrador de Rhode Island. Vários parques, edifícios e escolas receberam seus nomes em homenagem a ele.

![][9]

Você também pode tentar qualquer um destes termos:

- Pawtucket
- Pembroke
- goose +cape


##Próximas etapas##

Este é o primeiro tutorial da Pesquisa do Azure com base no NodeJS e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver alguma experiência com a Pesquisa do Azure, use este exemplo como um trampolim para experimentar sugestões (consultas de preenchimento automático ou de digitação antecipada), filtros e navegação facetada. Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](http://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode exibir os links em nossa [Lista de vídeos e Tutorial](https://msdn.microsoft.com/library/azure/dn798933.aspx) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
<!--HONumber=54--> 