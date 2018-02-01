---
title: Criar um cluster do Service Fabric do Linux no Azure | Microsoft Docs
description: Saiba como implantar um cluster do Service Fabric do Linux em uma rede virtual do Azure existente usando o CLI do Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b09e676a26336d1ef1e744f9e45066c4815fe21
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Implantar um cluster do Linux do Service Fabric em uma rede virtual do Azure
Este tutorial é a primeira parte de uma série. Você aprenderá como implantar um cluster do Service Fabric do Linux em uma [rede virtual do Azure (VNET)](../virtual-network/virtual-networks-overview.md) e [grupo de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) usando o CLI do Azure e um modelo. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos. Para criar um cluster do Windows usando o PowerShell, consulte [Create a secure Windows cluster on Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) (Criar um cluster seguro do Windows no Azure).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o CLI do Azure
> * Criar um cluster seguro do Service Fabric no Azure usando o CLI do Azure
> * Proteger o cluster com um certificado X.509
> * Conectar-se ao cluster usando a CLI do Service Fabric
> * Remover um cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Reduzir ou escalar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar a [CLI do Service Fabric](service-fabric-cli.md)
- Instalar a [CLI 2.0 do Azure](/cli/azure/install-azure-cli)

Os procedimentos a seguir criam um cluster de cinco nós do Service Fabric. Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Principais conceitos
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas. Uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento.

Um tipo de nó define o tamanho, o número e as propriedades de um conjunto de máquinas virtuais no cluster. Cada tipo de nó definido é configurado como um [conjunto de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/), um recurso de computação do Azure usado para implantar e gerenciar conjuntamente um grupo de máquinas virtuais. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. Os tipos de nós são usados na definição de funções para um conjunto de nós de cluster, como "front-end" ou "back-end".  Seu cluster pode ter mais de um tipo de nó, mas o tipo de nó primário deve ter pelo menos cinco VMs para clusters de produção (ou pelo menos três VMs para clusters de teste).  [Os serviços de sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados nos nós do tipo primário.

O cluster é protegido por um certificado de cluster. Um certificado de cluster é um certificado x.509 usado para proteger a comunicação de nó para nó e autenticar os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento.  Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer por HTTPS. Certificados autoassinados são somente para clusters de teste.  Para clusters de produção, use um certificado de uma autoridade de certificação (CA) como o certificado de cluster.

O certificado de cluster deve:

- conter uma chave privada.
- ser criado para troca de chaves, que deve ser exportável para um arquivo Troca de Informações Pessoais (.pfx).
- ter um nome de entidade que corresponda ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC (autoridade de certificação) para o domínio cloudapp.azure.com. Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

O Cofre de Chaves do Azure é usado para gerenciar certificados para clusters do Service Fabric no Azure.  Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Cofre de Chaves e os instala nas VMs do cluster.

Este tutorial implanta um cluster com cinco nós em um único tipo de nó. No entanto, em qualquer implantação de cluster de produção, o [planejamento da capacidade](service-fabric-cluster-capacity.md) é uma etapa importante. Aqui estão alguns pontos a serem considerados como parte desse processo.

- O número de nós e tipos de nó que o seu cluster precisa 
- As propriedades de cada tipo de nó (por exemplo, tamanho, primário, voltado para a Internet e número de VMs)
- As características de confiabilidade e durabilidade do cluster

## <a name="download-and-explore-the-template"></a>Baixar e explorar o modelo
Baixe os seguintes arquivos do modelo do Resource Manager:
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

O [vnet linuxcluster.json][template] implanta diversos recursos, incluindo os seguintes.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric
Um cluster do Linux é implantado com as seguintes características:
- um tipo de nó único 
- cinco nós no tipo de nó principal (configurável nos parâmetros de modelo)
- Sistema operacional: Ubuntu 16.04 LTS (configurável nos parâmetros de modelo)
- certificado protegidos (configurável nos parâmetros de modelo)
- [Serviço DNS](service-fabric-dnsservice.md) está habilitado
- [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros de modelo)
- [Nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros de modelo)
- ponto de extremidade de conexão de cliente: 19000 (configurável nos parâmetros de modelo)
- ponto de extremidade de gateway HTTP: 19080 (configurável nos parâmetros de modelo)

### <a name="azure-load-balancer"></a>Azure Load Balancer
Um balanceador de carga é implantado e testes e regras de configuração para as seguintes portas:
- ponto de extremidade de conexão do cliente: 19000
- ponto de extremidade de gateway HTTP: 19080 
- porta do aplicativo: 80
- porta do aplicativo: 443

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e grupo de segurança de rede
Os nomes de rede virtual, sub-rede e grupo de segurança de rede são declarados nos parâmetros de modelo.  Espaços de endereço da rede virtual e sub-rede também são declarados nos parâmetros do modelo:
- espaço de endereço da rede virtual: 10.0.0.0/16
- espaço de endereço de sub-rede do Service Fabric: 10.0.2.0/24

As seguintes regras de tráfego de entrada estão habilitadas no grupo de segurança de rede. Você pode alterar os valores de porta, alterando as variáveis de modelo.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB : 445
- Internodecommunication - 1025, 1026, 1027
- Intervalo de portas efêmeras – 49152 a 65534 (necessário um mínimo de 256 portas)
- Portas para o uso do aplicativo: 80 e 443
- Intervalo de portas do aplicativo – 49152 a 65534 (usada para comunicação de serviços e que não são abertas no balanceador de carga)
- Bloquear todas as outras portas

Se forem necessárias outras portas de aplicativo, você precisará ajustar o recurso de Microsoft.Network/loadBalancers e o recurso de Microsoft.Network/networkSecurityGroups para permitir o tráfego de entrada.

## <a name="set-template-parameters"></a>Definir os parâmetros do modelo
O arquivo de parâmetros [vnet cluster.parameters.json][parameters] declara muitos valores usados para implantar o cluster e os recursos associados. Alguns dos parâmetros que você talvez precise modificar para sua implantação:

|Parâmetro|Valor de exemplo|Observações|
|---|---||
|adminUserName|vmadmin| Um nome de usuário de administrador para as VMs do cluster. |
|adminPassword|Password#1234| Uma senha de administrador para as VMs do cluster.|
|clusterName|mysfcluster123| Nome do cluster. |
|location|southcentralus| Localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor da impressão digital do certificado. Por exemplo, "6190390162C988701DB5676EB81083EA608DCCF3". </p>| 
|certificateUrlValue|| <p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha a URL do certificado. Por exemplo, "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio se for criado um certificado auto-assinado ou for fornecido um arquivo de certificado.</p><p>Para usar um certificado existente que foi previamente carregado para um cofre de chaves, preencha o valor do cofre de origem. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implantar a rede virtual e o cluster
Em seguida, configure a topologia de rede e implante o cluster do Service Fabric. O modelo do Resource Manager [vnet-linuxcluster.json][template] cria uma rede virtual (VNET) e também uma sub-rede e um grupo de segurança de rede (NSG) e para o Service Fabric. O modelo também implanta um cluster com a segurança de certificado habilitada.  Para clusters de produção, use um certificado de uma autoridade de certificação (CA) como o certificado de cluster. Um certificado autoassinado pode ser usado para proteger clusters de teste.

O script a seguir usa o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) e o modelo para implantar um novo cluster protegido por um certificado existente. O comando também cria um novo cofre de chaves no Azure e carrega o certificado.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro
Conecte-se ao cluster usando o comando `sfctl cluster select` da CLI do Service Fabric por meio da sua chave.  Observação: use apenas a opção **--no-verify** para um certificado autoassinado.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verifique se você está conectado e se o cluster está íntegro usando o comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos
Os outros artigos nesta série de tutoriais usam o cluster que você acabou de criar. Se você não for imediatamente para o próximo artigo, talvez você queira excluir o cluster para evitar a cobrança de taxas. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos do cluster usando o comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma VNET no Azure usando o CLI do Azure
> * Criar um cluster seguro do Service Fabric no Azure usando o CLI do Azure
> * Proteger o cluster com um certificado X.509
> * Conectar-se ao cluster usando a CLI do Service Fabric
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como dimensionar o cluster.
> [!div class="nextstepaction"]
> [Dimensionar um cluster](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
