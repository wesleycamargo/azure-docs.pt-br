---
title: Configurar um cluster HDInsight com o Enterprise Security Package usando o Microsoft Azure Active Directory DS
description: Saiba como instalar e configurar um cluster do HDInsight Enterprise Security Package usando o Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408347"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services DS

Clusters de Enterprise Security Package fornecem acesso de vários usuários em clusters de HDInsight do Azure. Clusters de HDInsight com ESP estão conectados a um domínio para que os usuários do domínio podem usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data. 

Neste artigo, você aprenderá como configurar um cluster HDInsight com ESP usando o Azure Active Directory Domain Services (AD do Azure-DS).

>[!NOTE]
>O ESP está em GA no HDI 3.6 para Spark, Interactive e Hadoop. O ESP para os tipos de cluster HBase e Kafka está em versão prévia.

## <a name="enable-azure-ad-ds"></a>Habilitar o Microsoft Azure Active Directory DS

Habilitar o Microsoft Azure Active Directory DS é um pré-requisito antes de criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure). 

Quando o Azure AD-DS é habilitado, todos os usuários e objetos começam a ser sincronizados do AAD (Azure Active Directory) para o Azure AD-DS por padrão. A duração da operação de sincronização depende do número de objetos no AAD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

Os clientes podem escolher sincronizar apenas os grupos que precisam acessar os clusters do HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada de *sincronização com escopo*. Consulte [Configurar a sincronização com escopo do Microsoft Azure AD para um domínio gerenciado](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) para ver as instruções.

> [!NOTE]
> Somente administradores de locatários têm os privilégios para criar uma instância do Microsoft Azure Active Directory DS. A autenticação multifator precisa ser desabilitada somente para os usuários que acessarão o cluster.

Ao habilitar o LDAP Seguro, coloque o nome de domínio no nome da entidade ou o nome alternativo da entidade no certificado. Por exemplo, se seu nome de domínio for *contoso.com*, verifique se esse nome exato existe no seu nome da entidade ou nome alternativo da entidade no certificado. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Microsoft Azure Active Directory DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>Verificar o status de integridade do AAD-DS

Exiba o status de integridade dos Azure Active Directory Domain Services selecionando **Integridade** na categoria **Gerenciar**. Verifique se o status do AAD-DS está verde (em execução) e se a sincronização foi concluída.

![Integridade do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Adicionar identidade gerenciada

Depois de habilitar o Azure AD-DS, crie uma identidade gerenciada atribuída pelo usuário e atribua-a à função **Colaborador do HDInsight Domain Services** no controle de acesso do Azure AD-DS.

![Controle de acesso do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir uma identidade gerenciada à função **Colaborador do HDInsight Domain Services** garante que a identidade tenha acesso adequado para executar determinadas operações de serviços de domínio no domínio do AAD-DS. Para obter mais informações, consulte [O que são identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>Crie um cluster HDInsight com ESP

A próxima etapa é criar o cluster HDInsight com ESP habilitado por meio do Microsoft Azure Active Directory DS.

É mais fácil colocar a instância do Microsoft Azure Active Directory DS e o cluster HDInsight na mesma rede virtual do Azure. Se você optar por colocá-los em diferentes redes virtuais, será necessário emparelhar essas redes virtuais, de modo que as VMs do HDInsight tenham uma linha de visão para o controlador de domínio ingressar nas VMs. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md). Para testar se o emparelhamento for realizado corretamente, adicione uma VM a HDInsight VNET/Subnet e execute ping no nome de domínio ou execute **ldp.exe** para acessar o domínio do AAD-DS.

Ao criar um cluster HDInsight, você tem a opção de habilitar o Enterprise Security Package na guia personalizada. 

![Rede e segurança do Microsoft Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Depois de habilitar o ESP, configurações incorretas comuns relacionadas a Microsoft Azure Active Directory DS serão automaticamente detectadas e validadas.

![Validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

A detecção antecipada economiza tempo, permitindo que você corrija os erros antes de criar o cluster.

![Falha na validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Usuário administrador do cluster**: escolha um administrador para seu cluster do Microsoft Azure Active Directory DS sincronizados. Essa conta precisa já ter sido sincronizada e estar disponível no AAD-DS.

- **Grupos de acesso do cluster**: os grupos de segurança cujos usuários você deseja sincronizar com o cluster precisam estar disponíveis no Azure AD-DS. Por exemplo, o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: um exemplo é ldaps://contoso.com:636.

A seguinte captura de tela mostra as configurações bem-sucedidas no portal do Azure:

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A identidade gerenciada criada por você pode ser escolhida no menu suspenso de identidade gerenciada atribuída pelo usuário durante a criação de um novo cluster.

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Próximas etapas
* Para configurar as políticas do Hive e executar as consultas do Hive, consulte [Configurar políticas do Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).