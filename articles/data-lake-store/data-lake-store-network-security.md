---
title: Segurança de rede no Azure Data Lake Storage Gen1 | Microsoft Docs
description: Entenda como o firewall de IP e a integração de rede virtual funcionam no Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 0da5962bc0b48a387ee82a1db36099682e14bca3
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49168024"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1---preview"></a>Integração de rede virtual para o Azure Data Lake Storage Gen1 – Versão prévia

Integração de rede virtual para o Azure Data Lake Storage Gen1 (em versão prévia). A Integração VNet permite que você impeça o acesso não autorizado às contas do Azure Data Lake Storage Gen1 restringindo essas contas a redes virtuais e sub-redes específicas. Agora você pode configurar sua conta do ADLS Gen1 para aceitar somente o tráfego de redes virtuais e sub-redes designadas e bloquear o acesso de outros lugares. Isso ajuda a proteger sua conta do ADLS contra ameaças externas.

A integração VNet para o ADLS Gen1 usa a segurança do ponto de extremidade de serviço de rede virtual entre sua Rede Virtual e o serviço Azure Active Directory para gerar declarações de segurança adicionais no token de acesso. Essas declarações são usadas para autenticar a rede virtual na sua conta do ADLS Gen1 e permitir o acesso.

> [!NOTE]
> Essa tecnologia está em versão prévia e não recomendamos o uso em ambientes de produção.
>
> Não há qualquer custo adicional associado ao uso desses recursos. Sua conta será cobrada pela taxa padrão do ADLS Gen1 ([preços](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)) e todos os serviços do Azure que você usa ([preços](https://azure.microsoft.com/pricing/#product-picker)).

## <a name="scenarios-for-vnet-integration-for-adls-gen1"></a>Cenários de Integração VNET para o ADLS Gen1

A Integração VNet do ADLS Gen1 permite restringir o acesso à conta do ADLS Gen1 para sub-redes e redes virtuais designadas.  Outras VNets/VMs no Azure não terão acesso à conta depois que ela ficar restrita à sub-rede da rede virtual especificada.  Funcionalmente, a Integração VNet do ADLS Gen1 permite o mesmo cenário dos [pontos de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview).  Há algumas diferenças importantes detalhadas nas seções abaixo. 

![Diagrama do cenário para a Integração VNet do ADLS Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> As regras de firewall de IP existentes podem ser usadas, além das regras de VNet para também permitir o acesso a partir de redes locais. 

## <a name="optimal-routing-with-adls-gen1-vnet-integration"></a>Roteamento ideal com a Integração VNet do ADLS Gen1

O principal benefício dos pontos de extremidade de serviço de VNet é o [roteamento ideal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) de sua rede virtual.  Para executar a otimização de mesma rota para contas do ADLS Gen1, use as seguintes [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) a partir de sua rede virtual para sua conta do ADLS Gen1:

- **Endereço IP público do ADLS**: use o endereço IP público para suas contas do ADLS Gen1 de destino.  Identifique os endereços IP da conta do ADLS Gen1 [resolvendo os nomes DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) das contas.  Crie uma entrada separada para cada endereço.

```azurecli
# Create a Route table for your resource group
az network route-table create --resource-group $RgName --name $RouteTableName

# Create Route Table Rules for ADLS Public IP Addresses
# There will be one rule per ADLS Public IP Addresses 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the VNet and apply the newly created Route Table to it
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-vnet"></a>Exfiltração de dados da VNet do cliente

Além de proteger as contas do ADLS no acesso da Rede Virtual, convém também garantir que não haverá exfiltrações para uma conta não autorizada.

Nossa recomendação é usar uma solução de firewall em sua rede virtual para filtrar o tráfego de saída com base na URL da conta de destino e permitir o acesso apenas a contas autorizadas do ADLS Gen1.

Algumas opções disponíveis:
- [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview): você pode [implantar e configurar um Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) em sua rede virtual e proteger o tráfego de saída do ADLS restringindo-a à URL de conta conhecida e autorizada.
- Firewall de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/): se o administrador só autorizar o uso de determinados fornecedores de firewalls comerciais, use uma solução de firewall NVA disponível no Azure Marketplace para executar a mesma função.

> [!NOTE]
> O uso de firewalls no caminho de dados adicionará mais uma etapa ao caminho de dados e poderá ter um impacto no desempenho da rede em relação à troca de dados de ponta a ponta, incluindo a taxa de transferência disponível e a latência da conexão. 

## <a name="limitations"></a>Limitações
1.  Os clusters HDInsight devem ser criados após a adição da versão prévia.  Os clusters criados antes do suporte à Integração VNet do ADLS Gen1 estar disponível deverão ser recriados para dar suporte a esse novo recurso. 
2.  Ao criar um novo cluster HDInsight, a escolha de uma conta do ADLS Gen1 com a Integração VNet habilitada fará com que o processo falhe. Primeiro, desabilite a regra da rede virtual ou **Permita o acesso de todas as redes e serviços** por meio da folha **Firewall e redes virtuais** da conta do ADLS.  Confira a seção [Exceções](##Exceptions) para ver mais informações.
3.  A versão prévia da Integração VNet do ADLS Gen1 não funciona com [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
4.  Os dados de arquivo/pasta em sua conta habilitada para VNet do ADLS Gen1 não podem ser acessados no portal.  Isso inclui o acesso a partir de uma VM que esteja dentro da rede virtual e atividades como o uso do Data Explorer.  As atividades de gerenciamento de conta continuam a funcionar.  Os dados de arquivo/pasta na conta do ADLS habilitada para VNet podem ser acessados por meio de todos os recursos que não estejam no portal: acesso do SDK, scripts do PowerShell, outros serviços do Azure (quando não se originarem no portal), etc. 

## <a name="configuration"></a>Configuração

### <a name="step1-configure-your-vnet-to-use-aad-service-endpoint"></a>Etapa 1: Configurar a rede virtual para usar o ponto de extremidade de serviço do AAD
1.  Vá para o portal do Azure e faça logon em sua conta do Azure. 
2.  [Crie uma nova rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) em sua assinatura ou navegue até uma rede virtual existente.  Atualmente, a rede virtual deve estar na mesma região da conta do ADLS Gen 1. 
3.  Na folha da rede virtual, escolha **Pontos de extremidade de serviço**. 
4.  Clique em **Adicionar** para adicionar um novo ponto de extremidade de serviço.
![Adicionar um ponto de extremidade de serviço de rede virtual](media/data-lake-store-network-security/config-vnet-1.png)
5.  Escolha **Microsoft.AzureActiveDirectory** como o serviço do ponto de extremidade.
![Como escolher o ponto de extremidade de serviço Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)
6.  Escolha as sub-redes para as quais você pretende permitir a conectividade e clique em **Adicionar**.
![Escolher a sub-rede](media/data-lake-store-network-security/config-vnet-3.png)
7.  Pode levar até 15 minutos para o ponto de extremidade de serviço ser adicionado. Depois de adicionado, ele aparecerá na lista. Verifique se ele é exibido e se todos os detalhes estão configurados corretamente. 
![Adição bem-sucedida do ponto de extremidade de serviço](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-vnetsubnet-for-your-adls-gen1-account"></a>Etapa 2: Configurar a VNet/Sub-rede permitida para a conta do ADLS Gen1
1.  Depois de configurar sua rede virtual, [crie uma nova conta do Azure Data Lake Store Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) em sua assinatura ou navegue até uma conta do ADLS Gen1 existente. Atualmente, a conta do ADLS Gen1 deve estar na mesma região da VNET. 
2.  Escolha **Firewall e redes virtuais**.

  > [!NOTE]
  > Se você não vir **Firewall e redes virtuais** nas configurações, saia do portal. Feche o navegador. Limpe o cache do navegador. Reinicie o computador e tente novamente.

  ![Como adicionar uma regra de rede virtual à conta do ADLS](media/data-lake-store-network-security/config-adls-1.png)
3.  Escolha **Redes selecionadas**. 
4.  Clique em **Adicionar rede virtual existente**.
  ![Como adicionar uma rede virtual existente](media/data-lake-store-network-security/config-adls-2.png)
5.  Escolha as redes virtuais e sub-redes que terão conectividade e clique em **Adicionar**.
  ![Como escolher uma rede virtual e sub-redes](media/data-lake-store-network-security/config-adls-3.png)
6.  Verifique se as redes virtuais e sub-redes aparecem corretamente na lista e use a opção de **Salvar**.
  ![Como salvar a nova regra](media/data-lake-store-network-security/config-adls-4.png)

  > [!NOTE]
  > Pode levar até cinco minutos para que as configurações entrem em vigor depois de salvas.

7.  [Opcional] Além de redes virtuais e sub-redes, se você quiser permitir a conectividade de endereços IP específicos, faça isso na seção **Firewall** da mesma página. 

## <a name="exceptions"></a>Exceções
Há duas caixas de seleção na área Exceções na folha **Firewall e redes virtuais** que habilitam a conectividade de um conjunto de serviços e máquinas virtuais no Azure.
![Exceções de firewall e rede virtual](media/data-lake-store-network-security/firewall-exceptions.png)
- A opção **Permitir que todos os serviços do Azure acessem esta conta do Data Lake Store Gen1** permite todos os serviços do Azure, como o Azure Data Factory, os Hubs de Eventos, todas as VMs do Azure, entre outros, se comuniquem com sua conta do ADLS.

- A opção **Permitir que o Azure Data Lake Analytics acesse esta conta do Data Lake Storage Gen1** permite a conectividade do serviço Azure Data Lake Analytics com a conta do ADLS. 

Recomendamos que você mantenha essas exceções desligadas e ligue-as apenas se precisar de conectividade com esses outros serviços fora de sua rede virtual.
