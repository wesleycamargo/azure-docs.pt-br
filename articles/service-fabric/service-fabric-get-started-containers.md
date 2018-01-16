---
title: "Como criar um aplicativo de contêiner do Azure Service Fabric | Microsoft Docs"
description: "Crie seu primeiro aplicativo de contêiner do Windows no Azure Service Fabric.  Compile uma imagem do Docker com o seu aplicativo Python, envie a imagem por push para um registro de contêiner, compile e implante um aplicativo de contêiner do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: ca0817b37b6baaa4ef63dfb76790fb3b3735b55f
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2018
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Como criar seu primeiro aplicativo de contêiner do Service Fabric no Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este artigo mostra como criar uma imagem do Docker que contém um aplicativo de web Python [Flask](http://flask.pocoo.org/) e a implantá-lo em um cluster do Service Fabric.  Você também compartilhará seu aplicativo em contêineres pelo [Registro de Contêiner do Azure](/azure/container-registry/).  Este artigo pressupõe uma compreensão básica sobre o Docker. Saiba mais sobre o Docker lendo a [Visão geral de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
Um computador de desenvolvimento executando:
* Visual Studio 2015 ou Visual Studio 2017.
* [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obter Docker CE para o Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique no ícone de bandeja e selecione **Alternar para contêineres do Windows**. Isso é necessário para executar imagens do Docker com base no Windows.

Um cluster do Windows com três ou mais nós em execução no Windows Server 2016 com contêineres - [Criar um cluster](service-fabric-cluster-creation-via-portal.md) ou [experimente o Service Fabric gratuitamente](https://aka.ms/tryservicefabric).

Um registro no Registro de Contêiner do Azure - [Crie um registro de contêiner](../container-registry/container-registry-get-started-portal.md) em sua assinatura do Azure.

> [!NOTE]
> Ainda não há suporte para a implantação de contêineres em um cluster do Service Fabric no Windows 10 ou em um cluster com Docker CE. Este passo a passo testa localmente usando o mecanismo do Docker no Windows 10 e, por fim, implanta os serviços de contêiner em um cluster do Windows Server no Azure executando o Docker EE. 
>   

> [!NOTE]
> A versão 6.1 do Service Fabric tem suporte para a versão prévia 1709 do Windows Server. A rede aberta e o Serviço DNS do Service Fabric não funcionam com a versão 1709 do Windows Server. 
> 

## <a name="define-the-docker-container"></a>Defina o contêiner Docker
Crie uma imagem com base na [imagem do Python](https://hub.docker.com/_/python/) localizada no Hub do Docker.

Defina o contêiner do Docker em um Dockerfile. O Dockerfile contém instruções para configurar o ambiente do seu contêiner, carregar o aplicativo que você deseja executar e mapear portas. O Dockerfile é a entrada para o comando `docker build`, que cria a imagem.

Crie um diretório vazio e crie o arquivo *Dockerfile* (sem extensão de arquivo). Adicione o seguinte ao *Dockerfile* e salve as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para saber mais informações.

## <a name="create-a-simple-web-application"></a>Criar um aplicativo Web simples
Crie um aplicativo Web Flask que escuta a porta 80 retornar "Olá, Mundo!".  No mesmo diretório, crie o arquivo *requirements.txt*.  Adicione o seguinte e salve as alterações:
```
Flask
```

Crie também o arquivo *app.py* e adicione o seguinte:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():

    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Criar a imagem
Execute o comando `docker build` para criar a imagem que executa o seu aplicativo web. Abra uma janela do PowerShell e acesse o diretório que contém o Dockerfile. Execute o comando a seguir:

```
docker build -t helloworldapp .
```

Esse comando cria a nova imagem usando as instruções no seu Dockerfile de nomeando (-t marcação) a imagem "helloworldapp". A criação de uma imagem puxa a imagem base do Hub do Docker e cria uma nova imagem que adiciona seu aplicativo sobre a imagem base.  

Depois de concluir o comando de compilação, execute o comando `docker images` para ver informações sobre a nova imagem:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
Verifique a imagem localmente antes de enviá-la ao registro de contêiner.  

Executar o aplicativo:

```
docker run -d --name my-web-site helloworldapp
```

*name* fornece um nome para o contêiner em execução (em vez da ID do contêiner).

Depois que o contêiner iniciar, localize seu endereço IP para que você pode se conectar ao seu contêiner em execução em um navegador:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para o endereço IP retornado, por exemplo "http://172.31.194.61". Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

Para interromper o contêiner, execute:

```
docker stop my-web-site
```

Exclua o contêiner do seu computador de desenvolvimento:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registro de contêiner
Depois de verificar que o contêiner é executado na máquina de desenvolvimento, envie a imagem para seu registro no Registro de Contêiner do Azure.

Execute ``docker login`` para fazer logon em seu registro de contêiner as [credenciais de registro](../container-registry/container-registry-authentication.md).

O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação. Ou pode fazer logon usando o nome de usuário e a senha do registro.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando a seguir cria uma marca ou alias imagem, com um caminho totalmente qualificado para o registro. Este exemplo coloca a imagem no namespace ```samples``` para evitar confusão na raiz do registro.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Enviar a imagem para o eu registro de contêiner:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Crie o serviço em contêineres no Visual Studio
As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a criar um aplicativo em contêineres.

1. Inicie o Visual Studio.  Selecione **Arquivo** > **Novo** > **Projeto**.
2. Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **OK**.
3. Selecione **Contêiner** na lista de **modelos de serviço**.
4. Em **Nome da imagem** insira "myregistry.azurecr.io/samples/helloworldapp", a imagem é enviada para o seu repositório de contêiner.
5. Dê um nome ao seu serviço e clique em **OK**.

## <a name="configure-communication"></a>Configurar a comunicação
O serviço em contêineres precisa de um ponto de extremidade para comunicação. Adicione um elemento `Endpoint` com o protocolo, a porta e o tipo ao arquivo ServiceManifest.xml. Neste artigo, o serviço em contêineres escuta na porta 8081.  Neste exemplo, uma porta fixa 8081 é usada.  Se nenhuma porta for especificada, uma porta aleatória do intervalo de portas de aplicativo é escolhida.  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

Definindo um ponto de extremidade, o Service Fabric publica o ponto de extremidade para o Serviço de nomeação.  Outros serviços em execução no cluster podem resolver este contêiner.  Você também pode executar a comunicação de contêiner para contêiner usando o [proxy reverso](service-fabric-reverseproxy.md).  A comunicação é realizada fornecendo a porta de escuta de proxy reverso HTTP e o nome dos serviços com os quais você deseja se comunicar como variáveis de ambiente.

## <a name="configure-and-set-environment-variables"></a>Configurar e definir as variáveis de ambiente
Variáveis de ambiente podem ser especificadas para cada pacote de códigos no manifesto do serviço. Esse recurso está disponível para todos os serviços, independentemente de eles serem implantados como contêineres ou processos ou executáveis convidados. Você pode substituir valores de variáveis de ambiente no manifesto do aplicativo ou especificá-los durante a implantação como parâmetros de aplicativo.

O trecho XML do manifesto do serviço a seguir mostra um exemplo de como especificar variáveis de ambiente para um pacote de códigos:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Essas variáveis de ambiente podem ser substituídas no manifesto do aplicativo:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configurar mapeamento de porta para host e descoberta de contêiner para contêiner
Configure uma porta de host usada para se comunicar com o contêiner. A associação de porta mapeia a porta na qual o serviço está escutando dentro do contêiner para uma porta no host. Adicionar um elemento `PortBinding` no elemento `ContainerHostPolicies` do arquivo ApplicationManifest.xml.  Para este artigo, `ContainerPort` é 80 (o contêiner expõe a porta 80, conforme especificado no Dockerfile) e `EndpointRef` é "Guest1TypeEndpoint" (o ponto de extremidade definido anteriormente no manifesto do serviço).  As solicitações de entrada para o serviço na porta 8081 são mapeadas para a porta 80 no contêiner.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>Definir autenticação do registro de contêiner
Configurar a autenticação de registro de contêiner adicionando `RepositoryCredentials` a `ContainerHostPolicies` do arquivo ApplicationManifest.xml. Adicione a conta e a senha para o registro de contêiner myregistry.azurecr.io, o que permite ao serviço baixar a imagem de contêiner do repositório.

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

É recomendável que você criptografe a senha do repositório usando um certificado de criptografia que é implantado em todos os nós do cluster. Quando o Service Fabric implanta o pacote de serviço para o cluster, o certificado de criptografia é usado para descriptografar o texto cifrado.  O cmdlet Invoke-ServiceFabricEncryptText é usado para criar o texto cifrado para a senha, que é adicionado ao arquivo ApplicationManifest.xml.

O script a seguir cria um novo certificado autoassinado e o exporta para um arquivo PFX.  O certificado é importado para um cofre de chaves existente e, em seguida, implantado para o cluster do Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Criptografar a senha usando o cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Substitua a senha com o texto codificado retornado pelo cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) e defina `PasswordEncrypted` como "true".

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>Configurar o modo de isolamento
O Windows dá suporte a dois modos de isolamento para contêineres: processo e Hyper-V. Com o modo de isolamento de processo, todos os contêineres em execução no mesmo computador host compartilham o kernel com o host. Com o modo de isolamento do Hyper-V, os kernels são isolados entre cada contêiner do Hyper-V e o host do contêiner. O modo de isolamento é especificado no elemento `ContainerHostPolicies` no arquivo de manifesto do aplicativo. Os modos de isolamento que podem ser especificados são `process`, `hyperv` e `default`. O modo de isolamento padrão usa `process` por padrão em hosts do Windows Server e usa `hyperv` por padrão em hosts do Windows 10. O trecho a seguir mostra como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > O modo de isolamento do Hyper-v está disponível nas SKUs do Azure Ev3 e Dv3 que têm suporte para virtualização aninhada. 
   >
   >

## <a name="configure-resource-governance"></a>Configurar governança de recursos
A [governança de recursos](service-fabric-resource-governance.md) restringe os recursos que o contêiner pode usar no host. O elemento `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, é usado para declarar os limites de recurso para um pacote de códigos de serviço. Os limites de recursos podem ser definidos para os seguintes recursos: Memory, MemorySwap, CpuShares (CPU relative weight), MemoryReservationInMB, BlkioWeight (BlockIO relative weight).  Neste exemplo, o pacote de serviço Guest1Pkg obtém um núcleo nos nós de cluster em que ele é colocado.  Os limites de memória são absolutos e, portanto, o pacote de códigos é limitado a 1024 MB de memória (e a uma reserva de garantia reversível da mesma). Os pacotes de códigos (contêineres ou processos) não podem alocar mais memória do que esse limite. A tentativa de fazer isso resultará em uma exceção de memória insuficiente. Para que a imposição do limite de recursos funcione, todos os pacotes de códigos em um pacote de serviço devem ter limites de memória especificados.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Configurar o HEALTHCHECK do Docker 

Iniciando a versão 6.1, o Service Fabric integra automaticamente os eventos do [HEALTHCHECK do Docker](https://docs.docker.com/engine/reference/builder/#healthcheck) em seu relatório de integridade do sistema. Isso significa que, se o contêiner tiver o **HEALTHCHECK** habilitado, o Service Fabric relatará a integridade sempre que o status de integridade do contêiner for alterado conforme relatado pelo Docker. Um relatório de integridade **OK** será exibido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) quando o *health_status* for *íntegro* e um **AVISO** aparecerá quando o *health_status* for *não íntegro*. A instrução do **HEALTHCHECK** apontando para a verificação real que é executada para monitorar a integridade do contêiner deve estar presente no **dockerfile** usado ao gerar a imagem de contêiner. 

![HealthCheckHealthy][3]

![HealthCheckUnealthyApp][4]

![HealthCheckUnhealthyDsp][5]

Você pode configurar o comportamento do **HEALTHCHECK** para cada contêiner especificando as opções do **HealthConfig** como parte do **ContainerHostPolicies** no ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Por padrão, o *IncludeDockerHealthStatusInSystemHealthReport* é definido como **true** e o *RestartContainerOnUnhealthyDockerHealthStatus* é definido como  **false**. Se o *RestartContainerOnUnhealthyDockerHealthStatus* for definido como **true**, um contêiner relatando repetidamente um estado não íntegro será reiniciado (possivelmente em outros nós).

Se você deseja desabilitar a integração do **HEALTHCHECK** para todo o cluster do Service Fabric, precisará definir o [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) como **false**.

## <a name="deploy-the-container-application"></a>Como implantar o aplicativo de contêiner
Salve todas as suas alterações e compile o aplicativo. Para publicar o seu aplicativo, clique o botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e selecione **Publicar**.

Em **Ponto de Extremidade de Conexão**, insira o ponto de extremidade de gerenciamento para o cluster.  Por exemplo, "containercluster.westus2.cloudapp.azure.com:19000". Você pode encontrar o ponto de extremidade de conexão do cliente na folha de Visão geral para o cluster no [portal do Azure](https://portal.azure.com).

Clique em **Publicar**.

O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric. Abra um navegador e acesse http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implantação do aplicativo.  O aplicativo é implantado, mas fica em estado de erro até que a imagem seja baixada nos nós de cluster (o que pode levar algum tempo, dependendo do tamanho da imagem): ![Erro][1]

O aplicativo está pronto quando ele está em ```Ready``` estado: ![pronto][2]

Abra um navegador e acesse http://containercluster.westus2.cloudapp.azure.com:8081. Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

## <a name="clean-up"></a>Limpar
Você continua a incorrer em encargos enquanto o cluster estiver em execução, considere [excluir o cluster](service-fabric-cluster-delete.md).  [Clusters de terceiros](https://try.servicefabric.azure.com/) são excluídos automaticamente após algumas horas.

Depois que você enviar a imagem para o registro de contêiner, você pode excluir a imagem local do seu computador de desenvolvimento:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="specify-os-build-version-specific-container-images"></a>Especifique a versão da compilação do sistema operacional das imagens de contêiner específicas 

Os contêineres do Windows Server (modo de isolamento do processo) podem não ser compatíveis com versões mais recentes do sistema operacional. Por exemplo, os contêineres do Windows Server criados usando o Windows Server 2016 não funcionam na versão 1709 do Windows Server. Portanto, se os nós do cluster forem atualizados para a versão mais recente, os serviços de contêiner criados com versões anteriores do sistema operacional poderão falhar. Para contornar isso com a versão 6.1 do tempo de execução e mais recente, o Service Fabric oferece suporte à especificação de várias imagens do sistema operacional por contêiner e marca-as com as versões de compilação do sistema operacional (obtidas com a execução do `winver` em um prompt de comando do Windows).  É recomendável atualizar primeiro os manifestos do aplicativo e especificar as substituições de imagem por versão do sistema operacional antes de atualizar o sistema operacional nos nós. O trecho de código a seguir mostra como especificar várias imagens de contêiner no manifesto do aplicativo, **ApplicationManifest.xml**:


```xml
<ContainerHostPolicies> 
         <ImageOverrides> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
     </ContainerHostPolicies> 
```
A versão de compilação para o WIndows Server 2016 é 14393 e a versão de compilação para a versão 1709 do Windows Server é 16299. O manifesto do serviço continua a especificar apenas uma imagem por serviço de contêiner, como mostra o seguinte:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Os recursos de marcação da versão de compilação do sistema operacional só estão disponíveis para o Service Fabric no Windows
   >

Se o sistema operacional subjacente na VM é a compilação 16299 (versão 1709), o Service Fabric escolherá a imagem do contêiner correspondente a essa versão do Windows Server.  Se uma imagem de contêiner sem marcas também for fornecida junto com as imagens de contêiner marcadas no manifesto do aplicativo, o Service Fabric tratará a imagem não marcada como uma que funciona entre versões diferentes. É recomendável marcar as imagens de contêiner explicitamente.


## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo de manifestos de serviço e aplicativo do Service Fabric
Aqui estão os manifestos de aplicativo e serviço completos usados neste artigo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurar o intervalo de tempo antes do contêiner ser forçado a terminar

Você pode configurar um intervalo de tempo para a execução aguardar antes do contêiner ser removido após a exclusão do serviço (ou um movimento para outro nó) ter iniciado. Configurar o intervalo de tempo envia o comando `docker stop <time in seconds>` para o contêiner.   Para obter mais detalhes, consulte [parar docker](https://docs.docker.com/engine/reference/commandline/stop/). O intervalo de tempo de espera é especificado na seção `Hosting`. O trecho de manifesto do cluster a seguir mostra como definir o intervalo de espera:

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
O intervalo de tempo padrão é definido para 10 segundos. Como essa configuração é dinâmica, uma configuração somente atualiza no cluster que atualiza no tempo limite. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurar a execução para remover as imagens de contêiner não utilizadas

Você pode configurar o cluster do Service Fabric para remover as imagens de contêiner não utilizadas do nó. Essa configuração permite que o espaço em disco seja recapturado se houver imagens de contêiner demais no nó.  Para habilitar esse recurso, atualize a `Hosting` seção no manifesto do cluster, conforme mostrado no trecho a seguir: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Para as imagens que não devem ser excluídas, você pode especificá-las no parâmetro `ContainerImagesToSkip`. 


## <a name="configure-container-image-download-time"></a>Configurar o tempo de download de imagem de contêiner

Por padrão, o tempo de execução do Service Fabric aloca um tempo de 20 minutos para baixar e extrair as imagens de contêiner, o que funciona para a maioria das imagens de contêiner. Para imagens grandes, ou quando a conexão de rede estiver lenta, talvez seja necessário aumentar o tempo de espera antes de cancelar o download e a extração da imagem. Isso pode ser definido usando o atributo **ContainerImageDownloadTimeout** na seção **Hospedagem** do manifesto do cluster, conforme mostrado no trecho de código a seguir:

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Definir política de retenção de contêiner

Para ajudar a diagnosticar as falhas de inicialização do contêiner, o Service Fabric (versão 6.1 ou superior) oferece suporte à retenção de contêineres que encerraram ou falharam na inicialização. Essa política pode ser definida no arquivo **ApplicationManifest.xml** conforme mostrado no trecho de código a seguir:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A configuração **ContainersRetentionCount** especifica o número de contêineres para manter quando ocorre uma falha. Se um valor negativo for especificado, todos os contêineres com falha serão mantidos. Quando o atributo **ContainersRetentionCount** não for especificado, nenhum contêiner será retido. O atributo **ContainersRetentionCount** também oferece suporte a Parâmetros de Aplicativo para que os usuários possam especificar valores diferentes para clusters de teste e produção. É recomendável usar restrições de posicionamento para direcionar o serviço de contêiner para um determinado nó quando esses recursos forem usados com a finalidade de impedir que o serviço de contêiner se mova para outros nós. Todos os contêineres retidos usando esse recurso devem ser removidos manualmente.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Como implantar um aplicativo .NET em um contêiner](service-fabric-host-app-in-a-container.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
