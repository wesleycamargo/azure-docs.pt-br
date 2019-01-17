---
title: Usando perfis de versão de API com o Ruby no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com o Ruby no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: aafeeab50a60116ac93cbfa8acb0375224453b03
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353984"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Use perfis de versão de API com o Ruby no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Perfis de versão da API e Ruby

O SDK do Ruby do Azure Stack Resource Manager fornece ferramentas para ajudá-lo a criar e gerenciar sua infraestrutura. Provedores de recursos no SDK incluem computação, redes virtuais e armazenamento com a linguagem Ruby. Os perfis de API no SDK do Ruby permitem o desenvolvimento em nuvem híbrida, ajudando você a alternar entre os recursos globais do Azure e recursos no Azure Stack.

Um perfil de API é uma combinação de provedores de recursos e as versões de serviço. Você pode usar um perfil de API para combinar diferentes tipos de recursos.

- Para usar as versões mais recentes de todos os serviços, use o **mais recente** perfil da gem de pacote cumulativo de atualizações do SDK do Azure.
- Para usar os serviços compatíveis com o Azure Stack, use o **V2018_03_01** perfil da gem de pacote cumulativo de atualizações do SDK do Azure.
- Para usar a versão mais recente **api-version** de um serviço, use o **mais recente** perfil da gem específico. Por exemplo, se você gostaria de usar a versão mais recente **api-version** do serviço de computação sozinho, use o **mais recente** perfil dos **computação** gem.
- Para usar um determinado **versão da api** para um serviço, use as versões de API específicas definidas dentro do gem.

> [!NOTE]
> Você pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-ruby-sdk"></a>Instalar o SDK do Ruby do Azure

- Siga as instruções oficiais para instalar [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Siga as instruções oficiais para instalar [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Durante a instalação, escolha **Ruby adicionar à variável PATH**.
  - Instale o kit de desenvolvimento durante a instalação do Ruby quando solicitado.
  - Em seguida, instale o bundler usando o seguinte comando:  
    `Gem install bundler`
- Se não estiver disponível, crie uma assinatura e salve a ID da assinatura a ser usado posteriormente. As instruções para criar uma assinatura estão [aqui](../azure-stack-subscribe-plan-provision-vm.md).
- Crie uma entidade de serviço e salve sua ID e o segredo. As instruções para criar uma entidade de serviço para o Azure Stack estão [aqui](../azure-stack-create-service-principals.md).
- Certifique-se de que sua entidade de serviço tem a função de proprietário/Colaborador em sua assinatura. As instruções sobre como atribuir a função à entidade de serviço estão [aqui](../azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Instalar os pacotes de Rubygem

Você pode instalar os pacotes do Azure Rubygem diretamente.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

O SDK do Ruby do Azure Resource Manager está em visualização e provavelmente terá alterações da interface em versões futuras. Um número maior na versão secundária pode indicar alterações significativas.

## <a name="use-the-azuresdk-gem"></a>Use o gem azure_sdk

O gem **azure_sdk**, é um rollup de todas as gems com suporte no SDK do Ruby. Vejam essa pérola consiste em uma **mais recente** perfil, que oferece suporte a versão mais recente de todos os serviços. Ele apresenta o perfil de controle de versão duas **V2017_03_09** e **V2018_03_01** perfis, que são criados para o Azure Stack.

Você pode instalar a gem azure_sdk pacote cumulativo de atualizações com o seguinte comando:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Pré-requisitos

Para usar o SDK do Ruby do Azure com o Azure Stack, você deve fornecer os seguintes valores e, em seguida, defina os valores com variáveis de ambiente. Consulte as instruções após a tabela para seu sistema operacional sobre como definir as variáveis de ambiente.

| Valor | Variáveis de ambiente | DESCRIÇÃO |
| --- | --- | --- | --- |
| ID do locatário | AZURE_TENANT_ID | O valor do Azure Stack [ID do locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID do cliente | AZURE_CLIENT_ID | O serviço de ID do aplicativo principal salvo quando a entidade de serviço foi criada na seção anterior deste documento.  |
| ID da assinatura | AZURE_SUBSCRIPTION_ID | O [ID da assinatura](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como você pode acessar ofertas no Azure Stack. |
| Segredo do cliente | AZURE_CLIENT_SECRET | O aplicativo de serviço principal o segredo salvo quando a entidade de serviço foi criada. |
| Ponto de extremidade do Gerenciador de recursos | ARM_ENDPOINT | Ver [ponto de extremidade de Gerenciador de recursos do Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de extremidade do Gerenciador de recursos do Azure Stack

O Gerenciador de recursos do Microsoft Azure é uma estrutura de gerenciamento que permite aos administradores implantar, gerenciar e monitorar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, em uma única operação.

Você pode obter as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código.

 > [!NOTE]  
 > O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/` O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Arquivo JSON de exemplo:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
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
Para definir as variáveis de ambiente em um prompt de comando do Windows, use o seguinte formato:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux e sistemas baseados em Unix** sistemas com base em Unix, você pode usar o comando a seguir:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Perfis de API existentes

Gem Azure_sdk rollup tem os seguintes três perfis:

1. **V2018_03_01** perfil criado para o Azure Stack. Use esse perfil para utilizar todas as versões mais recentes dos serviços disponíveis no Azure Stack.
2. **V2017_03_09**  
  Perfil criado para o Azure Stack. Use esse perfil para os serviços sejam mais compatíveis com o Azure Stack.
3. **Mais recente**  
  Perfil consiste em versões mais recentes de todos os serviços. Use as versões mais recentes de todos os serviços.

Para obter mais informações sobre perfis de API e o Azure Stack, consulte o [perfis de resumo da API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Uso de perfil de API do SDK do Ruby do Azure

Você pode usar o seguinte código para instanciar um cliente de perfil. Esse parâmetro só é necessário para o Azure Stack ou em outras nuvens privadas. Global Azure já tem essas configurações por padrão.

```Ruby  
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
client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
```

O cliente de perfil pode ser usado para acessar os provedores de recursos individuais, como computação, armazenamento e rede:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2018_03_01::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definir funções de configuração do ambiente do Azure Stack

Para autenticar a entidade de serviço para o ambiente do Azure Stack, definir pontos de extremidade usando `get_active_directory_settings()`. Esse método usa o **ARM_Endpoint** variável de ambiente que você definir durante o estabelecimento de suas variáveis de ambiente:

```Ruby  
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
```

## <a name="samples-using-api-profiles"></a>Exemplos de uso de perfis de API

Você pode usar os exemplos a seguir encontrados no GitHub como referências para a criação de soluções com perfis de Ruby e API do Azure Stack:

- [Gerenciar recursos e grupos de recursos do Azure com Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Gerenciar máquinas virtuais usando o Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)
- [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Exemplo de Gerenciador de recursos e grupos

Para executar o exemplo, verifique se você instalou o Ruby. Se você estiver usando o Visual Studio Code, baixe a extensão do SDK do Ruby.

> [!NOTE]  
> Você pode obter o repositório para o exemplo em "[gerenciar recursos do Azure e grupos de recursos com o Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)".

1. Clone o repositório:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Instale as dependências usando o pacote:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Criar uma entidade de serviço usando o PowerShell e recuperar os valores necessários.

   Para obter instruções sobre como criar uma entidade de serviço, consulte [usar o Azure PowerShell para criar uma entidade de serviço com um certificado](../azure-stack-create-service-principals.md).

   Valores necessários são:
   - ID do locatário
   - ID do cliente
   - Segredo do cliente
   - ID da assinatura
   - Ponto de extremidade do Gerenciador de recursos

   Defina as seguintes variáveis de ambiente usando as informações que você recuperou da entidade de serviço criada por você.

   - Exportar AZURE_TENANT_ID = {sua id de locatário}
   - Exportar AZURE_CLIENT_ID = {sua id de cliente}
   - Exportar AZURE_CLIENT_SECRET = {seu segredo de cliente}
   - Exportar AZURE_SUBSCRIPTION_ID = {sua id da assinatura}
   - Exportar ARM_ENDPOINT = {sua url do Gerenciador de recursos de AzureStack}

   > [!NOTE]  
   > No Windows, use o conjunto em vez de exportação.

4. Verifique se que a variável local é definida como seu local do Azure Stack; Por exemplo, `LOCAL="local"`.

5. Adicione a seguinte linha de código se estiver usando o Azure Stack ou em outras nuvens privadas para direcionar os pontos de extremidade do Active Directory à direita:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Na variável de opções, adicione as configurações do Active Directory e a URL base para trabalhar com o Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Crie um cliente de perfil que tem como alvo o perfil do Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2018_03_01::Mgmt::Client.new(options)
   ```

8. Para autenticar a entidade de serviço com o Azure Stack, os pontos de extremidade devem ser definidos usando **get_active_directory_settings()**. Esse método usa o **ARM_Endpoint** variável de ambiente que você definir durante o estabelecimento de suas variáveis de ambiente:

   ```ruby  
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
   ```

9. Execute o exemplo.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Próximas etapas

- [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
- [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md)  
