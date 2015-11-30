<properties
   pageTitle="Configurar um cluster de Malha de Serviço no Portal do Azure | Microsoft Azure"
   description="Configurar um cluster de Malha de Serviço no Portal do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="chackdan"/>

# Configurar um cluster de Malha de Serviço no Portal do Azure

Esta página ajuda na configuração de um cluster de Malha de Serviço. Supõe-se que a sua assinatura tem a quantidade de núcleos suficiente para implantar as VMs de IaaS que comporão esse cluster.


## Criando o cluster

1. Faça logon no Portal do Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Clique em **+ Novo** para adicionar um novo modelo de recurso. Pesquise nosso modelo no Marketplace em Tudo - ele é chamado **Cluster de Malha de Serviço** (você pode navegar até **Tudo** clicando na categoria de nível superior - Marketplace > e, em seguida, procure "Malha" em "Tudo" e pressione enter - às vezes, o filtro automático não funciona, certifique-se de **pressionar enter**) ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. Selecione "Cluster de Malha de Serviço" da lista
4. Navegue até a folha do cluster de Malha de Serviço e clique em **Criar** e forneça os detalhes sobre o cluster
5. Crie um **novo Grupo de Recursos (RG)** - com o mesmo nome do Cluster - é melhor para localizá-los mais tarde. É especialmente útil quando você está tentando fazer alterações em sua implantação e/ou excluir o cluster.

  	Observação - Embora você possa optar por usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isso facilita bastante a exclusão de clusters não necessários

 	 ![CreateRG][CreateRG]


6. Certifique-se de selecionar a **assinatura** desejada para a implantação do cluster, especialmente se você tiver várias assinaturas..

7. Selecione um **local** no menu suspenso (se você deseja criá-lo em outro local, caso contrário o padrão será Oeste dos EUA)

8. Configurar seu **Tipo de nó**. O Tipo de nó pode ser visto como equivalentes em "Funções" nos Serviços de Nuvem. Eles definem os tamanhos de VM, o número de VMs e suas propriedades. O cluster pode ter mais de um Tipo de nó. A única restrição é que você precisará de pelo menos um Tipo de nó (principal ou o primeiro que você definir no portal) que seja de pelo menos 5 VMs.
	1. Selecione o tamanho de VM /Camada de preços necessário. (o padrão é Padrão D4, se apenas pretender usar esse cluster para testar seu aplicativo, há problemas em selecionar D2 ou qualquer tamanho menor de VM)	
	2. Escolha o número de VMs, você pode aumentar ou diminuir o número de VMs em um Tipo de nó no futuro, porém o principal ou o primeiro tipo de nó deve ter pelo menos 5 VMs
	3. Escolha um nome para o Tipo de nó (1 a 12 caracteres que contém somente letras e números)	
	4. Escolha o nome de usuário e a senha de área de trabalho remota da VM
	5. **Considerações de tipo de nó quando você tiver vários tipos de nó**. Se você estiver planejando implantar um cluster de Tipo de nó único, então pule para a próxima etapa.

		- Para explicar esse conceito, vamos usar um exemplo. Se desejar implantar um aplicativo que contém um serviço "Front-End" e um serviço de "Back-End" e se deseja colocar o serviço "Front-End" nas VMs menores (tamanhos de VM como A2, D2, etc) com portas abertas para a internet e o serviço de "Back-End" que é de computação intensiva em VMs maiores (com tamanhos de VM como D4, D6, D12 etc) que não são voltadas para a internet.
		- No entanto, você pode colocar ambos os serviços em um Tipo de nó, é recomendável que você coloque-os em um cluster com dois tipos de nós, cada tipo de nó pode ter propriedades distintas, como conectividade com a internet e o tamanho da VM e o número de VMs que podem ser escalado independentemente.
		- Defina o Tipo de nó que acabará tendo pelo menos 5 VMs primeiro. Os outros tipos de nó podem ter um mínimo de 1 VM.
					

  	![CreateNodeType][CreateNodeType]

9. **Portas do aplicativo** - se você planeja implantar seus aplicativos para o cluster imediatamente, então adicione as portas que você deseja abrir para seus aplicativos nesse Tipo de nó (ou nos Tipos de nó que você criou). Você pode adicionar portas para o Tipo de nó posteriormente modificando a balanceador de carga associado a esse Tipo de nó (você precisa adicionar um teste e, em seguida, adicionar o teste as regras de balanceador de carga). Fazê-lo nesse momento é um pouco mais fácil, já que a automação de portal adicionará os testes e regras necessários para o balanceamento de carga.
	1. Você pode encontrar as portas do aplicativo em seus manifestos de serviço que fazem parte do pacote de aplicativos. Vá para cada um dos aplicativos, abra os manifestos de serviço e anote todos os pontos de extremidade de “entrada” que seus aplicativos precisam para se comunicar com o mundo exterior.
	2. Adicione todas as portas, com vírgula no campo "Pontos de extremidade de entrada do aplicativo". O ponto de extremidade de conexão do cliente TCP é 19000 por padrão, portanto você não precisa especificá-los. Por exemplo, meu aplicativo precisa da porta "83" para ser aberto. Você encontrará isso no servicemanifest.xml em seu pacote de aplicativo (pode haver mais de um servicemanifest.xml).

  A maioria da dos aplicativos de exemplo usam as portas 80 e 8081, então adicione-as se você planeja implantar exemplos para esse cluster.

  ![Portas][Ports]



1. **Opcional: Propriedades de Posicionamento**- você não precisa adicionar quaisquer configurações aqui, uma propriedade de posicionamento padrão de "NodeTypeName" é adicionada pelo sistema. Você pode optar por adicionar mais se seu aplicativo tiver essa necessidade. 

  
## Configurações de segurança

Neste momento, a Malha de Serviço só oferece suporte a clusters de proteção por meio de um certificado X509. Antes de iniciar esse processo, você precisará carregar seu certificado no KeyVault. Consulte [Segurança do Cluster da Malha de Serviço](service-fabric-cluster-security.md) para saber como fazer isso.

Proteger seu cluster é opcional, mas é altamente recomendável. Se você optar por não proteger o cluster, você precisa alternar o Modo de segurança para "Nenhum".

Obtenha detalhes sobre considerações de segurança e como são documentadas em [Segurança de Cluster de Malha do Serviço](service-fabric-cluster-security.md)

![SecurityConfigs][SecurityConfigs]



## Opcional: Configurando o diagnóstico

Por padrão, os diagnósticos são habilitados no cluster para ajudar na solução de problemas. Se você quiser desabilitar o diagnóstico:

1. Navegue até a folha de Configurações de diagnóstico.

2. Altere a alternância de Status para Desativada.

## Opcionais: Configurações de Malha

Essa é uma opção avançada que permite que você altere as configurações padrões para o cluster de malha de serviço. é recomendável **não alterar** os padrões, a menos que você tenha certeza de que seja necessário para o seu aplicativo e/ou o Cluster.

## Concluindo a criação do cluster
Você pode clicar em **Resumo** para ver as configurações fornecidas ou baixar o modelo ARM que será usado para implantar o cluster. Depois de ter fornecido as configurações obrigatórias, o botão de criar será habilitado e você pode começar o processo de criação de cluster.
 

Você pode ver o progresso nas NOTIFICAÇÕES (clique no ícone do “Sino” próximo à barra de status para a direita da tela). Se você tivesse clicado em "Fixar no quadro inicial" durante a criação do cluster, você veria "Implantando o Cluster de Malha de Serviço" fixado no quadro inicial

![Notificações][Notifications]

## Exibindo o status do cluster

Depois que a implantação for concluída, você pode inspecionar o cluster no portal.

1. Vá para **Procurar** e clique no recurso - **Clusters de Malha de Serviço**.

2. Localize o cluster e clique nele.

  ![BrowseCluster][BrowseCluster]

3. Agora você pode ver os detalhes do cluster no painel, inclusive o endereço IP público do cluster. Observe que passar com o mouse sobre o **Endereço IP público de cluster** abrirá uma área de transferência, na qual você pode clicar para copiá-lo.

  ![ClusterDashboard][ClusterDashboard]

  A parte do Monitor do nó na folha do Painel de controle do cluster, indica o número de VMs que são íntegras vs não íntegras. Você pode encontrar mais detalhes sobre o estado de integridade na seção [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md) da nossa documentação


## Conectar-se ao cluster e implantando um aplicativo

Com o cluster configurado, agora você pode se conectar e começar a implantar aplicativos.

1. Em um computador que tem o SDK de Malha de Serviço instalado, inicie o Windows PowerShell.

2. Execute um dos seguintes conjuntos de comandos do PS dependendo se você criou um cluster seguro ou não.
 

- Opção 1 **Conectando-se a um cluster não seguro**. 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

Execute os seguintes comandos para implantar seu aplicativo, substituindo os caminhos mostrados pelos adequados em seu computador.

 - Opção 2 **Conectando-se a um cluster seguro**

Execute o seguinte para configurar o certificado no computador em que você pretende usar para executar o cmd de PS "Connect-serviceFabricCluster"

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Execute o seguinte PS para conectar-se agora a um cluster seguro. Os detalhes do certificado são os mesmos que você atribuiu no portal

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
Por exemplo, o comando PS acima deve ser semelhante ao seguinte.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


Execute os seguintes comandos para implantar seu aplicativo, substituindo os caminhos mostrados pelos adequados em seu computador. O exemplo a seguir implanta o aplicativo de exemplo de contagem de palavras.

Copie o pacote para o cluster conectado na etapa anterior.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
Register your application type with service fabric.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount" ````

Crie uma nova instância no tipo de Aplicativo que você acabou de registrar.

```powershell New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0 ````

Agora abra o navegador de sua escolha e conecte-se ao ponto de extremidade que o aplicativo está escutando. Para a minha contagem de aplicativo de exemplo, o URL parece o seguinte.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Gerenciando seu aplicativo da Malha do Serviço no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Segurança do Cluster de Malha de Serviço](service-fabric-cluster-security.md)
- [Introdução ao modelo de Integridade da Malha de Serviço](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=Nov15_HO4-->