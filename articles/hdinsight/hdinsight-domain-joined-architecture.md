---
title: "Arquitetura do Azure HDInsight associada ao domínio | Microsoft Docs"
description: "Aprenda a planejar o HDInsight associado ao domínio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: b5cd321f3cd4c415f5e97ca550b7ab1679324921
ms.openlocfilehash: 94ef8147f1f73b293e818fc508096789373df7fc


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planejar clusters Hadoop do Azure associados ao domínio no HDInsight

O Hadoop tradicional é um cluster de usuário único. Ele é adequado para a maioria das empresas que têm equipes de aplicativos menores que criam as cargas de trabalho de big data. Como o Hadoop está ganhando popularidade, muitas empresas estão mudando para um modelo em que clusters são gerenciados por equipes de TI, e várias equipes de aplicativo compartilham clusters. Assim, clusters de vários usuários são uma das funcionalidades mais solicitadas no HDInsight.

Em vez de criar sua própria autorização e autenticação multiusuário, o HDInsight recorre ao provedor de identidade mais popular: o AD (Active Directory). A poderosa funcionalidade de grupos de segurança no Active Directory pode ser usada para gerenciar a autorização de vários usuário no HDInsight. Integrando o HDInsight ao Active Directory, os usuários do Active Directory podem se comunicar com os clusters usando suas credenciais do Active Directory. O HDInsight mapeia o usuário do Active Directory para um usuário do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark thrift e assim por diante) funcionem sem problemas para o usuário autenticado.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight ao Active Directory

Integrando o HDInsight ao Active Directory, os nós do cluster HDInsight são associados ao domínio para o domínio do Active Directory. O HDInsight cria objetos de serviço para os serviços do Hadoop em execução no cluster e os coloca em uma UO (Unidade Organizacional) especificada no Active Directory. O HDInsight também cria mapeamentos de DNS reversos no domínio do Active Directory para os endereços IP de nós que ingressaram no domínio.

Para obter essa configuração, há várias arquiteturas que podem ser seguidas. Você precisa decidir qual arquitetura funciona melhor para você.

**1. HDInsight integrado ao AD em execução no Azure IAAS**

Essa é a arquitetura mais simples para a integração do HDInsight com o Active Directory. O controlador de domínio do Active Directory é executado em uma (ou várias) VMs (máquinas virtuais) no Azure. Geralmente, essas VMs estão em uma rede virtual. Configure outra rede virtual para o cluster HDInsight. Para que o HDInsight tenha uma linha de visão para o Active Directory, você precisa emparelhar essas redes virtuais usando o [emparelhamento de VNet para VNet](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md).

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Nessa arquitetura, você não pode usar o Azure Data Lake Store com o cluster HDInsight.
 

Pré-requisitos para o Active Directory:

* Deve-se criar uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) deve ser configurado para se comunicar com o Active Directory. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da sub-rede HDInsight (por exemplo 10.2.0.0/24 na figura anterior).
* É necessária uma conta de serviço ou uma conta de usuário, que é emitida para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores ao domínio do Active Directory.

**2. HDInsight integrado a um Azure AD somente de nuvem**

Para um Azure AD (Azure Active Directory) somente de nuvem, você precisa configurar um controlador de domínio para que o HDInsight possa ser integrado ao Azure Active Directory. Isso é feito usando o [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). O Azure AD DS cria máquinas de controlador de domínio na nuvem e fornece endereços IP para elas. Ele cria dois controladores de domínio para alta disponibilidade.

Atualmente, o Azure AD DS existe somente em VNets clássicas. Só é acessível por meio do portal clássico do Azure. A VNet HDInsight existe no portal do Azure e precisa ser emparelhada com a VNet clássica usando o emparelhamento de VNet para VNet.

> [!NOTE]
> O emparelhamento entre uma VNet clássica e uma VNet do Azure Resource Manager requer que as duas VNets estejam na mesma região e ambas as VNets estejam sob a mesma assinatura do Azure.

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Deve-se criar uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) devem ser configurados quando você configura o AD DS. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da Sub-rede HDI (por exemplo, 10.2.0.0/24 na figura anterior). 
* [Hashes de senha](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) devem ser sincronizados do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de usuário, que é emitida para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores ao domínio do Active Directory.

**3. HDInsight integrado a um AD local via VPN**

Essa arquitetura é semelhante a arquitetura 1. A única diferença é que o Active Directory é local, e a linha de visão para o HDInsight ao Active Directory é por meio de uma [conexão VPN do Azure para a rede local](../expressroute/expressroute-introduction.md).

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Nessa arquitetura, você não pode usar o Azure Data Lake Store com o cluster HDInsight.

Pré-requisitos para o Active Directory:

* Deve-se criar uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) deve ser configurado para se comunicar com o Active Directory. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da Sub-rede HDI (por exemplo, 10.2.0.0/24 na figura anterior).
* É necessária uma conta de serviço ou uma conta de usuário, que é emitida para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores ao domínio do Active Directory.

**4. HDInsight integrado a um AD local sincronizado com um Azure AD**

Essa arquitetura é semelhante à arquitetura 2. A única diferença é que o Active Directory local é sincronizado com o Azure Active Directory. Você precisa configurar um controlador de domínio na nuvem para que o HDInsight possa ser integrado ao Azure Active Directory. Isso é feito usando o [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (AD DS). O AD DS cria máquinas de controlador de domínio na nuvem e fornece endereços IP para elas. Ele cria dois controladores de domínio para alta disponibilidade.

Atualmente, o Azure AD DS existe somente em VNets clássicas. Só é acessível por meio do portal clássico do Azure. A VNet HDInsight existe no portal do Azure e precisa ser emparelhada com a VNet clássica usando o emparelhamento de VNet para VNet.

> [!NOTE]
> O emparelhamento entre uma VNet clássica e uma VNet do Azure Resource Manager requer que as duas VNets estejam na mesma região e ambas as VNets estejam sob a mesma assinatura do Azure.

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Deve-se criar uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) devem ser configurados quando você configura o AD DS. O certificado usado para configurar o LDAPS deve ser um certificado real (não um certificado autoassinado).
* Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da Sub-rede HDI (por exemplo, 10.2.0.0/24 na figura anterior). 
* [Hashes de senha](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) devem ser sincronizados do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de usuário, que é emitida para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores ao domínio do Active Directory.

**5. O HDInsight integrado a um Azure AD não padrão (recomendado somente para teste e desenvolvimento)**

Essa arquitetura é semelhante à arquitetura 2. Para a maioria das empresas, o acesso de administrador para o Active Directory é restrito apenas a determinados indivíduos. Portanto, quando você desejar fazer uma verificação de conceito ou apenas tentar criar um cluster associado ao domínio, em vez de esperar até que o administrador configure os pré-requisitos no Active Directory, pode ser benéfico apenas criar um novo Azure Active Directory na assinatura. Como se trata de um Azure AD que você criou, você tem permissões completas para esse Azure AD para configurar o AD DS.

O AD DS cria máquinas de controlador de domínio na nuvem e fornece endereços IP para elas. Ele cria dois controladores de domínio para alta disponibilidade.

O AD DS existe somente em VNets Clássicas hoje. Portanto, você precisa acessar o portal Clássico e criar uma VNet clássica para configurar o AD DS. A VNet HDInsight existe no portal do Azure e precisa ser emparelhada com a VNet clássica usando o emparelhamento de VNet para VNet.

> [!NOTE]
> O emparelhamento entre VNets Clássicas e do Azure Resource Manager requer que as VNets estejam na mesma região e ambas as VNets estejam na mesma assinatura do Azure.

![Topologia de cluster do HDInsight de associação de domínio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Pré-requisitos para o Active Directory:

* Deve-se criar uma [Unidade Organizacional](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), na qual você deseja colocar as VMs do cluster do HDInsight e as entidades de serviço usadas pelo cluster. 
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) devem ser configurados quando você configura o AD DS. Você pode criar um [certificado autoassinado](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) para configurar LDAPS. No entanto, para usar um certificado autoassinado, você precisa solicitar uma exceção de <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Zonas DNS reversas devem ser criadas no domínio para o intervalo de endereços IP da Sub-rede HDI (por exemplo, 10.2.0.0/24 na figura anterior). 
* [Hashes de senha](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) devem ser sincronizados do Azure AD para o AD DS.
* É necessária uma conta de serviço ou uma conta de usuário, que é emitida para criar o cluster HDInsight. Essa conta deve ter as seguintes permissões:

    - Permissões para criar objetos de entidade de serviço e objetos de computador na unidade organizacional.
    - Permissões para criar regras de proxy DNS reversas
    - Permissões para ingressar computadores ao domínio do Active Directory.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight associado a um domínio, confira [Configurar clusters HDInsight associados a domínio](hdinsight-domain-joined-configure.md).
* Para gerenciar clusters HDInsight associados a um domínio, confira [Gerenciar clusters HDInsight associados a domínio](hdinsight-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](hdinsight-domain-joined-run-hive.md).
* Para executar consultas do Hive usando SSH em clusters do HDInsight Ingressado no Domínio, confira [Use SSH with Linux-based Hadoop on HDInsight from Linux, Unix, or OS X](hdinsight-hadoop-linux-use-ssh-unix.md) (Usar SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X).




<!--HONumber=Feb17_HO1-->


