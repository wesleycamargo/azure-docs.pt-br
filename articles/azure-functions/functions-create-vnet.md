---
title: Integrar o Azure Functions a uma Rede Virtual do Azure
description: Um tutorial passo a passo mostrando como se conectar uma Função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 4f27bbeb43bbf373b621d151d68583f0041378b3
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894187"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrar um aplicativo de funções com uma rede virtual do Azure

Este tutorial passo a passo mostra como usar o Azure Functions para conectar-se aos recursos em uma VNET do Azure.

Para este tutorial, implantaremos um site do WordPress em uma VM em uma privada, não-acessível pela internet, rede virtual. Em seguida, implantaremos uma Função com acesso à internet e a rede virtual. Usaremos essa função para acessar os recursos do site do WordPress implantado dentro da VNET.

Para obter mais informações sobre como funciona o sistema, a solução de problemas e a configuração avançada, consulte o documento [Integrar seu aplicativo com uma Rede Virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). O Azure Functions no plano Premium têm os mesmos recursos de hospedagem dos aplicativos web, para que toda a funcionalidade e as limitações desse documento se aplicam às funções também.

## <a name="topology"></a>Topologia

 ![Interface do usuário de Integração VNet][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Criar uma máquina virtual dentro de uma VNET

Para começar, vamos criar uma VM pré-configurada executando o Wordpress dentro de uma rede virtual. 

WordPress em uma máquina virtual foi escolhido porque é um dos recursos menos caros que podem ser implantados dentro de uma VNET. Observe que esse cenário também pode trabalhar com qualquer recurso em uma rede virtual, como APIs REST, ambientes de serviço de aplicativo e outros serviços do Azure.

1. Vá para o portal do Azure
2. Adicione um novo recurso, abrindo a folha "Criar um recurso"
3. Pesquise por "[WordPress LEMP7 Max Performance no CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e abrir sua folha de criação 
4. Na folha de criação, configure a VM com as seguintes informações:
    1. Crie um novo grupo de recursos para esta VM fazer a limpeza de todos os recursos mais fácil no final do tutorial. Nomeei meu grupo de recursos "Função-VNET-Tutorial"
    1. Dê um nome exclusivo à máquina virtual. Eu nomeei o meu como “VNET-Wordpress”
    1. Selecione a região mais próxima de você
    1. Selecione o tamanho como B1s (1 vcpu e 1 GB de memória)
    1. Para a conta de administrador, escolha a autenticação de senha e insira um nome de usuário exclusivo e uma senha. Para este tutorial, você não precisará entrar na VM, a menos que você precise solucionar.
    
        ![Criar o guia de Noções básicas da VM](./media/functions-create-vnet/create-vm-1.png)

1. Move para a guia rede e insira as seguintes informações:
    1.  Criar uma nova rede virtual
    1.  Insira no seu intervalo de endereços privado desejado e uma sub-rede dentro desse intervalo de endereço. O tamanho da sub-rede determinará quantas VMs você pode usar no plano do Serviço de Aplicativo. Se o endereçamento IP e sub-redes são novidades para você, há um documento [abordando os fundamentos básicos](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). O endereçamento IP e as sub-redes são importantes nesse cenário, portanto, recomendo ler alguns artigos e assistir a alguns vídeos online até que faça sentido. 
        1. Neste exemplo, eu optei por usar a rede 10.10.0.0/16 com uma sub-rede de 10.10.1.0/24. Optei por provisionar excessivamente e usar uma sub-rede /16 porque é fácil calcular quais sub-redes estão disponíveis na rede 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Novamente na guia de Rede, defina o IP público como "Nenhum". Isso implantará a VM com acesso somente à rede virtual.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Crie a VM. Isso levará aproximadamente 5 minutos.
8. Depois que a VM é criada, visite sua guia rede e observe o endereço IP privado para uso posterior. A máquina virtual não deve ter um IP público.

    ![14]

Agora, você terá um site do wordpress implantado inteiramente dentro de sua rede virtual. Esse site não é acessível pela internet pública.

## <a name="create-a-premium-plan-function-app"></a>Criar um aplicativo de funções do plano Premium

A próxima etapa é criar um aplicativo de funções em um plano premium. O plano premium é um novo que oferece escala sem servidor com todos os benefícios de um plano de serviço de aplicativo dedicado. Aplicativos de funções do plano de consumo não dão suporte a integração de rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>Conectar seu Aplicativo de funções à sua VNET

Com um site do WordPress que hospedam arquivos de dentro de sua rede virtual, agora você pode conectar o aplicativo de funções para a rede virtual.

1.  No portal do Aplicativo de funções da etapa anterior, selecione **Recursos da Plataforma**, em seguida, selecione **Rede**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selecione **Clique para configurar** na integração da rede virtual

    ![Configurar o status do recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de rede virtual, selecione **Adicionar rede virtual (versão prévia)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Crie uma nova sub-rede para sua Função e o plano do Serviço de Aplicativo para usar. Observe que o tamanho da sub-rede restringirá o número total de VMs que você pode adicionar ao seu plano do serviço de aplicativo. Sua rede virtual automaticamente roteará o tráfego entre as sub-redes na sua rede virtual, portanto, não importa se sua Função estiver em uma sub-rede diferente da sua VM. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Criar uma Função que acessa um recurso em sua VNET

O Aplicativo de funções agora pode acessar a VNET com o nosso site do wordpress, então, vamos usar a função para acessar esse arquivo e servi-lo de volta para o usuário. Para este exemplo usaremos um site de wordpress como a API e um Proxy de Função como as funções de chamada porque são fáceis de configurar e visualizar. Você poderia facilmente usar qualquer outra API implantada em uma rede virtual e outra Função com o código fazendo chamadas de API para a API implantada dentro da sua VNET. Um SQL Server implantado em sua VNET é um exemplo perfeito.

1. No portal, abra o Aplicativo de funções da etapa anterior
1. Criar um Proxy selecionando **Proxies** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configure o nome de Proxy e a rota. Eu escolhi /plant como minha rota.
1. Preencha o IP do seu site de wordpress anterior e defina a URL de back-end como `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Agora, se você tentar visitar a URL do back-end diretamente, colando-os em uma nova guia do navegador, a página deve atingir o tempo limite. Isso é esperado, pois seu site do wordpress está conectado a apenas sua rede virtual e não pela internet. Se você colar a URL de Proxy para o navegador, você deve ver uma imagem de planta, extraída do site do Wordpress dentro da sua VNET. 

Seu Aplicativo de funções está conectado à Internet e sua rede virtual. O proxy está recebendo uma solicitação pela internet pública e, em seguida, agindo como um proxy HTTP simples para encaminhar a solicitação ao longo na rede virtual. O proxy retransmite, em seguida, a resposta de volta para você pela internet pública. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Próximas etapas

Funções em execução em um plano Premium compartilham a mesma infraestrutura subjacente do serviço de aplicativo como aplicativos Web. Isso significa que toda a documentação para aplicativos Web se aplica a suas funções de plano Premium.

1. [Saiba mais sobre a integração de rede virtual com o serviço de aplicativo / funções aqui](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Saiba mais sobre redes virtuais no Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [Habilitar para recursos de rede e de controle com ambientes do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [Conectar-se aos recursos individuais locais sem alterações de firewall usando conexões híbridas](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [Saiba mais sobre Proxies de função](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
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
