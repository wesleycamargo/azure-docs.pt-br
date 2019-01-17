---
title: Conecte-se, configure e ative o Azure Data Box Gateway no portal do Azure | Microsoft Docs
description: O terceiro tutorial para implantar o Data Box Gateway instrui você a conectar, configurar e ativar seu dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 5e83eb46a4d62c6aaf0862d4bb1aa046c5d64dde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120506"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Tutorial: Conectar, configurar, ativar o Azure Data Box Gateway (Versão Prévia) 

## <a name="introduction"></a>Introdução

Este tutorial descreve como conectar-se, configurar e ativar seu dispositivo Data Box Gateway usando a interface de usuário da web local. 

O processo de configuração e ativação pode levar cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se ao dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


> [!IMPORTANT]
> - O Data Box Gateway está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu Data Box Gateway, verifique se:

* Você provisionou um dispositivo virtual e obteve uma URL conectada a ele, conforme detalhado em [Provisionar um Data Box Gateway no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Provisionar um Data Box Gateway no VMware](data-box-gateway-deploy-provision-vmware.md).
* Você tem a chave de ativação do serviço Data Box Gateway que você criou para gerenciar dispositivos Data Box Gateway. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Abra uma janela do navegador e conecte-se à interface do usuário da Web local. Digite:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Use a URL de conexão observada no tutorial anterior. Você verá um erro indicando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**. (Essas etapas podem ser diferentes com base no navegador usado.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Entrar para o interface do usuário do seu dispositivo virtual da web. A senha padrão é *Senha1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Você será solicitado a alterar a senha do administrador do dispositivo. Digite uma nova senha que contenha entre 8 e 16 caracteres. A senha deve conter 3 dos seguintes: caracteres maiúsculos, minúsculos, numéricos e especiais.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Agora você está no **Dashboard** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
1. No painel, você pode acessar várias configurações necessárias para configurar e registrar o dispositivo virtual com o serviço Data Box Gateway. As **Configurações de rede**, as **Configurações de proxy Web** e as **Configurações de hora** são opcionais. São as únicas configurações obrigatórias **nome do dispositivo** e **configurações de nuvem**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Na página **Nome do dispositivo**, configure um nome amigável para o seu dispositivo. O nome amigável pode ter de 1 a 15 caracteres e pode conter letras, números e hífens.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Opcionalmente) configure suas **configurações de rede**. Você vê pelo menos uma interface de rede e mais, dependendo de quantos você configurou na máquina virtual subjacente. A página **Configurações de rede** para um dispositivo virtual com uma interface de rede ativada é mostrada abaixo.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Ao definir as configurações de rede, lembre-se do seguinte:

    - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Portanto, um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
    - Você pode configurar sua interface de rede como IPv4.

    >[!NOTE] 
    > É recomendável que você não alterne o endereço IP local da interface de rede de estático para DHCP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser registrado com o serviço e então altere. Em seguida, você pode exibir os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.

4. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na página **Proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: *http://&lt;endereço IP do host ou FDQN&gt;: Número de porta*. Observe que não há suporte para URLs HTTPS.
   2. Especifique a **Autenticação** como **Básica** ou **Nenhuma**.
   3. Se você estiver usando autenticação, você também precisará fornecer um **Nome de Usuário** e **Senha**.
   4. Clique em **Aplicar**. Isso validará e aplicará as configurações de proxy Web definidas.

5. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na página **Configurações de hora** :
    
    1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.
    2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.
    3. Opcionalmente, especifique um **Servidor NTP secundário** para o dispositivo.
    4. Clique em **Aplicar**. Isso validará e aplicará as configurações de hora definidas.

6. Na página **Configurações da nuvem**, ative seu dispositivo com o serviço Data Box Gateway no portal do Azure.
    
    1. Insira o **chave de ativação** que você obteve em [Obter a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para o Data Box Gateway.

    2. Clique em **Ativar**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Talvez você precise aguardar um minuto antes do dispositivo ser ativado com êxito. Após a ativação, a página será atualizada para indicar que o dispositivo foi ativado com êxito.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Conectar-se ao dispositivo virtual
> * Configurar e ativar o dispositivo virtual


Avance para o próximo tutorial para aprender como transferir dados com seu Data Box Gateway.

> [!div class="nextstepaction"]
> [Transferir dados com o Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
