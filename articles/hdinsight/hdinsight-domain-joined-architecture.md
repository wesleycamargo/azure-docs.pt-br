---
title: "Arquitetura do Azure HDInsight ingressado no domínio | Microsoft Docs"
description: "Aprenda a planejar o HDInsight associado ao domínio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7e34f47f09466a40993b4cc797ff1cad2bdaeafe
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planejar clusters Hadoop do Azure associados ao domínio no HDInsight

O Hadoop tradicional é um cluster de usuário único. Ele é adequado para a maioria das empresas que têm equipes de aplicativos menores que criam grandes cargas de trabalho de dados. À medida que o Hadoop ganha popularidade, muitas empresas estão mudando para um modelo em que clusters são gerenciados por equipes de TI, e várias equipes de aplicativo compartilham clusters. Assim, as funcionalidades que envolvem clusters multiusuários estão entre as funcionalidades mais solicitadas no Azure HDInsight.

Em vez de criar sua própria autorização e autenticação multiusuário, o HDInsight recorre ao provedor de identidade mais popular – o AD (Active Directory). A funcionalidade de segurança eficiente no AD pode ser usada para gerenciar a autorização multiusuário no HDInsight. Integrando HDInsight com o AD, você pode se comunicar com os clusters, usando suas credenciais do AD. O HDInsight mapeia o usuário do AD para um usuário do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark Thrift e outros) funcionem sem problemas para o usuário autenticado.

## <a name="integrate-hdinsight-with-ad-and-ad-on-iaas-vm"></a>Integrar o HDInsight com o AD e o AD em VM IaaS

Integrando o HDInsight com o Azure AD ou o AD em VM IaaS, os nós do cluster HDInsight são ingressados em um domínio. O HDInsight cria entidades de serviço para os serviços do Hadoop em execução no cluster e os coloca em uma UO (unidade organizacional) especificada no Azure AD ou AD em VM IaaS. O HDInsight também cria mapeamentos de DNS reversos no domínio para os endereços IP de nós que ingressaram no domínio.

Você pode obter essa configuração, usando várias arquiteturas. É possível escolher uma das arquiteturas a seguir.

**HDInsight integrado ao AD em execução no Azure IaaS**

Essa é a arquitetura mais simples para a integração do HDInsight com o Active Directory. O controlador de domínio do AD é executado em uma ou várias, VMs (máquinas virtuais) no Azure. Essas VMs são normalmente em uma rede virtual. Configure outra rede virtual para o cluster HDInsight. Para que o HDInsight tenha uma linha de visão para o Active Directory, você precisa emparelhar essas redes virtuais usando o [emparelhamento de VNet para VNet](../virtual-network/virtual-network-create-peering.md). Se você criar o Active Directory em ARM, você poderá criar o Active Directory e o HDInsight na mesma VNet e não precisará fazer o emparelhamento. 

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nessa arquitetura, você não pode usar o Azure Data Lake Store com o cluster HDInsight.


Pré-requisitos para o Active Directory:

* Deve-se criar uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você coloca as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
* [Protocolos de acesso de diretório leve](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) devem ser configurados para se comunicar com o AD. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* As zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDInsight (por exemplo 10.2.0.0/24 na figura anterior).
* Uma conta de serviço ou uma conta de usuário é necessária. Use essa conta para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores no domínio do Active Directory

**HDInsight integrado ao Azure AD somente de nuvem**

Para o Azure AD somente na nuvem, configure um controlador de domínio para que o HDInsight possa ser integrado ao Azure AD. Isso é feito usando o [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). O Azure AD DS cria computadores de controlador de domínio na nuvem e fornece endereços IP para eles. Ele cria dois controladores de domínio para alta disponibilidade.

Atualmente, o Azure AD DS só existe em redes virtuais clássicas. Isso só é acessível por meio do portal clássico do Azure. A rede virtual HDInsight existe no portal do Azure e precisa ser emparelhada com a rede virtual clássica usando o emparelhamento de VNet para VNet.

> [!NOTE]
> Emparelhamento entre uma rede virtual clássica e uma rede virtual do Azure Resource Manager requer que ambas as redes virtuais estão na mesma região e a mesma assinatura do Azure.

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos do Azure AD:

* Deve-se criar uma [unidade organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
* O [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) deve ser configurado quando você configura o AD DS. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* As zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDInsight (por exemplo 10.2.0.0/24 na figura anterior).
* Os [hashes de senha](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) devem ser sincronizados do Azure AD para o Azure AD DS.
* Uma conta de serviço ou uma conta de usuário é necessária. Use essa conta para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores no domínio do Azure AD

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight ingressado em um domínio, confira [Configurar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-configure.md).
* Para gerenciar clusters HDInsight ingressados em um domínio, confira [Gerenciar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight ingressados no domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

