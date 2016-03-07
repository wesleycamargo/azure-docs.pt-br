<properties
   pageTitle="Como implantar uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch | Microsoft Azure"
   description="Como usar a amostra JUnit para gerar e carregar dados em um cluster do Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Como implantar uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Este documento descreve como criar e usar uma amostra do JUnit que pode gerar e carregar dados em um cluster Elasticsearch como parte de um plano de teste JMeter. Essa abordagem é altamente flexível para testes de carga que podem gerar grandes quantidades de dados de teste sem depender de arquivos de dados externos.

> [AZURE.NOTE] Os testes de carga usados para avaliar o desempenho da ingestão de dados, descritos no documento Maximizando o desempenho da ingestão de dados com o Elasticsearch no Azure, foram construídos usando essa abordagem. Os detalhes do código JUnit estão descritos nesse documento.

Para testar o desempenho da ingestão de dados, o código JUnit foi desenvolvido usando o Eclipse (Mars), e as dependências foram resolvidas usando o Maven. Os procedimentos a seguir descrevem o processo detalhado para instalação do Eclipse, configuração do Maven, criação de um teste JUnit e implantação desse teste como uma amostra de Solicitação JUnit em um teste JMeter.

> [AZURE.NOTE] Para obter informações detalhadas sobre a estrutura e a configuração do ambiente de teste, confira [Como criar um ambiente de testes de desempenho para Elasticsearch no Azure][].

## Instalando pré-requisitos

Você precisará do [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp) na máquina de desenvolvimento. Também precisará instalar o [IDE Eclipse para desenvolvedores de Java](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE).

> [AZURE.NOTE] Se você estiver usando a VM mestre do JMeter descrita em [ Criando um ambiente de teste de desempenho para Elasticsearch no Azure][] como seu ambiente de desenvolvimento, baixe a versão para Windows 32 bits do instalador do Eclipse.

## Criando um projeto de teste JUnit para testes de carga do Elasticsearch

Inicie o IDE Eclipse, caso ainda não esteja em execução, e feche a página de *Boas-vindas*. No menu *Arquivo*, clique em *Novo* e clique em *Projeto Java*.

![](./media/guidance-elasticsearch/jmeter-deploy7.png)

Na janela *Novo Projeto Java*, digite um nome de projeto, selecione *Usar JRE padrão* e clique em *Concluir*.

![](./media/guidance-elasticsearch/jmeter-deploy8.png)

Na janela *Gerenciador de Pacotes*, expanda o nó com o mesmo nome de seu projeto. Verifique se ele contém uma pasta chamada *src* e uma referência ao JRE especificado.

![](./media/guidance-elasticsearch/jmeter-deploy9.png)

Clique com botão direito na pasta *src*, clique em *Novo* e clique em *Caso de Teste JUnit*.

![](./media/guidance-elasticsearch/jmeter-deploy10.png)

Na janela *Novo Caso de Teste JUnit*, selecione *Novo teste Junit 4*, insira um nome para o pacote (pode ser o mesmo que o nome do projeto, embora, por convenção, ele deva começar com uma letra minúscula), um nome para a classe de teste e selecione as opções que geram os fragmentos do método necessários para o teste. Deixe a caixa *Classe sob teste* vazia e clique em *Concluir*.

![](./media/guidance-elasticsearch/jmeter-deploy11.png)

Se a seguinte caixa de diálogo *Novo Caso de Teste JUnit* for exibida, especifique a opção para adicionar a biblioteca JUnit 4 ao caminho de compilação e, em seguida, clique em *OK*.

![](./media/guidance-elasticsearch/jmeter-deploy12.png)

Verifique se o código estrutural do teste JUnit foi gerado e exibido na janela do editor de Java.

![](./media/guidance-elasticsearch/jmeter-deploy13.png)

No *Gerenciador de Pacotes*, clique com o botão direito no nó de seu projeto, clique em *Configurar* e clique em *Converter em Projeto Maven*.

> [AZURE.NOTE] Com o Maven, você pode gerenciar com mais facilidade as dependências externas (como as bibliotecas de cliente Java do Elasticsearch) das quais um projeto depende.

![](./media/guidance-elasticsearch/jmeter-deploy14.png)

Na caixa de diálogo *Criar novo POM*, na caixa de listagem suspensa *Empacotamento*, selecione *jar* e clique em *Concluir*.

![](./media/guidance-elasticsearch/jmeter-deploy15.png)

O painel que aparece abaixo do editor de POM pode exibir o aviso *O caminho de compilação especifica o ambiente de execução J2SE-1.5. Não há JREs instalados no espaço de trabalho que sejam estritamente compatíveis com este ambiente*, dependendo de qual versão do Java você tiver instalada na máquina de desenvolvimento. Se você tiver uma versão do Java posterior à versão 1.5, você poderá ignorar com segurança esse aviso.

![](./media/guidance-elasticsearch/jmeter-deploy16.png)

No editor do POM, expanda *Propriedades* e clique em *Criar*.

![](./media/guidance-elasticsearch/jmeter-deploy17.png)

Na caixa de diálogo *Adicionar Propriedade*, na caixa *Nome*, digite *es.version*, na caixa *Valor* digite *1.7.2* e clique em *OK*. Essa é a versão da biblioteca de cliente Java do Elasticsearch a ser usada (essa versão pode ser substituída no futuro, e definir a versão como uma propriedade POM e fazer referência a essa propriedade em outro lugar no projeto permite que a versão seja alterada rapidamente).

![](./media/guidance-elasticsearch/jmeter-deploy18.png)

Clique na guia *Dependências* na base do editor POM e clique em *Adicionar* ao lado da caixa de listagem *Dependências*.

![](./media/guidance-elasticsearch/jmeter-deploy19.png)

Na caixa de diálogo *Selecionar Dependência*, na caixa *Id do Grupo*, digite *org.elasticsearch*, na caixa *Id do Artefato* digite *elasticsearch*, na caixa *Versão* digite *\\${es.version}* e clique em *OK*. As informações sobre a biblioteca de cliente java do Elasticsearch são mantidas no repositório online Maven Central, e essa configuração baixará automaticamente a biblioteca e suas dependências quando o projeto for compilado.

![](./media/guidance-elasticsearch/jmeter-deploy20.png)

No menu *Arquivo*, clique em *Salvar Tudo*. Essa ação salvará e compilará o projeto, baixando as dependências especificadas pelo Maven. Verifique se a pasta *Dependências do Maven* aparece no Gerenciador de Pacotes. Expanda essa pasta para exibir os arquivos jar baixados a fim de oferecer suporte a biblioteca de cliente Java do Elasticsearch.

![](./media/guidance-elasticsearch/jmeter-deploy21.png)

## Como importar um projeto de teste JUnit existente no Eclipse

Este procedimento pressupõe que você tenha baixado um projeto Maven criado anteriormente usando o Eclipse.

Inicie o IDE Eclipse. No menu *Arquivo*, clique em *Importar*.

![](./media/guidance-elasticsearch/jmeter-deploy22.png)

Na janela *Selecionar*, expanda a pasta *Maven*, clique em *Projeto Maven Existente* e clique em *Avançar*.

![](./media/guidance-elasticsearch/jmeter-deploy23.png)

Na janela *Projetos Maven*, especifique a pasta que contém o projeto (a pasta que contém o arquivo pom.xml), clique em *Selecionar Tudo* e clique em *Concluir*.

![](./media/guidance-elasticsearch/jmeter-deploy24.png)

Na janela *Gerenciador de Pacotes*, expanda o nó correspondente ao seu projeto. Verifique se o projeto contém uma pasta chamada *src*. Essa pasta contém o código-fonte do teste JUnit. O projeto pode ser compilado e implantado seguindo as instruções abaixo.

![](./media/guidance-elasticsearch/jmeter-deploy25.png)

## Como implantar um teste JUnit no JMeter

Este projeto pressupõe que você criou um projeto chamado LoadTest contendo uma classe de Teste JUnit chamada `BulkLoadTest.java` que aceita parâmetros de configuração passados como uma única cadeia de caracteres para um construtor (esse é o mecanismo esperado pelo JMeter).

No IDE Eclipse, no Gerenciador de Pacotes, clique com o botão direito no nó do projeto e clique em *Exportar*.

![](./media/guidance-elasticsearch/jmeter-deploy26.png)

No *Assistente de Exportação*, na página *Selecionar*, expanda o nó *Java*, clique em *Arquivo JAR* e clique em *Avançar*.

![](./media/guidance-elasticsearch/jmeter-deploy27.png)

Na página *Especificação de Arquivo JAR*, na caixa *Selecionar os recursos para exportação*, expanda o projeto que está sendo exportado e cancele a seleção de tudo, exceto da pasta *src*, desmarque *.classpath*, desmarque *.project* e desmarque *pom.xml*. Na caixa *Arquivo JAR*, forneça um nome de arquivo e o local para o JAR (ele deve receber a extensão de arquivo .jar) e clique em *Concluir*.

![](./media/guidance-elasticsearch/jmeter-deploy28.png)

Usando o Windows Explorer, copie o arquivo JAR que você acabou de criar para a JVM mestre do JMeter e salve-a na pasta *apache-jmeter-2.13\\lib\\junit* sob a pasta na qual instalou o JMeter (consulte o procedimento [Criando a máquina virtual JMeter mestre](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-master-virtual-machine) para saber mais).

Retorne ao Eclipse, expanda a janela Gerenciador de Pacotes e anote todos os arquivos JAR e seus locais listados na pasta *Dependências do Maven* do projeto. Observe que os arquivos exibidos na imagem a seguir podem variar, dependendo de qual versão do Elasticsearch você está usando:

![](./media/guidance-elasticsearch/jmeter-deploy29.png)

Usando o Windows Explorer, copie cada arquivo JAR citado na pasta Dependências do Maven para a pasta *apache-jmeter-2.13\\lib\\junit* na VM mestre do JMeter.

Se a pasta *lib\\junit* já contiver versões mais antigas desses arquivos JAR, remova essas versões. Se você deixá-las onde estão, talvez o teste JUnit não funcione, pois as referências podem ser resolvidas para os JARs errados.

Na VM mestre do JMeter, pare o JMeter caso ele esteja em execução. Inicie o JMeter. No JMeter, clique com o botão direito do mouse no *Plano de Teste*, clique em *Adicionar*, clique em *Threads (Usuários)* e clique em *Grupo de Threads*.

![](./media/guidance-elasticsearch/jmeter-deploy30.png)

No nó *Plano de Teste*, clique com o botão direito em *Grupo de Threads*, clique em *Adicionar*, clique em *Amostra* e clique em *Solicitação do JUnit*.

![](./media/guidance-elasticsearch/jmeter-deploy31.png)

Na página *Solicitação de JUnit*, selecione *Procurar anotações do JUnit4 (em vez de JUnit 3)*. Na caixa de listagem suspensa *Classname*, selecione a classe de teste de carga JUnit (ela estará listada na forma *&lt;pacote&gt;.&lt;classe&gt;*), na caixa de listagem suspensa *Método de Teste*, selecione o método de teste JUnit (esse é o método que executa o trabalho associado ao teste e deve ser marcado com a anotação *@test* no projeto do Eclipse) e digite os valores a serem passados para o construtor na caixa *Rótulo da Cadeia de Caracteres do Construtor*. Os detalhes mostrados na imagem a seguir são apenas exemplos. O *Classname*, o *Método de Teste* e o *Rótulo da Cadeia de Caracteres do Construtor* provavelmente diferem dos exibidos.

![](./media/guidance-elasticsearch/jmeter-deploy32.png)

Se sua classe não aparecer na caixa de listagem suspensa *Classname*, provavelmente o JAR não foi exportado corretamente ou não foi colocado na pasta *lib\\junit*, ou alguns dos JARs dependentes não presentes na pasta *lib\\junit*. Se isso ocorrer, exporte novamente o projeto do Eclipse e selecione os recursos *src*, copie o JAR na pasta *lib\\junit* e, em seguida, verifique se que você copiou todos os JARs dependentes listados pelo Maven na pasta *lib*.

Feche o JMeter. Não é necessário salvar o plano de teste. Copie o arquivo JAR que contém a classe de teste JUnit na pasta */home/&lt;nome de usuário&gt;/apache-jmeter-2.13/lib/junit* em cada uma das VMs subordinadas do JMeter (*&lt;nome de usuário&gt;* é o nome do usuário administrativo que você especificou quando criou a VM; confira [Como criar as máquinas virtuais subordinadas do JMeter](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-subordinate-virtual-machines) para saber mais.)

Copie os arquivos JAR dependentes exigidos para a classe de teste JUnit na pasta `/home/[username]/apache-jmeter-2.13/lib/junit` em cada uma das VMs JMeter subordinadas. Primeiro, remova as versões mais antigas dos arquivos JAR desta pasta.

Você pode usar o utilitário `pscp` para copiar os arquivos de um computador com Windows para o Linux.

[ Criando um ambiente de teste de desempenho para Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Como criar um ambiente de testes de desempenho para Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md

<!---HONumber=AcomDC_0224_2016-->