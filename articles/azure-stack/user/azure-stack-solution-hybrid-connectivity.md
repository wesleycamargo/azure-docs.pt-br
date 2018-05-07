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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8b5f59d3fea402efa50bdafd7fc0439a93051e69
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutorial: configurar a conectividade de nuvem híbrida com o Azure e a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode acessar os recursos com segurança no Azure global e a pilha do Azure usando o padrão de conectividade híbrida. 

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Manter os dados no local para privacidade ou requisitos normativos, mas têm acesso aos recursos do Azure global.
> - Manter um sistema herdado durante o uso de recursos e implantação de aplicativos de escala de nuvem no Azure global.

## <a name="prerequisites"></a>Pré-requisitos

Alguns componentes são necessários para criar uma implantação de conectividade híbrida e podem levar algum tempo para se preparar. 

Um parceiro do Azure OEM/Hardware podem implantar uma pilha do Azure de produção, e todos os usuários podem implantar um ASDK. Um operador de pilha do Azure também deve implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016. 

Se você já tiver alguns desses componentes, verifique se que eles atenderem aos requisitos antes de começar.

Este tópico também pressupõe que você tenha algum conhecimento do Azure e a pilha do Azure. Se você quiser saber antes de prosseguir, certifique-se de que comece por estes tópicos:
 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Principais conceitos de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
 - Criar um [aplicativo da Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) no Azure. Anote o novo URL do aplicativo Web, como ele é usado mais tarde.

### <a name="azure-stack"></a>Azure Stack
 - Use a produção de pilha do Azure ou implantar o Kit de desenvolvimento de pilha do Azure com links abaixo:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - A instalação levará algumas horas para ser concluída, por isso, planeje adequadamente.
 - Implantar [do serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS a pilha do Azure. 
 - [Criar plano de ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro do ambiente de pilha do Azure. 
 - [Criar assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente de pilha do Azure. 


### <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

 - Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
 - Certifique-se de que todos os recursos são implantados no mesmo região/local.

#### <a name="example-values"></a>Valores de exemplo

Os exemplos neste artigo usam os seguintes valores. Você pode usar esses valores para criar um ambiente de teste ou consultá-los para entender melhor os exemplos neste artigo. Para obter mais informações sobre configurações de Gateway de VPN em geral, confira [Sobre as configurações de Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Especificações de Conexão:
 - **Tipo de VPN:** baseada em rota
 - **Tipo de Conexão**: Site a site (IPsec)
 - **Tipo de gateway:** VPN
 - **Nome da conexão do Azure**: Azure-Gateway-AzureStack-S2SGateway (o portal será preencher automaticamente esse valor)
 - **Nome da conexão do Azure pilha**: AzureStack-Gateway-Azure-S2SGateway (o portal será preencher automaticamente esse valor)
 - **Chave compartilhada**: qualquer compatível com o hardware VPN, com valores em ambos os lados da conexão correspondentes
 - **Assinatura**: qualquer preferencial de assinatura
 - **Grupo de recursos**: teste-infraestrutura

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

> [!note]  
> Certifique-se de que não há nenhuma sobreposição de IPs em espaços de endereço de rede virtual do Azure ou a pilha do Azure. 

Para criar uma rede virtual no modelo de implantação do Gerenciador de recursos usando o portal do Azure. Use o [valores de exemplo](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) se você estiver usando estas etapas como um tutorial. Se você não estiver executando estas etapas como um tutorial, substitua os valores pelos seus próprios. 

1. Em um navegador, acesse o [Portal do Azure](http://portal.azure.com/) e entre com sua conta do Azure.
2. Clique em **Criar um recurso**. No **pesquisar no marketplace** digite `virtual network`'. Localize **rede Virtual** da lista retornada e abra o **rede Virtual** página.
3. Perto da parte inferior da página de rede Virtual, do **selecionar um modelo de implantação** , selecione **Gerenciador de recursos de**e, em seguida, selecione **criar**. Isso abre a página ‘Criar rede virtual’.
4. Na página **Criar rede virtual**, defina as configurações da VNet. Durante o preenchimento dos campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres digitados no campo forem válidos.
5. Repita essas etapas da **portal locatário** da pilha do Azure.

## <a name="add-a-gateway-subnet"></a>Adicionar uma sub-rede de gateway

Antes de conectar sua Rede Virtual a um gateway, você precisará criar a sub-rede de gateway para a Rede Virtual à qual você deseja se conectar. Os serviços de gateway usam os endereços IP especificados na sub-rede do gateway.

No [portal](http://portal.azure.com/), navegue até a rede virtual do Gerenciador de Recursos para o qual você deseja criar um gateway de rede virtual.

1. No **configurações** seção da página VNet, selecione **sub-redes** para expandir o **sub-redes** página.
2. Sobre o **sub-redes** página, selecione **+ sub-rede de Gateway** para abrir o **Adicionar sub-rede** página.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. O **Nome** da sua sub-rede será automaticamente preenchido com o valor 'GatewaySubnet'. Esse valor é necessário para o Azure reconheça a sub-rede como a sub-rede de gateway. Ajustar o preenchimento automático **um intervalo de endereços** valores para corresponder a seus requisitos de configuração, selecione **Okey** na parte inferior da página para criar a sub-rede.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Criar um Gateway de rede Virtual no Azure e o Azure pilha

1. No lado esquerdo da página do portal, selecione + e digite 'Gateway de rede Virtual' na pesquisa. Em **resultados**, localize e selecione **gateway de rede Virtual**.
2. Na parte inferior do **gateway de rede Virtual** página, selecione **criar**. Isso abre a página **Criar gateway de rede virtual**.
3. Sobre o **criar gateway de rede virtual** , especifique os valores para o gateway de rede virtual, conforme detalhado nos valores de exemplo e os valores adicionais detalhados abaixo.
    - **SKU**: básico
    - **Rede virtual**: selecione a rede Virtual que você criou anteriormente. Além disso, ele automaticamente selecionará a sub-rede de gateway que você criou anteriormente. 
    - **Configuração de IP primeiro**: Este é o IP público do seu Gateway. 
        - Clique o **criar configuração de IP do gateway** e você será levado para a **escolher endereço IP público** página.
        - Clique em **+ criar novo** para abrir o **criar endereço IP público** página.
        - Insira um **nome** para seu endereço IP público. Deixe o SKU como **básica**, em seguida, selecione **Okey** na parte inferior desta página para salvar suas alterações.

    > [!note]  
    > Gateway VPN atualmente suporta apenas a alocação de endereços IP públicos dinâmicos. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

4. Verifique as configurações. 
5. Clique em **Criar** para começar a criar o gateway de VPN. As configurações são validadas, e você verá o bloco "Implantar gateway de rede virtual" no painel. A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

    Depois de criar o gateway, exiba o endereço IP atribuído a ele observando a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Você pode selecionar o dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.
6. Repita essas etapas em sua implantação de pilha do Azure.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Criar o gateway de rede local no Azure e a pilha do Azure

O gateway de rede local geralmente se refere ao seu local. Você atribuir o site um nome pelo qual Azure ou Azure pilha pode se referir a ele, especifique o endereço IP do dispositivo VPN local para o qual você criará uma conexão. Você também pode especificar os prefixos de endereço IP que serão roteados por meio do gateway de VPN para o dispositivo VPN. Os prefixos de endereço que você especifica são os prefixos localizados em sua rede local. Se as alterações de rede local ou se você precisar alterar o endereço IP público para o dispositivo VPN, poderá atualizar facilmente os valores mais tarde.

1. No portal, selecione **+ criar um recurso**.
2. Na caixa de pesquisa, digite **gateway de rede Local**, em seguida, pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **gateway de rede Local**, em seguida, selecione o **criar** para abrir o **criar gateway de rede local** página.
3. Sobre o **Criar página de gateway de rede local**, especifique os valores para o gateway de rede local, conforme detalhado no nosso valores de exemplo e os valores adicionais detalhados abaixo.
    - **Endereço IP**: Este é o endereço IP público do dispositivo VPN que você deseja Azure ou a pilha do Azure para se conectar ao. Especifique um endereço IP público válido. O endereço IP não pode estar por trás do NAT e deve poder ser acessado pelo Azure. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
    - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. Use seus próprios valores aqui se você deseja se conectar ao site local, não os valores mostrados no exemplo.
    - **Configurar o BGP**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
    - **Assinatura**: Verifique se que a assinatura correta está sendo exibida.
    - **Grupo de recursos**: selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
    - **Local**: selecione o local em que esse objeto será criado no. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.
4. Quando tiver terminado de especificar os valores, selecione o **criar** botão na parte inferior da página para criar o gateway de rede local.
5. Repita essas etapas em sua implantação de pilha do Azure.

## <a name="configure-your-connection"></a>Configurar sua conexão

As conexões Site a Site para uma rede local exigem um dispositivo VPN. Nesta etapa, você deve configurar seu dispositivo VPN, conhecido como uma Conexão. Ao configurar sua Conexão, você precisará do seguinte:
    - Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
    - O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway VPN usando o portal do Azure, navegue até os gateways de rede Virtual e selecione o nome do seu gateway.
Crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN local.
1. No portal, selecione **+ criar um recurso**.
2. Na caixa de pesquisa, digite **conexões**, em seguida, pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **conexões**, em seguida, selecione o botão Criar para abrir o **criar conexões** página.
3. Sobre o **criar conexões** página, defina os valores para a conexão.
     - Noções básicas:
        1. **Tipo de Conexão**: selecione o Site a site (IPSec).
        2. **Grupo de recursos**: (selecione o grupo de recursos de teste)
     - Configurações:
        1. **Gateway de rede virtual**: selecione o gateway de rede Virtual que você criou anteriormente.
        2. **Gateway de rede local**: selecione o Gateway de rede Local que você criou anteriormente. 
        3. **Nome da Conexão**: isso automaticamente popular com os valores de dois gateways. 
        4. **Chave compartilhada**: o valor aqui deve corresponder ao valor que você está usando para o dispositivo VPN local no local. O exemplo usa “abc123”, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
    - Os valores restantes para **Assinatura**, **Grupo de Recursos** e **Local** são corrigidos.
4. Clique em **OK** para criar sua conexão. Você verá criar Conexão flash na tela.
5. Você pode exibir a conexão no * * página de conexões de gateway de rede virtual. O status será alterado de **desconhecido** para **conectando**e, em seguida, **êxito**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
