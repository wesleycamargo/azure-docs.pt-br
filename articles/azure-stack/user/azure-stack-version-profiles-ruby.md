---
title: Usando perfis de versão de API com Ruby na pilha do Azure | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com Ruby na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011166"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Use perfis de versão de API com Ruby na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="ruby-and-api-version-profiles"></a>Perfis de versão de API e Ruby

O SDK do Ruby para o Gerenciador de recursos do Azure pilha fornece ferramentas para ajudá-lo a criar e gerenciar sua infra-estrutura. Provedores de recursos no SDK incluem computação, redes virtuais e armazenamento com a linguagem Ruby. Perfis de API no SDK do Ruby permitem o desenvolvimento em nuvem híbrida, ajudando a alternar entre os recursos globais do Azure e os recursos na pilha do Azure.

Um perfil de API é uma combinação de provedores de recursos e as versões de serviço. Você pode usar um perfil de API para combinar diferentes tipos de recursos.

 - Para usar as versões mais recentes de todos os serviços, use o **mais recente** perfil da acumulação gem SDK do Azure.
 - Para usar os serviços compatíveis com a pilha do Azure, use o **V2017_03_09** perfil da acumulação gem SDK do Azure.
 - Para usar a versão de api mais recente de um serviço, use o **mais recente** perfil da gem específico. Por exemplo, se você deseja usar a versão de api mais recente do serviço de computação sozinho, use o **mais recente** perfil do **de computação** gem.
 - Para usar a versão específica de api para um serviço, use as versões de API específicas definidas dentro do gem.

> [!Note]   
> Você pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-ruby-sdk"></a>Instale o Azure SDK Ruby

 - Siga as instruções oficiais para instalar [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Siga as instruções oficiais para instalar [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Durante a instalação, escolha **Ruby adicionar a variável de caminho**
    - Instale o kit de desenvolvimento durante a instalação do Ruby quando solicitado.
    - Em seguida, instale o empacotador usando o seguinte comando:  
      `Gem install bundler`
 - Se não estiver disponível, crie uma assinatura e salve a ID de assinatura a ser usado posteriormente. Instruções para criar uma assinatura são [aqui](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Crie uma entidade de serviço e salve sua ID e o segredo. Instruções para criar uma entidade de serviço para a pilha do Azure são [aqui](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Verifique se que a entidade de serviço tem a função de Colaborador/proprietário em sua assinatura. As instruções sobre como atribuir funções a entidade de serviço estão [aqui](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Instalar os pacotes de rubygem

Você pode instalar os pacotes de rubygem do azure diretamente.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Lembre-se de que o SDK do Azure Resource Manager Ruby está em visualização e provavelmente terão últimas alterações de interface em versões futuras. Um maior número de versão secundária pode indicar que as alterações recentes.

## <a name="usage-of-the-azuresdk-gem"></a>Uso da gem azure_sdk

Gem, azure_sdk, é um pacote cumulativo de todas as gems com suporte no SDK do Ruby. Esta gem consiste em uma **mais recente** perfil, que oferece suporte para a versão mais recente de todos os serviços. Ele apresenta um perfil com versão **V2017_03_09** perfil, que é criado para a pilha do Azure.

Você pode instalar o gem de rollup azure_sdk com o seguinte comando:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Pré-requisito

Para usar o SDK do Azure Ruby com pilha do Azure, você deve fornecer os seguintes valores e, em seguida, definir valores de variáveis de ambiente. Consulte as instruções após a tabela para seu sistema operacional sobre como configurar as variáveis de ambiente. 

| Valor | Variáveis de ambiente | DESCRIÇÃO | 
| --- | --- | --- | --- |
| ID do locatário | AZURE_TENANT_ID | O valor da pilha do Azure [ID do locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID do cliente | AZURE_CLIENT_ID | O serviço de ID do aplicativo principal salvo quando a entidade de serviço foi criada na seção anterior deste documento.  |
| ID da assinatura | AZURE_SUBSCRIPTION_ID | O [ID da assinatura](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como você acessa ofertas na pilha do Azure. |
| Segredo do cliente | AZURE_CLIENT_SECRET | O aplicativo de serviço principal segredo salvo quando a entidade de serviço foi criada. |
| Ponto de extremidade do Gerenciador de recursos | ARM_ENDPOINT | Consulte [a pilha do Azure resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de extremidade do Gerenciador de recursos do Azure pilha

O Gerenciador de recursos do Microsoft Azure é uma estrutura de gerenciamento que permite aos administradores implantar, gerenciar e monitorar recursos do Azure. Gerenciador de recursos do Azure pode lidar com essas tarefas, como um grupo, em vez de individualmente, em uma única operação.

Você pode obter as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código.

  > [!Note]  
  > O **ResourceManagerUrl** no Kit de desenvolvimento de pilha para Azure (ASDK) é: `https://management.local.azurestack.external/`  
  > O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Arquivo JSON de exemplo:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>Conjunto de variáveis de ambiente

**Microsoft Windows**  
Para definir as variáveis de ambiente, no Prompt de comando do Windows, use o seguinte formato:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**sistemas baseados em Unix, Linux e macOS**  
Em sistemas baseados em Unix, você pode usar o comando como:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Perfis de API existentes

Gem azure_sdk rollup tem os seguintes dois perfis:

1. **V2017_03_09**  
  Perfil criado para a pilha do Azure. Use esse perfil para os serviços para serem mais compatíveis com a pilha do Azure.
2. **Mais recente**  
  Perfil consiste em versões mais recentes de todos os serviços. Use as versões mais recentes de todos os serviços.

Para obter mais informações sobre perfis de pilha do Azure e API, consulte um [perfis de resumo da API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Uso de perfil Ruby API do SDK do Azure

As linhas a seguir devem ser usadas para instanciar um cliente de perfil. Esse parâmetro só é necessário para a pilha do Azure ou outras nuvens privadas. Azure global já possui as seguintes configurações por padrão.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

O cliente de perfil pode ser usado para acessar os provedores de recursos individuais, como computação, armazenamento e rede.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definir funções de configuração do ambiente AzureStack

Para autenticar a entidade de serviço para o ambiente de pilha do Azure, definir os pontos de extremidade usando **get_active_directory_settings()**. Esse método usa o **ARM_Endpoint** variável de ambiente que você definir durante o estabelecimento de variáveis de ambiente.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Exemplos de uso de perfis de API

Você pode usar os exemplos a seguir encontrado no GitHub repositoreis como uma referência de criação de soluções com perfis Ruby e API de pilha do Azure:

 - [Gerenciar recursos e grupos de recursos do Azure com Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Gerenciar máquinas virtuais usando o Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Gerenciador de recursos de exemplo e grupos

Para executar o exemplo, certifique-se de que você tenha instalado o Ruby. Se você estiver usando o código do Visual Studio, baixe o SDK do Ruby como também uma extensão. 

> [!Note]  
> Você pode obter o repositório para o exemplo em "[gerenciar recursos do Azure e grupos de recursos com Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Clone o repositório.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Instale as dependências usando o pacote.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Criar uma entidade de serviço do Azure usando o PowerShell e recuperar os valores necessários. 

  Para obter instruções sobre como criar uma entidade de serviço, consulte [usar o Azure PowerShell para criar uma entidade de serviço com um certificado](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Valores necessários são:
  - ID do locatário
  - ID do cliente
  - Segredo do cliente
  - ID da assinatura
  - Ponto de extremidade do Gerenciador de recursos

  Defina as seguintes variáveis de ambiente usando as informações recuperadas da entidade de serviço é criado.

  - Exportar AZURE_TENANT_ID = {sua id de locatário}
  - Exportar AZURE_CLIENT_ID = {sua id de cliente}
  - Exportar AZURE_CLIENT_SECRET = {o segredo do cliente}
  - Exportar AZURE_SUBSCRIPTION_ID = {sua id da assinatura}
  - Exportar ARM_ENDPOINT = {sua url do Gerenciador de recursos de AzureStack}

  > [!Note]  
  > No Windows, use o conjunto em vez de exportação.

4. Certifique-se de que a variável local é definida para o local de AzureStack. Por exemplo LOCAL = "local"

5. Adicionar na seguinte linha de código, se estiver usando a pilha do Azure ou outras nuvens privadas para atingir os pontos de extremidade no active directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Dentro da variável de opções, adicione as configurações do active directory e a URL base para trabalhar com a pilha do Azure. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Crie perfil cliente que tem como alvo o perfil de pilha do Azure:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Para autenticar a entidade de serviço com a pilha do Azure, os pontos de extremidade devem ser definidos usando **get_active_directory_settings()**. Esse método usa o **ARM_Endpoint** variável de ambiente que você definir durante o estabelecimento de variáveis de ambiente.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Execute o exemplo.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Próximas etapas

* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)  
