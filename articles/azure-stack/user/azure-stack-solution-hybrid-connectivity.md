---
title: Configurar a conectividade de nuvem híbrida com o Azure e a pilha do Azure | Microsoft Docs
description: Saiba como configurar a conectividade de nuvem híbrida com o Azure e a pilha do Azure.
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
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605188"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: configurar a conectividade de nuvem híbrida com o Azure e a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode acessar os recursos com segurança no Azure global e a pilha do Azure usando o padrão de conectividade híbrida.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Manter os dados no local para atender aos requisitos normativos e privacidade, mas têm acesso aos recursos do Azure global.
> - Manter um sistema herdado durante o uso de recursos e implantação de aplicativos de escala de nuvem no Azure global.

## <a name="prerequisites"></a>Pré-requisitos

Alguns componentes são necessários para criar uma implantação de conectividade híbrida. Alguns desses componentes levará tempo para se preparar, portanto você precisa planejar adequadamente.

**Azure Stack**

Um parceiro de OEM/Hardware Azure possa implantar uma pilha do Azure de produção, e todos os usuários podem implantar um Kit de desenvolvimento de pilha do Azure (ASDK).

**Componentes da pilha do Azure**

Um operador de pilha do Azure deve implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016. Se você já tiver alguns desses componentes, verifique se que eles atenderem aos requisitos antes de iniciar este tutorial.

Este tutorial presume que você tenha conhecimentos básicos do Azure e a pilha do Azure. Para saber mais, antes de iniciar o tutorial, leia os seguintes artigos:

 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Principais conceitos de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 - Criar um [aplicativo da Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) no Azure. Anote a URL do aplicativo Web, pois você precisará no tutorial.

### <a name="azure-stack"></a>Azure Stack

 - Usar a pilha do Azure de produção ou implantar o Kit de desenvolvimento de pilha do Azure de https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Implantando o ASDK pode levar até 7 horas, por isso, planeje adequadamente.

 - Implantar [do serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS a pilha do Azure.
 - [Criar planos e ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) no ambiente de pilha do Azure.
 - [Criar assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente de pilha do Azure.

### <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de começar a configurar a conectividade de nuvem híbrida:

 - É necessário um endereço de IPv4 público externamente para seu dispositivo VPN. Esse endereço IP não pode ser localizado atrás de um NAT.
 - Todos os recursos são implantados no mesmo região/local.

#### <a name="tutorial-example-values"></a>Valores de exemplo do tutorial

Os exemplos neste tutorial usam os seguintes valores. Você pode usar esses valores para criar um ambiente de teste ou consultá-los para uma melhor compreensão dos exemplos. Para obter mais informações sobre configurações de Gateway de VPN em geral, confira [Sobre as configurações de Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificações de Conexão:

 - **Tipo de VPN:** baseada em rota
 - **Tipo de Conexão**: Site a site (IPsec)
 - **Tipo de gateway:** VPN
 - **Nome da conexão do Azure**: Azure-Gateway-AzureStack-S2SGateway (o portal será preencher automaticamente esse valor)
 - **Nome da conexão do Azure pilha**: AzureStack-Gateway-Azure-S2SGateway (o portal será preencher automaticamente esse valor)
 - **Chave compartilhada**: qualquer compatível com o hardware VPN, com valores em ambos os lados da conexão correspondentes
 - **Assinatura**: qualquer preferencial de assinatura
 - **Grupo de recursos**: teste-infraestrutura

Endereços IP de rede e sub-rede:

| Conexão de pilha do Azure/Azure | NOME | Sub-rede | Endereço IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Rede virtual do Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Redes de pilha do Azure | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Gateway de rede Virtual do Azure | Gateway do Azure |  |  |
| Gateway de rede Virtual da pilha do Azure | Gateway AzureStack |  |  |
| IP público do Azure | GatewayPublicIP do Azure |  | Determinada no momento da criação |
| IP público da pilha do Azure | AzureStack GatewayPublicIP |  | Determinada no momento da criação |
| Gateway de rede Local do Azure | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Valor IP público de pilha do Azure |
| Gateway de rede Local da pilha do Azure | S2SGateway do Azure<br>10.100.102.0/23 |  | Valor IP público do Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Criar uma rede virtual no global do Azure e na pilha do Azure

Use as etapas a seguir para criar uma rede virtual usando o portal. Você pode usá-los [valores de exemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se você estiver usando este artigo como um tutorial. Porém, se você estiver usando este artigo para configurar um ambiente de produção, substituir as configurações de exemplo com seus próprios valores.

> [!IMPORTANT]
> Certifique-se de que não existe uma sobreposição de endereços IP em espaços de endereço de rede virtual do Azure ou a pilha do Azure.

Para criar uma rede virtual no Azure:

1. Use seu navegador para conectar o [portal do Azure](http://portal.azure.com/) e entre com sua conta do Azure.
2. Selecione **criar um recurso**. No **pesquisar no marketplace** digite `virtual network`'. Localizar **rede Virtual** na lista de resultados e, em seguida, selecione **rede Virtual**.
3. Do **selecionar um modelo de implantação** , selecione **Gerenciador de recursos de**e, em seguida, selecione **criar**.
4. Em **criar rede virtual**, defina as configurações de rede virtual. Os nomes de campos obrigatórios são prefixados com um asterisco vermelho.  Quando você inserir um valor válido, o asterisco muda para uma marca de seleção verde.

Para criar uma rede virtual na pilha do Azure:

* Repita as etapas anteriores (1-4) usando a pilha do Azure **portal locatário**.

## <a name="add-a-gateway-subnet"></a>Adicionar uma sub-rede de gateway

Antes de conectar sua rede virtual a um gateway, você precisa criar a sub-rede de gateway para a rede virtual que você deseja se conectar. Os serviços de gateway usam os endereços IP que você especificar na sub-rede do gateway.

No [portal do Azure](http://portal.azure.com/), navegue até a rede virtual do Gerenciador de recursos onde você deseja criar um gateway de rede virtual.

1. Selecione a rede virtual para abrir o **rede Virtual** página.
2. Em **configurações**, selecione **sub-redes**.
3. Sobre o **sub-redes** página, selecione **+ sub-rede de Gateway** para abrir o **Adicionar sub-rede** página.

    ![Adicionar sub-rede de gateway](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. O **nome** para a sub-rede é automaticamente preenchida com o valor 'GatewaySubnet'. Esse valor é necessário para o Azure reconheça a sub-rede como a sub-rede de gateway.
5. Alterar o **um intervalo de endereços** valores que são fornecidos para atender aos seus requisitos de configuração e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Criar um Gateway de rede Virtual no Azure e o Azure pilha

Use as etapas a seguir para criar um gateway de rede virtual no Azure.

1. No lado esquerdo da página do portal, selecione **+** e digite 'gateway de rede virtual' no campo de pesquisa.
2. Em **resultados**, selecione **gateway de rede Virtual**.
3. Em **gateway de rede Virtual**, selecione **criar** para abrir o **criar gateway de rede virtual** página.
4. Em **criar gateway de rede virtual**, especifique os valores para o gateway de rede, conforme mostrado no **valores de exemplo do Tutorial**e os seguintes valores adicionais:

    - **SKU**: básico
    - **Rede virtual**: selecione a rede Virtual que você criou anteriormente. A sub-rede de gateway que você criou é selecionada automaticamente.
    - **Configuração de IP primeiro**: Este é o IP público do seu Gateway.
        - Selecione **criar configuração de IP do gateway**, que leva você para a **escolher endereço IP público** página.
        - Selecione **+ criar novo** para abrir o **criar endereço IP público** página.
        - Insira um **nome** para seu endereço IP público. Deixe o SKU como **básica**e, em seguida, selecione **Okey** para salvar suas alterações.

       > [!Note]
       > No momento, o Gateway VPN só dá suporte a alocação de endereços IP públicos dinâmicos. No entanto, isso não significa que o endereço IP alterado depois que ele é atribuído ao seu gateway VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Redimensionando, redefinir ou outros manutenção/atualizações internas para o gateway VPN não altere o endereço IP.

4. Verifique as configurações de gateway.
5. Selecione **criar** para criar o gateway de VPN. As configurações do gateway são validadas e o bloco "Gateway de rede Virtual de implantação" é mostrado no painel.

   >[!Note]
   >A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

    Depois de criar o gateway, você pode ver o endereço IP atribuído a ele, observando a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Para obter mais informações sobre o gateway, selecione o dispositivo.

6. Repita as etapas anteriores (1-5) na sua implantação de pilha do Azure.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Criar o gateway de rede local no Azure e a pilha do Azure

O gateway de rede local geralmente se refere ao seu local. Você dar um nome que o Azure ou Azure pilha pode se referir a e, em seguida, especifique o site:

- O endereço IP do dispositivo VPN local que você está criando uma conexão para.
- Os prefixos de endereço IP que serão roteados por meio do gateway VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local.

  >[!Note]
  >Se as alterações de rede local ou você precisar alterar o endereço IP público para o dispositivo VPN, você pode atualizar facilmente esses valores posteriormente.

1. No portal, selecione **+ criar um recurso**.
2. Na caixa de pesquisa, digite **gateway de rede Local**, em seguida, selecione **Enter** para pesquisar. Isso retornará uma lista de resultados.
3. Selecione **gateway de rede Local**, em seguida, selecione **criar** para abrir o **criar gateway de rede local** página.
4. Em **criar gateway de rede local**, especifique os valores para o gateway de rede local, usando nosso **valores de exemplo do Tutorial**. Inclua os seguintes valores adicionais.

    - **Endereço IP**: Este é o endereço IP público do dispositivo VPN que você deseja Azure ou a pilha do Azure para se conectar ao. Especifique um endereço IP público válido que não esteja protegido por um NAT para que o Azure pode alcançar o endereço. Se você não tiver o endereço IP no momento, você pode usar um valor do exemplo como um espaço reservado, mas você precisará voltar e substitua o espaço reservado com o endereço IP público de seu dispositivo VPN. Azure não pode se conectar ao dispositivo até que você forneça um endereço válido.
    - **Espaço de endereço**: Este é o intervalo de endereços para a rede que representa esta rede local. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos que você especificar não se sobrepõem com intervalos de outras redes que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. Use seus próprios valores se você deseja se conectar ao site local, não um valor de exemplo.
    - **Configurar o BGP**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
    - **Assinatura**: Verifique se que a assinatura correta está sendo exibida.
    - **Grupo de recursos**: selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
    - **Local**: selecione o local em que esse objeto será criado no. Talvez você queira selecionar que sua rede virtual reside no mesmo local, mas você não precisa fazer isso.
5. Quando você terminar de especificar os valores necessários, selecione **criar** para criar o gateway de rede local.
6. Repita essas etapas (1-5) na sua implantação de pilha do Azure.

## <a name="configure-your-connection"></a>Configurar sua conexão

As conexões Site a Site para uma rede local exigem um dispositivo VPN. O dispositivo VPN que você configura é conhecido como uma Conexão. Para configurar sua Conexão, você precisa:

- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
- O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway VPN usando o portal do Azure, navegue até os gateways de rede Virtual e selecione o nome do seu gateway.

Use as etapas a seguir para criar uma conexão VPN Site a Site entre o gateway de rede virtual e seu dispositivo VPN local.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Procurar **conexões**.
3. Em **resultados**, selecione **conexões**.
4. Em **Conexão**, selecione **criar**.
5. Em **criar Conexão**, defina as seguintes configurações:

    - **Tipo de Conexão**: selecione o Site a site (IPSec).
    - **Grupo de recursos**: selecione o grupo de recursos de teste.
    - **Gateway de rede virtual**: selecione o gateway de rede Virtual que você criou.
    - **Gateway de rede local**: selecione o Gateway de rede Local que você criou.
    - **Nome da Conexão**: esse campo é automaticamente preenchido usando os valores de dois gateways.
    - **Chave compartilhada**: esse valor deve corresponder ao valor que você está usando para o dispositivo VPN local no local. O exemplo de tutorial usa 'abc123', mas você pode (e deve) usar algo mais complexas. O importante é que este valor deve ser o mesmo valor que você especifica ao configurar seu dispositivo VPN.
    - Os valores para **Assinatura**, **Grupo de recursos** e **Local** são fixos.

6. Selecione **Okey** para criar sua conexão.

Você pode ver a conexão no **conexões** página do gateway de rede virtual. O status será alterado de *desconhecido* para *conectando*e, em seguida, *êxito*.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
