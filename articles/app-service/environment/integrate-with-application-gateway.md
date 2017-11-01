---
title: Integrando seu ILB ASE com um Gateway de Aplicativo do Azure
description: "Instruções passo a passo sobre como integrar um aplicativo em seu ILB ASE com o Gateway de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Integrando seu ILB ASE com um Gateway de Aplicativo #

Um [ASE (Ambiente do Serviço de Aplicativo) do Azure](./intro.md) é uma implantação do Serviço de Aplicativo do Azure na sub-rede da Rede Virtual do Azure de um cliente. Ele pode ser implantado com um ponto de extremidade público ou privado para acesso ao aplicativo. A implantação do ASE com um ponto de extremidade privado é chamada um ILB ASE.  
O Gateway de Aplicativo do Azure é um dispositivo virtual que fornece descarregamento de SSL, proteção de WAF e balanceamento de carga de camada 7. Ele pode escutar em um endereço IP público e rotear tráfego para o ponto de extremidade do aplicativo. As informações a seguir descrevem como integrar um Gateway de Aplicativo com WAF configurado a um aplicativo em um ILB ASE.  

A integração do Gateway de Aplicativo com o ILB ASE é em um nível de aplicativo.  Quando você configura o Gateway de Aplicativo com seu ILB ASE, você faz isso para aplicativos específicos no ILB ASE. Isso permite hospedar aplicativos multilocatário seguros em um único ILB ASE.  

![Gateway de Aplicativo apontando para o aplicativo em um ILB ASE][1]

Neste passo a passo, você vai:

* criar um Gateway de Aplicativo
* configurar o Gateway de Aplicativo para apontar para um aplicativo em seu ILB ASE
* configurar seu aplicativo para respeitar o nome de domínio personalizado
* editar o nome do host DNS público que aponta para o Gateway de Aplicativo

Para integrar o Gateway de Aplicativo ao ILB ASE, você precisa de:

* um ILB ASE
* um aplicativo em execução no ILB ASE
* um nome de domínio roteável da Internet a ser usado com seu aplicativo no ILB ASE
* o endereço do ILB usado pelo seu ILB ASE (ele está no portal do ASE em **Configurações -> Endereços IP**)

    ![Endereços IP usados pelo ILB ASE][9]
    
* um nome DNS público que é usado posteriormente para apontar para o Gateway de Aplicativo 

Para obter detalhes sobre como criar um ILB ASE, leia o documento [Criando e usando um ILB ASE][ilbase]

Este guia pressupõe que você deseja um Gateway de Aplicativo na mesma Rede Virtual do Azure em que o ASE é implantado. Antes de iniciar a criação do Gateway de Aplicativo, escolha ou crie uma sub-rede que você usará para hospedar o Gateway de Aplicativo. Você deve usar uma sub-rede diferente daquela nomeada GatewaySubnet e da sub-rede usada pelo ILB ASE.
Se você colocar o Gateway de Aplicativo na GatewaySubnet, não será possível criar um gateway de rede virtual mais tarde. Você também não pode colocá-lo na sub-rede usada por seu ILB ASE, pois o ASE é a única coisa que pode estar na sub-rede dele.

## <a name="steps-to-configure"></a>Etapas para configurar ##

1. De dentro do Portal do Azure, vá para **Novo > Rede > Gateway de Aplicativo** 
    1. Forneça:
        1. Nome do Gateway de Aplicativo
        1. Selecione o WAF
        1. Selecione a mesma assinatura usada para a VNet de ASE
        1. Crie ou selecione o grupo de recursos
        1. Selecione o local no qual a VNet ASE está

    ![Noções básicas de criação de novo Gateway de Aplicativo][2]   
    1. Na área Configurações, defina:
        1. A VNet do ASE
        1. A sub-rede na qual o Gateway de Aplicativo precisa ser implantada. Não use o GatewaySubnet, pois isso impedirá a criação de gateways de VPN
        1. Selecione Público
        1. Selecione um endereço IP público. Se você não tiver um, crie um nesse momento
        1. Configure para HTTP ou HTTPS. Se você configurar para HTTPS, será preciso fornecer um certificado PFX
        1. Selecione as configurações de firewall do aplicativo Web. Aqui você pode habilitar o firewall e também defini-lo para Detecção ou Prevenção, conforme você considerar adequado.

    ![Configurações de criação de novo Gateway de Aplicativo][3]
    
    1. Na análise da seção de resumo, selecione **Ok**. Pode demorar um pouco mais de 30 minutos para concluir a configuração do Gateway de Aplicativo.  

2. Depois de concluir a configuração do Gateway de Aplicativo, vá para o portal do Gateway de Aplicativo. Selecione **pool de back-end**.  Adicione o endereço do ILB ao ILB ASE.

    ![Configurar o pool de back-end][4]

3. Depois que o processamento é concluído para a configuração do pool de back-end, selecione **Sondas de integridade**. Crie um teste de integridade para o nome de domínio que você deseja usar para o aplicativo. 

    ![Configurar investigações da integridade][5]
    
4. Depois que o processamento é concluído para a configuração das investigações de integridade, selecione **Configurações HTTP**.  Edite a configuração existente lá, selecione **Usar investigação personalizada**e selecione o teste que você configurou.

    ![Definir configurações HTTP][6]
    
5. Vá para a **Visão Geral** do Gateway de Aplicativo e copie o endereço IP público usado para o Gateway de Aplicativo.  Defina esse endereço IP como um registro para seu nome de domínio de aplicativo ou use o nome DNS para esse endereço em um registro CNAME.  É mais fácil selecionar o endereço IP público e copiá-lo do endereço IP público da interface do usuário em vez de copiá-lo do link na seção Visão Geral de Gateway de Aplicativo. 

    ![Portal do Gateway de Aplicativo][7]

6. Defina o nome de domínio personalizado para o aplicativo no ILB ASE.  Vá para o aplicativo no portal e, em Configurações, selecione **Domínios personalizados**

![Definir o nome de domínio personalizado no aplicativo][8]

Há informações sobre como definir nomes de domínio personalizados para seus aplicativos Web aqui: [Configurando nomes de domínio personalizados para seu aplicativo Web][custom-domain]. A diferença entre um aplicativo em um ILB ASE e esse documento é que não há nenhuma validação no nome de domínio.  Já que você é proprietário do DNS que gerencia os pontos de extremidade do aplicativo, você pode colocar tudo que quiser lá. O nome de domínio personalizado que você adicionar nesse caso não precisará ser no DNS, mas ainda precisará ser configurado com o aplicativo. 

Depois da instalação ser concluída e você permitir um curto período de tempo para que as alterações ao DNS serem propagadas, você poderá acessar o aplicativo pelo nome de domínio personalizado que você criou. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
