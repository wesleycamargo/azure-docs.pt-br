---
title: Configurar um cluster HDInsight com o Enterprise Security Package usando o Microsoft Azure Active Directory DS
description: Saiba como instalar e configurar um cluster do Enterprise Security Package de HDInsight usando o Azure Active Directory Domain Services
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968366"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services DS

Clusters de Enterprise Security Package fornecem acesso de vários usuários em clusters de HDInsight do Azure. Clusters de HDInsight com ESP estão conectados a um domínio para que os usuários do domínio podem usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data. 

Neste artigo, você aprenderá como configurar um cluster HDInsight com ESP usando o Azure Active Directory Domain Services (AD do Azure-DS).

>[!NOTE]
>ESP está disponível no HDI 3.6 + para Spark, interativo e Hadoop. O ESP para tipos de cluster do HBase está em visualização.


## <a name="enable-azure-ad-ds"></a>Habilitar o Microsoft Azure Active Directory DS

Habilitar o Microsoft Azure Active Directory DS é um pré-requisito antes de criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure). 

> [!NOTE]
> Somente administradores de locatários têm os privilégios para criar uma instância do Microsoft Azure Active Directory DS. Se você usa o Azure Data Lake Storage Gen1 como armazenamento padrão do HDInsight, verifique se o locatário padrão do Azure AD para Data Lake Storage Gen1 é o mesmo que o domínio do cluster HDInsight. Como o Hadoop depende do Kerberos e da autenticação básica, a autenticação multifator precisa ser desabilitada para usuários que acessarão o cluster.

O LDAP Seguro é para um domínio gerenciado do Microsoft Azure Active Directory DS. Ao habilitar o LDAPS, coloque o nome de domínio no nome da entidade ou o nome alternativo da entidade no certificado. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Microsoft Azure Active Directory DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Adicionar identidade gerenciada

Depois que você habilitou o Microsoft Azure Active Directory DS, crie uma identidade gerenciada e atribua à função do **Colaborador de serviços de domínio HDInsight** no controle de acesso do AD DS do Azure.

![Controle de acesso do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Para obter mais informações, consulte [O que são identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Crie um cluster HDInsight com ESP

A próxima etapa é criar o cluster HDInsight com ESP habilitado por meio do Microsoft Azure Active Directory DS.

É mais fácil colocar a instância do Microsoft Azure Active Directory DS e o cluster HDInsight na mesma rede virtual do Azure. Se você optar por colocá-los em diferentes redes virtuais, será necessário emparelhar essas redes virtuais, de modo que as VMs do HDInsight tenham uma linha de visão para o controlador de domínio ingressar nas VMs. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Quando você cria um cluster HDInsight, você tem a opção de habilitar o Enterprise Security Package para conectar-se o seu cluster com o MIcrosoft Azure Active Directory DS. 

![Rede e segurança do Microsoft Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Depois de habilitar o ESP, configurações incorretas comuns relacionadas a Microsoft Azure Active Directory DS serão automaticamente detectadas e validadas.

![Validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

A detecção antecipada economiza tempo, permitindo que você corrija os erros antes de criar o cluster.

![Falha na validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Usuário administrador do cluster**: escolha um administrador para seu cluster do Microsoft Azure Active Directory DS sincronizados.

- **Grupos de acesso do cluster**: os grupos de segurança cujos usuários você deseja sincronizar com o cluster devem estar sincronizados e estarem disponíveis no Microsoft Azure Active Directory DS. Por exemplo, HiveUsers. Se você quiser especificar vários grupos de usuários, separe-os por ponto-e-vírgula ";". O grupo deve existir no diretório antes do provisionamento. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Se o grupo não existir, ocorrerá um erro: "HiveUsers de grupo não encontrado no Active Directory".

- **URL LDAPS**: um exemplo é ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Insira a URL completa, incluindo "ldaps://" e o número da porta (:636).

A seguinte captura de tela mostra a configuração no Portal do Azure:

   ![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Próximas etapas
* Para configurar as políticas do Hive e executar as consultas do Hive, consulte [Configurar políticas do Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

