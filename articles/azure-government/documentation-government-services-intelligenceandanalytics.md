---
title: "Inteligência + Análises do Azure Government | Microsoft Docs"
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Inteligência + Análises do Azure Government
Este artigo descreve os serviços, variações e considerações sobre inteligência e análise para o ambiente do Azure Government.

## <a name="azure-hdinsight"></a>Azure HDInsight
O HDInsight geralmente está disponível no Azure Government.

### <a name="variations"></a>Variações
Os seguintes recursos do HDInsight não estão disponíveis no Azure Government no momento.

* O HDInsight não está disponível no Windows.
* O Azure Data Lake Store não está disponível no Azure Government no momento. O Armazenamento de Blobs do Azure é a única opção de armazenamento disponível no momento.
* A integração com o Active Directory Domain Services do Azure ainda não está disponível. Para criar clusters de Hadoop seguros sem o Active Directory Domain Services, selecione um dos seguintes cenários de instalação:

1. HDINSIGHT INTEGRADO AO ACTIVE DIRECTORY EM EXECUÇÃO NO AZURE IAAS

  Isso é, de longe, a arquitetura mais simples para a integração do HDInsight com o Active Directory. O diagrama da arquitetura é fornecido abaixo. Nesta arquitetura, você terá um controlador de domínio do Active Directory em execução em uma (ou várias) VMs no Azure. Geralmente essas VMs estarão dentro de uma rede virtual. Você pode configurar uma nova rede virtual na qual pode colocar seu cluster do HDInsight. Para que o HDInsight tenha acesso ao Active Directory, você precisará emparelhar essas redes virtuais usando o [Emparelhamento VNET a VNET] (https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). 
  
  Pré-requisitos necessários para a configuração no Active Directory
     * Deve-se criar uma unidade organizacional, na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster. 
     * LDAPS deve ser configurado para se comunicar com o Active Directory. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
     * Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDI.
     * Uma conta de serviço ou uma conta de usuário é necessária, que será usada para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões
        * Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
        * Permissões para criar regras de proxy DNS reversas.
        * Permissões para ingressar computadores ao domínio do Active Directory.
        
2. HDINSIGHT INTEGRADO COM UM ACTIVE DIRECTORY LOCAL VIA CONFIGURAÇÃO DE VPN
  
  Essa arquitetura é como a arquitetura #1. A única diferença é que, nesse caso, o Active Directory é local e o acesso ao HDInsight para o Active Directory é por meio de uma [conexão VPN do Azure para a rede local] (https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction). O diagrama de arquitetura para essa configuração é mostrado abaixo. 
  
  Pré-requisitos necessários para a configuração no Active Directory local
     * Deve-se criar uma unidade organizacional, na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
     * LDAPS deve ser configurado para se comunicar com o Active Directory. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
     * Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDI.
     * Uma conta de serviço ou uma conta de usuário é necessária, que será usada para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões
        * Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
        * Permissões para criar regras de proxy DNS reversas.
        * Permissões para ingressar computadores ao domínio do Active Directory.


As URLs do Log Analytics são diferentes no Azure Governamental:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Cluster do HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Para saber mais, veja a [Documentação pública do HDInsight do Azure](../hdinsight/hdinsight-hadoop-introduction.md).


## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Jan17_HO2-->


