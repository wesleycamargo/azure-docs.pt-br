---
title: Usando perfis de versão de API com o Java no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com o Java no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/28/2018
ms.openlocfilehash: eef9e45d71dd5a8c29112f74deaf8342dc0d1406
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101492"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Usar perfis de versão da API Java no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O SDK do Java para o Azure Stack Resource Manager fornece ferramentas para ajudá-lo a criar e gerenciar sua infraestrutura. Provedores de recursos no SDK incluem computação, rede, armazenamento, serviços de aplicativos, e [KeyVault](../../key-vault/key-vault-whatis.md). O SDK do Java incorpora perfis da API, incluindo as dependências no arquivo POM. XML que carrega os módulos corretos no arquivo. Java. No entanto, você pode adicionar vários perfis como dependências, como o **2018-03-01-hybrid**, ou **mais recente** como o perfil do Azure. Usar essas dependências carrega o módulo correto para que quando você cria o tipo de recurso, você pode selecionar qual versão de API desses perfis que você deseja usar. Isso permite que você use as versões mais recentes no Azure, enquanto desenvolve contra as mais recentes versões de API para o Azure Stack. Usar o SDK do Java permite uma experiência de desenvolvedor de nuvem híbrida real. Os perfis de API no SDK do Java permitem o desenvolvimento em nuvem híbrida, ajudando você a alternar entre os recursos globais do Azure e recursos no Azure Stack.

## <a name="java-and-api-version-profiles"></a>Perfis de versão da API e Java

Um perfil de API é uma combinação de provedores de recursos e as versões de API. Você pode usar um perfil de API para obter a versão mais recente e mais estável de cada tipo de recurso em um pacote de provedor de recursos.

- Para usar as versões mais recentes de todos os serviços, use o **mais recente** perfil como a dependência.
    
   - Para usar o perfil mais recente, a dependência é **com.microsoft.azure**.

   - Para usar os serviços compatíveis com o Azure Stack, use o **com.microsoft.azure.profile\_2018\_03\_01\_híbrida** perfil.
    
      - Isso é para ser especificado no arquivo POM. XML como uma dependência, que carrega os módulos automaticamente se você escolher a classe correta na lista suspensa, como você faria com o .NET.
        
      - A parte superior de cada módulo aparece da seguinte maneira:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Dependências aparecem da seguinte maneira:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Para usar versões de API específicas para um tipo de recurso em um provedor de recursos específico, use as versões de API específicas definidas por meio do intellisense.

Observe que você pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-java-sdk"></a>Instalar o Java do Azure SDK

Use as etapas a seguir para instalar o SDK do Java:

1. Siga as instruções oficiais para instalar o Git. Para obter instruções, consulte [Introdução - instalação do Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Siga as instruções oficiais para instalar o [SDK do Java](https://zulu.org/download/) e [Maven](https://maven.apache.org/). A versão correta é a versão 8 do Kit de desenvolvedor de Java. O Apache Maven correto é a versão 3.0 ou superior. A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do Java Development Kit para concluir o guia de início rápido. Para obter mais informações, consulte [criar sua primeira função com Java e Maven](../../azure-functions/functions-create-first-java-maven.md).

3. Para instalar os pacotes de dependência correto, abra o arquivo POM. XML em seu aplicativo Java. Adicione uma dependência, conforme mostrado no código a seguir:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Os pacotes que precisam ser instaladas depende da versão de perfil que você deseja usar. Os nomes de pacote para as versões de perfil são:
    
   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. Se não estiver disponível, crie uma assinatura e salve a ID da assinatura para uso posterior. Para obter instruções sobre como criar uma assinatura, consulte [criar assinaturas de ofertas no Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6. Crie uma entidade de serviço e salve a ID do cliente e o segredo do cliente. Para obter instruções sobre como criar uma entidade de serviço para o Azure Stack, consulte [fornecer acesso a aplicativos para o Azure Stack](../azure-stack-create-service-principals.md). Observe que a ID do cliente é também conhecido como a ID do aplicativo durante a criação de uma entidade de serviço.

7. Verifique se que a entidade de serviço tem a função de proprietário/Colaborador em sua assinatura. Para obter instruções sobre como atribuir uma função à entidade de serviço, consulte [fornecer acesso a aplicativos para o Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o SDK de Java do Azure com o Azure Stack, você deve fornecer os seguintes valores e, em seguida, definir valores com variáveis de ambiente. Para definir as variáveis de ambiente, consulte as instruções seguintes à tabela para seu sistema operacional.

| Valor                     | Variáveis de ambiente | DESCRIÇÃO                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID do locatário                 | AZURE_TENANT_ID            | O valor do Azure Stack [ <span class="underline">ID do locatário</span>](../azure-stack-identity-overview.md).                                                          |
| ID do cliente                 | AZURE_CLIENT_ID             | O serviço de ID do aplicativo principal salvo quando a entidade de serviço foi criada na seção anterior deste documento.                                                                                              |
| ID da assinatura           | AZURE_SUBSCRIPTION_ID      | O [ <span class="underline">ID da assinatura</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) é como você pode acessar ofertas no Azure Stack.                |
| Segredo do cliente             | AZURE_CLIENT_SECRET        | O aplicativo de serviço principal o segredo salvo quando a entidade de serviço foi criada.                                                                                                                                   |
| Ponto de extremidade do Gerenciador de recursos | ARM_ENDPOINT              | Ver [ <span class="underline">o ponto de extremidade do Gerenciador de recursos do Azure Stack</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Local padrão                  | RESOURCE_LOCATION    | Local para o Azure Stack                                                                                                                                                                                                |

Para localizar a ID do locatário para o Azure Stack, siga as instruções encontradas [aqui](../azure-stack-csp-ref-operations.md). Para definir as variáveis de ambiente, faça o seguinte:

### <a name="microsoft-windows"></a>Microsoft Windows

Para definir as variáveis de ambiente em um prompt de comando do Windows, use o seguinte formato:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>sistemas baseados em Unix, Linux e macOS

Em sistemas baseados em Unix, você pode usar o comando a seguir:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar no certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando o ASDK, você precisará confiar no certificado de raiz da autoridade de certificação no computador remoto. Você não precisará fazer isso com os sistemas integrados.

#### <a name="windows"></a> Windows

1. Exportar o certificado autoassinado de pilha do Azure para sua área de trabalho

1. Em um shell cmd, altere o diretório para %JAVA_HOME%\bin

1. Execute este comando:

```shell
      .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de extremidade do Gerenciador de recursos do Azure Stack

O Gerenciador de recursos do Microsoft Azure é uma estrutura de gerenciamento que permite aos administradores implantar, gerenciar e monitorar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, em uma única operação.

Você pode obter as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código.

Observe as seguintes considerações:

- O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: https://management.local.azurestack.external/

- O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`

Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Arquivo JSON de exemplo:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Perfis de API existentes

1.  **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Perfil mais recente criado para o Azure Stack. Use esse perfil para os serviços sejam mais compatíveis com o Azure Stack como são 1808 carimbo ou adicional.

2.  **com.microsoft.azure**: Consiste nas versões mais recentes de todos os serviços de perfil. Use as versões mais recentes de todos os serviços.

Para obter mais informações sobre perfis de API e o Azure Stack, consulte o [perfis de resumo da API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Uso de perfil de API do SDK Java do Azure

O código a seguir autentica a entidade de serviço no Azure Stack. Ele cria um token pelo locatário ID e a base de autenticação, que é específica para o Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Isso permite que você use as dependências de perfil de API para implantar seu aplicativo com êxito para o Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definir funções de configuração do ambiente do Azure Stack

Para registrar a nuvem do Azure Stack com os pontos de extremidade corretos, use o seguinte código:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

O `getActiveDirectorySettings` chamada no código a seguir recupera os pontos de extremidade dos pontos de extremidade de metadados. Ele declara as variáveis de ambiente da chamada feita:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Exemplos de uso de perfis de API

Você pode usar os seguintes exemplos do GitHub como referências para a criação de soluções com perfis do .NET e a API do Azure Stack:

  - [Gerenciar grupos de recursos](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

  - [Gerenciar contas de armazenamento](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

  - [Gerenciar uma máquina Virtual](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Projeto de teste de unidade de exemplo 

1. Clone o repositório usando o seguinte comando:
    
   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Criar uma entidade de serviço e atribuir uma função para acessar a assinatura. Para obter instruções sobre como criar uma entidade de serviço, consulte [usar o Azure PowerShell para criar uma entidade de serviço com um certificado](../azure-stack-create-service-principals.md).

3. Recupere os valores de variáveis de ambiente necessárias a seguir:
    
   -  AZURE_TENANT_ID
   -  AZURE_CLIENT_ID
   -  AZURE_CLIENT_SECRET
   -  AZURE_SUBSCRIPTION_ID
   -  ARM_ENDPOINT
   -  RESOURCE_LOCATION

4. Defina as seguintes variáveis de ambiente usando as informações que você recuperou da entidade de serviço criado usando o prompt de comando:
    
   - Exportar AZURE_TENANT_ID = {sua ID de locatário}
   - Exportar AZURE_CLIENT_ID = {sua ID de cliente}
   - Exportar AZURE_CLIENT_SECRET = {seu segredo de cliente}
   - Exportar AZURE_SUBSCRIPTION_ID = {sua ID da assinatura}
   - Exportar ARM_ENDPOINT = {sua URL do Gerenciador de recursos de pilha do Azure}
   - Exportar RESOURCE_LOCATION = {local do Azure Stack}

   No Windows, use **definir** em vez de **exportar**.

5. Use o `getactivedirectorysettings` código para recuperar o ponto de extremidade de metadados do arm e usar o cliente HTTP para definir as informações de ponto de extremidade.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6. No arquivo POM. XML, adicione a dependência a seguir para usar o perfil de 2018-03-01-híbrido para o Azure Stack. Essa dependência instalará os módulos associados a esse perfil para os provedores de recursos de computação, rede, armazenamento, serviços de aplicativo e KeyVault.
      
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8. No prompt de comando que foi aberto para definir as variáveis de ambiente, digite a seguinte linha:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre perfis de API, consulte:

- [Gerenciar perfis de versão de API no Azure Stack](azure-stack-version-profiles.md)
- [Versões de API do provedor de recursos com suporte por perfis](azure-stack-profiles-azure-resource-manager-versions.md)
