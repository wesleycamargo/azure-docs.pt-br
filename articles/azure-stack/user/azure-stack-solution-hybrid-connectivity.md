---
title: Configurar a conectividade de nuvem híbrida com o Azure e o Azure Stack | Microsoft Docs
description: Saiba como configurar a conectividade de nuvem híbrida com o Azure e o Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 84e952514b88620af565cf7e9775c055476fb530
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247554"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: configurar a conectividade de nuvem híbrida com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode acessar os recursos com segurança no Azure global e o Azure Stack usando o padrão de conectividade híbrida.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Manter os dados locais para atender aos requisitos regulamentares ou privacidade, mas tem acesso aos recursos do Azure globais.
> - Manter um sistema herdado ao usar a implantação de aplicativos de escala de nuvem e recursos no Azure global.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar híbrido aplicativos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.


## <a name="prerequisites"></a>Pré-requisitos

Alguns componentes são necessários para criar uma implantação de conectividade híbrida. Alguns desses componentes levará tempo para se preparar, portanto será necessário planejar adequadamente.

**Azure Stack**

Um parceiro de OEM/Hardware do Azure pode implantar uma pilha do Azure de produção, e todos os usuários podem implantar um Kit de desenvolvimento do Azure Stack (ASDK).

**Componentes de pilha do Azure**

Um operador de pilha do Azure deve implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016. Se você já tiver alguns desses componentes, verifique se que eles atendem aos requisitos antes de iniciar este tutorial.

Este tutorial pressupõe que você tenha algum conhecimento básico do Azure e o Azure Stack. Para saber mais antes de iniciar o tutorial, leia os artigos a seguir:

 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Principais conceitos do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se você não tiver uma assinatura do Azure, crie uma  [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  antes de começar.
 - Criar uma [aplicativo Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) no Azure. Anote a URL do aplicativo Web, pois você precisará no tutorial.

### <a name="azure-stack"></a>Azure Stack

 - Usar a Azure Stack de produção ou implantar o Kit de desenvolvimento do Azure Stack de https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Implantando o ASDK pode levar até 7 horas, portanto, planeje adequadamente.

 - Implante [serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) os serviços de PaaS para o Azure Stack.
 - [Criar planos e ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) no ambiente do Azure Stack.
 - [Criar assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente do Azure Stack.

### <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de começar a configurar a conectividade de nuvem híbrida:

 - Você precisa de um endereço de IPv4 público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode ser localizado atrás de NAT.
 - Todos os recursos são implantados no mesmo região/local.

#### <a name="tutorial-example-values"></a>Valores de exemplo do tutorial

Os exemplos neste tutorial usam os valores a seguir. Você pode usar esses valores para criar um ambiente de teste ou consultá-los para uma melhor compreensão sobre os exemplos. Para obter mais informações sobre o Gateway de VPN configurações em geral, consulte [sobre configurações de Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificações de Conexão:

 - **Tipo de VPN**: Baseado em rotas
 - **Tipo de conexão**: Site a Site (IPsec)
 - **Tipo de gateway**: VPN
 - **Nome de conexão do Azure**: Azure-Gateway-AzureStack-S2SGateway (portal preencherá automaticamente esse valor)
 - **Nome de conexão do Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (portal preencherá automaticamente esse valor)
 - **Chave compartilhada**: Qualquer compatível com o hardware VPN, com correspondência de valores nos dois lados da conexão
 - **Assinatura**: Qualquer assinatura preferencial
 - **Grupo de recursos**: Teste-infraestrutura

Endereços IP de rede e sub-rede:

| Conexão do Azure/Azure Stack | NOME | Sub-rede | Endereço IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Gateway de rede Virtual do Azure | Azure-Gateway |  |  |
| Gateway de rede Virtual do Azure Stack | AzureStack-Gateway |  |  |
| IP público do Azure | Azure-GatewayPublicIP |  | Determinado no momento da criação |
| IP público do Azure Stack | AzureStack-GatewayPublicIP |  | Determinado no momento da criação |
| Gateway de rede Local do Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valor IP público do Azure Stack |
| Gateway de rede Local do Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Valor IP público do Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Criar uma rede virtual no global Azure e o Azure Stack

Use as etapas a seguir para criar uma rede virtual por meio do portal. Você pode usá-los [valores de exemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se você estiver usando este artigo como um tutorial. Mas, se você estiver usando este artigo para configurar um ambiente de produção, substitua as configurações de exemplo pelos seus próprios valores.

> [!IMPORTANT]
> Você deve garantir que não há uma sobreposição dos endereços IP no Azure ou Azure Stack espaços de endereço de rede virtual.

Para criar uma rede virtual no Azure:

1. Use seu navegador para conectar-se para o [portal do Azure](http://portal.azure.com/) e entre com sua conta do Azure.
2. Selecione **criar um recurso**. No **pesquisar no marketplace** , insira `virtual network`'. Encontre **rede Virtual** na lista de resultados e, em seguida, selecione **rede Virtual**.
3. Dos **selecione um modelo de implantação** , selecione **Gerenciador de recursos**e, em seguida, selecione **criar**.
4. Na **criar rede virtual**, defina as configurações de rede virtual. Os nomes de campos obrigatórios são prefixados com um asterisco vermelho.  Quando você inserir um valor válido, o asterisco é alterado para uma marca de seleção verde.

Para criar uma rede virtual no Azure Stack:

* Repita as etapas anteriores (1-4) usando o Azure Stack **portal de locatário**.

## <a name="add-a-gateway-subnet"></a>Adicionar uma sub-rede de gateway

Antes de conectar sua rede virtual a um gateway, você precisará criar a sub-rede de gateway para a rede virtual que você deseja se conectar. Os serviços de gateway usam os endereços IP especificados na sub-rede do gateway.

No [portal do Azure](http://portal.azure.com/), navegue até a rede virtual do Gerenciador de recursos onde você deseja criar um gateway de rede virtual.

1. Selecione a rede virtual para abrir o **rede Virtual** página.
2. Na **as configurações**, selecione **sub-redes**.
3. Sobre o **sub-redes** página, selecione **+ subrede de Gateway** para abrir o **Adicionar sub-rede** página.

    ![Adicionar sub-rede de gateway](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. O **nome** da sub-rede será automaticamente preenchida com o valor 'GatewaySubnet'. Esse valor é necessário para o Azure reconhecer a sub-rede como a sub-rede do gateway.
5. Alterar o **intervalo de endereços** valores que são fornecidos para corresponder aos seus requisitos de configuração e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Criar um Gateway de rede Virtual no Azure e o Azure Stack

Use as seguintes etapas para criar um gateway de rede virtual no Azure.

1. No lado esquerdo da página do portal, selecione **+**  e digite 'gateway de rede virtual' no campo de pesquisa.
2. Na **resultados**, selecione **gateway de rede Virtual**.
3. Na **gateway de rede Virtual**, selecione **Create** para abrir o **criar gateway de rede virtual** página.
4. Na **criar gateway de rede virtual**, especifique os valores para seu gateway de rede, conforme mostrado na **valores de exemplo Tutorial**e os seguintes valores adicionais:

    - **SKU**: básico
    - **Rede virtual**: Selecione a rede Virtual que você criou anteriormente. A sub-rede de gateway que você criou é selecionada automaticamente.
    - **Primeira configuração de IP**:  Isso é o IP público do seu Gateway.
        - Selecione **criar configuração de IP de gateway**, isso leva você até a **escolher endereço IP público** página.
        - Selecione **+ criar novo** para abrir o **criar endereço IP público** página.
        - Insira um **nome** para seu endereço IP público. Deixe o SKU como **básicas**e, em seguida, selecione **Okey** para salvar suas alterações.

       > [!Note]
       > Atualmente, o Gateway de VPN só dá suporte a alocação de endereço IP público dinâmico. No entanto, isso não significa que o endereço IP é alterado depois que ele é atribuído ao gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. O redimensionamento, a redefinição ou outras manutenções/atualizações internas para seu gateway de VPN não altere o endereço IP.

4. Verifique se as configurações de gateway.
5. Selecione **Create** para criar o gateway VPN. As configurações de gateway são validadas e o bloco "Gateway de rede Virtual implantado" é mostrado em seu painel.

   >[!Note]
   >A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

    Depois de criar o gateway, você pode ver o endereço IP atribuído a ele observando a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Para obter mais informações sobre o gateway, selecione o dispositivo.

6. Repita as etapas anteriores (1 a 5) em sua implantação do Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Criar o gateway de rede local no Azure e o Azure Stack

O gateway de rede local geralmente se refere ao seu local. Você dar um nome que o Azure ou Azure Stack pode se referir a e, em seguida, especifique o site:

- O endereço IP do dispositivo VPN local que você está criando uma conexão para.
- Os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local.

  >[!Note]
  >Se as alterações de rede local, ou você precisar alterar o endereço IP público para o dispositivo VPN, você pode atualizar facilmente esses valores posteriormente.

1. No portal, selecione **+ criar um recurso**.
2. Na caixa de pesquisa, digite **gateway de rede Local**, em seguida, selecione **Enter** para pesquisar. Isso retornará uma lista de resultados.
3. Selecione **gateway de rede Local**, em seguida, selecione **Create** para abrir o **criar gateway de rede local** página.
4. Na **criar gateway de rede local**, especifique os valores para o gateway de rede local, usando nossa **valores de exemplo Tutorial**. Inclua os seguintes valores adicionais.

    - **Endereço IP**: Isso é o endereço IP público do dispositivo VPN que você deseja que Azure ou Azure Stack para se conectar ao. Especifique um endereço IP público válido que não está protegido por um NAT, para que o Azure pode acessar o endereço. Se você não tiver o endereço IP no momento, você pode usar um valor do exemplo como um espaço reservado, mas você terá de voltar e substitua o espaço reservado com o endereço IP público do dispositivo VPN. Azure não pode se conectar ao dispositivo até que você forneça um endereço válido.
    - **Espaço de endereço**: Isso é o intervalo de endereços da rede que representa essa rede local. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos especificados não se sobrepõem aos intervalos de outras redes em que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. Se você quiser se conectar ao seu site local, não um valor de exemplo, use seus próprios valores.
    - **Definir as configurações BGP**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
    - **Assinatura**: Verifique se a assinatura correta está sendo exibida.
    - **Grupo de Recursos**: Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
    - **Localização**: Selecione o local em que esse objeto será criado. Talvez você queira selecionar que sua rede virtual reside no mesmo local, mas não é necessário fazê-lo.
5. Quando terminar de especificar os valores necessários, selecione **Create** para criar o gateway de rede local.
6. Repita essas etapas (1 a 5) em sua implantação do Azure Stack.

## <a name="configure-your-connection"></a>Configurar sua conexão

As conexões Site a Site para uma rede local exigem um dispositivo VPN. O dispositivo VPN que você configure é conhecido como uma Conexão. Para configurar sua Conexão, você precisa:

- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
- O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway VPN usando o portal do Azure, navegue até gateways de rede Virtual, em seguida, selecione o nome do seu gateway.

Use as etapas a seguir para criar uma conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Pesquise **conexões**.
3. Na **resultados**, selecione **conexões**.
4. Na **Conexão**, selecione **criar**.
5. Na **criar Conexão**, defina as seguintes configurações:

    - **Tipo de conexão**: Selecione o Site a site (IPSec).
    - **Grupo de Recursos**: Selecione o grupo de recursos de teste.
    - **Gateway de rede virtual**: Selecione o gateway de rede Virtual que você criou.
    - **Gateway de rede local**: Selecione o Gateway de rede Local que você criou.
    - **Nome da Conexão**: Isso é preenchido automaticamente usando os valores de dois gateways.
    - **Chave compartilhada:**  Esse valor deve corresponder ao valor que você está usando para seu dispositivo VPN no local. Exemplo do tutorial usa "abc123", mas você pode (e deve) usar algo mais complexo. O importante é que esse valor deve ser o mesmo valor que você especifica ao configurar seu dispositivo VPN.
    - Os valores para **Assinatura**, **Grupo de recursos** e **Local** são fixos.

6. Selecione **Okey** para criar sua conexão.

Você pode ver a conexão na **conexões** página do gateway de rede virtual. O status será alterado de *desconhecido* à *conectando*e, em seguida, para *Succeeded*.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
