---
title: Configurar clusters HDInsight ingressados no domínio usando o AAD-DS
description: Saiba como instalar e configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849815"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services

Os clusters ingressados em domínio fornecem acesso multiusuário em clusters HDInsight. Clusters HDInsight ingressados em domínio são conectados a um domínio, para que os usuários do domínio possam usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de Big Data. 

Neste artigo, você aprenderá como instalar e configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Criar Azure ADDS

Habilitar o AAD-DS (Azure AD Domain Services) é um pré-requisito antes que você possa criar um cluster HDInsight ingressado em domínio. Para habilitar uma instância do AAD-DS, consulte [How to enable AAD-DS using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Como habilitar o AAD-DS usando o Portal do Azure). 

> [!NOTE]
> Somente os administradores de locatário têm os privilégios para criar uma instância do AAD-DS. Se você usar o ADLS (Azure Data Lake Storage) como o armazenamento padrão para o HDInsight, verifique se o locatário padrão do Azure AD para o ADLS é o mesmo do domínio para o cluster HDInsight. Como o Hadoop depende do Kerberos e da autenticação básica, a autenticação multifator precisa ser desabilitada para usuários que terão acesso ao cluster.

Depois que a instância do AAD-DS tiver sido provisionada, será necessário criar uma conta de serviço no AAD (que será sincronizada com o AAD-DS) com as permissões corretas. Se essa conta de serviço já existir, você precisará redefinir sua senha e aguardar até que seja sincronizado ao AAD-DS (essa redefinição resultará na criação do hash da senha kerberos e poderá levar até 30 minutos para ser sincronizada com o AAD-DS). Essa conta de serviço deve ter os seguintes privilégios:

- Adicionar computadores ao domínio e colocar entidades de serviço na UO que você especificar durante a criação do cluster.
- Criar entidades de serviço na UO que você especificar durante a criação do cluster.

> [!NOTE]
> Como o Apache Zeppelin usa o nome de domínio para autenticar a conta de serviço administrativo, a conta de serviço DEVE ter o mesmo nome de domínio que o sufixo UPN para que o Apache Zeppelin funcione corretamente.

Para saber mais sobre UOs e como gerenciá-las, consulte [Create an OU on an AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) (Criar uma UO em um AAD-DS). 

É necessário um LDAP seguro para o Domínio gerenciado do AAD-DS. Para habilitar o LDAP seguro, consulte [How to configure secure LDAP (LDAPS) for an AAD-DS managed domain](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (Como configurar o LDAPS [LDAP seguro] para um domínio gerenciado pelo AAD-DS).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Crie um cluster HDInsight associado a um domínio

A próxima etapa é criar o cluster HDInsight usando o AAD-DS e a conta de serviço criada na seção anterior.

É mais fácil de colocar o AAD-DS e o cluster HDInsight na mesma VNet (rede virtual) do Azure. Caso você tenha decidido colocá-las em VNets diferentes, é necessário emparelhar as VNets para que as VMs do HDI tenham uma linha de visão para o controlador de domínio para ingressar as VMs. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Nome de domínio**: o nome de domínio associado ao AAD-DS. Por exemplo, contoso.onmicrosoft.com
- **Nome de usuário de domínio**: a conta de serviço no domínio gerenciado criada na seção anterior. Por exemplo, hdiadmin@contoso.onmicrosoft.com. Esse usuário de domínio será o administrador deste cluster HDInsight.
- **Senha do domínio**: A senha da conta de serviço.
- **Unidade Organizacional**: Insira o nome diferenciado da UO que você deseja usar com o cluster HDInsight. Por exemplo: OU = HDInsightOU, DC = contoso, DC = onmicrosohift, DC = com. Se essa UO não existir, o cluster HDInsight tentará criar a UO usando os privilégios que a conta de serviço tem. (Por exemplo, se a conta de serviço estiver no Grupo de Administradores do AAD DS, ela terá as permissões corretas para criar uma UO; caso contrário, talvez seja necessário criar a UO primeiro e conceder à conta de serviço controle total sobre essa UO primeiro – consulte [Create an OU on an AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) [Criar uma UO em um AAD-DS]).

    > [!IMPORTANT]
    > É importante incluir todos o DCs separados por vírgula após a UO (por exemplo, UO=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com).

- **URL LDAPS**: Por exemplo, ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Insira a URL completa incluindo os ldaps:// e o número da porta (:636)

- **Acessar grupo de usuários**: Especifique o grupo de segurança cujos usuários você deseja sincronizar com o cluster. Por exemplo, HiveUsers. Se você quiser especificar vários grupos de usuários, separe-os por vírgula ','.
 
A seguinte captura de tela mostra a configuração no Portal do Azure:

![Configuração do Active Directory Domain Services ingressado no domínio do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Próximas etapas
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar-se a clusters do HDInsight Ingressados no Domínio, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

