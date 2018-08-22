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
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037871"
---
# <a name="azure-partner-customer-usage-attribution"></a>Atribuição de uso do cliente do parceiro do Azure

Como um parceiro de software para o Microsoft Azure, suas soluções exigem componentes do Azure ou é implantado diretamente na infraestrutura do Azure.  Hoje em dia, quando o cliente implanta uma solução de parceiro e provisiona seus próprios recursos do Microsoft Azure, é difícil para o parceiro obter visibilidade quanto para o status dessas implantações e difícil de ser fibra óptica em impacto para o crescimento do Microsoft Azure. Adicionar um nível mais alto de visibilidade ajuda os parceiros a se alinharem com as equipes de vendas da Microsoft e a obterem crédito para programas de parceria da Microsoft.   

A Microsoft está criando um novo método para ajudar os parceiros a controlar melhor o uso do Microsoft Azure que é um resultado de um cliente de implantação do software no Azure. Esse novo método baseia-se no uso do Azure Resource Manager para orquestrar a implantação de serviços do Azure.

Como um parceiro da Microsoft, você pode associar o uso do Azure com os recursos do Azure, que provisionam em nome do cliente.  Esta associação pode ser feita por meio do Microsoft Azure Marketplace, o repositório de Início Rápido, repositórios do github privado e até mesmo 1 em compromissos de 1 cliente.  Para habilitar o acompanhamento, há duas abordagens disponíveis:

- Modelos do Azure Resource Manager: Modelos do Azure Resource Manager ou modelos de solução para implantar os serviços do Azure para executar o software do parceiro. Os modelos do Azure Resource Manager são arquivos JSON que definem a infraestrutura e a configuração de sua solução do Azure. Ao criar um modelo do Azure Resource Manager, você pode implantar a solução em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente. 

- As APIs do Gerenciador de recursos do Azure: parceiros podem chamar as APIs do Gerenciador de recursos do Azure diretamente para implantar um modelo do Azure Resource Manager ou para gerar a API chama diretamente provisionar serviços do Azure. 

## <a name="method-1-azure-resource-manager-templates"></a>Método 1: Modelos do Azure Resource Manager 

Hoje, muitas soluções de parceiros são implantadas em uma assinatura de cliente usando modelos do Azure Resource Manager.  Se você já tiver um modelo do Azure Resource Manager disponível no Azure Marketplace, no GitHub ou como um guia de início rápido, o processo de modificar o modelo para habilitar esse novo método de rastreamento deve ser muito simples.  Se você não estiver usando um modelo do Azure Resource Manager atualmente, aqui estão alguns links para ajudar você a melhor compreender os modelos do Azure Resource Manager e como criar um: 

*   [Criar e implantar seu primeiro modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Guia para criar um modelo de solução para o Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Instruções: adicionar um GUID para seu modelo do Azure Resource Manager existente

Adicionar o GUID é uma única modificação do arquivo de modelo principal:
 1. Criar um GUID, vamos supor que o valor gerado é eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Abra o modelo do Azure Resource Manager
 3. Adicione um novo recurso no arquivo de modelo principal. O recurso só precisará estar em Maintemplate ou azuredeploy. JSON, não em qualquer modelo aninhado ou vinculado.
 4. Insira o GUID após o "pid-", conforme mostrado acima.

   O resultado deve ser semelhante a esse exemplo: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Modelo de seleção para todos os erros
 6. Republique o modelo nos repositórios apropriados

## <a name="sample-template-code"></a>Código do modelo de exemplo

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Método 2: APIs do Azure Resource Manager

Em alguns casos, você pode preferir fazer chamadas diretamente em relação a REST APIs do Azure Resource Manager para implantar os serviços do Azure. [O Azure dá suporte a vários SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools) para habilitar isso.  Você pode usar um dos SDKs, ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Azure Resource Manager, você deve marcar sua solução usando as instruções acima.  Se você não estiver usando um modelo do Azure Resource Manager e fazendo chamadas diretas de API, você ainda pode marcar sua implantação para associar o uso de recursos do Azure. 

**Como marcar uma implantação usando as APIs do Gerenciador de recursos do Azure:** para essa abordagem, ao projetar suas chamadas de API que você incluirá um GUID no cabeçalho de agente do usuário na solicitação. O GUID deve ser adicionado para cada SKU ou oferta.  A cadeia de caracteres precisa ser formatada com o prefixo pid - e, em seguida, inclua o parceiro gerado GUID.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>O formato GUID para inserção no agente do usuário: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 / / inserir seu GUID após o "pid-"

O formato da cadeia de caracteres é importante. Se o prefixo "pid-" não for incluído, não será possível consultar os dados. Diferentes SDKs para fazer isso de forma diferente.  Para implementar esse método, você precisará examinar o suporte e a abordagem para o SDK do Azure preferencial. 

**Exemplo usando o SDK do Python:** para Python, você precisa usar o atributo "config". Você só pode adicionar a um useragent. Veja um exemplo:

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>Isso precisa ser feito para cada cliente, não há nenhuma configuração estática global (você pode optar por fazer uma fábrica de cliente para certificar-se de que todos os clientes estão fazendo. 
>[Informações de referência adicionais](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Como marcar uma implantação usando o Azure PowerShell ou a CLI do Azure: se você implantar recursos por meio de AzurePowerShell pode acrescentar sua GUID, usando o método a seguir:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Para anexar seu GUID ao usar a CLI do Azure, defina a variável de ambiente AZURE_HTTP_USER_AGENT.  Você pode definir esta variável dentro do escopo de um script ou definir globalmente, para uso de escopo de shell:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Registrando GUIDs/ofertas

Para que o GUID a ser incluído em nosso controle, ele deve ser registrado.  

Todos os registros para GUIDs de modelo serão feitos por meio do Portal do Microsoft Cloud Partner (CPP). 

1. Peça o [Azure Marketplace](http://aka.ms/listonazuremarketplace) hoje e obtenha acesso ao portal de parceiros de nuvem.

 *  Os parceiros precisarão [ter um perfil no CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) e serem incentivados a lista a oferta no Azure Marketplace ou AppSource 
 *  Parceiros poderão registrar vários GUIDs 
 *  Parceiros também poderão registrar um GUID para os modelos/ofertas de solução não é do Marketplace
 
2. Entra no [Portal do Cloud Partner](https://cloudpartner.azure.com/)
3. No canto superior direito do portal, clique no ícone da conta e, em seguida, clique em **Perfil de publicação**

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. Na página Perfil, clique em **Adicionar GUID de Rastreamento.**

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. No campo expandido, insira o GUID de rastreamento (apenas o GUID, sem o prefixo "pid-") no campo **GUID de Rastreamento** e insira o nome ou descrição de oferta no campo **Descrição Personalizada**.

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. Para registrar mais de um GUID, clique em **Adicionar GUID de Rastreamento** novamente. Isso abrirá outro campo expandido. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. Após concluir a edição, clique em **Salvar** para salvar as alterações. 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>Verificação de implantação de GUID 

Depois que você tiver modificado o modelo e executado uma implantação de teste, você pode usar o seguinte script do PowerShell para recuperar os recursos que foram implantados e marcados. 

Você pode usá-lo para verificar se o GUID foi adicionado ao seu modelo do Azure Resource Manager com êxito. Não se aplica à implantação da API do Azure Resource Manager.

Faça logon no Azure e selecione a assinatura que contém a implantação que você deseja verificar antes de executar o script. Deve ser executado dentro do contexto de assinatura da implantação.

O nome GUID e o resourceGroup da implantação são os parâmetros necessários.

Você pode encontrar o script original [aqui](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Orientação sobre como criar GUIDs

Um GUID (identificador global exclusivo) é um número de referência exclusivo de 32 dígitos hexadecimais. Para criar um GUID, você deve usar um gerador GUID para criar seus GUIDs para acompanhamento.  Há vários [geradores GUID online](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) você pode usar.

Você será incentivado a criar um GUID exclusivo para cada canal de distribuição e a oferta.  Por exemplo, tiver duas soluções e ambas estiverem implantadas por meio de um modelo e estão disponível no Microsoft Azure Marketplace e no GitHub.  Criar quatro GUIDS:

*   Oferta A no Microsoft Azure Marketplace 
*   Oferta B no GitHub
*   Oferta B no Microsoft Azure Marketplace 
*   Oferta B no GitHub

A emissão de relatórios será feito pelo parceiro (ID de parceiro da Microsoft) e GUID. 

Você também pode optar por rastrear GUIDs em um nível mais granular, ou seja, a SKU (onde SKUs são variantes de uma oferta).

## <a name="guidance-on-privacy-and-data-collection"></a>Orientação sobre privacidade e coleta de dados

Os parceiros devem fornecer mensagens para informar aos clientes que as implantações que incluem o acompanhamento de GUID do Azure Resource Manager permitirá que a Microsoft relate o uso do Azure associado com essas implantações para o parceiro.  Alguns idiomas de exemplo estão abaixo. Onde ele indicar "PARCEIRO", você deve preencher seu próprio nome da empresa. Além disso, os parceiros devem garantir que o idioma se alinha aos seus próprios dados incluindo opções para os clientes a serem excluídos da faixa de políticas de privacidade e coleta: 

**Para implantações do modelo do Azure Resource Manager**

Quando a implantação desse modelo Microsoft for capaz de identificar a instalação de nosso software PARCEIRO com os recursos do Azure implantados.  O Microsoft será capaz de correlacionar os recursos do Azure usados para dar suporte o software.  A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Esses dados serão coletados e controlados por políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter. 

**Para implantações de SDK ou a API**

Ao implantar o software de PARCEIRO, a Microsoft será capaz de identificar a instalação de nosso software PARCEIRO com os recursos do Microsoft Azure implantados.  O Microsoft será capaz de correlacionar os recursos do Azure usados para dar suporte o software.  A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e operar seus negócios. Esses dados serão coletados e controlados por políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

## <a name="support"></a>Suporte

Para obter assistência, siga as etapas a seguir:
 1. Visite a página de suporte localizada em [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Para problemas com a associação do uso – selecione o tipo de problema: **integração do Marketplace** e a Categoria: **Outros** e, em seguida, clique em **Iniciar solicitação.** 

Para questões sobre como acessar o  Portal do Microsoft Cloud Partner – selecione o tipo de problema: **integração do Marketplace** e a categoria: **Problema de acesso** e, em seguida, clique em **Iniciar solicitação.**

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. Preencha os campos obrigatórios na próxima página e clique em **Continuar.**
 4. Preencha os campos de texto livre na próxima página. **Importante**: Preencha o título do Incidente com **"Rastreamento de Uso do ISV"** e descreva detalhadamente problema no campo de texto livre grande.  Preencha o restante do formulário e clique em **Enviar**. 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>Perguntas frequentes

**Qual é o benefício de adicionar o GUID para o modelo?**

A Microsoft fornecerá a parceiros com uma exibição de implantações de clientes de seus modelos e insights sobre seu uso influenciado.  A Microsoft e o parceiro também podem usar essas informações para estimular a participação mais próxima entre as equipes de vendas. A Microsoft e o parceiro podem também usá-lo para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Microsoft Azure. 

**Quem pode adicionar um GUID para um modelo?**

O recurso de rastreamento destina-se para se conectar a solução de parceiro para os clientes de uso do Azure.  Os dados de uso estão vinculados a identidade de um parceiro Microsoft Partner Network (MPN ID) e relatórios estarão disponível para parceiros no Cloud Partner Portal (CPP).  

**Após a adição de um GUID pode ser alterado?**
 
Sim, um parceiro de implementação ou o cliente pode personalizar o modelo e pode alterar ou remover o GUID. Sugerimos que os parceiros proativamente descrevem a função do recurso e do GUID a seus clientes e parceiros para evitar a remoção ou edições para o GUID de rastreamento.  A alteração do GUID afetará apenas implantações novas e não existentes e recursos.

**Quando os relatórios estarão disponível?**

Uma versão beta dos relatórios deve estar disponível em breve.  A emissão de relatórios será integrada ao Portal do Cloud Partner (CPP)

**Posso acompanhar modelos implantados de um repositório não Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo é implantado, o uso será rastreado.  
Os parceiros devem ter um perfil no Portal do Cloud Partner para registrar os modelos relacionados publicados fora do Azure Marketplace. 

**Há uma diferença se o modelo é implantado no Azure Marketplace em comparação com outros repositórios como o GitHub?**

Sim, os parceiros que publicarem ofertas no Microsoft Azure Marketplace podem receber dados mais detalhados sobre implantações do Microsoft Azure Marketplace.  Os parceiros serão beneficiadas por expor sua oferta aos clientes no portal do Microsoft Azure Marketplace e no portal de gerenciamento do Azure. As ofertas do Azure Marketplace também geram clientes potenciais para o parceiro.

**E se eu criar um modelo personalizado para um compromisso com o cliente individuais?**

Você está ainda bem-vindo ao adicionar o GUID para o modelo.  Se você usar um GUID existente que foi registrado, ele será incluído nos relatórios.  Se você criar um novo GUID, você precisará registrar o novo GUID para obtê-lo incluído no rastreamento.

**O cliente que recebe a emissão de relatórios também?**

Os clientes são atualmente capazes de controlar o uso de recursos individuais ou grupos de recursos definidos pelo cliente dentro do portal de gerenciamento do Azure.   

**Essa metodologia de acompanhamento é semelhante ao Parceiro Digital de Registro (DPOR)?**

Esse novo método de conexão à implantação e o uso de solução de um parceiro destina-se a fornecer um mecanismo para vincular a uma solução de parceiro ao uso do Azure.  O DPOR destina-se a associar uma consultoria (integrador de sistemas) ou o parceiro de gerenciamento (provedor de serviço gerenciado) com a assinatura do Azure do cliente.   
