<properties 
	pageTitle="Provisionar a Máquina Virtual de Ciência de Dados da Microsoft | Microsoft Azure" 
	description="Configure e crie uma Máquina Virtual de Ciência de Dados no Azure para realizar a análise e o aprendizado de máquina." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="bradsev" />


# Provisionar uma Máquina Virtual de Ciência de Dados da Microsoft

## Introdução

A Máquina Virtual de Ciência de Dados da Microsoft é uma imagem de máquina virtual (VM) do Azure pré-instalada e configurada com diversas ferramentas populares que são usadas para a análise de dados e o aprendizado de máquina. As ferramentas incluídas são:

- Revolution R Open
- Distribuição do Anaconda Python
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express edition
- SDK do Azure


A ciência de dados envolve a iteração em uma sequência de tarefas: encontrar, carregar e pré-processar dados, compilar e testar modelos, bem como implantar os modelos para o consumo em aplicativos inteligentes. Não é incomum que os cientistas de dados usem uma variedade de ferramentas para executar essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e baixar e instalá-las. A Máquina Virtual de Ciência de Dados da Microsoft pode aliviar essa carga.

A Máquina Virtual de Ciência de Dados da Microsoft impulsiona seu projeto de análise. Ela permite que você trabalhe em tarefas em uma variedade de linguagens, incluindo R, Python, SQL e C#. O Visual Studio fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft.

Não há encargos de software para esta imagem da VM de ciência de dados. Você só paga pelas taxas de uso do Azure que dependem do tamanho da máquina virtual que será provisionada com esta imagem da VM. Mais detalhes sobre as taxas de computação podem ser encontrados [aqui](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/).


## Pré-requisitos

Antes de criar uma Máquina Virtual de Ciência de Dados da Microsoft, você deve ter o seguinte:

- **Uma assinatura do Azure**: para obter uma, confira [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Uma conta de armazenamento do Azure**: para criar uma, confira [Criar uma conta de armazenamento do Azure](storage-create-storage-account.md#create-a-storage-account) Como alternativa, a conta de armazenamento pode ser criada como parte do processo de criação da VM se você não desejar usar uma conta existente.


## Criar sua Máquina Virtual de Ciência de Dados da Microsoft

Veja as etapas para criar uma instância da Máquina Virtual de Ciência de Dados da Microsoft:

1.	Navegue até a máquina virtual listada no [Portal Clássico do Azure](https://portal.azure.com/#gallery/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.	 Clique no botão **Criar** na parte inferior para ser levado para um assistente.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 As seções a seguir fornecem as **entradas** para cada uma das **5 etapas** (enumeradas à direita da figura acima) do assistente usado para criar a Máquina Virtual de ciência de Dados da Microsoft. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:

  **a. Noções básicas**:

   - **Nome**: o nome do servidor de ciência de dados que você está criando.
   - **Nome de Usuário**: ID de logon da conta de administrador
   - **Senha**: senha da conta de administrador
   - **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado
   - **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente
   - **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede

  **b. Tamanho**:

   - Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Você pode obter mais opções de tamanhos de VM selecionando “Exibir Tudo”

  **c. Configurações**

   - **Tipo de disco**: escolha Premium se você preferir uma SSD (unidade de estado sólido); caso contrário, escolha “Standard”.
   - **Conta de Armazenamento**: é possível criar uma nova conta de armazenamento do Azure em sua assinatura ou usar uma já existente no mesmo *Local* que foi escolhido na etapa Noções básicas do assistente.
   - **Outros parâmetros**: na maioria dos casos, você simplesmente usará os valores padrão. É possível focalizar o link informativo para obter ajuda sobre um campo específico, caso você queira considerar o uso de valores não padrão.

  **d. Resumo**:

   - Verifique se todas as informações inseridas estão corretas.

  **e. Comprar**:

   - Clique em **Comprar** para iniciar o provisionamento. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 


O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal Clássico do Azure.

## Como acessar uma Máquina Virtual de Ciência de Dados da Microsoft

Depois de criar a VM, é possível fazer logon nela usando a área de trabalho remota com as credenciais da conta de Administrador criada na seção Noções básicas da etapa 4.

Depois de criar e provisionar sua VM, você estará pronto para começar a usar as ferramentas que estão instaladas e configuradas nela. Há ícones da área de trabalho e blocos do menu Iniciar para várias das ferramentas.

## Como criar uma senha forte na Máquina Virtual de Ciência de Dados

Execute o seguinte comando a partir de um prompt de comando na Máquina Virtual de Ciência de Dados para criar sua própria senha forte para a máquina.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Insira a senha quando solicitado.

Você verá o hash de senha no formato "sha1:xxxxxx" na saída. Copie esse hash de senha e substitua o hash existente em seu arquivo de configuração do bloco de anotações localizado em: **C:\\Aaqs.ipython\\profile\_nbserver\\ipython\_notebook\_config.py** com um nome de parâmetro ***c.NotebookApp.password***.

Você só deve substituir o valor de hash existente que está entre aspas. As aspas e o prefixo ***sha1:*** para o valor do parâmetro precisam ser mantidos.

Por fim, você precisa parar e reiniciar o servidor do Ipython que está em execução na VM como uma tarefa agendada do Windows chamada "Start\_IPython\_Notebook". Se a nova senha não for aceita após a reinicialização dessa tarefa, tente reiniciar a máquina virtual.

## Ferramentas Instaladas na Máquina Virtual de Ciência de Dados da Microsoft

### R
Se desejar usar o R para a análise, a VM tem o RRO (Revolution R Open) instalado. Esta é uma distribuição de Software Livre do R e é totalmente compatível com o CRAN-R. Contém o mecanismo mais recente do mecanismo de software livre do R junto com a Biblioteca de Kernel Intel Math. Um IDE chamado "RRO RGui" também é fornecido na VM. Sinta-se à vontade para baixar e usar outros IDEs, bem como o [RStudio](http://www.rstudio.com).

### Python
Para o desenvolvimento com Python, a distribuição do Anaconda Python 2.7 foi instalada. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar Ferramentas Python para Visual Studio (PTVS) que são instaladas na edição do Visual Studio 2015 Community ou um dos IDEs agrupado com Anaconda como IDLE ou Spyder. Você pode iniciar um desses pesquisando na barra de pesquisa (tecla **Win** + **S**).

### IPython Notebook
A distribuição do Anaconda também é incluída em um IPython Notebook, um ambiente de compartilhamento de código e análise. Um servidor do IPython Notebook foi pré-configurado. Há um ícone de área de trabalho para iniciar o navegador para acessar o servidor do bloco de anotações. Se estiver na VM por meio da área de trabalho remota, você também poderá visitar [https://localhost:9999/](https://localhost:9999/) para acessar o servidor do IPython notebook (Observação: basta clicar em Continuar se receber avisos de certificado.)

### Visual Studio 2015 Community edition
Visual Studio Community edition instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode ser usada para fins de avaliação e para equipes muito pequenas. Você pode consultar os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547). Abra o Visual Studio clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar**. Você também pode pesquisar programas com **Win** + **S** e inserindo “Visual Studio”.

Observação: você pode receber uma mensagem informando que o seu período de avaliação expirou. É possível inserir credenciais da Conta da Microsoft ou criar uma e inseri-las para obter acesso ao Visual Studio Community Edition. Lá, você pode criar projetos em linguagens como C# e Python

### SQL Server Express
Uma versão limitada do SQL Server também é fornecida com o Community Edition do Visual Studio. Você pode acessar o SQL Server iniciando o **SQL Server Management Studio**. O nome da VM será populado como o Nome do Servidor. Use a Autenticação do Windows quando estiver conectado como o administrador no Windows. Quando estiver no SQL Server Management Studio, você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL.

### As tabelas 
Várias ferramentas do Azure são instaladas na VM: - há um atalho da área de trabalho para acessar a documentação do SDK do Azure. - **AzCopy** usado para mover dados para dentro e para fora de sua Conta do Armazenamento do Microsoft Azure. - **Gerenciador do Armazenamento do Azure** usado para percorrer os objetos armazenados em sua Conta do Armazenamento do Azure. - **Microsoft Azure Powershell** - uma linguagem de script usada para administrar seus recursos do Azure em uma linguagem de script que também está instalada em sua VM.

###Power BI

Para ajudá-lo a compilar ótimos painéis e visualizações, o **Power BI Desktop** foi instalado. Use essa ferramenta para extrair dados de fontes diferentes, criar painéis e relatórios e publicá-los na nuvem. Para saber mais, visite o site do [Power BI](http://powerbi.microsoft.com).

Observação: será necessário uma conta do Office 365 para acessar o Power BI.

## Ferramentas de desenvolvimento adicionais da Microsoft
O [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser usado para descobrir e baixar outras ferramentas de desenvolvimento da Microsoft. Também é um atalho para a ferramenta fornecida na área de trabalho de Máquina de Virtual de Ciência de Dados da Microsoft.

<!---HONumber=AcomDC_1223_2015-->