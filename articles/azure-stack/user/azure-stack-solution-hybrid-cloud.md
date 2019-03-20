---
title: Implantar uma nuvem híbrida com o Azure e o Azure Stack | Microsoft Docs
description: Saiba como implantar uma nuvem híbrida com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 336a2a3fd98f7829694eb095ff2646d9d361afd3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097313"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Tutorial: Implantar uma solução de nuvem híbrida com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este tutorial mostra como implantar uma solução de nuvem híbrida que usa a nuvem pública do Azure e a nuvem privada do Azure Stack.

Ao usar uma solução de nuvem híbrida, você pode combinar os benefícios de conformidade de uma nuvem privada com a escalabilidade da nuvem pública. Além disso, seus desenvolvedores podem tirar proveito do ecossistema de desenvolvedor da Microsoft e aplique suas habilidades para ambientes de nuvem e locais.

## <a name="overview-and-assumptions"></a>Visão geral e suposições

Você pode seguir este tutorial para configurar um fluxo de trabalho que permite aos desenvolvedores implantar um aplicativo web idênticos para uma nuvem pública e uma nuvem privada. Este aplicativo será capaz de acessar uma rede de roteável não Internet hospedada na nuvem privada. Esses aplicativos web são monitorados e quando há um aumento no tráfego, um programa modifica os registros DNS para redirecionar o tráfego para a nuvem pública. Quando o tráfego atinge o nível antes do pico, o tráfego é roteado para a nuvem privada.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> - Implante um servidor de banco de dados do SQL Server conectado híbrido.
> - Conecte-se um aplicativo web no Azure global para uma rede híbrida.
> - Configure o DNS para o dimensionamento da nuvem.
> - Configure certificados SSL para o dimensionamento da nuvem.
> - Configurar e implantar o aplicativo web.
> - Criar um perfil do Gerenciador de tráfego e configurá-lo para o dimensionamento da nuvem.
> - Configure o Application Insights, monitoramento e alertas para o aumento do tráfego.
> - Configure o tráfego automático, alternando entre global do Azure e o Azure Stack.

### <a name="assumptions"></a>Suposições

Este tutorial pressupõe que você tenha um conhecimento básico do Azure global e o Azure Stack. Se você quiser saber mais antes de iniciar o tutorial, consulte estes artigos:

 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Principais conceitos do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Este tutorial também pressupõe que você tenha uma assinatura do Azure. Se você não tiver uma assinatura, você poderá [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, certifique-se de que você pode atender aos seguintes requisitos:

- Um Kit de desenvolvimento do Azure Stack (ASDK) ou uma assinatura em um sistema integrado do Azure Stack. Para implantar um Kit de desenvolvimento do Azure Stack, siga as instruções em [implantar o ASDK usando o instalador](../asdk/asdk-deploy.md).
- A instalação do Azure Stack deve ter o seguinte instalado:
  - O serviço de aplicativo do Azure. Trabalhar com seu operador de pilha do Azure para implantar e configurar o serviço de aplicativo do Azure em seu ambiente. Este tutorial requer o serviço de aplicativo tem a função de trabalho dedicada disponível pelo menos um (1).
  - Uma imagem do Windows Server 2016
  - Um Windows Server 2016 com uma imagem do Microsoft SQL Server
  - Os planos apropriados e as ofertas
  - Um nome de domínio para seu aplicativo web. Se você não tiver um nome de domínio, você poderá comprar um de um provedor de domínio, como GoDaddy, Bluehost e InMotion.
- Um certificado SSL para seu domínio de uma autoridade de certificação confiável como LetsEncrypt.
- Um aplicativo web que se comunica com um banco de dados do SQL Server e dá suporte ao Application Insights. Você pode baixar o [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) aplicativo de exemplo do GitHub.
- Uma rede híbrido entre uma rede virtual do Azure e a rede virtual do Azure Stack. Para obter instruções detalhadas, consulte [configurar a conectividade de nuvem híbrida com o Azure e o Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Um pipeline CI/CD (implantação) / integração contínua híbrida com um agente de compilação particular no Azure Stack. Para obter instruções detalhadas, consulte [configurar identidade de nuvem híbrida com aplicativos do Azure e o Azure Stack](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Implantar um servidor de banco de dados do SQL Server híbrida-conectada

1. Inscreva-se ao portal do usuário do Azure Stack.

2. Sobre o **Dashboard**, selecione **Marketplace**.

    ![Azure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. Na **Marketplace**, selecione **de computação**e, em seguida, escolha **mais**. Sob **mais**, selecione o **licença gratuita do SQL Server: O desenvolvedor do SQL Server 2017 no Windows Server** imagem.

    ![Selecione uma imagem de máquina virtual](media/azure-stack-solution-hybrid-cloud/image2.png)

4. Em **livre de licença do SQL Server: O desenvolvedor do SQL Server 2017 no Windows Server** selecionar **criar**.

5. Na **Noções básicas > definir as configurações básicas**, forneça um **nome** para a máquina virtual (VM), uma **nome de usuário** para o SA do SQL Server e um **senha** para SA.  Dos **assinatura** lista suspensa, selecione a assinatura que você está implantando. Para **grupo de recursos**, use **escolher existente** e colocar a VM no mesmo grupo de recursos do aplicativo web do Azure Stack.

    ![Definir as configurações básicas para a VM](media/azure-stack-solution-hybrid-cloud/image3.png)

6. Sob **tamanho**, escolhe um tamanho para sua VM. Para este tutorial, recomendamos A2_Standard ou um DS2_V2_Standard.

7. Sob **Configurações > configurar recursos opcionais**, defina as seguintes configurações:

   - **Conta de armazenamento**. Crie uma nova conta se você precisar de um.
   - **Rede virtual**

     > [!Important]  
     > Verifique se que sua VM do SQL Server é implantado na mesma rede virtual que os gateways de VPN.

   - **Endereço IP público**. Você pode usar as configurações padrão.
   - **Grupo de segurança de rede** (NSG). Crie um novo NSG.
   - **Extensões e monitoramento**. Mantenha as configurações padrão.
   - **Conta de armazenamento de diagnóstico**. Crie uma nova conta se você precisar de um.
   - Selecione **Okey** para salvar sua configuração.

     ![Configurar os recursos opcionais](media/azure-stack-solution-hybrid-cloud/image4.png)

1. Sob **configurações do SQL Server**, defina as seguintes configurações:
   - Para **conectividade SQL**, escolha **pública (Internet)**.
   - Para **porta**, mantenha o padrão **1433**.
   - Para **autenticação do SQL**, selecione **habilitar**.

     > [!Note]  
     > Quando você habilita a autenticação do SQL, ele deve preencher automaticamente com as informações de "SQLAdmin" que você configurou no **Noções básicas de**.

   - Para o restante das configurações, mantenha os padrões. Selecione **OK**.

     ![Definir as configurações do SQL Server](media/azure-stack-solution-hybrid-cloud/image5.png)

9. Na **resumo**, examine a configuração de máquina virtual e, em seguida, selecione **Okey** para iniciar a implantação.

    ![Resumo da configuração](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Levará algum tempo para criar a nova VM. Você pode exibir o STATUS de suas VMs em **máquinas virtuais**.

    ![Máquinas virtuais](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Criar aplicativos web no Azure e o Azure Stack

O serviço de aplicativo do Azure simplifica a executar e gerenciar um aplicativo web. Como o Azure Stack é consistente com o Azure, o serviço de aplicativo pode executar em ambos os ambientes. Você usará o serviço de aplicativo para hospedar seu aplicativo.

### <a name="create-web-apps"></a>Crie aplicativos web

1. Criar um aplicativo web no Azure, seguindo as instruções em [gerenciar um plano do serviço de aplicativo no Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Verifique se que você colocar o aplicativo web na mesma assinatura e grupo de recursos como sua rede híbrida.

2. Repita a etapa anterior (1) no Azure Stack.

### <a name="add-route-for-azure-stack"></a>Adicionar rota para o Azure Stack

O serviço de aplicativo no Azure Stack deve ser roteável pela Internet pública para permitir aos usuários acessar seu aplicativo. Se o Azure Stack é acessível pela Internet, anote o endereço IP público ou a URL do aplicativo web do Azure Stack.

Se você estiver usando um ASDK, você poderá [configurar um mapeamento NAT estático](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) para expor o serviço de aplicativo fora do ambiente virtual.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Conectar-se um aplicativo web no Azure para uma rede híbrida

Para fornecer conectividade entre o front-end no Azure e o banco de dados do SQL Server no Azure Stack, o aplicativo web ser conectado à rede híbrida entre o Azure e o Azure Stack. Para habilitar a conectividade, você precisará:

- Configurar conectividade Ponto a Site
- Configurar o aplicativo Web
- Modificar o gateway de rede local no Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Configurar a rede virtual do Azure para conectividade ponto a site

O gateway de rede virtual no lado do Azure da rede híbrida deve permitir conexões de ponto a site integrar com o serviço de aplicativo do Azure.

1. No Azure, navegue até a página de gateway de rede virtual. Sob **as configurações**, selecione **configuraçãopontoasite**.

    ![Opção de ponto a site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Selecione **configurar agora** configurar o point-to-site.

    ![Iniciar a configuração ponto a site](media/azure-stack-solution-hybrid-cloud/image9.png)

3. No **Point-to-site** configuração de página, insira o intervalo de endereço IP privado que você deseja usar na **pool de endereços**.

   > [!Note]  
   > Certifique-se de que o intervalo especificado não coincide com nenhum dos intervalos de endereços já usados por sub-redes nos componentes do Azure ou Azure Stack globais da rede híbrida.

   Sob **tipo de túnel**, desmarque as **VPN IKEv2**. Selecione **salvar** para concluir a configuração ponto a site.

   ![Configurações de ponto a site](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrar o aplicativo de serviço de aplicativo do Azure com rede híbrida

1. Para conectar o aplicativo para a rede virtual do Azure, siga as instruções em [habilitando a integração de rede virtual](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Navegue até **configurações** para o plano de serviço de aplicativo que hospeda o aplicativo web. Em **Configurações**, selecione **Rede**.

    ![Configurar Rede](media/azure-stack-solution-hybrid-cloud/image11.png)

3. Na **integração de VNET**, selecione **clique aqui para gerenciar**.

    ![Gerenciar integração de rede virtual](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Selecione a rede virtual que você deseja configurar. Sob **ROTEADAS do endereços de IP para VNET**, insira o intervalo de endereços IP para rede virtual do Azure, a VNet de pilha do Azure e os espaços de endereço ponto a site. Selecione **salvar** para validar e salvar essas configurações.

    ![Intervalos de endereços IP para encaminhar](media/azure-stack-solution-hybrid-cloud/image13.png)

Para saber mais sobre como o serviço de aplicativo se integra com redes virtuais do Azure, consulte [integrar seu aplicativo com uma rede Virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Configurar a rede virtual do Azure Stack

O gateway de rede local na rede virtual do Azure Stack precisa ser configurado para rotear o tráfego do intervalo de endereços de ponto para site de serviço de aplicativo.

1. No Azure Stack, navegue até **gateway de rede Local**. Em **Configurações**, escolha **Configuração**.

    ![Opção de configuração de gateway](media/azure-stack-solution-hybrid-cloud/image14.png)

2. Na **espaço de endereço**, insira o intervalo de endereços de ponto a site para o gateway de rede virtual no Azure.l, selecione **salvar** para validar e salvar essa configuração.

    ![Espaço de endereço ponto a site](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Configurar o DNS para o dimensionamento da nuvem

Ao configurar corretamente o DNS para aplicativos de nuvem, os usuários podem acessar as instâncias do Azure e o Azure Stack globais do seu aplicativo web. A configuração do DNS para este tutorial também permite rotear o tráfego do Azure Traffic Manager quando a carga aumenta ou diminui.

Este tutorial usa o DNS do Azure para gerenciar o DNS. (Domínios de serviço de aplicativo não funcionarão).

### <a name="create-subdomains"></a>Crie subdomínios

Como o Gerenciador de tráfego depende de CNAMEs de DNS, um subdomínio é necessário para rotear o tráfego corretamente para pontos de extremidade. Para obter mais informações sobre mapeamento de domínio e registros DNS, consulte [mapear domínios com o Gerenciador de tráfego](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Para o Azure, você criará um subdomínio que os usuários do ponto de extremidade pode usar para acessar seu aplicativo web. Para este tutorial, pode usar **app.northwind.com**, mas você deve personalizar esse valor com base em seu próprio domínio.

Você também precisará criar um subdomínio com um registro a para o ponto de extremidade do Azure Stack. Você pode usar **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Configurar um domínio personalizado no Azure

1. Adicione a **app.northwind.com** nome de host para o aplicativo web do Azure pelo [mapeando um CNAME para o serviço de aplicativo do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Configurar domínios personalizados no Azure Stack

1. Adicione a **azurestack.northwind.com** nome do host para o aplicativo da web do Azure Stack por [mapear um registro para o serviço de aplicativo do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Use o endereço IP roteáveis na Internet para o aplicativo de serviço de aplicativo.

2. Adicione a **app.northwind.com** nome do host para o aplicativo da web do Azure Stack por [mapeando um CNAME para o serviço de aplicativo do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Use o nome do host que você configurou na etapa anterior (1) como o destino para o CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Configurar certificados SSL para o dimensionamento da nuvem

Você precisa garantir que os dados confidenciais coletados pelo seu aplicativo web são seguros em trânsito, entre em repouso no banco de dados SQL.

Você vai configurar seus aplicativos web do Azure e o Azure Stack para usar certificados SSL para todo o tráfego de entrada.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Adicionar o SSL ao Azure e o Azure Stack

Para adicionar o SSL para o Azure:

1. Certifique-se de que o certificado SSL obtido é válido para o subdomínio que você criou. (É okey usar certificados curinga).

2. No Azure, siga as instruções na **preparar seu aplicativo web** e **associar o certificado SSL** seções o [associar um certificado SSL personalizado existente a aplicativos Web do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artigos. Selecione **SSL baseado em SNI** como o **SSL tipo**.

3. Redirecione todo o tráfego para a porta HTTPS. Siga as instruções na **impor HTTPS** seção o [associar um certificado SSL personalizado existente a aplicativos Web do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artigo.

Para adicionar o SSL para o Azure Stack:

- Repita as etapas 1 a 3, o que você usou para o Azure.

## <a name="configure-and-deploy-the-web-application"></a>Configurar e implantar o aplicativo web

Você vai configurar o código do aplicativo para relatar a telemetria para a instância correta do Application Insights e configurar os aplicativos da web com as cadeias de caracteres de conexão certa. Para saber mais sobre o Application Insights, consulte [o que é o Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Adicionar Application Insights

1. Abra o aplicativo web no Microsoft Visual Studio.

2. [Adicione o Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) ao seu projeto para transmitir a telemetria que o Application Insights usa para criar alertas quando o tráfego da web aumenta ou diminui.

### <a name="configure-dynamic-connection-strings"></a>Configurar cadeias de conexão dinâmica

Cada instância do aplicativo web usará um método diferente para se conectar ao banco de dados SQL. O aplicativo do Azure usa o endereço IP privado da máquina virtual (VM) do SQL Server e o aplicativo no Azure Stack usa o endereço IP público da VM do SQL Server.

> [!Note]  
> Em um sistema integrado do Stack do Azure, o endereço IP público não deve ser roteável pela Internet. Em um Azure Stack desenvolvimento ASDK (Kit), o endereço IP público não é roteável fora o ASDK.

Você pode usar variáveis de ambiente do serviço de aplicativo para passar uma cadeia de caracteres de conexão diferentes para cada instância do aplicativo.

1. Abra o aplicativo no Visual Studio.

2. Abra Startup.cs e encontre o bloco de código a seguir:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Substitua o bloco de código anterior com o código a seguir, que usa uma cadeia de caracteres de conexão definida no arquivo appSettings. JSON:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Definir as configurações de aplicativo do serviço de aplicativo

1. Crie cadeias de caracteres de conexão para o Azure e o Azure Stack. As cadeias de caracteres devem ser o mesmo, exceto para os endereços IP que são usados.

2. No Azure e o Azure Stack, adicione a cadeia de caracteres de conexão apropriado [como uma configuração de aplicativo](https://docs.microsoft.com/azure/app-service/web-sites-configure) no aplicativo web, usando `SQLCONNSTR\_` como um prefixo no nome.

3. **Salvar** as configurações do aplicativo web e reinicie o aplicativo.

## <a name="enable-automatic-scaling-in-global-azure"></a>Habilitar o dimensionamento automático no Azure global

Quando você cria seu aplicativo web em um ambiente de serviço de aplicativo é iniciado com uma instância. Automaticamente, você pode escalar horizontalmente para adicionar instâncias para fornecer que mais recursos para seu aplicativo de computação. Da mesma forma, você pode reduzir horizontalmente e reduzir o número de instâncias automaticamente suas necessidades de aplicativo.

> [!Note]  
> Você precisa ter um plano de serviço de aplicativo para configurar a escala horizontal e escala em. Se você não tiver um plano, crie um antes de iniciar as próximas etapas.

### <a name="enable-automatic-scale-out"></a>Habilitar o dimensionamento automático-out

1. No Azure, encontre o plano de serviço de aplicativo para os sites que você deseja escalar horizontalmente e, em seguida, selecione **Scale-out (plano do serviço de aplicativo)**.

    ![Expansão](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Selecione **habilitar o dimensionamento automático**.

    ![Habilitar dimensionamento automático](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Insira um nome para **nome da configuração de dimensionamento automático**. Para o **padrão** regra de dimensionamento automático, selecione **dimensionar com base em uma métrica**. Defina as **limites de instância** para **mínimo: 1**, **máximo: 10**, e **padrão: 1**.

    ![Configure o dimensionamento automático](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Selecione **+ adicionar uma regra**.

5. Na **origem métrica**, selecione **recurso atual**. Use os seguintes critérios e ações para a regra.

**Critérios**

1. Sob **agregação de tempo** selecionar **médio**.

2. Sob **nome da métrica**, selecione **percentual de CPU**.

3. Sob **operador**, selecione **maior do que**.

   - Defina as **limite** à **50**.
   - Defina as **duração** à **10**.

**Ação**

1. Sob **operação**, selecione **aumentar contagem por**.

2. Defina as **contagem de instâncias** à **2**.

3. Defina as **resfriamento** à **5**.

4. Selecione **Adicionar**.

5. Selecione o **+ adicionar uma regra**.

6. Na **origem métrica**, selecione **recurso atual.**

   > [!Note]  
   > O recurso atual conterá o nome do/GUID do seu plano serviço de aplicativo e o **tipo de recurso** e **recursos** listas suspensas serão desabilitadas.

### <a name="enable-automatic-scale-in"></a>Habilitar o dimensionamento automático no

Quando o tráfego diminui, o aplicativo web do Azure automaticamente pode reduzir o número de instâncias ativas para reduzir os custos. Essa ação é menos agressiva de expansão para minimizar o impacto sobre os usuários do aplicativo.

1. Navegue até a **padrão** scale-out de condição, selecione **+ adicionar uma regra**. Use os seguintes critérios e ações para a regra.

**Critérios**

1. Sob **agregação de tempo** selecionar **médio**.

2. Sob **nome da métrica**, selecione **percentual de CPU**.

3. Sob **operador**, selecione **menor que**.

   - Defina as **limite** à **30**.
   - Defina as **duração** à **10**.

**Ação**

1. Sob **operação**, selecione **diminuir contagem por**.

   - Defina as **contagem de instâncias** à **1**.
   - Defina as **resfriamento** à **5**.

2. Selecione **Adicionar**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Criar um perfil do Gerenciador de tráfego e configurar o dimensionamento da nuvem

Você criará um perfil do Gerenciador de tráfego no Azure e, em seguida, configurar pontos de extremidade para habilitar o dimensionamento da nuvem.

### <a name="create-traffic-manager-profile"></a>Criar perfil do Gerenciador de Tráfego

1. Selecione **criar um recurso**
2. Selecione **de rede**
3. Selecione **perfil do Traffic Manager** e configure o seguinte:

   - Na **nome**, insira um nome para seu perfil. Esse nome **deve** ser exclusivo na zona trafficmanager.net e é usado para criar um novo nome DNS (por exemplo, northwindstore.trafficmanager.net).
   - Para **método de roteamento**, selecione o **ponderado**.
   - Para **assinatura**, selecione a assinatura que você deseja criar esse perfil em.
   - Na **grupo de recursos**, crie um novo grupo de recursos para este perfil.
   - Em **Local do grupo de recursos**, selecione o local do grupo de recursos. Essa configuração refere-se para o local do grupo de recursos e não tem impacto sobre o perfil do Gerenciador de tráfego que será implantado globalmente.

4. Selecione **Criar**.

    ![Criar perfil do Gerenciador de Tráfego](media/azure-stack-solution-hybrid-cloud/image19.png)

   Quando a implantação global do seu perfil do Gerenciador de tráfego for concluída, ela é mostrada na lista de recursos para o grupo de recursos que ele foi criado em.

### <a name="add-traffic-manager-endpoints"></a>Adicionar pontos de extremidade do Gerenciador de Tráfego

1. Pesquise o perfil do Gerenciador de tráfego que você criou. (Se você navegar até o grupo de recursos para o perfil, selecione o perfil.)

2. Na **perfil do Gerenciador de tráfego**, em **configurações**, selecione **pontos de extremidade**.

3. Selecione **Adicionar**.

4. Na **Adicionar ponto de extremidade**, use as seguintes configurações para o Azure Stack:

   - Para **tipo**, selecione **ponto de extremidade externo**.
   - Insira um **nome** para esse ponto de extremidade.
   - Para **o nome de domínio totalmente qualificado (FQDN) ou IP** insira a URL externa para seu aplicativo da web do Azure Stack.
   - Para **peso**, mantenha o padrão **1**. Esse peso resulta em todo o tráfego direcionado para esse ponto de extremidade, se ele estiver íntegro.
   - Deixe **adicionar como desabilitado** desmarcada.

5. Selecione **Okey** para salvar o ponto de extremidade do Azure Stack.

Em seguida, você configurará o ponto de extremidade do Azure.

1. Na **perfil do Gerenciador de tráfego**, selecione **pontos de extremidade**.
2. Selecione **+Adicionar**.
3. Na **Adicionar ponto de extremidade**, use as seguintes configurações para o Azure:

   - Para **tipo**, selecione **ponto de extremidade do Azure**.
   - Insira um **nome** para esse ponto de extremidade.
   - Para **tipo de recurso de destino**, selecione **serviço de aplicativo**.
   - Para **recurso de destino**, selecione **escolha um serviço de aplicativo** para ver uma lista de aplicativos Web na mesma assinatura.
   - Em **Recursos**, escolha o Serviço de Aplicativo que deseja adicionar como o primeiro ponto de extremidade.
   - Para **peso**, selecione **2**. Isso resulta em todo o tráfego direcionado para esse ponto de extremidade, se o ponto de extremidade primário não está íntegro, ou você tem um regra/alerta que direciona o tráfego quando disparado novamente.
   - Deixe **adicionar como desabilitado** desmarcada.

4. Selecione **Okey** para salvar o ponto de extremidade do Azure.

Depois que ambos os pontos de extremidade são configurados, eles são listados em **perfil do Gerenciador de tráfego** quando você seleciona **pontos de extremidade**. O exemplo na captura de tela a seguir mostra dois pontos de extremidade, com informações de status e a configuração para cada um deles.

![Pontos de extremidade](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Configurar o Application Insights, monitoramento e alertas

O Azure Application Insights permite monitorar seu aplicativo e enviar alertas com base nas condições que você configurou. Alguns exemplos são: o aplicativo não está disponível, está apresentando falhas ou está mostrando os problemas de desempenho.

Você usará as métricas do Application Insights para criar alertas. Quando disparam esses alertas, seus aplicativos Web instância será automaticamente alternar do Azure Stack para Azure para escalar horizontalmente e, em seguida, de volta para o Azure stack reduzir horizontalmente.

### <a name="create-an-alert-from-metrics"></a>Crie um alerta de métricas

Navegue até o grupo de recursos para este tutorial e, em seguida, selecione a instância do Application Insights para abrir **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Você usará este modo de exibição para criar um alerta de expansão e uma escala no alerta.

### <a name="create-the-scale-out-alert"></a>Criar o alerta de expansão

1. Sob **configurar**, selecione **alertas (clássico)**.
2. Selecione **adicionar alerta de métrica (clássico)**.
3. Na **Adicionar regra**, configure o seguinte:

   - Para **nome**, insira **intermitência para nuvem do Azure**.
   - Um **descrição** é opcional.
   - Sob **fonte**, **alerta sobre**, selecione **métricas**.
   - Sob **critérios**, selecione sua assinatura, o grupo de recursos para seu perfil do Gerenciador de tráfego e o nome do perfil do Gerenciador de tráfego para o recurso.

4. Para **métrica**, selecione **taxa de solicitação**.
5. Para **condição**, selecione **maior do que**.
6. Para **limite**, insira **2**.
7. Para **período**, selecione **nos últimos 5 minutos**.
8. Sob **notificar via**:
   - Marque a caixa de seleção **proprietários, colaboradores e leitores de Email**.
   - Insira seu endereço de email para **email(s) de administrador adicionais**.

9. Na barra de menus, selecione **salvar**.

### <a name="create-the-scale-in-alert"></a>Criar a escala de alerta

1. Sob **configurar**, selecione **alertas (clássico)**.
2. Selecione **adicionar alerta de métrica (clássico)**.
3. Na **Adicionar regra**, configure o seguinte:

   - Para **nome**, insira **escala novamente no Azure Stack**.
   - Um **descrição** é opcional.
   - Sob **fonte**, **alerta sobre**, selecione **métricas**.
   - Sob **critérios**, selecione sua assinatura, o grupo de recursos para seu perfil do Gerenciador de tráfego e o nome do perfil do Gerenciador de tráfego para o recurso.

4. Para **métrica**, selecione **taxa de solicitação**.
5. Para **condição**, selecione **menor que**.
6. Para **limite**, insira **2**.
7. Para **período**, selecione **nos últimos 5 minutos**.
8. Sob **notificar via**:
   - Marque a caixa de seleção **proprietários, colaboradores e leitores de Email**.
   - Insira seu endereço de email para **email(s) de administrador adicionais**.

9. Na barra de menus, selecione **salvar**.

A captura de tela a seguir mostra os alertas para escalar horizontalmente e reduzir horizontalmente.

   ![Alertas (clássico)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Redirecionar o tráfego entre o Azure e o Azure Stack

Você pode configurar manual ou automático alternar de tráfego do seu aplicativo Web alternando entre o Azure e o Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Configurar a troca manual entre o Azure e o Azure Stack

Quando seu site da Web atinge os limites que você configure, você receberá um alerta. Use as etapas a seguir para redirecionar o tráfego do Azure manualmente.

1. No portal do Azure, selecione seu perfil do Gerenciador de tráfego.

    ![Pontos de extremidade do Gerenciador de Tráfego](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Selecione **Pontos de extremidade**.
3. Selecione o **ponto de extremidade do Azure**.
4. Sob **Status** , selecione **Enabled**e, em seguida, selecione **salvar**.

    ![Habilitar ponto de extremidade do Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. Na **pontos de extremidade** para o perfil do Traffic Manager, selecione **ponto de extremidade externo**.
6. Sob **Status** , selecione **desabilitado**e, em seguida, selecione **salvar**.

    ![Desabilitar ponto de extremidade do Azure Stack](media/azure-stack-solution-hybrid-cloud/image24.png)

Depois que os pontos de extremidade são configurados, o tráfego de aplicativo vai para o seu aplicativo web de escala horizontal do Azure em vez do aplicativo da web do Azure Stack.

 ![Pontos de extremidade alterados](media/azure-stack-solution-hybrid-cloud/image25.png)

Para inverter o fluxo de volta para o Azure Stack, use as etapas anteriores para:

- Habilitar o ponto de extremidade do Azure Stack
- Desabilitar o ponto de extremidade do Azure

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Configurar a comutação automática entre o Azure e o Azure Stack

Você também pode usar o monitoramento do Application Insights se seu aplicativo for executado em um [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) ambiente fornecido pelo Azure Functions.

Nesse cenário, você pode configurar o Application Insights para usar um webhook que chama um aplicativo de funções. Este aplicativo automaticamente habilita ou desabilita um ponto de extremidade em resposta a um alerta.

Use as etapas a seguir como um guia para configurar a troca de tráfego automático.

1. Crie um aplicativo de funções do Azure.
2. Crie uma função disparada por HTTP.
3. Importe os SDKs do Azure para o Gerenciador de recursos, aplicativos Web e o Gerenciador de tráfego.
4. Desenvolva o código para:

   - Autenticar sua assinatura do Azure.
   - Use um parâmetro que alterna os pontos de extremidade do Gerenciador de tráfego para direcionar o tráfego para o Azure ou Azure Stack.

5. Salvar seu código e adicione a URL do aplicativo de funções com os parâmetros apropriados para o **Webhook** seção das configurações de regra de alerta do Application Insights.
6. Tráfego será redirecionado automaticamente quando um alerta do Application Insights é acionado.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
