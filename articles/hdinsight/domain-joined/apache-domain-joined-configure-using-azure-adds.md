---
title: Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure AD-DS
description: Saiba como instalar e configurar um cluster do HDInsight Enterprise Security Package usando o Azure Active Directory Domain Services.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/9/2018
ms.openlocfilehash: 6218a96b3939b2a07832dd3d6d19327cfb039b68
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986926"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services DS

Clusters de Enterprise Security Package fornecem acesso de vários usuários em clusters de HDInsight do Azure. Clusters de HDInsight com ESP estão conectados a um domínio para que os usuários do domínio podem usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data. 

Neste artigo, você aprenderá como configurar um cluster HDInsight com ESP usando o Azure Active Directory Domain Services (AD do Azure-DS).

>[!NOTE]
>O ESP está em GA no HDI 3.6 para Spark, Interactive e Hadoop. O ESP para os tipos de cluster HBase e Kafka está em versão prévia.

## <a name="enable-azure-ad-ds"></a>Habilitar o Microsoft Azure Active Directory DS

> [!NOTE]
> Somente administradores de locatários têm os privilégios para criar uma instância do Microsoft Azure Active Directory DS. Se o armazenamento de cluster for o Azure Data Lake Store (ADLS) Gen1 ou Gen2, desabilite a Autenticação Multifator (MFA) somente para os usuários que acessarão o cluster. Se o armazenamento de cluster for o Armazenamento de Blobs do Azure (WASB), não desabilite a MFA.

Habilitar o Azure AD-DS é um pré-requisito antes de criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure). 

Quando o Azure AD-DS é habilitado, todos os usuários e objetos começam a ser sincronizados do Azure Active Directory para o Azure AD-DS por padrão. A duração da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

Os clientes podem escolher sincronizar apenas os grupos que precisam acessar os clusters do HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada de *sincronização com escopo*. Consulte [Configurar a sincronização com escopo do Microsoft Azure AD para um domínio gerenciado](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) para ver as instruções.

Ao habilitar o LDAP Seguro, coloque o nome de domínio no nome da entidade ou o nome alternativo da entidade no certificado. Por exemplo, se seu nome de domínio for *contoso.com*, verifique se esse nome exato existe no seu nome da entidade ou nome alternativo da entidade no certificado. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Microsoft Azure Active Directory DS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD-DS
Exiba o status de integridade dos Azure Active Directory Domain Services selecionando **Integridade** na categoria **Gerenciar**. Verifique se o status do Azure AD-DS está verde (em execução) e se a sincronização foi concluída.

![Integridade do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Uma **identidade gerenciada atribuída ao usuário** é usada para simplificar as operações de serviços de domínio. Quando você atribui a identidade gerenciada à função de Colaborador de Serviços de Domínio do HDInsight, ela pode ler, criar, modificar e excluir operações de serviços de domínio. Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o HDInsight Enterprise Security Package. As identidades gerenciadas podem ser criadas em qualquer assinatura. Para saber mais, confira [Gerenciar identidades para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para configurar uma identidade gerenciada para uso com clusters ESP do HDInsight, crie uma identidade gerenciada atribuída pelo usuário, se você ainda não tiver uma. Confira [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) para obter instruções. Em seguida, atribua a identidade gerenciada à função **Colaborador de Serviços de Domínio do HDInsight** no Controle de acesso do Azure AD DS (é necessário ter privilégios de administrador do AAD-DS para fazer essa atribuição de função).

![Controle de acesso do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir uma identidade gerenciada à função **Colaborador do HDInsight Domain Services** garante que a identidade tenha acesso adequado para executar determinadas operações de serviços de domínio no domínio do AAD-DS.

Após a criação da identidade gerenciada e o recebimento da função correta, o administrador do AAD-DS pode definir quem pode usar essa identidade gerenciada. Para configurar usuários para a identidade gerenciada, o administrador deve selecionar a identidade gerenciada no portal e depois clicar em **Controle de Acesso (IAM)** em **Visão geral**. Em seguida, à direita, atribua a função "Operador de Identidade Gerenciada" para os usuários ou grupos que desejam criar clusters ESP do HDInsight. Por exemplo, o administrador do AAD-DS pode atribuir essa função ao grupo "MarketingTeam" para a identidade gerenciada "sjmsi", conforme mostra a figura abaixo.

![Atribuição da Função de Operador de Identidade Gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Considerações de rede

Depois de habilitar o Azure AD-DS, um servidor DNS (Serviço de Nomes de Domínio) local é executado nas VMs (máquinas virtuais) do AD. Configure sua VNET (rede virtual) do Azure AD-DS para usar esses servidores DNS personalizados. Para localizar os endereços IP corretos, selecione **Propriedades** na categoria **Gerenciar** e examine os Endereços IP listados abaixo de **Endereço IP na Rede Virtual**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Altere a configuração dos servidores DNS na VNET do Azure AD-DS para usar esses IPs personalizados selecionando **Servidores DNS** na categoria **Configurações**. Em seguida, clique no botão de opção ao lado de **Personalizado**, insira o primeiro endereço IP na caixa de texto abaixo e clique em **Salvar**. Adicione outros Endereços IP usando as mesmas etapas.

![Atualizar a configuração do VNET DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil colocar a instância do Microsoft Azure Active Directory DS e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar VNETs diferentes, emparelhe essas redes virtuais para que o controlador de domínio fique visível às VMs de HDI. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md). 

Após o emparelhamento das VNETs, configure a VNET do HDInsight para usar um servidor DNS personalizado e insira os IPs privados do Azure AD-DS como os endereços de servidor DNS. Quando as duas VNETs usarem os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e será acessível do HDInsight. Por exemplo, se o seu nome de domínio for "contoso.com", após essa etapa, executar o ping em "contoso.com" deverá resolver para o IP correto do Azure AD-DS. 

![Configurar Servidores DNS Personalizados para VNET emparelhada](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

**Para testar** se sua rede estiver configurada corretamente, adicione uma VM do Windows à Sub-rede/VNET do HDInsight e execute o ping no nome de domínio (que deve resolver para um IP), depois execute **ldp.exe** para acessar o domínio do Azure AD-DS. Em seguida, **adicione essa VM do Windows ao domínio para confirmar** que todas as chamadas RPC necessárias tenham êxito entre o cliente e o servidor. Você também pode usar **nslookup** para confirmar o acesso de rede à sua conta de armazenamento ou qualquer banco de dados externo que você possa usar (por exemplo, metastore externo do Hive ou banco de dados do Ranger DB).
Verifique se todas as [portas necessárias](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) estejam na lista de permissões nas regras do grupo de segurança de sub-rede do AAD-DS, se o AAD-DS for protegido por um NSG. 

## <a name="create-a-hdinsight-cluster-with-esp"></a>Crie um cluster HDInsight com ESP

Após a configuração correta das etapas anteriores, a próxima etapa é criar o cluster HDInsight com ESP habilitado. Ao criar um cluster HDInsight, você pode habilitar o Enterprise Security Package na guia **personalizada**. Se você preferir usar um modelo do Azure Resource Manager para implantação, use a experiência do portal uma vez e baixe o modelo previamente preenchido na última página "Resumo" para reutilização futura.

![Rede e segurança do Microsoft Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Depois de habilitar o ESP, configurações incorretas comuns relacionadas a Microsoft Azure Active Directory DS serão automaticamente detectadas e validadas.

![Validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

A detecção antecipada economiza tempo, permitindo que você corrija os erros antes de criar o cluster.

![Falha na validação de domínio do pacote de segurança empresarial do Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Quando você cria um cluster HDInsight ingressado no domínio, você deve fornecer os seguintes parâmetros:

- **Usuário administrador do cluster**: escolha um administrador para seu cluster do Microsoft Azure Active Directory DS sincronizados. Essa conta precisa já ter sido sincronizada e estar disponível no Azure AD-DS.

- **Grupos de acesso do cluster**: os grupos de segurança cujos usuários você deseja sincronizar com o cluster precisam estar disponíveis no Azure AD-DS. Por exemplo, o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: um exemplo é ldaps://contoso.com:636.

A seguinte captura de tela mostra uma configuração bem-sucedida no portal do Azure:

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A identidade gerenciada criada por você pode ser escolhida no menu suspenso de identidade gerenciada atribuída pelo usuário durante a criação de um novo cluster.

![Configuração dos Serviços de Domínio do Active Directory do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>Próximas etapas
* Para configurar as políticas do Hive e executar as consultas do Hive, consulte [Configurar políticas do Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
