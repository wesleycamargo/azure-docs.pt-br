---
title: Atribuição de uso de parceiros e clientes do Microsoft Azure
description: Visão geral de como acompanhar o uso do cliente para soluções do Microsoft Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: c3690c9be940a69bd2f8745493d4e2648bac6d9b
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42139996"
---
# <a name="azure-partner-customer-usage-attribution"></a>Atribuição de uso do cliente do parceiro do Azure

Como parceiro de software do Azure, suas soluções exigem componentes do Azure ou precisam ser implantadas diretamente na infraestrutura do Azure. Os clientes que implantam uma solução de parceiro e provisionam seus próprios recursos do Azure podem achar difícil obter visibilidade do status da implantação e obter uma ótica do impacto no crescimento do Azure. Quando você adiciona um nível mais alto de visibilidade, você se alinha às equipes de vendas da Microsoft e obtém crédito para os programas de parceiros da Microsoft.   

A Microsoft agora oferece um método para ajudar os parceiros a rastrear melhor o uso do Azure de implantações de clientes de seus softwares no Azure. O novo método usa o Azure Resource Manager para orquestrar a implantação dos serviços do Azure.

Como parceiro da Microsoft, você pode associar o uso do Azure a quaisquer recursos do Azure fornecidos em nome de um cliente. Você pode formar a associação por meio do Azure Marketplace, do repositório do Quickstart, dos repositórios privados do GitHub e do envolvimento individual do cliente. Para habilitar o rastreamento, as duas abordagens estão disponíveis:

- Modelos do Azure Resource Manager: modelos do Gerenciador de Recursos ou modelos de solução para implantar os serviços do Azure para executar o software do parceiro. Os parceiros podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração de sua solução do Azure. Um modelo do Resource Manager permite que você e seus clientes implantem sua solução em todo o seu ciclo de vida. Você pode ter certeza de que seus recursos são implantados em um estado consistente. 

- APIs do Azure Resource Manager: os parceiros podem chamar as APIs do Resource Manager diretamente para implantar um modelo do Resource Manager ou gerar as chamadas da API para provisionar diretamente os serviços do Azure. 

## <a name="use-resource-manager-templates"></a>Use modelos do Gerenciador de Recursos

Muitas soluções de parceiros são implantadas na assinatura de um cliente usando modelos do Resource Manager. Se você tiver um modelo do Resource Manager disponível no Azure Marketplace, no GitHub ou como início rápido, o processo para modificar o modelo para ativar o novo método de rastreamento deverá ser direto. Se você não estiver usando um modelo do Azure Resource Manager, aqui estão alguns links para ajudá-lo a entender melhor os modelos do Resource Manager e como criar um: 

*   [Crie e implante seu primeiro modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Criar um modelo de solução para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>Adicionar um GUID para o modelo

Para adicionar um identificador global exclusivo (GUID), você faz uma única modificação no arquivo de modelo principal:

1. Criar um GUID (por exemplo, eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso no arquivo de modelo principal. O recurso precisa estar no arquivo **mainTemplate.json** ou **azuredeploy.json**, e não em nenhum modelo aninhado ou vinculado.

1. Insira o valor GUID após o **pid -** prefixo (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique o modelo para todos os erros.

1. Republique o modelo nos repositórios apropriados.

### <a name="sample-template-code"></a>Código do modelo de exemplo

![Código do modelo de exemplo](media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG)

## <a name="use-the-resource-manager-apis"></a>Use as APIs do Gerenciador de recursos

Em alguns casos, talvez você prefira fazer chamadas diretamente contra as APIs REST do Resource Manager para implantar os serviços do Azure. [Azure dá suporte a vários SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs, ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Resource Manager, deverá marcar sua solução seguindo as instruções descritas anteriormente. Se você não estiver usando um modelo do Resource Manager e fazendo chamadas diretas à API, ainda poderá marcar sua implantação para associar o uso de recursos do Azure. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implantação com as APIs do Resource Manager

Para essa abordagem de rastreamento, ao criar suas chamadas de API, inclua um GUID no cabeçalho do agente do usuário na solicitação. Adicione o GUID para cada oferta ou SKU. Formatar a cadeia de caracteres com o **pid -** de prefixo e incluir o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente do usuário: 

![Formato GUID de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> O formato da cadeia de caracteres é importante. Se o prefixo **pid-** não estiver incluído, não será possível consultar os dados. Diferentes SDKs acompanham de forma diferente. Para implementar esse método, revise a abordagem de suporte e acompanhamento do seu SDK do Azure preferido. 

### <a name="example-the-python-sdk"></a>Exemplo: O SDK do Python

Para o Python, use o **config** atributo. Você só pode adicionar o atributo a um UserAgent. Aqui está um exemplo:

![Adicione o atributo para um agente do usuário](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Você pode marcar uma fábrica de clientes para ter certeza de que todos os clientes estão rastreando. Para obter mais informações, consulte este [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marca uma implantação usando o Azure PowerShell

Se você implantar recursos por meio do Azure PowerShell, acrescente seu GUID usando o seguinte método:

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marca uma implantação usando a CLI do Azure

Quando você usa a CLI do Azure para anexar seu GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT**. Você pode definir essa variável dentro do escopo de um script. Você também pode definir a variável globalmente para o escopo da shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="register-guids-and-offers"></a>Registrar os GUIDs e ofertas

Para incluir um GUID em nosso rastreamento, o GUID deve ser registrado.  

Todos os registros para GUIDs de modelo são feitos por meio do Portal do Parceiro do Azure Marketplace Cloud (CPP). 

Depois de adicionar o GUID ao seu modelo ou no agente do usuário e registrar o GUID no CPP, todas as implantações são rastreadas. 

1. Aplicar a [do Azure Marketplace](http://aka.ms/listonazuremarketplace) e obter acesso para o CPP.

   * Os parceiros são necessários para [tiver um perfil no CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Você é incentivado a listar a oferta no Azure Marketplace ou no AppSource.
   * Parceiros podem registrar vários GUIDs.
   * Os parceiros podem registrar um GUID para os modelos e ofertas de soluções que não são do Marketplace.
 
1. Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

1. No canto superior direito, selecione o ícone da sua conta e, em seguida, selecione **perfil do editor**.

   ![Selecione o perfil do Editor](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Na **página Perfil**, selecione **Adicionar GUID de rastreamento.**

   ![Selecione Adicionar GUID de rastreamento](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Na caixa **GUID de rastreamento**, insira seu GUID de rastreamento. Insira o GUID sem as **pid -** prefixo. Na caixa **Descrição personalizada**, insira o nome ou a descrição da sua oferta.

   ![Página de perfil](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![Insira o GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Para registrar mais de um GUID, selecione **Adicionar GUID de Rastreamento** novamente. Caixas adicionais aparecerão na página.

   ![Selecione Adicionar GUID de rastreamento novamente](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![Insira outro GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Clique em **Salvar**.

   ![Selecionar Salvar](media/marketplace-publishers-guide/guid-dev-center-save.png)

Depois de adicionar o GUID ao seu modelo ou no agente do usuário e registrar o GUID no CPP, todas as implantações são rastreadas. 

## <a name="verify-the-guid-deployment"></a>Verificar a implantação de GUID 

Depois de modificar seu modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos implantados e marcados. 

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica a implantação da API do Gerenciador de recursos.

Entre no Azure. Selecione a assinatura com a implantação que você deseja verificar antes de executar o script. Execute o script dentro do contexto de assinatura da implantação.

O **GUIDE** e o **grupo de recursos** nome da implantação são parâmetros obrigatórios.

Você pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um número de referência exclusivo que tem 32 dígitos hexadecimais. Para criar GUIDs para rastreamento, você deve usar um gerador de GUID. Existem vários [geradores GUID on-line](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) que você pode usar.

Crie um GUID exclusivo para cada canal de distribuição e a oferta. Se você implantar duas soluções usando um modelo e cada uma delas estiver disponível no Azure Marketplace e no GitHub, será necessário criar quatro GUIDS:

*   Oferta A no Microsoft Azure Marketplace 
*   Oferta B no GitHub
*   Oferta B no Microsoft Azure Marketplace 
*   Oferta B no GitHub

Emissão de relatórios é feita, o valor de parceiro (ID de parceiro da Microsoft) e o GUID. 

Você também pode rastrear GUIDs em um nível mais granular, como o SKU, em que as SKUs são variantes de uma oferta.

## <a name="notify-your-customers"></a>Notificar os clientes

Os parceiros devem informar seus clientes sobre implantações que usam o rastreamento GUID do Resource Manager. A Microsoft relata o uso do Azure associado a essas implantações no parceiro. Os exemplos a seguir incluem conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<parceiro > com o nome da sua empresa. Os parceiros devem certificar-se de que a notificação esteja alinhada com suas políticas de privacidade e coleta de dados, incluindo opções para os clientes serem excluídos do rastreamento. 

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelos do Resource Manager

Quando você implanta esse modelo, a Microsoft consegue identificar a instalação do software \<PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou a API

Quando você implanta o software \<PARTNER>, a Microsoft pode identificar a instalação do software \< PARTNER> com os recursos do Azure implantados. Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Os dados são coletados e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Obtenha suporte

Se você precisar de assistência, siga estas etapas.

1. Vá para a [página de suporte](https://go.microsoft.com/fwlink/?linkid=844975). 

1. Em **Tipo de problema**, selecione **Onboarding do Marketplace**.

1. Escolha o **categoria** para seu problema:

   - Para problemas de uso de associação, selecione **outros**.
   - Para problemas de acesso com o Azure Marketplace CPP, selecione **problema de acesso**.
   
    ![Escolha a categoria do problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Iniciar solicitação**.

1. Na próxima página, insira os valores necessários. Selecione **Continuar**.

1. Na próxima página, insira os valores necessários.

   > [!Important] 
   > Na caixa **Título do incidente**, insira **Acompanhamento de uso do ISV**. Descreva seu problema em detalhes.
   
   ![Insira o controle de uso do ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e selecione **Enviar**.

## <a name="faq"></a>Perguntas frequentes

**Qual é o benefício de adicionar o GUIA ao modelo?**

A Microsoft fornece aos parceiros uma visão das implantações de clientes de seus modelos e insights sobre seu uso influenciado. Tanto a Microsoft quanto o parceiro podem usar essas informações para promover um envolvimento mais próximo entre as equipes de vendas. Tanto a Microsoft quanto o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure. 

**Quem pode adicionar um GUID para um modelo?**

O recurso de rastreamento destina-se a conectar a solução do parceiro ao uso do Azure do cliente. Os dados de uso estão vinculados à identidade do Microsoft Partner Network de um parceiro (ID do MPN). Os relatórios estão disponíveis para parceiros no CPP.

**Depois de um GUID é adicionado, ela pode ser alterada?**
 
Sim, um cliente ou parceiro de implementação pode personalizar o modelo e alterar ou remover o GUID. Sugerimos que os parceiros proativamente descrevem a função do recurso e do GUID a seus clientes e parceiros para evitar a remoção ou edições para o GUID de rastreamento. Alterar o GUID afeta somente implantações e recursos novos e não existentes.

**Quando os relatórios estarão disponível?**

Uma versão beta dos relatórios deve estar disponível em breve. Emissão de relatórios será integrado ao CPP.

**Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo for implantado, o uso será rastreado. Os parceiros devem ter um perfil no CPP para registrar os modelos relacionados que são publicados fora do Azure Marketplace. 

**Há uma diferença se o modelo é implantado no Azure Marketplace em comparação com outros repositórios como o GitHub?**

Sim, os parceiros que publicam ofertas no Azure Marketplace podem receber dados mais detalhados sobre implantações do Azure Marketplace. Os parceiros se beneficiam da exposição de sua oferta aos clientes no portal do Azure Marketplace e no portal do Azure. Ofertas no Azure Marketplace também geram clientes potenciais para o parceiro.

**E se eu criar um modelo personalizado para um compromisso com o cliente individuais?**

Ainda é bem-vindo para adicionar o GUIA ao modelo. Se você usar um GUID registrado existente, ele será incluído no relatório. Se você criar um novo GUID, precisará registrar o novo GUID para incluí-lo no rastreamento.

**O cliente que recebe a emissão de relatórios também?**

Os clientes podem acompanhar seu uso de recursos individuais ou grupos de recursos definido pelo cliente no portal do Azure.   

**Essa metodologia de acompanhamento é semelhante ao Parceiro Digital de Registro (DPOR)?**

Esse novo método de conexão com a implantação e o uso de solução de um parceiro fornece um mecanismo para vincular a uma solução de parceiro ao uso do Azure. A DPOR destina-se a associar um parceiro de consultoria (Integrador de Sistemas) ou de gerenciamento (Provedor de Serviço Gerenciado) à assinatura do Azure de um cliente.   
