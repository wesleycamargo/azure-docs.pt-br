---
title: "Configurar clusters HDInsight ingressados no domínio usando o PowerShell – Azure | Microsoft Docs"
description: "Saiba como instalar e configurar clusters HDInsight ingressados no domínio usando o Azure PowerShell"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9da76bb5f649817cd2f027f3d0eb46d58a996b4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Configurar clusters HDInsight ingressados no domínio (visualização) usando o Azure PowerShell
Saiba como configurar um cluster Azure HDInsight com o Azure AD (Azure Active Directory) e o [Apache Ranger](http://hortonworks.com/apache/ranger/) usando o Azure PowerShell. Um script do Azure PowerShell é fornecido para tornar a configuração mais rápida e menos sujeita a erros. O HDInsight ingressado em domínio só pode ser configurado em clusters baseados em Linux. Para obter mais informações, consulte [Introduzir clusters HDInsight ingressados no domínio](hdinsight-domain-joined-introduction.md).

> [!IMPORTANT]
> O Oozie não está habilitado no HDInsight ingressado no domínio.

Uma configuração típica de cluster HDInsight ingressado em um domínio envolve as seguintes etapas:

1. Crie uma VNet clássica do Azure para o Azure AD.  
2. Crie e configure o Azure AD e o Azure AD DS.
3. Adicione uma VM à VNet clássica para criar a unidade organizacional. 
4. Crie uma unidade organizacional para o Azure AD DS.
5. Crie uma VNet HDInsight no modo de gerenciamento de recursos do Azure.
6. Configure zonas DNS inversas para o Azure AD DS.
7. Emparelhe as duas VNets.
8. Crie um cluster HDInsight.

O script do PowerShell fornecido executa as etapas 3 a 7. Você deve percorrer as etapas 1 e 2 manualmente.  Se você preferir não usar o Azure PowerShell, confira [Configurar clusters HDInsight ingressados no domínio](hdinsight-domain-joined-configure.md). 

Um exemplo de topologia final será semelhante ao seguinte:

![Topologia de HDInsight ingressado no domínio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

Já que o Azure AD dá suporte atualmente apenas a VNets (redes virtuais) clássicas e os clusters HDInsight baseados em Linux dão suporte apenas a VNets com base no Azure Resource Manager, a integração do Azure AD do HDInsight requer duas VNets e um emparelhamento entre elas. Para as informações de comparação entre os dois modelos de implantação, veja [Implantação do Azure Resource Manager versus clássica: compreenda os modelos de implantação e o estado de seus recursos](../azure-resource-manager/resource-manager-deployment-model.md). As duas VNets devem estar na mesma região que o Azure AD DS.

> [!NOTE]
> Este tutorial presume que você não tenha um Azure AD. Se você tiver um, poderá ignorar a parte contida na etapa 2.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter os seguintes itens para percorrer este tutorial:

* Familiarize-se com o [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) e sua estrutura de [preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Certifique-se de que sua assinatura está na lista de permissões para essa visualização pública. Você pode fazer isso enviando um email para hdipreview@microsoft.com com sua ID da assinatura.
* Um certificado SSL assinado por uma autoridade de autenticação para seu domínio. O certificado é necessário para configurar o LDAP seguro. Os certificados autoassinados não podem ser usados.
* PowerShell do Azure.  Confira [Instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Crie uma VNet clássica do Azure para o Azure AD.
Para instruções, confira [aqui](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic).

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Crie e configure o Azure AD e o Azure AD DS.
Para instruções, confira [aqui](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).

## <a name="run-the-powershell-script"></a>Executar o script do PowerShell
O script do PowerShell pode ser baixado no [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight). Extraia o arquivo zip e salve os arquivos localmente.

**Para editar o script do PowerShell**

1. Abra run.ps1 usando o ISE do Windows PowerShell ou outro editor de texto.
2. Preencha os valores das seguintes variáveis:
   
   * **$SubscriptionName** – o nome da assinatura do Azure em que você deseja criar o cluster HDInsight. Você já criou uma rede virtual clássica nesta assinatura e criará uma rede virtual do Azure Resource Manager para o cluster HDInsight na assinatura.
   * **$ClassicVNetName** -a rede virtual clássica que contém o Azure AD DS. Essa rede virtual deve estar na mesma assinatura fornecida acima. Essa rede virtual deve ser criada usando o portal do Azure, e não o portal clássico. Se você seguir as instruções em [Configurar clusters HDInsight ingressados no domínio (Visualização)](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic), o nome padrão é contosoaadvnet.
   * **$ClassicResourceGroupName** – o nome do grupo do Gerenciador de Recursos para a rede virtual clássica mencionada acima. Por exemplo, contosoaadrg. 
   * **$ArmResourceGroupName** – o nome do grupo de recursos em que você deseja criar o cluster HDInsight. Você pode usar o mesmo grupo de recursos de $ArmResourceGroupName.  Se o grupo de recursos não existir, o script o criará.
   * **$ArmVNetName** -o nome da rede virtual do Gerenciador de Recursos na qual você deseja criar o cluster HDInsight. Essa rede virtual será colocada em $ArmResourceGroupName.  Se a rede virtual não existir, o script do PowerShell a criará. Se existir, ela deve ser parte do grupo de recursos fornecido acima.
   * **$AddressVnetAddressSpace** – o espaço de endereço de rede para a rede virtual do Gerenciador de Recursos. Verifique se esse espaço de endereço está disponível. Esse espaço de endereço não pode se sobrepor ao espaço de endereço da rede virtual clássica. Por exemplo, “10.1.0.0/16”
   * **$ArmVnetSubnetName** -o nome da sub-rede da rede virtual do Gerenciador de Recursos em que você deseja colocar as VMs do cluster HDInsight. Se a sub-rede não existir, o script do PowerShell a criará. Se existir, ela deve ser parte da rede virtual fornecida acima.
   * **$AddressSubnetAddressSpace** – o intervalo de endereços de rede para a sub-rede da rede virtual do Gerenciador de Recursos. Os endereços IP da VM do cluster HDInsight serão do intervalo de endereços dessa sub-rede. Por exemplo, “10.1.0.0/24”.
   * **$ActiveDirectoryDomainName** – o nome do domínio do Azure AD no qual você deseja ingressar as VMs do cluster HDInsight. Por exemplo, “contoso.onmicrosoft.com”
   * **$ClusterUsersGroups** – o nome comum dos grupos de segurança do seu AD que você deseja sincronizar com o cluster HDInsight. Os usuários neste grupo de segurança serão capazes de entrar no painel do cluster usando suas credenciais de Domínio do Active Directory. Esses grupos de segurança devem existir no active directory. Por exemplo, "hiveusers" ou "clusteroperatorusers".
   * **$OrganizationalUnitName** -a unidade organizacional no domínio, na qual você deseja colocar as VMs do cluster HDInsight e as entidades de serviço usadas pelo cluster. O script do PowerShell criará essa UO se ela não existir. Por exemplo, "HDInsightOU".
3. Salve as alterações.

**Para executar o script**

1. Execute o **Windows PowerShell** como Administrador.
2. Navegue até a pasta do run.ps1. 
3. Execute o script digitando o nome do arquivo e pressione **ENTER**.  Três diálogos de entrada aparecem:
   
   1. **Entre no portal clássico do Azure** – insira as credenciais que você usa para entrar no portal clássico do Azure. Você deve ter criado o Azure AD e o Azure AD DS usando essas credenciais.
   2. **Entre no portal do Azure Resource Manager** – insira as credenciais que você usa para entrar no portal do Azure Resource Manager.
   3. **Nome de usuário de domínio** – insira as credenciais do nome de usuário do domínio em que você deseja ser o administrador no cluster HDInsight. Se você criou um Azure AD do zero, deve ter criado o usuário com esta documentação. 
      
      > [!IMPORTANT]
      > Insira o nome de usuário neste formato: 
      > 
      > Nomededomínio\nomedeusuário (por exemplo, contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      Esse usuário deve ter três privilégios: para ingressar computadores no domínio do Active Directory fornecido; para criar entidades de serviço e objetos de computador na Unidade Organizacional fornecida para e adicionar regras de proxy DNS invertidas.

Durante a criação de zonas DNS reversas, o script solicitará que você insira uma identificação de rede. Essa ID de rede deve ser o prefixo de endereço da rede virtual do Gerenciador de Recursos. Por exemplo, se seu espaço de endereço de sub-rede da rede virtual do Gerenciador de Recursos é 10.2.0.0/24, digite 10.2.0.0/24 quando a ferramenta solicita a ID de rede. 

## <a name="create-hdinsight-cluster"></a>Criar cluster HDInsight
Nesta seção, você criará um cluster Hadoop baseado em Linux no HDInsight usando o Portal do Azure ou então o [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo do Resource Manager para criar clusters Hadoop no HDInsight, confira [Criar clusters Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para criar um cluster HDInsight ingressado no Domínio usando o Portal do Azure**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo**, **Inteligência + análise** e então em **HDInsight**.
3. Da folha **Novo cluster HDInsight**, insira ou selecione os valores a seguir:
   
   * **Nome do cluster**: insira um novo nome do cluster para o cluster HDInsight ingressado no domínio.
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Configuração do cluster**:
     
     * **Tipo de cluster**: Hadoop. Atualmente, apenas clusters Hadoop dão suporte ao HDInsight ingressado no domínio.
     * **Sistema Operacional**: Linux.  Apenas clusters HDInsight baseados em Linux dão suporte ao HDInsight ingressado no domínio.
     * **Versão**: Hadoop 2.7.3 (HDI 3.5). Somente a versão 3.5 de cluster HDInsight dá suporte ao HDInsight ingressado no domínio.
     * **Tipo de cluster**: PREMIUM
       
       Clique em **Selecionar** para salvar as alterações.
   * **Credenciais**: configure as credenciais para o usuário do cluster e o usuário SSH.
   * **Fonte de Dados**: crie uma nova conta de armazenamento ou use uma conta de armazenamento existente como a conta de armazenamento padrão para o cluster HDInsight. A localização deve ser a mesma que a das duas VNets.  A localização também é a localização do cluster HDInsight.
   * **Preço**: selecione o número de nós de trabalho do seu cluster.
   * **Configurações avançadas**: 
     
     * **Ingresso no domínio e Sub-rede/VNet**: 
       
       * **Configurações de domínio**: 
         
         * **Nome de domínio**: contoso.onmicrosoft.com
         * **Nome de usuário de domínio**: insira um nome de usuário de domínio. Esse domínio deve ter os seguintes privilégios: adicionar computadores ao domínio e colocá-los na unidade organizacional que você configurou anteriormente; criar entidades de serviço dentro da unidade organizacional que você configurou anteriormente; criar entradas de DNS reverso. Esse usuário de domínio se tornará o administrador deste cluster HDInsight ingressado no domínio.
         * **Senha de domínio**: insira a senha de usuário de domínio.
         * **Unidade Organizacional**: insira o nome diferenciado da UO que você configurou anteriormente. Por exemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Acessar grupo de usuários**: especifique o grupo de segurança cujos usuários você deseja sincronizar com o cluster. Por exemplo, HiveUsers.
           
           Clique em **Selecionar** para salvar as alterações.
           
           ![Configuração de domínio para configurar o portal do HDInsight ingressado no domínio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Rede virtual**: contosohdivnet
       * **Subrede**: Subnet1
         
         Clique em **Selecionar** para salvar as alterações.        
         Clique em **Selecionar** para salvar as alterações.
   * **Grupo de Recursos**: selecione o grupo de recursos usado para a VNet HDInsight (contosohdirg).
4. Clique em **Criar**.  

Outra opção para criar o cluster HDInsight de domínio é usar um modelo do Azure Resource Manager. O procedimento a seguir mostra como fazer isso:

**Para criar um cluster HDInsight ingressado no domínio usando um modelo do Azure Resource Manager**

1. Clique na imagem a seguir para abrir um modelo do Resource Manager no Portal do Azure. O modelo do Resource Manager está localizado em um contêiner de blob público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Na folha **Parâmetros**, insira os seguintes valores:
   
   * **Assinatura**: (Selecione sua assinatura do Azure).
   * **Grupo de recursos**: clique em **Usar existente** e especifique o mesmo grupo de recursos que você vem usando.  Por exemplo, contosohdirg. 
   * **Localização**: especifique uma localização para o grupo de recursos.
   * **Nome do Cluster**: insira um nome para o cluster Hadoop que você criará. Por exemplo, contosohdicluster.
   * **Tipo de Cluster**: selecione um tipo de cluster.  O valor padrão é **hadoop**.
   * **Localizaçao**: selecione uma localização para o cluster.  A conta de armazenamento padrão usa a mesma localização.
   * **Contagem de Nós de Trabalho do Cluster**: selecione o número de nós de trabalho.
   * **Nome e senha de logon do cluster**: o nome de logon padrão é **admin**.
   * **Nome de usuário e senha SSH**: o nome de usuário padrão é **sshuser**.  Você pode renomeá-lo. 
   * **ID da Rede Virtual**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Sub-rede da Rede Virtual**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **Nome de Domínio**: contoso.onmicrosoft.com
   * **DN da Unidade Organizacional**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **DNs do Grupo de Usuários do Cluster**: "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (Insira o nome do usuário administrador do domínio)
   * **DomainAdminPassword**: (Insira a senha do usuário administrador do domínio)
   * **Concordo com os termos e condições declarados acima**: (Marcar)
   * **Fixar no painel**: (Marcar)
3. Clique em **Comprar**. Você verá um novo bloco intitulado **Implantando a implantação de modelo**. A criação de um cluster demora cerca de 20 minutos. Após a criação do cluster, você pode clicar na folha do cluster no portal para abri-la.

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Próximas etapas

* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](hdinsight-domain-joined-run-hive.md).
* Para usar o SSH para se conectar a clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

