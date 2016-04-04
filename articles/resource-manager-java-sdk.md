<properties
   pageTitle="SDK do Gerenciador de Recursos para .Java| Microsoft Azure"
   description="Uma visão geral dos exemplos de autenticação e de uso do SDK do Gerenciador de Recursos para Java"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/16/2016"
   ms.author="navale;tomfitz;"/>
   
# SDK do Gerenciador de Recursos do Azure para Java
   
Os SDKs de Visualização do ARM (Azure Resource Manager - Gerenciador de Recursos do Azure) estão disponíveis em várias linguagens e plataformas. Cada uma dessas implementações de linguagem estão disponíveis por meio dos gerenciadores de pacotes de seu ecossistema e do GitHub.

O código em cada um desses SDKs é gerado a partir de [especificações da API RESTful do Azure](https://github.com/azure/azure-rest-api-specs). Essas especificações são software livre e se baseiam na especificação do Swagger v2. O código do SDK é um código gerado por um projeto de software livre chamado [AutoRest](https://github.com/azure/autorest). O AutoRest transforma essas especificações da API RESTful em bibliotecas de cliente em várias linguagens. Se houver aspectos do código gerado nos SDKs que você deseja melhorar, todo o conjunto de ferramentas para criar SDKs está liberado, disponível gratuitamente e baseado no formato da especificação da API amplamente adotado.

O SDK do Gerenciador de Recursos do Azure para Java é hospedado no [repositório do SDK do Azure para Java](https://github.com/azure/azure-sdk-for-java) do GitHub. Observe que, no momento em que este documento estiver sendo gravado, o SDK estará em modo de visualização. Os seguintes pacotes estão disponíveis:

* Gerenciamento de Computação: (azure-mgmt-compute)
* Gerenciamento de DNS: (azure-mgmt-dns)
* Gerenciamento de Rede: (azure-mgmt-network)
* Gerenciamento de Recursos: (azure-mgmt-resources)
* Gerenciamento de SQL: (azure-mgmt-sql)
* Gerenciamento de Armazenamento: (azure-mgmt-storage)
* Gerenciamento do Gerenciador de Tráfego: (azure-mgmt-traffic-manager)
* Utilitários e Auxiliares: (azure-mgmt-utility)
* Gerenciamento de Sites/Aplicativos Web: (azure-mgmt-websites)

Siga a página Wiki de Recursos do [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) para obter uma lista atualizada.

## Pré-requisitos
1. Java versão 1.6 ou posterior
2. [maven](https://maven.apache.org/) Se você deseja desenvolver no SDK

## Obter o SDK
Os jars distribuídos do [Maven](https://maven.apache.org/) são a maneira recomendada para começar com o SDK do Azure para Java. Você pode adicionar essas dependências a muitas das ferramentas de gerenciamento de dependência do Java (Maven, Gradle, Ivy...). Siga este [link](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22) para obter uma lista das bibliotecas disponíveis no maven.

Como alternativa, você obtém o sdk diretamente da origem usando o git. Para obter o código-fonte do SDK via Git:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(Os exemplos nesta visão geral usarão o Maven como a origem dos pacotes SDK)

O SDK inclui exemplos para alguns dos cenários: autenticação, provisionamento de máquina virtual e muito mais. Todos podem ser encontrados no repositório GitHub [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Classes Auxiliares

O SDK inclui classes auxiliares para vários pacotes principais. As classes auxiliares são implementadas no pacote azure-mgmt-utility:

* AuthHelper - classe auxiliar de autenticação
* ComputeHelper - classe auxiliar de computação
* NetworkHelper - classe auxiliar de rede
* ResourceHelper - classe auxiliar de implantações
* StorageHelper - classe auxiliar de armazenamento

**Informações de dependência do Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

Isso usará a versão 0.9.1 do pacote do utilitário.

## Autenticação

A autenticação para ARM é tratada pelo Azure AD (Active Directory). Para se conectar a uma API, você primeiro precisa se autenticar com o Azure AD para receber um token de autenticação que poderá ser passado em cada solicitação. Para obter esse token, primeiro você precisa criar o que chamamos de Aplicativo do Azure AD e uma Entidade de Serviço que será usada para fazer logon. Siga [Criar Aplicativo do Azure AD e Entidade de Serviço](./resource-group-create-service-principal-portal.md) para obter instruções detalhadas.

Após criar a entidade de serviço, você deverá ter:

* ID do Cliente (GUID)
* Segredo do cliente (cadeia de caracteres)
* Id do locatário (GUID) ou nome de domínio (cadeia de caracteres)

Quando tiver esses valores, você poderá obter um Token de Acesso do Active Directory, válido por uma hora.

O Java SDK inclui uma classe auxiliar AuthHelper que cria o token de acesso, uma vez recebidos a ID do cliente, o segredo e a ID de locatário. O exemplo a seguir, na classe [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java), usa o método *getAccessTokenFromServicePrincipalCredentials* de AuthHelper para obter o token de acesso:

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Criar uma Máquina Virtual 
O pacote de utilitário inclui uma classe auxiliar [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) para criar uma máquina virtual. Alguns exemplos para trabalhar com máquinas virtuais podem ser encontrados no pacote azure-mgmt-samples em [Computação](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

A seguir temos um fluxo simples para criar uma máquina virtual. Neste exemplo, a classe auxiliar criará o armazenamento e a rede como parte da criação da VM:

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Implantar um modelo
A classe [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) foi criada para facilitar o processo de implantação de um modelo do ARM com o SDK do Java.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```
## Listar todas as Máquinas Virtuais
Não é necessário usar as classes auxiliares (embora elas possam facilitar sua vida); em vez disso, use diretamente as classes de serviço para cada provedor de recursos. Neste exemplo, listamos alguns dos recursos na assinatura autenticada – para cada grupo de recursos, encontre as máquinas virtuais e os IPs associados a ela.

```java
// authenticate and get access token
Configuration config = createConfiguration();
ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

// list all resource groups     
ArrayList<ResourceGroupExtended> resourceGroups = resourceManagementClient.getResourceGroupsOperations().list(null).getResourceGroups();
for (ResourceGroupExtended resourcesGroup : resourceGroups) {
   String rgName = resourcesGroup.getName();
   System.out.println("Resource Group: " + rgName);
   
   // list all virtual machines
   ArrayList<VirtualMachine> vms = computeManagementClient.getVirtualMachinesOperations().list(rgName).getVirtualMachines();
   for (VirtualMachine vm : vms) {
      System.out.println("    VM: " + vm.getName());
      // list all nics
      ArrayList<NetworkInterfaceReference> nics = vm.getNetworkProfile().getNetworkInterfaces();
      for (NetworkInterfaceReference nicReference : nics) {
         String[] nicURI = nicReference.getReferenceUri().split("/");
         NetworkInterface nic = networkResourceProviderClient.getNetworkInterfacesOperations().get(rgName, nicURI[nicURI.length - 1]).getNetworkInterface();
         System.out.println("        NIC: " + nic.getName());
         System.out.println("        Is primary: " + nic.isPrimary());
         ArrayList<NetworkInterfaceIpConfiguration> ips = nic.getIpConfigurations();

         // find public ip address
         for (NetworkInterfaceIpConfiguration ipConfiguration : ips) {
               System.out.println("        Private IP address: " + ipConfiguration.getPrivateIpAddress());
               String[] pipID = ipConfiguration.getPublicIpAddress().getId().split("/");
               PublicIpAddress pip = networkResourceProviderClient.getPublicIpAddressesOperations().get(rgName, pipID[pipID.length - 1]).getPublicIpAddress();
               System.out.println("        Public IP address: " + pip.getIpAddress());
         }
      }
}  
```

Mais exemplos podem ser encontrados nos pacotes de exemplos em [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Ajuda e Leitura Adicional
SDK do Azure para documentação do Java: [documentos do Java](http://azure.github.io/azure-sdk-for-java/)

Caso encontre quaisquer bugs com o SDK, registre um problema em [Problemas](https://github.com/Azure/azure-sdk-for-java/issues) ou confira [StackOverflow no SDK do Azure para Java](http://stackoverflow.com/questions/tagged/azure-java-sdk).

<!---HONumber=AcomDC_0323_2016-->