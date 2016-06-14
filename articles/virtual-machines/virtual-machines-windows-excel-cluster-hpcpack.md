<properties
 pageTitle="Cluster de HPC Pack para Excel e SOA | Microsoft Azure"
 description="Introdução à execução de cargas de trabalho SOA e Excel em larga escala em um cluster HPC Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="05/25/2016"
 ms.author="danlep"/>

# Introdução com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA

Este artigo mostra como implantar um cluster do Microsoft HPC Pack nos serviços de infraestrutura do Azure (IaaS) usando um modelo de início rápido do Azure ou, se preferir, um script de implantação do Azure PowerShell. Você usará as imagens de VM do Azure Marketplace projetadas para executar cargas de trabalho de arquitetura orientada a serviços (SOA) ou Microsoft Excel com o HPC Pack. Você pode usar o cluster para executar os serviços SOA e HPC simples do Excel de um computador de cliente local. Os serviços do Excel HPC incluem descarregamento de pasta de trabalho do Excel e funções definidas pelo usuário do Excel ou UDFs.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Em um alto nível, o diagrama a seguir mostra o cluster de HPC Pack que você criará.

![Cluster HPC com nós que executam cargas de trabalho do Excel][scenario]

## Pré-requisitos

*   **Computador cliente** - você precisará de um computador cliente baseado no Windows para executar o script de implantação de cluster do Azure PowerShell (se você escolher esse método de implantação) e enviar trabalhos de exemplo do Excel e SOA ao cluster.

*   **Uma assinatura do Azure**: se não tiver uma, você poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

*   **Cota para núcleos**: talvez seja necessário aumentar a cota de núcleos, especialmente se você implantar vários nós de cluster com tamanhos de VM de vários núcleos. Se você estiver usando um modelo de início rápido do Azure, lembre-se de que a cota de núcleos no Gerenciador de Recursos é por região do Azure, e talvez seja necessário aumentar a cota de uma região específica. Consulte [Limites de assinatura do Azure, cotas e restrições](../azure-subscription-service-limits.md). Para aumentar a cota, [abra uma solicitação de atendimento ao cliente online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

*   **Licença do Microsoft Office**: se você implantar nós de computação usando uma imagem de VM do Marketplace HPC Pack com Microsoft Excel, uma versão de avaliação de 30 dias do Microsoft Excel Professional Plus 2013 será instalada nos nós de computação. Depois que o período de avaliação terminar, você precisará fornecer uma licença do Microsoft Office válida para ativar o Excel e continuar executando cargas de trabalho. Confira [Ativação do Excel](#excel-activation) mais adiante neste artigo.


## Etapa 1. Configurar um cluster de HPC Pack no Azure

Mostraremos duas maneiras de configurar o cluster: primeiro, usando um modelo de início rápido do Azure e o portal do Azure; e segundo, usando um script de implantação do Azure PowerShell.


### Usar um modelo de início rápido
Use um modelo de início rápido do Azure para implantar de maneira rápida e fácil um cluster do HPC Pack no portal do Azure. Ao abrir o modelo no portal de visualização, você obtém uma interface do usuário simples na qual inserir as configurações para seu cluster. Siga estas etapas:

>[AZURE.TIP]Se você quiser, use um [modelo do Azure Marketplace](https://ms.portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que crie um cluster semelhante especificamente para cargas de trabalho do Excel. As etapas diferem ligeiramente das que se seguem.

1.  Visite a [página Criar modelo de Cluster de HPC no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se quiser, reveja as informações sobre o modelo e o código-fonte.

2.  Clique em **Implantar no Azure** para iniciar uma implantação com o modelo no portal do Azure.

    ![Implantar o modelo no Azure][github]

3.  No portal, siga estas etapas para especificar os parâmetros para o modelo de cluster HPC.

    a. Na página **Parâmetros**, insira os valores para os parâmetros de modelo. Clique no ícone ao lado de cada configuração para obter informações de Ajuda. Os valores de exemplo são mostrados na tela a seguir. Este exemplo criará um novo cluster de HPC Pack chamado *hpc01* no domínio *hpc.local* que consiste em um nó principal e dois nós de computação. Os nós de computação serão criados a partir de uma imagem de VM do HPC Pack, incluindo o Microsoft Excel.

    ![Inserir parâmetros][parameters]

    >[AZURE.NOTE]A VM do nó principal será criada automaticamente a partir da [imagem mais recente do Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do HPC Pack 2012 R2 no Windows Server 2012 R2. Atualmente, a imagem se baseia no HPC Pack 2012 R2 Update 3.
    >
    >Serão criadas VMs do nó de computação a partir da imagem mais recente da família do nó de computação selecionado. Selecione a opção **ComputeNodeWithExcel** para a imagem do nó de computação HPC Pack mais recente que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013. Se quiser implantar um cluster para sessões SOA gerais ou para o descarregamento de Excel UDF, escolha a opção **ComputeNode** (sem o Excel instalado).

    b. Selecione a assinatura.

    c. Criar um novo grupo de recursos para o cluster, como *hpc01RG*.

    d. Escolha um local para o grupo de recursos, como EUA Central.

    e. Na página **Termos legais**, analise os termos. Se você concordar, clique em **Criar**. Quando tiver terminado de definir os valores para o modelo, clique em **Criar**.

4.  Quando a implantação for concluída (normalmente leva cerca de 30 minutos), exporte o arquivo de certificado de cluster do nó principal do cluster. Em uma etapa posterior, este certificado público será importado no computador cliente para fornecer a autenticação do lado do servidor para a conexão segura de HTTP.

    a. Conecte-se ao nó principal pela Área de Trabalho Remota no portal do Azure.

     ![Conectar-se ao nó principal][connect]

    b. Use os procedimentos padrão no Gerenciador de Certificados para exportar o certificado de nó principal (localizado em Cert:\\LocalMachine\\My) sem a chave privada. Neste exemplo, exporte *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exportar o certificado][cert]

### Use o script de implantação do HPC Pack IaaS

O script de implantação do HPC Pack IaaS é outra forma versátil para implantar um cluster de HPC Pack. Ele cria um cluster no modelo de implantação clássica, enquanto o modelo usa o modelo de implantação do Azure Resource Manager. Além disso, o script é compatível com uma assinatura no serviço Azure Global ou Azure China.

**Pré-requisitos adicionais**

* **Azure PowerShell** - [Instale e configure o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no computador cliente.

* **Script de implantação do Pacote HPC IaaS**: baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.5.0 ou posterior do script.

**Criar o arquivo de configuração**

 O script de implantação do HPC Pack IaaS usa um arquivo de configuração XML como entrada que descreve a infraestrutura do cluster do HPC. Para implantar um cluster que consiste em um nó principal e 18 nós de computação criados a partir da imagem do nó de computação que inclui o Microsoft Excel, substitua os valores para o seu ambiente no seguinte arquivo de configuração de exemplo. Para obter mais informações sobre o arquivo de configuração, veja o arquivo Manual.rtf na pasta scripts e [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

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
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas sobre o arquivo de configuração**

* O **VMName** do nó principal **DEVE** ser exatamente igual a **ServiceName**, ou a execução dos trabalhos SOA falhará.

* Verifique se você especificou **EnableWebPortal** para que o certificado de nó principal seja gerado e exportado.

* O arquivo especifica um script PowerShell pós-configuração PostConfig.ps1 para definir certas configurações no nó principal, como a cadeia de conexão do armazenamento do Azure, removendo a função do nó de computação do nó principal e colocando todos os nós online quando eles são implantados. Veja abaixo um exemplo de script.

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
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

1.  Abra o console do PowerShell no computador cliente como administrador.

2.  Altere o diretório para a pasta de scripts (E:\\IaaSClusterScript neste exemplo).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Execute o comando a seguir para implantar o cluster de HPC Pack. Este exemplo supõe que o arquivo de configuração esteja localizado em E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

O script de implantação do HPC Pack será executado por algum tempo. Uma das ações do script é exportar e baixar o certificado do cluster e salvá-lo na pasta Documentos do usuário atual no computador cliente. O script gerará uma mensagem semelhante a esta: Em uma etapa seguinte, você importará o certificado no repositório de certificados apropriado.
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## Etapa 2. Descarregaras pastas de trabalho do Excel e executar UDFs de um cliente local

### Ativação do Excel

Ao usar a imagem da VM ComputeNodeWithExcel para cargas de trabalho de produção, você precisará fornecer uma chave de licença válida do Microsoft Excel para ativar o Excel nos nós de computação. Caso contrário, a versão de avaliação do Excel vai expirar em 30 dias e a execução das pastas de trabalho do Excel falhará constantemente com a COMException (0x800AC472). Se isso acontecer, faça logon no nó principal e execute clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` em todos os nós de computação do Excel via Gerenciador de Cluster HPC.
    
Isso rearma o Excel para outros 30 dias de avaliação. Você pode fazer isso duas vezes, no máximo. Depois disso, você precisará fornecer uma chave de licença válida do Office.

O Office Professional Plus 2013 instalado nessa imagem de VM é uma edição de volume com uma GVLK (Chave de Licença de Volume Genérico), que pode ser ativada por meio do KMS (Serviço de Gerenciamento de Chaves)/AD-BA (Ativação Baseada no Active Directory) ou uma MAK (Chave de Ativação Múltipla). Para usar o KMS/AD-BA, use um servidor KSM existente ou configure um novo (que pode ser no nó principal) usando o Pacote de Licença de Volume do Microsoft Office 2013. Em seguida, ative a chave do host KMS via Internet ou por telefone. Em seguida, execute clusrun `ospp.vbs` para definir o servidor KMS e a porta e ative o Office em todos os nós de computação do Excel. Para usar a MAK, primeiramente execute clusrun `ospp.vbs` para inserir a chave e ative todos os nós de computação do Excel via Internet ou telefone.

>[AZURE.NOTE]As chaves de produto de varejo do Office Professional Plus 2013 não podem ser usadas com essa imagem de VM. Se você tiver chaves válidas e a mídia de instalação do Office ou das edições do Excel que não seja dessa edição de volume do Office Professional Plus 2013, você também poderá desinstalar essa edição de volume e instalar a edição que tem. O nó de computação do Excel reinstalado pode ser capturado como uma imagem de VM personalizada para ser usada em uma implantação em escala.

### Descarregar pastas de trabalho do Excel

Siga estas etapas para descarregar uma planilha do Excel para executar no cluster de HPC Pack no Azure. Para fazer isso, você deve ter o Excel 2010 ou 2013 já instalado no computador cliente.

1. Use um dos métodos da Etapa 1 para implantar um cluster de HPC Pack com a imagem do nó de computação do Excel. Obter o arquivo de certificado (.cer) do cluster e o nome de usuário e a senha.

2. No computador cliente, importe o certificado de cluster em Cert:\\CurrentUser\\Root.

3. Verifique se o Excel está instalado. Crie um arquivo Excel.exe.config com o seguinte conteúdo na mesma pasta com Excel.exe no computador cliente. Isso garante que o suplemento de COM do Excel HPC Pack 2012 R2 será carregado com êxito.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.	Baixe a [instalação total do HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) e instale o cliente do HPC Pack, ou baixe e instale os [utilitários de cliente do HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) e o Visual C++ 2010 redistribuível apropriado do computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

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

>[AZURE.NOTE] Há um limite de 34 caracteres no Excel 2010 e a caixa de diálogo do conector de cluster 2013. Se o nome completo do cluster for maior, por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com, ele não caberá na caixa de diálogo. A solução alternativa é definir uma variável no nível da máquina como *CCP\_IAASHN* com o valor do nome do cluster longo e inserir *CCP\_IAASHN%* na caixa de diálogo como o nome de nó principal do cluster.

Depois que o cluster for implantado com êxito, continue com as etapas a seguir para executar um exemplo interno do UDF do Excel. Para UDFs personalizados do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para compilar os XLLs e implantá-los no cluster IaaS.

1.	Abra uma nova pasta de trabalho do Excel. Na faixa de opções **Desenvolver**, clique em **Suplementos**. Na caixa de diálogo, clique em **Procurar**, navegue até a pasta %CCP\_HOME%Bin\\XLL32 e selecione o exemplo ClusterUDF32.xll. Se o ClusterUDF32 não existir no computador cliente, você pode copiá-lo da pasta %CCP\_HOME%Bin\\XLL32 no nó principal.

    ![Selecionar o UDF][udf]

2.	Clique em **Arquivo** > **Opções** > **Avançado**. Em **Fórmulas**, marque **Permitir que as funções XLL definidas pelo usuário executem um cluster de cálculo**. Em seguida, clique em **Opções** e digite o nome completo do cluster em **Nome do nó principal do cluster**. Conforme observado anteriormente, essa caixa de entrada é limitada a 34 caracteres, de modo que um nome de cluster longo pode não caber. Você pode usar variáveis no nível da máquina aqui para nomes longos de cluster.)

    ![Configurar o UDF][options]

3.	Clique na célula com o valor =XllGetComputerNameC() e pressione Enter para executar o cálculo de UDF no cluster IaaS. A função simplesmente recupera o nome do nó de computação no qual um UDF é executado. Para a primeira execução, uma caixa de diálogo de credenciais solicita o nome de usuário e a senha para se conectar ao cluster IaaS.

    ![Executar UDF][run]

    Quando houver uma grande quantidade de células para calcular, pressione Alt-Shift-Ctrl + F9 para executar o cálculo em todas as células.

## Etapa 3. Executar uma carga de trabalho SOA a partir de um cliente local

Para executar aplicativos SOA gerais no cluster de HPC Pack IaaS, primeiro use um dos métodos da Etapa 1 para implantar o cluster IaaS, usando a imagem de um nó de computação genérico (porque você não precisará do Excel em nós de computação). Depois, siga estas etapas:

1. Após receber o certificado do cluster, importe-o para o computador cliente em Cert:\\CurrentUser\\Root.

2. Instale o [SDK do HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49921) e [Utilitários de cliente do HPC Pack 2012 R2 Atualização 3](https://www.microsoft.com/download/details.aspx?id=49923) para desenvolver e executar aplicativos cliente SOA.

3. Baixe o [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633) HelloWorldR2. Abra o HelloWorldR2.sln no Visual Studio 2010 ou 2012.

4. Compile o projeto EchoService primeiro e implante o serviço no cluster IaaS da mesma maneira que implanta em um cluster local. Para obter etapas detalhadas, consulte o Leiame.doc no HelloWordR2. Modifique e crie o HellWorldR2 e outros projetos, conforme descrito a seguir para gerar os aplicativos cliente SOA em execução em um cluster de IaaS do Azure de um computador cliente local.

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

* Se preferir, use o TransportScheme padrão em SessionStartInfo ou defina-o explicitamente como Http.

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

Para fazer isso, defina explicitamente um sinalizador UseAzureQueue para false em SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### Usar associação NetTcp

Para usar a associação NetTcp, a configuração é semelhante a conectar-se a um cluster local. Você precisará abrir alguns pontos de extremidade na VM do nó principal. Se você usou o script de implantação IaaS do HPC Pack para criar o cluster, por exemplo, defina os pontos de extremidade no portal clássico do Azure seguindo as etapas abaixo.


1. Pare a VM.

2. Adicione as portas TCP 9090, 9087, 9091, 9094 para a Sessão, Agente, trabalhador do Agente e Serviços de dados, respectivamente

    ![Configurar pontos de extremidade][endpoint]

3. Inicie a VM.

O aplicativo cliente SOA não requer alterações, exceto do nome principal para o nome completo do cluster IaaS.

## Próximas etapas

* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para saber mais sobre como executar cargas de trabalho do Excel com o HPC Pack.

* Consulte [Gerenciamento de serviços SOA no Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para saber mais sobre como implantar e gerenciar serviços SOA com HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png

<!---HONumber=AcomDC_0601_2016-->