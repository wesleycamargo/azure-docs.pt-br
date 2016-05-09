<properties
	pageTitle="Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados | Microsoft Azure"
	description="Execute várias tarefas de exploração e modelagem de dados na Máquina Virtual de Ciência de Dados."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="gokuma;weig;bradsev" />

# Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados

A Máquina Virtual de Ciência de Dados da Microsoft (DSVM) é um poderoso ambiente de desenvolvimento de ciência de dados que permite executar várias tarefas de exploração e modelagem de dados. O ambiente já vem compilado e agrupado com várias ferramentas populares de análise de dados que facilitam o início rápido de sua análise para implantações locais, na nuvem ou híbridas. A DSVM trabalha de forma muito próxima com muitos serviços do Azure e é capaz de ler e processar dados que já estão armazenados no Azure, no Azure SQL Data Warehouse, Azure Data Lake, Armazenamento do Azure ou no Banco de Dados de Documentos. Ela também aproveita outras ferramentas de análise, como o Aprendizado de Máquina do Azure e o Azure Data Factory.


Neste artigo, mostrarei detalhadamente como usar sua DSVM para executar várias tarefas de ciência de dados e interagir com outros serviços do Azure. Veja algumas tarefas que você pode realizar na DSVM:

1. Explorar dados e desenvolver modelos localmente na DSVM usando o Microsoft R Server, Python
2. Usar um notebook Jupyter para experimentar com seus dados em um navegador usando Python 2, Python 3, Microsoft R, uma versão de R pronta para empresas desenvolvida para escalabilidade e desempenho
3. Operacionalizar modelos criados usando R e Python no Aprendizado de Máquina do Azure para que os aplicativos cliente possam acessar seus modelos usando uma interface simples de serviços Web
4. Administrar os recursos do Azure usando o Portal do Azure ou o Powershell
5. Estender o espaço de armazenamento e compartilhar conjuntos de dados/códigos em grande escala com toda sua equipe criando um Armazenamento de Arquivos do Azure como uma unidade montável na DSVM
6. Compartilhar código com sua equipe usando o Github e acessar o repositório usando os clientes Git pré-instalados — Git Bash, Git GUI.
7. Acessar vários serviços de análise e dados do Azure, como Armazenamento de Blobs do Azure, Azure Data Lake, Azure HDInsight (Hadoop), Banco de Dados de Documentos do Azure, Azure SQL Data Warehouse e bancos de dados
8. Criar relatórios e painéis usando o Power BI Desktop pré-instalado na DSVM e implantá-los na nuvem
9. Dimensionar dinamicamente a DSVM para atender às suas necessidades de projeto
10. Instalar ferramentas adicionais na sua máquina virtual   


>[AZURE.NOTE] Muitos serviços de armazenamento e análise de dados listados neste artigo sofrerão cobranças adicionais por uso. Confira a página [Preço do Azure](https://azure.microsoft.com/pricing/) para obter detalhes.


**Pré-requisitos**

- Você também precisará de uma assinatura do Azure. Você pode se inscrever para uma avaliação gratuita [aqui](https://azure.microsoft.com/free/).

- As instruções para o provisionamento de uma Máquina Virtual de Ciência de Dados no Portal do Azure estão disponíveis em [Criação de uma máquina virtual](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## 1\. Explorar dados e desenvolver modelos usando o Microsoft R Server ou Python

Você pode usar linguagens como R e Python para fazer a análise de dados diretamente na DSVM.

Para R, você pode usar um IDE chamado "Revolution R Enterprise 8.0", que pode ser encontrado no menu Iniciar ou na área de trabalho. A Microsoft forneceu bibliotecas adicionais sobre o Open Source/CRAN-R para permitir a análise escalonável e a capacidade de analisar dados maiores que o tamanho da memória permitido fazendo análise paralela em partes. Você também pode instalar um IDE do R que quiser, por exemplo, [RStudio](https://www.rstudio.com/products/rstudio-desktop/).

Para Python, você pode usar um IDE como o Visual Studio Community Edition, que tem a extensão PTVS (Ferramentas Python para o Visual Studio) pré-instalada. Por padrão, somente um Python 2.7 básico está configurado no PTVS (sem nenhuma biblioteca de análise como SciKit, Pandas). Para habilitar o Anaconda Python 2.7 e 3.5, é preciso fazer o seguinte:

* Criar ambientes personalizados para cada versão navegando até **Ferramentas** -> **Ferramentas Python** -> **Ambientes Python** e clicando em "**+ Personalizado**" no Visual Studio 2015 Community Edition
* Forneça uma descrição e defina os caminhos de prefixo do ambiente como *c:\\anaconda* para o Anaconda Python 2.7 OU *c:\\anaconda\\envs\\py35* para o Anaconda Python 3.5
* Clique em **Detecção Automática** e em **Aplicar** para salvar o ambiente.

Veja a aparência do ambiente personalizado configurado no Visual Studio.

![Configuração do PTVS](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

Confira a [documentação do PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter mais detalhes sobre como criar os Ambientes Python.

Agora você está pronto para criar um novo projeto em Python. Navegue até **Arquivo** -> **Novo** -> **Projeto** -> **Python** e selecione o tipo de aplicativo Python que você está criando. Você pode definir o ambiente do Python para o projeto atual como a versão que quiser (Anaconda 2.7 ou 3.5): clique com botão direito do mouse no **Ambiente Python**, escolha **Adicionar/remover ambientes Python** e, em seguida, selecione o ambiente desejado para associar ao projeto. Saiba mais sobre como trabalhar com o PTVS na página de [documentação](https://github.com/Microsoft/PTVS/wiki) do produto.

## 2\. Usando um Notebook Jupyter para explorar e modelar seus dados com o Python ou R

O Notebook Jupyter é um poderoso ambiente que fornece um "IDE" baseado em navegador para exploração e modelagem de dados. É possível usar Python 2, Python 3 ou R (ambos software livre e Microsoft R Server) em um Notebook Jupyter.

Para iniciar o Notebook Jupyter, clique no ícone do menu Iniciar/ícone da área de trabalho chamado **Notebook Jupyter**. Na DSVM, também é possível navegar até "https://localhost:9999/" para acessar o Notebook Jupyter. Se ele solicitar uma senha, use as instruções fornecidas na seção ***Como criar uma senha forte no servidor de notebook Jupyter*** do tópico [Provisionar a Máquina Virtual do Microsoft Data Science](machine-learning-data-science-provision-vm.md) para criar uma senha forte para acessar o notebook do Jupyter.

Assim que abrir o notebook, você verá um diretório que contém alguns notebooks de exemplo que são pré-empacotados com a DSVM. Agora você pode:

- clicar no notebook e ver o código.
- executar cada célula pressionando **SHIFT-ENTER**.
- executar o notebook inteiro clicando em **Célula** -> **Executar**
- criar um novo notebook clicando no ícone Jupyter (canto superior esquerdo), clicando no botão **Novo** à direita e escolhendo a linguagem do notebook (também conhecida como kernels).   


>[AZURE.NOTE] Atualmente, oferecemos suporte ao Python 2.7, Python 3.5 e R. O kernel do R permite programação no Open Source R, bem como no Microsoft R Server escalonável corporativo.


Uma vez no notebook, você pode explorar seus dados, criar o modelo e testar o modelo usando suas bibliotecas escolhidas.


## 3\. Compilar os modelos usando R e Python e operacionalizá-los usando o Aprendizado de Máquina do Azure

Depois de criar e validar o modelo, a próxima etapa geralmente é implantá-lo na produção. Isso permite aos aplicativos cliente invocar as previsões do modelo em tempo real ou em m modo de lote. O Aprendizado de Máquina do Azure fornece um mecanismo para operacionalizar um modelo compilado em R ou Python.

Quando você operacionaliza seu modelo no Aprendizado de Máquina do Azure, um serviço Web é exposto, o que permite aos clientes fazer chamadas REST que são passadas nos parâmetros de entrada e recebem previsões do modelo como saídas.


>[AZURE.NOTE] Se ainda não tiver se inscrito no AM do Azure, você poderá obter um espaço de trabalho gratuito ou um espaço de trabalho padrão visitando a página inicial do [Estúdio AM do Azure](https://studio.azureml.net/) e clicando em "Introdução".


### Compilar e operacionalizar modelos em Python

Veja um trecho de código desenvolvido em um Notebook Jupyter Python que compila um modelo simples usando a biblioteca SciKit-learn.

	#IRIS classification
	from sklearn import datasets
	from sklearn import svm
	clf = svm.SVC()
	iris = datasets.load_iris()
	X, y = iris.data, iris.target
	clf.fit(X, y)

O método usado para implantar seus modelos em python no Aprendizado de Máquina do Azure insere a previsão do modelo em uma função, e a decora com atributos fornecidos pela biblioteca pré-instalada de python do Aprendizado de Máquina do Azure, indicando sua ID do espaço de trabalho, Chave de API e os parâmetros de entrada e retorno do Aprendizado de Máquina do Azure.

	from azureml import services
	@services.publish(workspaceid, auth_token)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(int) #0, or 1, or 2
	def predictIris(sep_l, sep_w, pet_l, pet_w):
 	inputArray = [sep_l, sep_w, pet_l, pet_w]
	return clf.predict(inputArray)

Agora, um cliente pode fazer chamadas ao serviço Web. Há wrappers de conveniência que constroem as solicitações à API REST. Veja um exemplo de código para consumir o serviço Web.

	# Consume through web service URL and keys
	from azureml import services
	@services.service(url, api_key)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(float)
	def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

	IrisPredictor(3,2,3,4)


>[AZURE.NOTE] Atualmente, a biblioteca de Aprendizado de Máquina do Azure só tem suporte no Python 2.7.


### Criar e operacionalizar modelos R

Você pode implantar modelos R criados na Máquina Virtual de Ciência de Dados ou em algum lugar do Aprendizado de Máquina do Azure, de uma maneira semelhante à realizada para o Python. Execute estas etapas:

- crie um arquivo settings.json, conforme abaixo, para fornecer sua ID de espaço de trabalho e o token de autenticação.
- escreva um wrapper para a função de previsão do modelo.
- chame ```publishWebService``` na biblioteca de Aprendizado de Máquina do Azure para transmitir o wrapper da função.  

Estes são o procedimento e os trechos de código que podem ser usados para configurar, compilar, publicar e consumir um modelo como um serviço Web no Aprendizado de Máquina do Azure.

#### Configuração

1.  Instale o pacote R do AM do Azure ao digitar ```install.packages("AzureML")``` no IDE do Revolution R Enterprise 8.0 ou IDE do R.
2.  Baixe o RTools [daqui](https://cran.r-project.org/bin/windows/Rtools/). Você precisa do utilitário zip no caminho (e chamado zip.exe) para operacionalizar o pacote R no AM do Azure.
3.  Crie um arquivo settings json em um diretório chamado ```.azureml``` em seu diretório base e insira os parâmetros de seu espaço de trabalho de AM do Azure:

Estrutura do arquivo settings.json:

	{"workspace":{
	"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
	"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
	}}


#### Compilar um modelo em R e publicá-lo no AM do Azure

	library(AzureML)
	ws <- workspace(config="~/.azureml/settings.json")

	if(!require("lme4")) install.packages("lme4")
	library(lme4)
	set.seed(1)
	train <- sleepstudy[sample(nrow(sleepstudy), 120),]
	m <- lm(Reaction ~ Days + Subject, data = train)

	# Define a prediction function to publish based on the model:
	sleepyPredict <- function(newdata){
  		predict(m, newdata=newdata)
	}

	ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### Consumir o modelo implantado no AzureML

Para consumir o modelo em um aplicativo cliente, usamos a biblioteca do Aprendizado de Máquina do Azure para pesquisar o serviço Web publicado por nome usando a chamada à API `services` para determinar o ponto de extremidade. Em seguida, basta chamar a função `consume` e passar o quadro de dados a ser previsto. O código a seguir é usado para consumir o modelo publicado como um serviço Web do Aprendizado de Máquina do Azure.


	library(AzureML)
	library(lme4)
	ws <- workspace(config="~/.azureml/settings.json")

	s <-  services(ws, name = "sleepy lm")
	s <- tail(s, 1) # use the last published function, in case of duplicate function names

	ep <- endpoints(ws, s)

	# OK, try this out, and compare with raw data
	ans = consume(ep, sleepstudy)$ans

Saiba mais sobre a biblioteca de R do Aprendizado de Máquina do Azure [aqui](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).


## 4\. Administrar os recursos do Azure usando o Portal do Azure ou o Powershell

O DSVM não só permite que você crie uma solução de análise localmente na máquina virtual, mas também permite acessar serviços na nuvem do Microsoft Azure. O Azure fornece vários serviços de análise de dados, armazenamento e computação, além de outros serviços que você pode administrar e acessar da DSVM.

Para administrar os recursos de nuvem e a assinatura do Azure, você pode usar o navegador e apontar para o [Portal do Azure](portal.azure.com). Também é possível usar o Azure Powershell para administrar os recursos e a assinatura do Azure por meio de um script. Você pode executar o Azure PowerShell de um atalho na área de trabalho ou clicar em "Microsoft Azure Powershell" no menu Iniciar. Consulte a [documentação do Microsoft Azure Powershell](../powershell-azure-resource-manager.md) para obter mais informações sobre como você pode administrar seus recursos e assinatura do Azure usando os scripts do Windows PowerShell.


## 5\. Estender o espaço de armazenamento com um sistema de arquivos compartilhado

Os cientistas de dados podem compartilhar grandes conjuntos de dados, códigos ou outros recursos dentro da equipe. O DSVM em si tem cerca de 70 GB de espaço disponível. Para estender o armazenamento, você pode usar o Serviço de Arquivos do Azure e montá-lo no DSVM ou acessá-lo por meio da API REST.


>[AZURE.NOTE] O espaço máximo do compartilhamento do Serviço de Arquivos do Azure é de 5 TB e o limite do tamanho de arquivo individual é 1 TB.


É possível usar o Azure Powershell para criar um compartilhamento do Serviço de Arquivos do Azure. Veja o script a ser executado no Azure PowerShell para criar um compartilhamento do Serviço de Arquivos do Azure.

	# Authenticate to Azure.
	Login-AzureRmAccount
	# Select your subscription
	Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
	# Create a new resource group.
	New-AzureRmResourceGroup -Name <dsvmdatarg>
	# Create a new storage account. You can reuse existing storage account if you wish.
	New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
	# Set your current working storage account
	Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

	# Create a Azure File Service Share
	$s = New-AzureStorageShare <<teamsharename>>
	# Create a directory under the FIle share. You can give it any name
	New-AzureStorageDirectory -Share $s -Path <directory name>
	# List the share to confirm that everything worked
	Get-AzureStorageFile -Share $s


Agora que você criou um compartilhamento de arquivos do Azure, é possível montá-lo em qualquer máquina virtual no Azure. É altamente recomendável que a VM esteja no mesmo datacenter do Azure que a conta de armazenamento para evitar latência e cobranças por transferência de dados. Veja os comandos para montar a unidade na DSVM que você pode executar no Azure PowerShell.


	# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
	cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

	# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
	net use z:  \<mydatadisk>.file.core.windows.net<<teamsharename>>


Agora, você pode acessar essa unidade como faria com qualquer unidade normal na VM.

## 6\. Compartilhar código com sua equipe usando o Github

O Github é um repositório de códigos onde você pode encontrar muitas fontes e códigos de exemplo para diferentes ferramentas usando diversas tecnologias compartilhadas pela comunidade de desenvolvedores. Ele usa Git como a tecnologia para rastrear e armazenar versões dos arquivos de código. O Github também é uma plataforma em que você pode criar seu próprio repositório para armazenar o código compartilhado e a documentação da sua equipe, implementar o controle de versão e também controlar quem tem acesso para exibir e contribuir com código. Visite as [páginas de ajuda do Github](https://help.github.com/) para obter mais informações sobre como usar o Git. Você pode usar o Github como uma das formas de colaborar com sua equipe, usar o código desenvolvido pela comunidade e retribuir com código na comunidade.

O DSVM já vem carregado com ferramentas cliente na linha de comando e na GUI para acessar o repositório do Github. A ferramenta de linha de comando para trabalhar com Git e Github é chamada Git Bash. O Visual Studio instalado na DSVM tem as extensões de Git. Você pode encontrar ícones de inicialização para essas ferramentas no menu Iniciar e na área de trabalho.

Para baixar o código de um repositório Github, você usará o comando ```git clone```. Por exemplo, para baixar o repositório de ciência de dados publicado pela Microsoft no diretório atual, você pode executar o comando a seguir quando estiver no ```git-bash```.

	git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

No Visual Studio, você pode fazer a mesma operação de clonagem. A captura de tela abaixo mostra como acessar as ferramentas Git e Github no Visual Studio.

![Git no Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


Encontre mais informações sobre como usar Git para trabalhar com o repositório Github nos vários recursos disponíveis em github.com. O [roteiro](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) é uma referência útil.


## 7\. Acessar vários serviços de análise e dados do Azure

### Blob do Azure

O blob do Azure é um armazenamento em nuvem confiável e econômico para pequenos e grandes volumes de dados. Vamos analisar como você pode mover os dados para o Blob do Azure e acessar os dados armazenados em um Blob do Azure.

**Pré-requisito**

- **Crie sua conta de Armazenamento de Blobs do Azure no [Portal do Azure](http://portal.azure.com).**

![Create\_Azure\_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- Confirme se a ferramenta de linha de comando pré-instalada, AzCopy, foi encontrada em ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Você pode adicionar o diretório que contém o azcopy.exe à sua variável de ambiente PATH para evitar ter que digitar o caminho completo do comando ao executar essa ferramenta. Para saber mais sobre a ferramenta AzCopy, consulte a [documentação do AzCopy](../storage/storage-use-azcopy.md)

- Inicie o Gerenciador de Armazenamento do Azure clicando um ícone na área de trabalho da VM. Você também a encontrará no diretório ***C:\\Arquivos de Programas (x86)\\Neudesic\\Azure Storage Explorer 6***.


![AzureStorageExplorer\_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)


**Mover dados da VM para o Blob do Azure: AzCopy**

Para mover dados entre seus arquivos locais e o armazenamento de blobs, você pode usar o AzCopy na linha de comando ou no PowerShell:

	AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Substitua **C:\\minhapasta** pelo caminho em que seu arquivo está armazenado, **minhacontadearmazenamento** pelo nome da conta do armazenamento de blobs, **meucontêiner** pelo nome do contêiner, **chave da conta de armazenamento** pela sua chave de acesso de armazenamento de blobs. Você pode encontrar as credenciais da conta de armazenamento no [Portal do Azure](http://portal.azure.com).

![StorageAccountCredential\_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Execute o comando AzCopy no PowerShell ou em um prompt de comando. Veja um exemplo de uso do comando AzCopy:


	# Copy *.sql from local machine to a Azure Blob
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

	# Copy back all files from Azure Blob container to Local machine

	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Depois de executar o comando AzCopy para copiar um blob do Azure, seu arquivo será mostrado resumidamente no Gerenciador de Armazenamento do Azure.

![AzCopy\_run\_finshed\_Storage\_Explorer\_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**Mover dados da VM para o Blob do Azure: Gerenciador de Armazenamento do Azure**

Você também pode carregar dados do arquivo local na VM usando o Gerenciador de Armazenamento do Azure:


![](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_upload_v2.png)


**Ler dados do Blob do Azure: módulo de leitor do AML**

No Estúdio do Aprendizado de Máquina do Azure, é possível usar um **módulo Leitor** para ler dados do blob.


![AML\_ReaderBlob\_Module\_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**Ler dados do Blob do Azure: ODBC do Python**

Você pode usar **BlobService** biblioteca para ler dados diretamente do blob em um Notebook Jupyter ou em um programa Python.

Primeiro, importe os pacotes necessários:

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random

Em seguida, conecte suas credenciais de conta do Blob do Azure e leia os dados no Blob:

    CONTAINERNAME = 'xxx'
	STORAGEACCOUNTNAME = 'xxxx'
	STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
	BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
	localfilename = 'trip_data_1.csv'
	LOCALDIRECTORY = os.getcwd()
	LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

	#download from blob
	t1 = time.time()
	blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
	t2 = time.time()
	print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

	#unzipping downloaded files if needed
	#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
	#    z.extractall(LOCALDIRECTORY)

	df1 = pd.read_csv(LOCALFILE, header=0)
	df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
	print 'the size of the data is: %d rows and  %d columns' % df1.shape

Os dados são lidos como em um quadro de dados:

![IPNB\_data\_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### Azure Data Lake

O Armazenamento do Azure Data Lake é um repositório de grande escala para cargas de trabalho de análise de big data e é compatível com HDFS (Hadoop Distributed File System), funciona com o ecossistema do Hadoop e com a Análise Azure Data Lake. Vamos mostrar como você pode mover os dados para o Armazenamento do Azure Data Lake e executar análises usando a Análise Azure Data Lake.

**Pré-requisito**

- Crie sua Análise Azure Data Lake no [Portal do Azure](http://portal.azure.com).

![Azure\_Data\_Lake\_Create\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- As **Ferramentas do Azure Data Lake** no **Visual Studio** encontradas neste [link](https://www.microsoft.com/download/details.aspx?id=49504) já estão instaladas no Visual Studio Community Edition, que está na máquina virtual. Após iniciar o Visual Studio e fazer logon em sua assinatura do Azure, você verá a conta e o armazenamento de Análise de Dados do Azure no painel esquerdo do Visual Studio.

![Azure\_Data\_Lake\_PlugIn\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


**Mover dados da VM para o Data Lake: Gerenciador do Azure Data Lake**

Você pode usar o **Gerenciador do Azure Data Lake** para carregar dados dos arquivos locais em sua Máquina Virtual para o armazenamento do Data Lake.

![Azure\_Data\_Lake\_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Você também pode compilar um pipeline de dados para direcionar para produção o movimento de dados, de ou para o Azure Data Lake, usando o [Azure Data Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Indicamos este [artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) a fim de orientá-lo pelas etapas de criação dos pipelines de dados.

**Ler dados no Blob do Azure para Data Lake: U-SQL**

Se os dados residirem no Armazenamento de Blobs, você poderá lê-los diretamente no blob do armazenamento do Azure na consulta U-SQL. Antes de compor a consulta U-SQL, verifique se sua conta de Armazenamento de Blobs está vinculada ao Azure Data Lake. Acesse o **Portal do Azure**, encontre o painel de Análise Azure Data Lake, clique em **Adicionar Fonte de Dados**, escolha o tipo de armazenamento como **Armazenamento do Azure** e insira o Nome e a Chave da Conta de Armazenamento do Azure. Em seguida, você poderá fazer referência aos dados armazenados na conta de armazenamento.

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


No Visual Studio, você pode ler dados no armazenamento de blobs, manipular alguns dados, realizar engenharia de recursos, bem como gerar os dados resultantes para o Azure Data Lake ou Armazenamento de Blobs do Azure. Quando fizer referência aos dados no Armazenamento de Blobs, use ****wasb://**; quando fizer referência aos dados no Azure Data Lake, use ****swbhdfs://**

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Você pode usar as seguintes consultas U-SQL no Visual Studio:

	@a =
	    EXTRACT medallion string,
	            hack_license string,
	            vendor_id string,
	            rate_code string,
	            store_and_fwd_flag string,
	            pickup_datetime string,
	            dropoff_datetime string,
	            passenger_count int,
	            trip_time_in_secs double,
	            trip_distance double,
	            pickup_longitude string,
	            pickup_latitude string,
	            dropoff_longitude string,
	            dropoff_latitude string

	    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
	    USING Extractors.Csv();

	@b =
	    SELECT vendor_id,
	    COUNT(medallion) AS cnt_medallion,
	    SUM(passenger_count) AS cnt_passenger,
	    AVG(trip_distance) AS avg_trip_dist,
	    MIN(trip_distance) AS min_trip_dist,
	    MAX(trip_distance) AS max_trip_dist,
	    AVG(trip_time_in_secs) AS avg_trip_time
	    FROM @a
	    GROUP BY vendor_id;

	OUTPUT @b   
	TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
	USING Outputters.Csv();

	OUTPUT @b   
	TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
	USING Outputters.Csv();



Depois que a consulta for enviada ao servidor, será exibido um diagrama mostrando o status do seu trabalho.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**Dados da consulta no Data Lake: U-SQL**

Depois que o conjunto de dados for incluído no Azure Data Lake, você poderá usar a [linguagem U-SQL](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. A linguagem U-SQL é semelhante à T-SQL, mas combina alguns recursos do C# para que os usuários escrevam módulos personalizados, funções definidas pelo usuário, etc. Você pode usar os scripts da etapa anterior.

Depois que a consulta é enviada ao servidor, tripdata\_summary.CSV pode ser encontrado resumidamente no **Gerenciador do Azure Data Lake**; você pode visualizar os dados clicando com o botão direito do mouse no arquivo.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

Para ver as informações do arquivo:

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### Clusters HDInsight Hadoop

O Azure HDInsight é um serviço gerenciado do Apache Hadoop, Spark, HBase e Storm na nuvem. Você pode trabalhar facilmente com clusters do Azure HDInsight da máquina virtual de ciência de dados.

**Pré-requisito**

- Crie sua conta de Armazenamento de Blobs do Azure no [Portal do Azure](http://portal.azure.com). Essa conta de armazenamento é usada para armazenar dados dos clusters HDInsight.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- Personalize os Clusters do Azure HDInsight Hadoop no [Portal do Azure](machine-learning-data-science-customize-hadoop-cluster.md)

  - É preciso vincular a conta de armazenamento criada com o cluster HDInsight quando ele é criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

  - Você deve habilitar o **Acesso Remoto** ao nó principal do cluster após sua criação. Lembre das credenciais de acesso remoto que você especificar aqui (diferentes daqueles especificadas para o cluster durante sua criação): você precisará delas abaixo.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

  - Criar um espaço de trabalho de AM do Azure. Seus testes com o Aprendizado de Máquina serão armazenados neste espaço de trabalho de AM. Selecione as opções destacadas no Portal, conforme mostra a captura de tela abaixo.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


  - Em seguida, insira os parâmetros de seu espaço de trabalho de AM do Azure

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

  - Carregue os dados usando o IPython Notebook. Primeiro, importe os pacotes necessários, insira as credenciais, crie um banco de dados em sua conta de armazenamento e carregue os dados nos clusters HDI.


		#Import required Packages
		import pyodbc
		import time as time
		import json
		import os
		import urllib
		import urllib2
		import warnings
		import re
		import pandas as pd
		import matplotlib.pyplot as plt
		from azure.storage.blob import BlobService
		warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


		#Create the connection to Hive using ODBC
		SERVER_NAME='xxx.azurehdinsight.net'
		DATABASE_NAME='nyctaxidb'
		USERID='xxx'
		PASSWORD='xxxx'
		DB_DRIVER='Microsoft Hive ODBC Driver'
		driver = 'DRIVER={' + DB_DRIVER + '}'
		server = 'Host=' + SERVER_NAME + ';Port=443'
		database = 'Schema=' + DATABASE_NAME
		hiveserv = 'HiveServerType=2'
		auth = 'AuthMech=6'
		uid = 'UID=' + USERID
		pwd = 'PWD=' + PASSWORD
		CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
		connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
		cursor=connection.cursor()


		#Create Hive database and tables
		queryString = "create database if not exists nyctaxidb;"
		cursor.execute(queryString)

		queryString = """
		                create external table if not exists nyctaxidb.trip
		                (
		                    medallion string,
		                    hack_license string,
		                    vendor_id string,
		                    rate_code string,
		                    store_and_fwd_flag string,
		                    pickup_datetime string,
		                    dropoff_datetime string,
		                    passenger_count int,
		                    trip_time_in_secs double,
		                    trip_distance double,
		                    pickup_longitude double,
		                    pickup_latitude double,
		                    dropoff_longitude double,
		                    dropoff_latitude double)  
		                PARTITIONED BY (month int)
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)

		queryString = """
		                create external table if not exists nyctaxidb.fare
		                (
		                    medallion string,
		                    hack_license string,
		                    vendor_id string,
		                    pickup_datetime string,
		                    payment_type string,
		                    fare_amount double,
		                    surcharge double,
		                    mta_tax double,
		                    tip_amount double,
		                    tolls_amount double,
		                    total_amount double)
		                PARTITIONED BY (month int)
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)


		#Upload data from blob storage to HDI cluster
		for i in range(1,13):
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
		    cursor.execute(queryString)
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
		    cursor.execute(queryString)


- Como alternativa, você pode seguir este [passo a passo](machine-learning-data-science-process-hive-walkthrough.md) para carregar os dados sobre Táxis de NYC no cluster HDI. As principais etapas incluem:

	- AzCopy: baixar CSVs compactados do blob público na pasta local
	- AzCopy: carregar CSVs descompactados da pasta local no cluster HDI
	- Fazer logon no nó do cabeçalho do cluster Hadoop e se preparar para análise de exploração de dados

Depois que os dados são carregados no cluster HDI, você pode verificá-los no Gerenciador de Armazenamento do Azure. Um banco de dados nyctaxidb é criado no cluster HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/Upload_Data_to_HDI_cluster_Azure_Explorer.PNG)


**Exploração de dados: consultas Hive no Python**

Uma vez que os dados estão no cluster Hadoop, você pode usar o pacote pyodbc para se conectar aos Clusters Hadoop e consultar o banco de dados usando Hive para exploração e engenharia de recurso. É possível exibir as tabelas existentes que criamos na etapa de pré-requisitos.

	queryString = """
	    show tables in nyctaxidb2;
	    """
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Vamos examinar o número de registros em cada mês e as frequências de gorjetas na tabela de corridas:

	queryString = """
	    select month, count(*) from nyctaxidb.trip group by month;
	    """
	results = pd.read_sql(queryString,connection)

	%matplotlib inline

	results.columns = ['month', 'trip_count']
	df = results.copy()
	df.index = df['month']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


	queryString = """
	    SELECT tipped, COUNT(*) AS tip_freq
	    FROM
	    (
	        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
	        FROM nyctaxidb.fare
	    )tc
	    GROUP BY tipped;
	    """
	results = pd.read_sql(queryString,connection)

	results.columns = ['tipped', 'trip_count']
	df = results.copy()
	df.index = df['tipped']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Também podemos computar a distância entre o local de captura e o destino do passageiro e compará-la com a distância da corrida.

	queryString = """
	                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
	                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
	                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
	                    from nyctaxidb.trip
	                    where month=1
	                        and pickup_longitude between -90 and -30
	                        and pickup_latitude between 30 and 90
	                        and dropoff_longitude between -90 and -30
	                        and dropoff_latitude between 30 and 90;
	            """
	results = pd.read_sql(queryString,connection)
	results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

	results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
	                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
	df = results.loc[results['trip_distance']<=100] #remove outliers
	df = df.loc[df['direct_distance']<=100] #remove outliers
	plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


Agora, vamos preparar uma conjunto reduzido de dados (1%) para modelagem. Podemos usar esses dados no módulo de leitor do AML.


		queryString = """
	    create  table if not exists nyctaxi_downsampled_dataset_testNEW (
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    pickup_hour string,
	    pickup_week string,
	    weekday string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double,
	    direct_distance double,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double,
	    tipped string,
	    tip_class string
	    )
	    row format delimited fields terminated by ','
	    lines terminated by '\\n'
	    stored as textfile;
		"""
		cursor.execute(queryString)

		--- now insert contents of the join into the above internal table

		queryString = """
	    insert overwrite table nyctaxi_downsampled_dataset_testNEW
	    select
	    t.medallion,
	    t.hack_license,
	    t.vendor_id,
	    t.rate_code,
	    t.store_and_fwd_flag,
	    t.pickup_datetime,
	    t.dropoff_datetime,
	    hour(t.pickup_datetime) as pickup_hour,
	    weekofyear(t.pickup_datetime) as pickup_week,
	    from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
	    t.passenger_count,
	    t.trip_time_in_secs,
	    t.trip_distance,
	    t.pickup_longitude,
	    t.pickup_latitude,
	    t.dropoff_longitude,
	    t.dropoff_latitude,
	    t.direct_distance,
	    f.payment_type,
	    f.fare_amount,
	    f.surcharge,
	    f.mta_tax,
	    f.tip_amount,
	    f.tolls_amount,
	    f.total_amount,
	    if(tip_amount>0,1,0) as tipped,
	    if(tip_amount=0,0,
	    if(tip_amount>0 and tip_amount<=5,1,
	    if(tip_amount>5 and tip_amount<=10,2,
	    if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
	    from
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    pickup_datetime,
	    dropoff_datetime,
	    passenger_count,
	    trip_time_in_secs,
	    trip_distance,
	    pickup_longitude,
	    pickup_latitude,
	    dropoff_longitude,
	    dropoff_latitude,
	    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	    radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
	    rand() as sample_key

	    from trip
	    where pickup_latitude between 30 and 90
	        and pickup_longitude between -90 and -30
	        and dropoff_latitude between 30 and 90
	        and dropoff_longitude between -90 and -30
	    )t
	    join
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    pickup_datetime,
	    payment_type,
	    fare_amount,
	    surcharge,
	    mta_tax,
	    tip_amount,
	    tolls_amount,
	    total_amount
	    from fare
	    )f
	    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
	    where t.sample_key<=0.01
		"""
		cursor.execute(queryString)

Após alguns instantes, você pode ver que os dados foram carregados nos clusters Hadoop:

	queryString = """
	    select * from nyctaxi_downsampled_dataset limit 10;
	    """
	cursor.execute(queryString)
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**Ler dados no HDI usando AML: módulo de leitor**

Você também pode usar o módulo de **leitor** no estúdio AM para acessar o banco de dados no cluster Hadoop. Insira as credenciais dos clusters HDI e da Conta de Armazenamento do Azure e você poderá criar modelos de aprendizado de máquina usando o banco de dados nos clusters HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

O conjunto de dados pontuado pode ser exibido:

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### Azure SQL Data Warehouse e bancos de dados

O SQL Data Warehouse do Azure é um data warehouse elástico como um serviço, com experiência em SQL Server de nível corporativo.

Você pode provisionar o SQL Data Warehouse do Azure seguindo as instruções fornecidas neste [artigo](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Após provisionar seu Azure SQL Data Warehouse, você poderá usar este [passo a passo](machine-learning-data-science-process-sqldw-walkthrough.md) para carregar, explorar e modelar dados usando os dados do SQL Data Warehouse.

#### Banco de Dados de Documentos do Azure

O Banco de Dados de Documentos do Azure é um banco de dados NoSQL na nuvem. Ele permite trabalhar com documentos como JSON, bem como armazenar e consultar os documentos.

Você precisa seguir as etapas de pré-requisitos para acessar o Banco de Dados de Documentos na DSVM.

1. Instalar o SDK do Python para Banco de Dados de Documentos (Execute ```pip install pydocumentdb``` no prompt de comando)
1. Criar a conta e o banco de dados do Banco de Dados de Documentos no [portal do Azure](https://portal.azure.com)
1. Baixar a "ferramenta de Migração do Banco de Dados de Documentos" [aqui](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) e extrair em um diretório de sua escolha
1. Importar dados JSON (dados de vulcão) armazenados em um [blob público](https://cahandson.blob.core.windows.net/samples/volcano.json) no Banco de Dados de Documentos com os seguintes parâmetros de comando para a ferramenta de migração (dtui.exe do diretório no qual você instalou a ferramenta de migração do Banco de Dados de Documentos). Insira os parâmetros de local de origem e destino de acordo com os dados abaixo.

	/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Depois de importar os dados, você pode acessar o Jupyter e abrir o notebook chamado *DocumentDBSample*, que contém o código python para acessar o Banco de Dados de Documentos e fazer algumas consultas básicas. Aprenda mais sobre o Banco de Dados de Documentos ao visitar a [página de documentação](https://azure.microsoft.com/documentation/learning-paths/documentdb/) do serviço


## 8\. Criar relatórios e painel usando o Power BI Desktop

Vamos visualizar o arquivo JSON, Volcano, que vimos no exemplo acima do Banco de Dados de Documentos no Power BI para obter informações visuais dos dados. As etapas detalhadas estão disponíveis no [artigo sobre o Power BI](../documentdb/documentdb-powerbi-visualize.md). As etapas principais são as seguintes:

1. Abra o Power BI Desktop e execute "Obter Dados". Especifique a URL como: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Você deverá ver os registros JSON importados como uma lista
3. Converta a lista em uma tabela para que o PowerBI possa trabalhar com ela
4. Expanda as colunas clicando no ícone de expansão (o ícone com a "seta para a esquerda e uma seta para a direita" à direita da coluna)
5. Perceba que o local é um campo "Registro". Expanda o registro e selecione apenas as coordenadas. A coordenada é uma coluna de lista
6. Adicione uma nova coluna para converter a coluna de coordenadas da lista em uma coluna LatLong separada por vírgula concatenando os dois elementos no campo da lista coordenada usando a fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Por fim, converta a coluna ```Elevation``` em Decimal e escolha **Fechar** e **Aplicar**.

Em vez de seguir as etapas acima, você pode colar o código a seguir que extrai do script as etapas acima no Editor Avançado do Power BI, que permite escrever as transformações de dados em uma linguagem de consulta.


	let
	    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
	    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
	    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
	    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
	    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
	    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
	in
	    #"Changed Type"



Agora você tem os dados no modelo de dados do Power BI. Seu Power BI Desktop deve ser parecido com o mostrado abaixo.

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

É possível iniciar a criação de relatórios e as visualizações usando o modelo de dados. Você pode executar as etapas deste [artigo sobre o Power BI](../documentdb/documentdb-powerbi-visualize.md#build-the-reports) para criar um relatório. O resultado final será um relatório semelhante ao que se segue.

![Exibição de relatório do Power BI Desktop — conector do Power BI](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## 9\. Dimensionar dinamicamente a DSVM para atender às suas necessidades de projeto

Você pode escalar verticalmente a DSVM para atender às necessidades do seu projeto. Se você não precisar usar a VM à noite ou nos fins de semana, basta desligá-la no [Portal do Azure](https://portal.azure.com).

>[AZURE.NOTE]  Isso incorrerá em cobranças de computação se você usar apenas o botão de desligamento do sistema operacional na VM.

Se precisar lidar com análise em larga escala e precisar de mais capacidade de CPU e/ou memória e/ou disco, você poderá encontrar uma ampla opção de tamanhos de VM em termos de núcleos de CPU, capacidade de memória e tipos de disco (incluindo unidades de estado sólido) que atendam às suas necessidades de computação e orçamentárias. A lista completa de VMs e seus preços de computação por hora estão disponíveis na página [Preço de Máquinas Virtuais do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/).

Da mesma forma, se sua necessidade de capacidade de processamento de VM for reduzida (por exemplo: você moveu uma grande carga de trabalho para um cluster Hadoop ou um Spark), você poderá reduzir verticalmente o cluster no [Portal do Azure](https://portal.azure.com) e acessar as configurações da sua instância VM. Veja uma captura de tela.


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## 10\. Instalar ferramentas adicionais na sua máquina virtual

Agrupamos várias ferramentas as quais acreditamos que atendem a muitas das necessidades de análise comum de dados e que pouparão seu tempo, evitando a instalação e a configuração de cada ambiente, e seu dinheiro, pagando apenas por aquilo que você usar.

Você pode aproveitar outros serviços de análise e dados do Azure listados neste artigo para aprimorar seu ambiente de análise. Entendemos que, em alguns casos, suas necessidades podem exigir ferramentas adicionais, incluindo algumas ferramentas de terceiros. Você tem acesso administrativo completo na máquina virtual para instalar novas ferramentas necessárias. Também é possível instalar pacotes adicionais no Python e no R que não foram pré-instalados. Para Python, você pode usar ```conda``` ou ```pip```. Para R, você pode usar o ```install.packages()``` no console do R ou usar o IDE e escolher "**Pacotes** -> **Instalar Pacotes...**".

## Resumo
Essas são apenas algumas das coisas que você pode fazer na Máquina Virtual de Ciência de Dados da Microsoft. Há muito mais coisas que você fazer para torná-la um ambiente eficaz de análise.

<!---HONumber=AcomDC_0427_2016-->