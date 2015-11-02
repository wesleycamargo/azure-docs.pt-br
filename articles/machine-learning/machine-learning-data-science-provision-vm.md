<properties 
	pageTitle="Provisionar uma Máquina Virtual de Ciência de Dados | Microsoft Azure" 
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
	ms.date="10/20/2015" 
	ms.author="bradsev" />


# Provisionar uma Máquina Virtual de Ciência de Dados

## Introdução

A Máquina Virtual de Ciência de Dados é uma imagem de VM (máquina virtual) do Azure pré-instalada e configurada com diversas ferramentas populares que são usadas para a análise de dados e o aprendizado de máquina. As ferramentas incluídas são:

- Revolution R Open
- Distribuição do Anaconda Python
- Visual Studio Community Edition
- Power BI Desktop
- SQL Server Express edition


Realizar a Ciência de Dados envolve a iteração em uma sequência de tarefas: encontrar, carregar e pré-processar dados, compilar e testar modelos, bem como implantar os modelos para o consumo em aplicativos inteligentes. Não é incomum que os cientistas de dados usem uma variedade de ferramentas para executar essas tarefas. Pode ser muito demorado encontrar as versões corretas do software e baixar e instalá-las. A máquina virtual de Ciência de Dados pode reduzir uma grande parte dessa tarefa.

Use a VM de Ciência de Dados para facilitar essa tarefa. Acelere o início de seu projeto de análise criando uma imagem que inclui o software geralmente usado para tarefas de análise e de aprendizado de máquina em uma variedade de linguagens, incluindo R, Python, SQL e C#. O Visual Studio fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft.

## Pré-requisitos

Antes de criar uma VM do Azure, você deve ter o seguinte:

- **Uma assinatura do Azure**: consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Uma conta de armazenamento do Azure**: veja [Criar uma conta de armazenamento do Azure](storage-whatis-account.md) A conta de armazenamento pode ser criada como parte do processo de criação da VM se você não desejar usar uma conta existente.


## Criar sua VM de ciência de dados

As etapas envolvidas na criação de sua instância da VM de ciência de dados são as seguintes:

1.	Faça logon no portal do Azure
2.	Navegue até **Novo** -> **Computação** -> **Marketplace** e procure *Máquina Virtual de Ciência de Dados*. Selecione a VM chamada “**Máquina Virtual de Ciência de Dados**” publicados pela Microsoft para ver um painel que descreve a Máquina Virtual de Ciência de Dados
3.	Clique no botão **Criar** na parte inferior para ser levado para um assistente. ![provision-data-science-vm](./media/machine-learning-data-science-provision-vm/provision-data-science-virtual-machine.png)
4.	 As seções a seguir fornecem as entradas para cada etapa no assistente usada para criar a VM de Ciência de Dados


 **1. Noções básicas**:

- **Nome**: o nome do seu servidor de ciência de dados que você está criando.
- **Nome de Usuário**: ID de logon da conta de administrador
- **Senha**: senha da conta de administrador
- **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado
- **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente
- **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede

 **2. Tamanho**:

- Selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Você pode obter mais opções de tamanhos de VM selecionando “Exibir Tudo”

 **3. Configurações**

 **4. Tipo de disco**:

- Escolha Premium se preferir SSD; se não, escolha “Standard”

 **5. Conta de Armazenamento**:


- É possível criar uma nova conta de armazenamento do Azure em sua assinatura ou usar uma já existente no mesmo Local que foi escolhido na etapa Noções básicas do assistente.
- Na maioria dos casos, você apenas usará o padrão para o restante dos parâmetros. É possível focalizar o link informativo para obter ajuda sobre um campo específico, caso você deseje usar não padrão.
- Verifique se todas as informações inseridas estão corretas.
- Clique em **Comprar** para iniciar o provisionamento. Um link para os termos da transação é fornecido. A VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 

O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal do Azure.

## Como acessar a VM de Ciência de Dados

Depois de criar a VM, é possível fazer logon nela usando a área de trabalho remota com as credenciais da conta de Administrador criada na seção Noções básicas da etapa 4.

Depois de criar e provisionar sua VM, você estará pronto para começar a usar as ferramentas que estão instaladas e configuradas nela. Há ícones da área de trabalho e blocos do menu Iniciar para várias das ferramentas.

## Ferramentas instaladas na VM de Ciência de Dados

### R
Se desejar usar o R para a análise, a VM tem o RRO (Revolution R Open) instalado. Esta é uma distribuição de Software Livre do R e é totalmente compatível com o CRAN-R. Contém o mecanismo mais recente do mecanismo de software livre do R junto com a Biblioteca de Kernel Intel Math. Um IDE chamado “RRO RGui” também é fornecido na VM. Fique à vontade para baixar e usar outros IDEs, bem como o [RStudio](http://www.rstudio.com).

### Python
Para o desenvolvimento com Python, a distribuição do Anaconda Python 2.7 foi instalada. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar pacotes de IDEs com o Anaconda como o IDLE ou Spyder. Você pode iniciar um desses pesquisando na barra de pesquisa (tecla **Win** + **S**).

### IPython Notebook
A distribuição do Anaconda também é incluída em um IPython Notebook, um ambiente de compartilhamento de código e análise. Um servidor do Ipython Notebook foi pré-configurado. Há um ícone de área de trabalho para iniciar o navegador para acessar o servidor do bloco de anotações. Se estiver na VM por meio da área de trabalho remota, você também poderá visitar [https://localhost:9999/](https://localhost:9999/) para acessar o servidor do IPython Notebook (Observação: basta clicar em Continuar se receber avisos de certificado.)

### Visual Studio 2015 Community edition
Visual Studio Community edition instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode ser usada para fins de avaliação e para equipes muito pequenas. Confira os termos de uso ***aqui*** (Link TBD). Abra o Visual Studio, clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar**. Você também pode procurar programas com **Win** + **S** e inserindo “Visual Studio”.

Observação: você pode receber uma mensagem informando que o seu período de avaliação expirou. É possível inserir credenciais da Conta da Microsoft ou criar uma e inseri-las para obter acesso ao Visual Studio Community Edition. Lá, você pode criar projetos em linguagens como C# e Python

### SQL Server Express
Uma versão limitada do SQL Server também é fornecida com o Community Edition do Visual Studio. Você pode acessar o SQL Server iniciando o **SQL Server Management Studio**. O nome da VM será populado como o Nome do Servidor. Use a Autenticação do Windows quando estiver conectado como o administrador no Windows. Quando estiver no SQL Server Management Studio, você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL.

### As tabelas 
Várias ferramentas do Azure são instaladas na VM: - Há um atalho da área de trabalho para acessar a documentação do SDK do Azure. - **AzCopy** usado para mover dados para dentro e para fora de sua Conta do Armazenamento do Microsoft Azure. - **Gerenciador de Armazenamento do Azure** usado para percorrer os objetos armazenados em sua Conta do Armazenamento do Azure. - **Microsoft Azure Powershell** - uma linguagem de script usada para administrar seus recursos do Azure em uma linguagem de script que também é instalada em sua VM.

###Power BI

Para ajudá-lo a criar ótimos painéis e visualizações, o **Power BI Desktop** foi instalado. Use essa ferramenta para extrair dados de fontes diferentes, criar painéis e relatórios e publicá-los na nuvem. Para obter informações, confira o site do [Power BI](http://powerbi.microsoft.com).

Observação: será necessário uma conta do Office 365 para acessar o Power BI.

## Ferramentas de desenvolvimento adicionais da Microsoft
O [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser usado para descobrir e baixar outras ferramentas de desenvolvimento da Microsoft. Também há um atalho para a ferramenta fornecida na área de trabalho da VM.

<!---HONumber=Oct15_HO4-->