<properties
 pageTitle="Introdução a um cluster de HPC Pack para executar cargas de trabalho do Excel e SOA | Microsoft Azure"
	description="."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>
<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/18/2015"
	ms.author="danlep"/>

# Introdução com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA

Este artigo mostra como implantar um cluster de HPC Pack nos serviços de infraestrutura do Azure (IaaS) usando um modelo de início rápido do Azure ou um script de implantação do Azure PowerShell. Você usará as imagens de VM do Azure Marketplace projetadas para executar cargas de trabalho de arquitetura orientada a serviços (SOA) ou Microsoft Excel com o HPC Pack. Você pode usar o cluster para executar os serviços SOA e HPC simples do Excel de um computador de cliente local. Os serviços do Excel HPC incluem descarregamento de pasta de trabalho do Excel e funções definidas pelo usuário do Excel ou UDFs.

Em um alto nível, o diagrama a seguir mostra o cluster de HPC Pack que você criará.

![Cluster HPC com nós que executam cargas de trabalho do Excel][scenario]

## Pré-requisitos

* **Computador cliente** - você precisará de um computador cliente baseado no Windows para executar o script de implantação de cluster do Azure PowerShell (se você escolher esse método de implantação) e enviar trabalhos de exemplo do Excel e SOA ao cluster.

* **Assinatura do Azure** - se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Cota de núcleos** - talvez seja necessário aumentar a cota de núcleos, especialmente se você optar por implantar vários nós de cluster com tamanhos de VM de vários núcleos. Se você estiver usando um modelo de início rápido do Azure, lembre-se de que a cota de núcleos no Gerenciador de Recursos é por região do Azure, e talvez seja necessário aumentar a cota de uma região específica. Consulte [Limites de assinatura do Azure, cotas e restrições](../azure-subscription-service-limits.md). Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.


## Etapa 1. Configurar um cluster de HPC Pack no Azure

Mostraremos duas maneiras de configurar o cluster: primeiro, usando um modelo de início rápido do Azure e o Portal de Visualização do Azure; e segundo, usando um script de implantação do Azure PowerShell.


### Usar um modelo de início rápido
Use um modelo de início rápido do Azure para implantar de maneira rápida e fácil um cluster do HPC Pack no Portal de Visualização do Azure. Ao abrir o modelo no portal, você obtém uma interface do usuário simples onde inserir as configurações para seu cluster. Siga estas etapas:

1. Visite a [página Criar modelo de Cluster de HPC no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se quiser, reveja as informações sobre o modelo e o código-fonte.

2. Clique em **implantar no Azure** para iniciar uma implantação com o modelo no Portal de Visualização do Azure.

    ![Implantar o modelo no Azure][github]

3. No portal, siga estas etapas para especificar os parâmetros para o modelo de cluster HPC.

    a. Na página **Editar Grupo de Regras**, clique em**Salvar**.

    ![Salvar do modelo][template]

    b. Na página **Parâmetros**, insira os valores para os parâmetros de modelo. Clique no ícone ao lado de cada configuração para obter informações de Ajuda. Os valores de exemplo são mostrados na tela a seguir. Este exemplo criará um novo cluster de HPC Pack chamado *hpc01* no domínio *hpc.local* que consiste em um nó principal e dois nós de computação. Os nós de computação serão criados a partir de uma imagem de VM do HPC Pack, incluindo o Microsoft Excel.

    ![Inserir parâmetros][parameters]

    >[AZURE.NOTE]A VM do nó principal será criada automaticamente a partir da [imagem mais recente do Marketplace](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do HPC Pack 2012 R2 no Windows Server 2012 R2. Atualmente, a imagem se baseia no HPC Pack 2012 R2 Update 2.
    >
    >Serão criadas VMs do nó de computação a partir da imagem mais recente da família do nó de computação selecionado. Selecione a opção **ComputeNode** para a mais recente imagem de computação do HPC Pack 2012 R2 Update 2 para fins gerais. Selecione a opção **ComputeNodeWithExcel**para a imagem do nó de computação HPC Pack que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013. Se quiser implantar um cluster para sessões SOA gerais ou para o descarregamento de Excel UDF, escolha a opção **ComputeNode** (sem o Excel instalado).
    >
    >Ao usar **ComputeNodeWithExcel** para cargas de trabalho de produção, será necessário fornecer uma licença válida do Excel para ativar o Excel em nós de computação. Caso contrário, a versão de avaliação do Excel poderá expirar em 30 dias e a execução da pasta de trabalho do Excel falharia constantemente com a exceção COMExeption (0x800AC472). Se isso acontecer, você poderá fazer logon no nó principal para executar clusrun “%ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe” em todos os nós de computação do Excel por meio do console do Gerenciador de Cluster de HPC a fim de reativar o Excel para mais 30 dias de avaliação. A quantidade máxima de rearmações do período de carência é de 2, depois disso talvez seja necessário fornecer uma licença válida do Excel.

    c. Selecione a assinatura.

    d. Criar um novo grupo de recursos para o cluster, como *hpc01RG*.

    e. Escolha um local para o grupo de recursos, como Leste dos EUA.

    f. Na página **Termos legais**, analise os termos. Se você concordar, clique em **Comprar**.

    g. Quando tiver terminado de definir os valores para o modelo, clique em **Criar**.

    ![Criar o cluster][create]

3.	Quando a implantação for concluída (normalmente leva cerca de 30 minutos), exporte o arquivo de certificado de cluster do nó principal do cluster. Em uma etapa posterior, este certificado público será importado no computador cliente para fornecer a autenticação do lado do servidor para a conexão segura de HTTP.

    a. Conecte-se ao nó principal pela Área de Trabalho Remota no Portal de Visualização do Azure.

     ![Conectar-se ao nó principal][connect]

    b. Use os procedimentos padrão para usar o Gerenciador de Certificados para exportar o certificado de nó principal (localizado em Cert:\\LocalMachine\\My) sem a chave privada. Neste exemplo, exporte *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exportar o certificado][cert]

### Use o script de implantação do HPC Pack IaaS

O script de implantação do HPC Pack IaaS é outra forma versátil para implantar um cluster de HPC Pack. Ele é executado no modo de Gerenciamento de Serviços do Azure (ASM), enquanto o modelo é executado no modo do Gerenciador de Recursos do Azure (ARM). Além disso, o script é compatível com uma assinatura no serviço Azure Global ou Azure China.

**Pré-requisitos adicionais**

* **Azure PowerShell** - [Instale e configure o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no computador cliente.

* **Script de implantação do HPC Pack IaaS**- Baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.4.0 ou posterior do script.

**Criar o arquivo de configuração**

 O script de implantação do HPC Pack IaaS usa um arquivo de configuração XML como entrada que descreve a infraestrutura do cluster do HPC. Para implantar um cluster que consiste em um nó principal e 18 nós de computação criados a partir da imagem do nó de computação que inclui o Microsoft Excel, substitua os valores para o seu ambiente no seguinte arquivo de configuração de exemplo. Para obter mais informações sobre o arquivo de configuração, consulte o arquivo Manual.rtf na pasta de script ou a [documentação do script](https://msdn.microsoft.com/library/azure/dn864734.aspx).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4864.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre o arquivo de configuração**

* O **VMName** do nó principal devem ser exatamente igual ao **ServiceName**.

* Verifique se você especificou **EnableWebPortal** para que o certificado de nó principal seja gerado e exportado.

* O script do PowerShell pós-configuração PostConfig.ps1 define certas configurações no nó principal, como configurar a cadeia de conexão do armazenamento do Azure, removendo a função do nó de computação do nó principal e colocando todos os nós online quando eles são implantados. Veja abaixo um exemplo de script.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Execute o script**

1. Abra o console do PowerShell no computador cliente como administrador.

2. Altere o diretório para a pasta de scripts (E:\\IaaSClusterScript neste exemplo).

    ```
    cd E:\IaaSClusterScript
```

4. Execute o comando a seguir para implantar o cluster de HPC Pack. Este exemplo supõe que o arquivo de configuração esteja localizado em E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

O script de implantação do HPC Pack será executado por algum tempo. Uma das ações do script é exportar e baixar o certificado do cluster e salvá-lo na pasta Documentos do usuário atual no computador cliente. O script gerará uma mensagem semelhante a esta: Em uma etapa seguinte, você importará o certificado no repositório de certificados apropriado.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Etapa 2. Descarregaras pastas de trabalho do Excel e executar UDFs de um cliente local

### Descarregar pastas de trabalho do Excel

Siga estas etapas para descarregar uma planilha do Excel para executar no cluster de HPC Pack no Azure. Para fazer isso, você deve ter o Excel 2010 ou 2013 já instalado no computador cliente.

1. Use um dos métodos da Etapa 1 para implantar um cluster de HPC Pack com a imagem do nó de computação do Excel. Obter o arquivo de certificado (.cer) do cluster e o nome de usuário e a senha.

2. No computador cliente, importe o certificado de cluster em Cert:\\CurrentUser\\Root.

3. Verifique se o Excel está instalado. Crie um arquivo Excel.exe.config com o seguinte conteúdo na mesma pasta com Excel.exe no computador cliente. Isso garante que o suplemento de COM do Excel do HPC Pack 2012 R2 seja carregado com êxito.

    ```
<?xml version="1.0"?>
<configuration>
  <startup useLegacyV2RuntimeActivationPolicy="true">
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
  </startup>
</configuration>
```
4.	Baixe a [instalação total do HPC Pack 2012 R2 Update 2](http://www.microsoft.com/download/details.aspx?id=47755) e instale o cliente do HPC Pack, ou baixe e instale os [utilitários de cliente do HPC Pack 2012 R2 Update 2](https://www.microsoft.com/download/details.aspx?id=47754) e o Visual C++ 2010 redistribuível apropriado do computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.	Neste exemplo, podemos usar uma pasta de trabalho do Excel de exemplo chamada ConvertiblePricing\_Complete.xlsb, disponível para download [aqui](https://www.microsoft.com/pt-BR/download/details.aspx?id=2939).

6.	Copie a pasta de trabalho do Excel para uma pasta de trabalho, como D:\\Excel\\Run.

7.	Abra a pasta de trabalho do Excel. Na faixa de opções **Desenvolver**, clique em**Suplementos COM** e confirme que o suplemento HPC Pack Excel seja carregado com êxito, conforme mostrado na seguinte imagem.

    ![Suplemento do Excel para o HPC Pack][addin]

8.	Edite a macro do VBA HPCControlMacros no Excel, alterando as linhas comentadas, conforme mostrado no script a seguir. Substitua os valores apropriados para o seu ambiente.

    ![Macro do Excel para o HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Copie a pasta de trabalho do Excel para um diretório de carregamento como D\\Excel\\Upload, conforme especificado na constante HPC\_DependsFiles na macro VBA.

10.	Clique no botão **Cluster** na planilha para executar a pasta de trabalho no cluster IaaS do Azure.

### Executar UDFs do Excel

Para executar UDFs do Excel, siga as etapas de 1 a 3 acima para configurar o computador cliente. Para UDFs do Excel, você não precisa ter o aplicativo do Excel instalado em nós de computação, assim você pode escolher a imagem de um nó de computação normal na Etapa 1 em vez de a imagem do nó de computação com o Excel.

>[AZURE.NOTE]Há um limite de 34 caracteres no Excel 2010 e a caixa de diálogo do conector de cluster 2013. Se o nome completo do cluster for maior, por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com, ele não caberá na caixa de diálogo. A solução alternativa é aplicar a Atualização 2 QFE KB3085833 (baixe [aqui](http://www.microsoft.com/pt-BR/download/details.aspx?id=48725)) para a API de Sessão SOA na máquina cliente, em seguida definir uma variável para toda a máquina, por exemplo, *CCP\_IAASHN* com o valor do nome do cluster longo e a entrada *%CCP\_IAASHN %* na caixa de diálogo como o nome do nó principal do cluster.

Depois que o cluster for implantado com êxito, continue com as etapas a seguir para executar um exemplo interno do UDF do Excel. Para UDFs personalizados do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar os XLLs e implantá-los no cluster IaaS.

1.	Abra uma nova pasta de trabalho do Excel. Na faixa de opções **Desenvolver**, clique em **Suplementos**. Na caixa de diálogo, clique em **Procurar**, navegue até a pasta %CCP\_HOME%Bin\\XLL32 e selecione o exemplo ClusterUDF32.xll.

    ![Selecionar o UDF][udf]

2.	Clique em **Arquivo** > **Opções** > **Avançado**. Em **Fórmulas**, marque **Permitir que as funções XLL definidas pelo usuário executem um cluster de cálculo**. Em seguida, clique em **Opções** e digite o nome completo do cluster em **Nome do nó principal do cluster**. Conforme observado anteriormente, essa caixa de entrada é limitada a 34 caracteres, de modo que um nome de cluster longo pode não caber. Você pode configurar um nome completo mais curto quando implantar um cluster via script de implantação IaaS.

    ![Configurar o UDF][options]

3.	Clique na célula com o valor =XllGetComputerNameC() e pressione Enter para executar o cálculo de UDF no cluster IaaS. A função simplesmente recupera o nome do nó de computação no qual um UDF é executado. Para a primeira execução, uma caixa de diálogo de credenciais solicita o nome de usuário e a senha para se conectar ao cluster IaaS.

    ![Executar UDF][run]

    Quando houver uma grande quantidade de células para calcular, pressione Alt-Shift-Ctrl + F9 para executar o cálculo em todas as células.

## Etapa 3. Executar uma carga de trabalho SOA a partir de um cliente local

Para executar aplicativos SOA gerais no cluster de HPC Pack IaaS, primeiro use um dos métodos da Etapa 1 para implantar o cluster IaaS, usando a imagem de um nó de computação genérico (porque você não precisará do Excel em nós de computação). Depois, siga estas etapas:

1. Após receber o certificado do cluster, importe-o para o computador cliente em Cert:\\CurrentUser\\Root.

2. Instale o [SDK do HPC Pack 2012 R2 Update 2](http://www.microsoft.com/download/details.aspx?id=47756) e [Utilitários de cliente do HPC Pack 2012 R2 Atualização 2](https://www.microsoft.com/download/details.aspx?id=47754) para desenvolver e executar aplicativos cliente SOA.

3. Baixe o [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633) HellowWorldR2. Abra o HelloWorldR2.sln no Visual Studio 2010 ou 2012.

4. Compile o projeto EchoService primeiro e implante o serviço no cluster IaaS da mesma maneira que implanta em um cluster local. Para obter etapas detalhadas, consulte o Leiame.doc no HelloWordR2. Modifique e crie o HellowWorldR2 e outros projetos, conforme descrito a seguir para gerar os aplicativos cliente SOA em execução em um cluster de IaaS do Azure de um computador cliente local.

### Usar associação Http com fila de armazenamento do Azure

Para usar a associação de Http com uma fila de armazenamento do Azure, faça algumas alterações no código de exemplo.

* Atualize o nome do cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Opcionalmente, use o padrão TransportScheme em SessionStartInfo ou defina explicitamente como Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Use a associação padrão para o BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou defina explicitamente usando o basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Opcionalmente, defina o sinalizador UseAzureQueue como verdadeiro na SessionStartInfo. Se não estiver configurado, ele será definido como true por padrão quando o nome do cluster tiver sufixos de domínio do Azure e o TransportScheme for Http.

    ```
    info.UseAzureQueue = true;
```

###Usar associação Http sem fila de armazenamento do Azure

Para fazer isso, defina explicitamente um sinalizador UseAzureQueue para false no SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Usar associação NetTcp

Para usar a ligação NetTcp, a configuração é como conectar-se a um cluster local. Você precisará abrir alguns pontos de extremidade na VM do nó principal. No Portal de Gerenciamento do Azure, faça o seguinte.


1. Pare a VM.

2. Adicione as portas TCP 9090, 9087, 9091, 9094 para a Sessão, Agente, trabalhador do Agente e Serviços de dados, respectivamente

    ![Configurar pontos de extremidade][endpoint]

3. Inicie a VM.

O aplicativo cliente SOA não requer alterações, exceto do nome principal para o nome completo do cluster IaaS.

## Próximas etapas

* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para saber mais sobre como executar cargas de trabalho do Excel com o HPC Pack.

* Consulte [Gerenciamento de serviços SOA no Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para saber mais sobre como implantar e gerenciar serviços SOA com HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=September15_HO1-->