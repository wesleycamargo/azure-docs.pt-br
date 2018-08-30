---
title: Configurar um cluster HDInsight ingressado no domínio usando o Azure AD DS
description: Saiba como configurar um cluster HDInsight ingressado no domínio usando o Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 17924b0a00f4605d41492768b0124c583664aca6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042134"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>Configurar um cluster HDInsight ingressado no domínio usando o Azure Active Directory Domain Services

Os clusters ingressados no domínio fornecem acesso multiusuário nos clusters HDInsight do Azure. Os clusters HDInsight ingressados no domínio são conectados a um domínio para que os usuários do domínio possam usar suas credenciais de domínio para autenticarem-se com os clusters e executarem trabalhos de Big Data. 

Neste artigo, você aprenderá como configurar um cluster HDInsight ingressado no domínio usando o Azure AD DS (Azure Active Directory Domain Services).

## <a name="enable-azure-ad-ds"></a>Habilitar o Azure AD DS

Habilitar o Azure AD DS é um pré-requisito para que você possa criar um cluster HDInsight ingressado no domínio. Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure). 

> [!NOTE]
> Somente administradores de locatários têm os privilégios para criar uma instância do Azure AD DS. Se você usa o Azure Data Lake Storage Gen1 como armazenamento padrão do HDInsight, verifique se o locatário padrão do Azure AD para Data Lake Storage Gen1 é o mesmo que o domínio do cluster HDInsight. Como o Hadoop depende do Kerberos e da autenticação básica, a autenticação multifator precisa ser desabilitada para usuários que acessarão o cluster.

Após provisionar a instância do Azure AD DS, crie uma conta de serviço no Azure AD (Azure Active Directory) com as permissões corretas. Se essa conta de serviço já existir, redefina a senha e aguarde até que ela seja sincronizada com o Azure AD DS. Essa redefinição resultará na criação do hash de senha do Kerberos e poderá levar até 30 minutos para sincronizar com o Azure AD DS. 

A conta de serviço precisa dos seguintes privilégios:

- Adicionar computadores ao domínio e colocar entidades de serviço na UO que você especificar durante a criação do cluster.
- Criar entidades de serviço na UO que você especificar durante a criação do cluster.

> [!NOTE]
> Como o Apache Zeppelin usa o nome de domínio para autenticar a conta de serviço administrativo, a conta de serviço *deve* ter o mesmo nome de domínio que o sufixo UPN para que o Apache Zeppelin funcione corretamente.

Para saber mais sobre UOs e como gerenciá-las, consulte [Criar uma UO em um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

O LDAP Seguro é para um domínio gerenciado do Azure AD DS. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Criar um cluster HDInsight ingressado no domínio

A próxima etapa é criar o cluster HDInsight usando o Azure AD DS e a conta de serviço que você criou na seção anterior.

É mais fácil colocar a instância do Azure AD DS e o cluster HDInsight na mesma rede virtual do Azure. Se você optar por colocá-los em diferentes redes virtuais, será necessário emparelhar essas redes virtuais, de modo que as VMs do HDInsight tenham uma linha de visão para o controlador de domínio ingressar nas VMs. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Nome de domínio**: o nome de domínio associado ao Azure AD DS. Um exemplo é contoso.onmicrosoft.com.

- **Nome de usuário de domínio**: a conta de serviço no domínio gerenciado do Azure ADDS DC que você criou na seção anterior. Um exemplo é hdiadmin@contoso.onmicrosoft.com. Esse usuário de domínio será o administrador deste cluster HDInsight.

- **Senha do domínio**: A senha da conta de serviço.

- **Unidade Organizacional**: o nome diferenciado da UO que você quer usar com o cluster HDInsight. Um exemplo é OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com. Se essa UO não existir, o cluster HDInsight tentará criar a UO usando os privilégios da conta de serviço. Por exemplo, se a conta de serviço estiver no grupo Administradores do Azure AD DS, ela terá as permissões corretas para criar uma UO. Caso contrário, talvez seja necessário criar a UO primeiro e conceder à conta de serviço controle total sobre essa UO. Para obter mais informações, consulte [Criar uma UO em um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > Incluir todos os DCs, separados por vírgulas, após a UO (por exemplo, OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com).

- **URL LDAPS**: um exemplo é ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Insira a URL completa, incluindo "ldaps://" e o número da porta (:636).

- **Acessar grupo de usuários**: Especifique o grupo de segurança cujos usuários você deseja sincronizar com o cluster. Por exemplo, HiveUsers. Se você quiser especificar vários grupos de usuários, separe-os por ponto-e-vírgula ";". O grupo deve existir no diretório antes do provisionamento. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Se o grupo não existir, ocorrerá um erro: "HiveUsers de grupo não encontrado no Active Directory".

A seguinte captura de tela mostra a configuração no Portal do Azure:

   ![Configuração do Active Directory Domain Services ingressado no domínio do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Próximas etapas
* Para configurar as políticas do Hive e executar as consultas do Hive, consulte [Configurar políticas do Hive para clusters HDInsight ingressados no domínio](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight ingressados no domínio, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

