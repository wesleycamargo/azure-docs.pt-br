---
title: Conecte-se, configure e ative o Azure Data Box Gateway no portal do Azure | Microsoft Docs
description: O terceiro tutorial para implantar o Data Box Gateway instrui você a conectar, configurar e ativar seu dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402314"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Conectar-se, configurar, ativar o Gateway de caixa de dados do Azure

## <a name="introduction"></a>Introdução

Este tutorial descreve como se conectar ao, configurar e ativar seu dispositivo de Gateway da caixa de dados por meio da interface do usuário da web local. 

O processo de configuração e ativação pode levar cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu Data Box Gateway, verifique se:

* Você provisionou um dispositivo virtual e obteve uma URL conectada a ela conforme detalhado no [provisionar um Gateway de dados de caixa no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [provisionar um Gateway de dados de caixa no VMware](data-box-gateway-deploy-provision-vmware.md).
* Você tem a chave de ativação do serviço Data Box Gateway que você criou para gerenciar dispositivos Data Box Gateway. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Abra uma janela do navegador e o acesso local da web da interface do usuário do dispositivo em:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Use a URL de conexão observada no tutorial anterior. Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site.

2. Selecione **Continuar para essa página da Web**. Essas etapas podem variar dependendo do navegador que você está usando.
   
    ![Mensagem de erro de certificado de segurança do site](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Entrar para o interface do usuário do seu dispositivo virtual da web. A senha padrão é *Senha1*. 
   
    ![Entrar interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. No prompt, altere a senha do dispositivo. A nova senha deve conter entre 8 e 16 caracteres. Ele deve conter 3 das seguintes opções: caracteres maiusculos, minúsculos, numérico e especial.

    ![Alterar a senha de dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Você está agora no **painel** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
O painel exibe as várias configurações que são necessárias para configurar e registrar o dispositivo virtual com o serviço de Gateway da caixa de dados. O **nome do dispositivo**, **configurações de rede**, **configurações de proxy Web**, e **configurações de hora** são opcionais. São as únicas configurações obrigatórias **configurações de nuvem**.
   
![Web local da interface do usuário "Painel" página](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. No painel esquerdo, selecione **nome do dispositivo**e, em seguida, insira um nome amigável para seu dispositivo. O nome amigável deve conter de 1 a 15 caracteres e ter letras, números e hifens.

    ![Página de "Nome do dispositivo" de interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcional) No painel esquerdo, selecione **as configurações de rede** e, em seguida, defina as configurações. Em seu dispositivo virtual, você verá pelo menos uma interface de rede e muito mais, dependendo de quantos configurados na máquina virtual subjacente. A página **Configurações de rede** para um dispositivo virtual com uma interface de rede ativada é mostrada abaixo.
    
    ![Página de "Configurações de rede" de interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Como definir configurações de rede, tenha em mente:

    - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
    - Você pode configurar sua interface de rede como IPv4.

     >[!NOTE] 
     > É recomendável que você não alterne o endereço IP local do adaptador de rede de estático para DHCP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser registrado com o serviço e então altere. Posteriormente, você pode visualizar os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.

3. (Opcional) configure seu servidor proxy da Web. Embora a configuração de proxy da web seja opcional, se você usar um proxy da web, você pode configurá-lo somente nesta página.
   
   ![Página de "Configurações de proxy da Web" de interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Sobre o **proxy da Web** página, faça o seguinte:
   
   1. Na **URL do proxy Web**, digite a URL neste formato: `http://&lt;host-IP address or FQDN&gt;:Port number`. Não há suporte para URLs HTTPS.
   2. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**.
   3. Se você estiver usando a autenticação, insira um **nome de usuário** e **senha**.
   4. Para validar e aplicar as configurações de proxy da web, selecione **aplicar**.

4. (Opcional) No painel esquerdo, selecione **Configurações de hora** e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para seu dispositivo. 

    Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![Página de "Configurações de tempo" de interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    No **configurações de hora** página, faça o seguinte:
    
    1. No **fuso horário** lista suspensa, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está implantado.
        O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    2. Especifique um **servidor NTP primário** para seu dispositivo ou aceite o valor padrão de `time.windows.com`.   
        Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    3. Opcionalmente, na **o servidor NTP secundário** , digite um servidor secundário para seu dispositivo.

    4. Para validar e aplicar as configurações de hora configuradas, selecione **Aplicar**.

6. No painel esquerdo, selecione **configurações de nuvem**e, em seguida, ativar seu dispositivo com o serviço de Gateway da caixa de dados no portal do Azure.
    
    1. No **chave de ativação** , digite o **chave de ativação** que você obteve na [obter a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para o Gateway de caixa de dados.

    2. Selecione **Ativar**.
       
         ![Página de "Configurações de nuvem" da interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. O dispositivo está ativado e atualizações críticas, se disponível, são aplicadas automaticamente. Você verá uma notificação para esse efeito. Monitore o progresso da atualização por meio do portal do Azure.

        ![Página de "Configurações de nuvem" da interface do usuário da web local](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A caixa de diálogo também tem uma chave de recuperação que você deve copiar e salvar em um local seguro. Essa chave é usada para recuperar os dados no caso do dispositivo não é inicializado.**


    4. Talvez você precise aguardar alguns minutos para concluir com êxito a atualização. Após a atualização é concluída, entrar dispositivo. O **configurações de nuvem** página atualizado para indicar que o dispositivo é ativado com êxito.

        ![Página de "Configurações de nuvem" de interface do usuário da web local atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

A configuração do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Conectar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Para saber como transferir dados com o seu Gateway de caixa de dados, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com o Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
