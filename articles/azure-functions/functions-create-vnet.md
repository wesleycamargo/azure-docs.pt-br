---
title: Integrar o Azure Functions com uma rede virtual do Azure
description: Um tutorial passo a passo que mostra como se conectar a uma função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002760"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrar um aplicativo de funções com uma rede virtual do Azure

Este tutorial mostra como usar o Azure Functions para conectar-se aos recursos em uma rede virtual do Azure.

Para este tutorial, implantaremos um site do WordPress em uma VM em uma rede virtual que não está acessível pela internet. Em seguida, implantaremos uma função com acesso à internet e a rede virtual. Vamos usar essa função para acessar os recursos do site do WordPress implantado dentro da rede virtual.

Para obter mais informações sobre como funciona o sistema, solução de problemas e configuração avançada, consulte [integrar seu aplicativo com uma rede virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). O Azure functions no plano Premium têm os mesmos recursos de hospedagem dos aplicativos web, para que todas as funcionalidades e limitações neste artigo se aplicam às funções.

## <a name="topology"></a>Topologia

 ![Interface do usuário para integração da rede virtual][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar uma máquina virtual dentro de uma rede virtual

Para começar, vamos criar uma VM pré-configurada que executa o WordPress em uma rede virtual. 

Escolhemos o WordPress em uma máquina virtual, porque ele é um dos recursos menos caros que podem ser implantados em uma rede virtual. Observe que esse cenário também pode trabalhar com qualquer recurso em uma rede virtual, como APIs REST, ambientes de serviço de aplicativo e outros serviços do Azure.

1. Vá para o portal do Azure.
2. Adicione um novo recurso, abrindo o **criar um recurso** folha.
3. Pesquise por "[WordPress LEMP7 Max Performance no CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e abrir sua folha de criação. 
4. Sobre o **Noções básicas sobre** guia, configure a VM com as seguintes informações:
    1. Crie um novo grupo de recursos para esta VM fazer a limpeza de todos os recursos mais fácil no final do tutorial. Aqui, estamos usando "Função-VNET-Tutorial" como um exemplo.
    1. Dê um nome exclusivo à máquina virtual. Estamos usando "Rede virtual-Wordpress" como um exemplo.
    1. Selecione a região mais próxima de você.
    1. Selecione o tamanho como B1s (1 vCPU e 1 GB de memória).
    1. Para a conta de administrador, escolha a autenticação de senha e insira um nome de usuário exclusivo e uma senha. Para este tutorial, você não precisará entrar para a VM, a menos que você precise solucionar.
    
        ![Guia de Noções básicas para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

1. Mover para o **rede** guia e insira as seguintes informações:
    1.  Crie uma nova rede virtual.
    1.  Insira um intervalo de endereço privado e uma sub-rede dentro desse intervalo de endereço. O tamanho da sub-rede determinará quantas VMs você pode usar no plano do Serviço de Aplicativo. Se o endereçamento IP e sub-redes é novidade para você, há uma [documento que aborda os conceitos básicos](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Divisão em sub-redes e endereçamento IP são importantes nesse cenário, portanto, é recomendável que você leia alguns artigos e assista a alguns vídeos online até que ele faz sentido. 
    
        Neste exemplo, podemos está optando por usar a rede 10.10.0.0/16 com uma sub-rede de 10.10.1.0/24. Estamos excesso de provisionamento e usando um /16 sub-rede porque é fácil calcular quais sub-redes estão disponíveis na rede 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Novamente o **Networking** guia, defina o IP público como **None**. Esta etapa será implantar a VM com acesso somente na rede virtual.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Crie a VM. O processo levará cerca de 5 minutos.
8. Depois que a VM é criada, vá para seu **rede** guia e anote o endereço IP privado para uso posterior. A máquina virtual não deve ter um IP público.

    ![14]

Agora você tem um site do WordPress implantado inteiramente dentro de sua rede virtual. Esse site não é acessível pela internet pública.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar um aplicativo de funções em um plano Premium

A próxima etapa é criar um aplicativo de funções em um plano Premium. Um plano Premium traz escala sem servidor com todos os benefícios de um plano do serviço de aplicativo dedicado. Aplicativos de funções criados por meio do plano de consumo não dão suporte a integração da rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Conectar seu aplicativo de funções à sua rede virtual

Com um site do WordPress que hospedam arquivos de dentro de sua rede virtual, agora você pode conectar o aplicativo de funções na rede virtual.

1.  No portal do aplicativo de funções da etapa anterior, selecione **recursos da plataforma**. Em seguida, selecione **rede**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selecione **clique aqui para configurar** sob **integração VNet**.

    ![Status de configuração de um recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de rede virtual, selecione **Adicionar rede virtual (versão prévia)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Crie uma nova sub-rede para sua função e o plano de serviço de aplicativo para usar. Observe que o tamanho da sub-rede restringirá o número total de VMs que você pode adicionar ao seu plano de serviço de aplicativo. Sua rede virtual roteará o tráfego entre as sub-redes na sua rede virtual, assim, não importa o que sua função está em uma sub-rede diferente da sua VM automaticamente. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Criar uma função que acessa um recurso em sua rede virtual

O aplicativo de função agora pode acessar a rede virtual com o nosso site de WordPress. Então, vamos usar a função para acessar esse arquivo e servi-lo de volta para o usuário. Neste exemplo, vamos usar um site de WordPress como um proxy e a API como a função de chamada porque eles são mais fácil de configurar e visualizar. 

Você pode facilmente usar qualquer outra API implantada em uma rede virtual. Você também pode usar outra função com o código que faz chamadas de API para a API implantada em sua rede virtual. Uma instância do SQL Server implantada em sua rede virtual é um exemplo perfeito.

1. No portal, abra o aplicativo de função da etapa anterior.
1. Criar um proxy, selecionando **Proxies** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configure o nome de proxy e a rota. Este exemplo usa "/ planta" como uma rota.
1. Preencha o IP do seu site de WordPress anterior e defina **URL de back-end** para `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Agora, se você tentar visitar a URL do back-end diretamente, colando-os em uma nova guia do navegador, a página deve atingir o tempo limite. Isso ocorre porque o seu site do WordPress está conectado à sua rede virtual e não pela internet. Se você colar a URL do proxy no navegador, você deve ver uma imagem de fábrica (extraída do site do WordPress) dentro de sua rede virtual. 

Seu aplicativo de funções está conectado à internet e sua rede virtual. O proxy está recebendo uma solicitação pela internet pública e, em seguida, agindo como um proxy HTTP simples para encaminhar a solicitação ao longo na rede virtual. O proxy retransmite, em seguida, a resposta de volta para você pela internet pública. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Próximos passos

Funções em execução em um plano Premium compartilham a mesma infraestrutura subjacente do serviço de aplicativo como aplicativos web em planos de PremiumV2. Toda a documentação para aplicativos web se aplica a suas funções de plano Premium.

* [Saiba mais sobre as opções de rede em funções](./functions-networking-options.md)
* [Leia as perguntas frequentes sobre o acesso à rede de funções](./functions-networking-faq.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controle com ambientes do serviço de aplicativo](../app-service/environment/intro.md)
* [Conectar-se aos recursos individuais locais sem alterações de firewall usando conexões híbridas](../app-service/app-service-hybrid-connections.md)
* [Saiba mais sobre Proxies de funções](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
