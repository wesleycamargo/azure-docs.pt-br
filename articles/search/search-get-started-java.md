<properties 
	pageTitle="Introdução à Pesquisa do Azure em Java" 
	description="Percorra a criação de um aplicativo de Pesquisa do Azure personalizado usando Java como sua linguagem de programação." 
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
	ms.date="03/30/2015" 
	ms.author="heidist"/>

#Introdução à Pesquisa do Azure em Java#

Aprenda a criar um aplicativo de pesquisa Java personalizado que usa a Pesquisa do Azure para sua experiência de pesquisa. O tutorial usa a [API REST do Serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e operações usados neste exercício.

Para compilar e testar este exemplo, usamos o seguinte software:

- [Eclipse IDE para desenvolvedores de Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Certifique-se de baixar a versão EE. Uma das etapas de verificação requer um recurso que está apenas nesta edição.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Para executar esse exemplo você deve ter um serviço de Pesquisa do Azure, no qual você pode se inscrever pelo [Portal de Gerenciamento do Azure](https://portal.azure.com).

> [AZURE.TIP]Baixe o código-fonte deste tutorial em [Demonstração do Java da Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkId=530197) no Github.

## Sobre os dados

Este aplicativo de exemplo usa dados do [USGS (Serviço Geológico dos Estados Unidos)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Rhode Island para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, como hospitais e escolas, bem como características geológicas como rios, lagos e picos.

Neste aplicativo, o programa **SearchServlet.java** cria e carrega o índice usando uma construção [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando o conjunto de dados filtrado do USGS por meio de um Banco de dados SQL do Azure público. Credenciais predefinidas e informações de conexão para a fonte de dados online são fornecidas no código do programa. Em termos de acesso a dados, nenhuma configuração adicional é necessária.

> [AZURE.NOTE]Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar a camada padrão, esse limite não se aplica e você pode modificar este código para usar um conjunto de dados maior. Para obter detalhes sobre a capacidade de cada camada de preços, consulte [Limites e restrições](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Sobre os arquivos de programa

A lista a seguir descreve os arquivos que são relevantes para este exemplo.

- Search.jsp: fornece a interface do usuário
- SearchServlet.java: fornece métodos (semelhantes a um controlador em MVC)
- SearchServiceClient.java: lida com solicitações HTTP
- SearchServiceHelper.java: uma classe auxiliar que fornece métodos estáticos
- Document.Java: fornece o modelo de dados
- config.properties: define a URL do serviço de Pesquisa e a chave de Api
- Pom.xml: uma dependência do Maven


## Criar o serviço

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na barra de navegação rápida, clique em **Novo** | **Dados + armazenamento** | **Pesquisa**.
 
     ![][1]

3. Configure o nome do serviço, camada de preços, grupo de recursos, assinatura e local. Essas configurações são necessárias e não podem ser alteradas após o início do fornecimento do serviço.

     ![][2]

	- O **nome do serviço** deve ser exclusivo, com letras minúsculas, menos de 15 caracteres e sem espaços. Esse nome se torna parte do ponto de extremidade de seu serviço de Pesquisa do Azure. Consulte [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber mais sobre as convenções de nomenclatura. 
	
	- A **Camada de preços** determina a capacidade e a cobrança. Ambas as camadas fornecem os mesmos recursos, mas em níveis diferentes de recursos.
	
		- Um serviço na camada **Gratuito** é executado em clusters compartilhados com outros assinantes. Essa camada oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção. A implantação de um serviço gratuito geralmente demora apenas alguns minutos.
		- Um serviço na camada **Padrão** é executado em recursos dedicados e é altamente escalonável. Inicialmente, um serviço padrão é configurado com uma réplica e uma partição, mas você pode ajustar a capacidade após o serviço ser criado. A implantação de um serviço padrão demora mais tempo, normalmente cerca de quinze minutos.
	
	- **Grupos de recursos** são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, Sites do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.
	
	- **Assinatura** permite que você escolha entre várias assinaturas, se você tiver mais de uma.
	
	- **Local** é a região do datacenter. Atualmente, todos os recursos devem ser executados no mesmo datacenter. Não há suporte para a distribuição de recursos em vários datacenters.

4. Clique em **Criar** para provisionar o serviço.

Observe as notificações na barra de navegação rápida. Um aviso será exibido quando o serviço estiver pronto para uso.

<a id="sub-2"></a>
## Localizar o nome do serviço e a chave de Api do serviço de Pesquisa do Azure

Depois que o serviço for criado, você poderá retornar ao portal para obter a URL e `api-key`. Conexões com seu serviço de Pesquisa requerem que você tenha tanto uma URL quanto um `api-key` para autenticar a chamada.

1. Na barra de navegação rápida, clique em **Home** e clique no Serviço de Pesquisa para abrir o painel do serviço. 

2. No painel de serviço, você verá blocos com as informações essenciais, bem como o ícone de chave para acessar as chaves de administrador.

  	![][3]

3. Copie a URL do serviço e uma chave de administrador. Você precisará dos três posteriormente, ao adicioná-los ao arquivo **config.properties**.

## Baixar os arquivos de exemplo

1. Vá para [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) no Github.

2. Clique em **Baixar ZIP**, salve o arquivo .zip no disco e, em seguida, extraia todos os arquivos que ele contém. Considere a possibilidade de extrair os arquivos no espaço de trabalho Java para facilitar a localização do projeto posteriormente.

3. Os arquivos de exemplo são somente leitura. Clique com o botão direito em propriedades da pasta e limpe o atributo somente leitura.

Todas as modificações de arquivos subsequentes e instruções de execução serão feitas nos arquivos nessa pasta.

## Projeto de importação

1. No Eclipse, escolha **Arquivo** | **Importação** | **Geral** | **Projetos existentes no espaço de trabalho**.

    ![][4]

2. Em **Selecionar diretório raiz**, navegue até a pasta que contém os arquivos de exemplo. Selecione a pasta que contém a pasta .project. O projeto deve aparecer na lista **Projetos** como um item selecionado.

    ![][12]

3. Clique em **Concluir**.

4. Use o **Gerenciador de Projetos** para exibir e editar os arquivos. Se não ainda estiver aberto, clique em **Janela** | **Exibição** | **Gerenciador de Projetos** ou use o atalho para abri-lo.

## Configurar a URL de serviço e a chave de Api

1. Em **Gerenciador de Projetos**, clique duas vezes em **config.properties** para editar as definições de configuração que contêm o nome do servidor e a chave de Api. 
 
2. Consulte as etapas explicadas anteriormente neste artigo,em que você encontrou a URL de serviço e a chave de Api no [Portal do Azure](https://portal.azure.com), para obter os valores que você vai inserir agora em **config.properties**.

3. Em **config.properties**, substitua "Chave Api" com a chave de Api para o serviço. Em seguida, o nome do serviço (o primeiro componente da URL http://servicename.search.windows.net) substitui o "nome do serviço" no mesmo arquivo.

	![][5]

## Configurar ambientes de projeto, compilação e tempo de execução

1. No Eclipse, no Gerenciador de Projetos, clique com botão direito no projeto | **Propriedades** | **Facetas do projeto**.

2. Selecione **Módulo da Web dinâmico**, **Java** e **JavaScript**.

    ![][6]

3. Clique em **Aplicar**.

4. Selecione **Janela** | **Preferências** | **Servidor** | **Ambientes de tempo de execução** | **Adicionar..**.

5. Expanda o Apache e selecione a versão do servidor Apache Tomcat instalado anteriormente. Em nosso sistema, instalamos a versão 8.

	![][7]

6. Na próxima página, especifique o diretório de instalação do Tomcat. Em um computador Windows, isso provavelmente será C:\\Arquivos de Programas\\Apache Software Foundation\\Tomcat *versão*.

6. Clique em **Concluir**.
 
7. Selecione **Janela** | **Preferências** | **Java** | **JREs instalado** | **Adicionar**.

8. Em **Adicionar JRE**, selecione **VM padrão**.

10. Clique em **Próximo**.
 
11. Na Definição do JRE, na página inicial do JRE, clique em **Diretório**.

12. Navegue até **Arquivos de Programas** | **Java** e selecione o JDK instalado anteriormente. É importante selecionar o JDK como o JRE.

13. Em JREs instalados, escolha o **JDK**. Suas configurações devem ter aparência semelhante à captura de tela a seguir.

    ![][9]

14. Opcionalmente, selecione **Janela** | **Navegador da Web** | **Internet Explorer** para abrir o aplicativo em uma janela do navegador externo. Usar um navegador externo oferece uma melhor experiência de aplicativo Web.

    ![][8]

Agora, você concluiu as tarefas de configuração. Em seguida, você compilará e executará o projeto.

## Compilar o projeto
 
1. No Gerenciador de Projetos, clique no nome do projeto e escolha **Executar como** | **Compilação Maven...** para configurar o projeto.

    ![][10]

8. Em Editar configurações, em Metas, digite "instalação limpa" e, em seguida, clique em **Executar**.
 
Mensagens de status são passadas para a janela do console. Você deve ver a mensagem COMPILAÇÃO BEM-SUCEDIDA, indicando que o foi projeto compilado sem erros.

## Executar o aplicativo

Nesta última etapa, você executará o aplicativo em um ambiente de tempo de execução do servidor local.

Se você ainda não especificou um ambiente de tempo de execução do servidor no Eclipse, você precisará fazer isso primeiro.

1. No Gerenciador de Projetos, expanda **WebContent**.

5. Clique com o botão direito em **Search.jsp** | **Executar como** | **Executar no servidor**. Selecione o servidor Apache Tomcat e, em seguida, clique em **Executar**.

> [AZURE.TIP]Se você usou um espaço de trabalho não padrão para armazenar seu projeto, você provavelmente precisará modificar **Configuração de Execução** para indicar o local do projeto, para evitar um erro de inicialização do servidor. No Gerenciador de Projetos, clique com o botão direito em **Search.jsp** | **Executar como** | **Configurações de execução**. Selecione o servidor Apache Tomcat. Clique em **Argumentos**. Clique em **Espaço de Trabalho** ou **Sistema de Arquivos** para definir a pasta que contém o projeto.

Quando você executar o aplicativo, verá uma janela do navegador, fornecendo uma caixa de pesquisa para inserir termos.

Aguarde aproximadamente um minuto antes de clicar em **Pesquisa** para dar ao serviço tempo de criar e carregar o índice. Se você receber um erro HTTP 404, basta esperar um pouco mais antes de tentar novamente.

## Pesquisar em dados USGS

O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisa** em uma caixa de pesquisa vazia, você obterá as 50 entradas principais, que é o padrão.

A inserção de um termo de pesquisa fornecerá ao mecanismo de pesquisa algo para continuar. Tente digitar um nome regional. "Roger Williams" foi o primeiro governador de Rhode Island. Vários parques, edifícios e escolas receberam seu nome em homenagem.

![][11]

Você também pode tentar qualquer um destes termos:

- Pawtucket
- Pembroke
- goose +cape

## Próximas etapas

Este é o primeiro tutorial da Pesquisa do Azure com base em Java e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver um plano de fundo na Pesquisa do Azure, você pode usar este exemplo como um trampolim para experimentos adicionais, talvez aumentando a [página de pesquisa](../search-pagination/) ou implementando [navegação facetada](../search-faceted-navigation/). Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para desenvolver uma compreensão do que você pode criar. Visite nossa [página de documentação](http://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode ver os links em nossa [Lista de vídeos e Tutoriais](https://msdn.microsoft.com/library/azure/dn798933.aspx) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-11.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!--HONumber=54--> 