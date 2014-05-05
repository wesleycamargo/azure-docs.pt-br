<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Mecanismo de recomendação do Hadoop" pageTitle="Mecanismo de recomendação do Hadoop (.NET) | Azure" metaKeywords="Azure Apache Mahout, exemplo de recomendação do Azure, tutorial de recomendação do Azure, mecanismo de recomendação do Azure" description="Um tutorial que ensina como usar o mecanismo de recomendação Apache Mahout com o Azure para criar sugestões de música com base nos hábitos do ouvinte." disqusComments="1" umbracoNaviHide="1" title="Mecanismo de recomendação simples usando o Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />



# Mecanismo de recomendação simples usando o Apache Mahout

O Apache Mahout™ é uma biblioteca de aprendizado de máquina para uso em aplicativos de aprendizado de máquina escalonáveis. Os mecanismos de recomendação são um dos tipos mais populares de aplicativos de aprendizado de máquina em uso atualmente e têm vários aplicativos de marketing óbvios.

O Apache Mahout fornece uma implementação interna para Filtragem de Colaboração baseada em item. Essa abordagem é amplamente usada para conduzir mineração de dados de recomendação. A filtragem de colaboração baseada em item foi desenvolvida pela Amazon.com. A ideia aqui é que os dados sobre as preferências do usuário que exibem correlações entre as preferências de itens podem ser usados para inferir as preferências de futuros usuários de um grupo semelhante.

Neste tutorial, você usa o site [Million Song Dataset (a página pode estar em inglês)](http://labrosa.ee.columbia.edu/millionsong/tasteprofile) e baixa o [conjunto de dados (a página pode estar em inglês)](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip) para criar recomendações de músicas para usuários com base em seus hábitos de ouvinte.



Você aprenderá:

* Como usar mecanismos de recomendação

Este tutorial é composto dos seguintes segmentos:

1. [Instalação e configuração](#setup)
2. [Examinando e formatando dados](#segment1)
3. [Instalando o Mahout](#Segment2)
4. [Executando o trabalho Mahout](#segment2)

## <a name="setup"></a>Instalação e configuração 

Este tutorial pressupõe que você tenha instalado o Azure e a visualização do HDInsight e criado um cluster HDInsight no qual você pode executar um exemplo. Se você ainda não tiver feito isso, consulte o tutorial [Introdução ao Azure HDInsight](/pt-br/manage/services/hdinsight/get-started-hdinsight/) para obter instruções sobre como atender a esses pré-requisitos.

## <a name="segment1"></a>Examinando e formatando os dados 

Este exemplo trata da maneira como os usuários expressam uma preferência para determinadas músicas. Pressupõe-se que o número de vezes que um usuário ouve uma música fornece uma medida da preferência do usuário por essa música. Os padrões detectados nos dados de preferências podem ser usados para prever as preferências futuras do usuário com base em algumas de suas preferências musicais expressadas. Você pode exibir um exemplo desse conjunto de dados na seção **Descrição** da página da web [Subconjunto de perfis de preferências do Echo Nest](http://labrosa.ee.columbia.edu/millionsong/tasteprofile):

![O subconjunto de perfis de preferências do Echo Nest][echo-nest]

###Dados de exemplo do Million Song Dataset

Para usar esse conjunto de dados com o Mahout você precisa fazer duas coisas:

1.	Converter as IDs das músicas e dos usuários para valores inteiros.
2.	Salvar os novos valores com suas classificações em um arquivo separado por vírgula.

Se você não tiver o Visual Studio 2010 instalado, ignore esta etapa e vá para a seção Executando o trabalho Mahout para obter uma versão gerada previamente.

Inicie o Visual Studio 2010. No Visual Studio, selecione **Arquivo -> Novo -> Projeto**. No painel **Modelos Instalados**, no nó **Visual C#**, selecione a categoria **Janela** e selecione **Aplicativo de Console** na lista. Nomeie o projeto "ConvertToMahoutInput" e clique no botão **OK**.

![criando um aplicativo de console][create-console-app]

###Criando um aplicativo de console

1. Depois de criar o aplicativo, abra o arquivo **Program.cs** e adicione os seguintes membros estáticos à classe **Program**:


		const char tab = '\u0009';
		static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
		static Dictionary<string, int> songMapping = new Dictionary<string, int>();	


2. Em seguida, adicione a instrução `using System.IO;` e preencha o método **Main** com o seguinte código:

		var inputStream = File.Open(args[0], FileMode.Open);
		var reader = new StreamReader(inputStream);

		var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
		var writer = new StreamWriter(outStream);

		var i = 1;

		var line = reader.ReadLine();
		while (!string.IsNullOrWhiteSpace(line))
		{
    		i++;
    		if (i > 5000)
			break;
    		var outLine = line.Split(tab);

    		int user = GetUser(outLine[0]);
    		int song = GetSong(outLine[1]);

    		writer.Write(user);
	    	writer.Write(',');
	    	writer.Write(song);
	   	 	writer.Write(',');
	   	 	writer.WriteLine(outLine[2]);

    		line = reader.ReadLine();
		}

		Console.WriteLine("saved {0} lines to {1}", i, args[0]);
	
		reader.Close();
		writer.Close();
	
		SaveMapping(usersMapping, "users.csv");
		SaveMapping(songMapping, "mInput.csv");
	
		Console.WriteLine("Mapping saved");
		Console.ReadKey();


3. Agora, crie as funções **GetUser** e **GetSong** que convertem as ids em inteiros:

		static int GetUser(string user)
		{
    		if (!usersMapping.ContainsKey(user))
        		usersMapping.Add(user, usersMapping.Count + 1);

    		return usersMapping[user];
		}

		static int GetSong(string song)
		{
    		if (!songMapping.ContainsKey(song))
        		songMapping.Add(song, songMapping.Count + 1);

    		return songMapping[song];
		}

4. Finalmente, crie o utilitário que implementa o método SaveMapping que salva dicionários de mapeamento de bot em arquivos .csv.

		static void SaveMapping(Dictionary<string, int> mapping, string fileName)
		{
    		var stream = File.Open(fileName, FileMode.Create);
    		var writer = new StreamWriter(stream);

    		foreach (var key in mapping.Keys)
    		{
        		writer.Write(key);
        		writer.Write(',');
        		writer.WriteLine(mapping[key]);
    		}

    		writer.Close();
		}

5. Baixe os dados de exemplo [deste link](http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip). Depois do download, abra **train\_triplets.txt.zip** e extraia **train\_triplets.txt**.

	Ao executar o utilitário, inclua um argumento de linha de comando com o local do **train\_triplets.txt**. Para fazer isso, clique com o botão direito do mouse no nó do projeto **ConvertToMahoutInput** no **Gerenciador de Soluções** e selecione **Propriedades**. Na página de propriedades do projeto, selecione a guia**Depurar** no lado esquerdo e adicione o caminho de &lt;localpath&gt;train\_triplets.txt à caixa de texto **Argumentos de linha de comando**:

	![configurando os argumentos de linha de comando][set-cmd-line-args]

###Configurando o argumento de linha de comando

- Pressione **F5** para executar o programa. Depois de concluir, abra a pasta **bin\Debug** no local no qual o projeto foi salvo e exiba a saída do utilitário.  Você deve localizar users.txt e mInput.txt

## <a name="segment2"></a>Instalando o Mahout 

- Abra o portal do cluster HDInsight e, em seguida, clique no ícone **Área de Trabalho Remota**.

	![O ícone de Gerenciar Cluster][mng-cluster-icon]

###O ícone de Área de Trabalho Remota

O HDInsight não inclui o Mahout por padrão. Mas como faz parte do ecossistema do Hadoop, ele pode ser baixado no site do [Mahout](http://mahout.apache.org/). A versão mais recente é a 0.7, mas esse conjunto de instruções é compatível com a versão 0.5 ou 0.7.

1. Primeiro, baixe o [Mahout versão 0.7](http://www.apache.org/dyn/closer.cgi/mahout/) em sua máquina local.

2. Em seguida, copie-o no cluster selecionando o arquivo zip local e pressione control-v para copiar e colá-lo em seu cluster Hadoop.

	![Carregando o Mahout][uploading-mahout]

###Copiando o Mahout no Headnode

1. Finalmente, clique com o botão direito do mouse no arquivo zip depois que o processo de cópia for executado, extraia a distribuição do Mahout em C:\apps\dist.  Agora você tem o Mahout instalado em C:\apps\dist\mahout-distribution-0.7.  

2. Renomeie a pasta para c:\apps\dist\mahout-0.7 para manter a simplicidade.  

### <a name="segment3"></a>Executando o trabalho Mahout 

1. Copie o arquivo **mInput.txt** da pasta **bin\Debug** para o **c:\\** no cluster remoto. Depois que o arquivo for copiado, extraia-o. Conforme mencionado na seção anterior, a cópia de um arquivo em uma sessão RDP remota é feita pressionando control-C no seu computador local depois de selecionar os arquivos, e control-v na janela da sessão RDP. 

2. Crie um arquivo que contenha a ID do usuário para o qual você irá gerar recomendações. Para fazer isso, basta criar um arquivo de texto chamado **users.txt** no **c:\\** contendo a id de um único usuário.

<div class="dev-callout"> 
<b>Observação</b> 
<p>Você pode gerar recomendações para mais usuários, colocando suas IDs em linhas separadas. Se encontrar problemas ao gerar mInput.txt e users.txt, você poderá baixar uma versão gerada previamente neste <a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">repositório</a> do github. 

É mais conveniente baixar tudo como um <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">arquivo zip</a>. Localize o users.txt e o mInput.txt e copie-os no cluster remoto na pasta c:\</p> 
</div>

Neste ponto, você deve abrir uma janela do terminal do Hadoop e navegar para a pasta que contém o users.txt e o mInput.txt.  

![Janela de comando do Mahout][mahout-cmd-window]

###Janela de comando do Hadoop

1. Em seguida, copie **mInput.txt** e **users.txt** no HDFS. Para fazer isso, abra o **Shell de Comando do Hadoop** e execute os seguintes comandos:

		hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
		hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2. Verifique se os arquivos foram copiados no HDFS:

		hadoop fs -ls input/

	Esse comando deve mostrar:  

		Found 2 items
		-rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
		-rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3. Agora, podemos executar o trabalho Mahout usando o seguinte comando:

		c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

	Há muitas outras funções de "distância" que o mecanismo de recomendação pode usar para comparar a fector do recurso para diferentes usuários, você pode experimentar e alterar a classe Similarity para SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE.  Para fins deste tutorial não mostraremos o aspecto da ciência dos dados detalhados do Mahout. 

4. O trabalho Mahout deve ser executado por vários minutos, depois dos quais será criado um arquivo de saída. Execute o seguinte comando para criar uma cópia local do arquivo de saída:

		hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5. Abra o arquivo **output.txt** na pasta raiz do **c:\\** e inspecione seu conteúdo. A estrutura do arquivo é a seguinte:

		user	[song:rating,song:rating, ...]

6. Para usar outras partes do Mahout no seu cluster, você deve salvar uma cópia do Mahout.cmd no diretório bin da distribuição do Mahout.  


## <a name="summary"></a>Resumo 

Os mecanismos de recomendação fornecem funcionalidade importante para muitos sites de rede social modernos, compras online, mídia de streaming e outros sites da internet. O Mahout fornece um mecanismo de recomendação pronto para uso, que é fácil de usar, contém muitos recursos úteis e é escalonável no Hadoop.

##Próximas etapas

Embora este artigo demonstre o uso da linha de comando do Hadoop, você também pode executar tarefas usando o Console Interativo do HDInsight. Para obter mais informações, consulte [Diretrizes: JavaScript Interativo do HDInsight e Consoles do Hive][interactive-console].


[echo-nest]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
[create-console-app]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
[set-cmd-line-args]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
[mng-cluster-icon]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
[uploading-mahout]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
[mahout-cmd-window]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG

