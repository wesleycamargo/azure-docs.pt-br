---
title: Conecte-se, configure e ative um dispositivo do Azure Data Box Edge no portal do Azure | Microsoft Docs
description: O terceiro tutorial para implantar o Data Box Edge instrui você a conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: f4ca513e3c2e2345dc0005b95ddb7927c0164ffe
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383010"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Tutorial: Conectar, configurar e ativar o Azure Data Box Edge (versão prévia) 

Este tutorial descreve como conectar-se, configurar e ativar seu dispositivo Azure Data Box Edge usando a interface do usuário Web local. 

O processo de configuração e ativação pode levar cerca de 20 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Antes de pedir e implantar essa solução, examine os [Termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu dispositivo Data Box Edge, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar Data Box Edge](data-box-edge-deploy-install.md).
* Você tem a chave de ativação do serviço Caixa de Dados que você criou para gerenciar o dispositivo Data Box Edge. Para obter mais informações, vá para [Preparar para implantar a Borda da Caixa de Dados do Azure](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Configure o adaptador Ethernet no computador usado para conectar-se ao dispositivo Edge com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

1. Conecte o computador à porta 1 do seu dispositivo. 

1. Abrir uma janela do navegador e acessar o local da web da interface do usuário do dispositivo em https://192.168.100.10.  
    Essa ação pode levar alguns minutos depois que você tenha ativado o dispositivo. 

    Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro de certificado de segurança do site](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Selecione **Continuar para essa página da Web**.  
    Essas etapas podem variar dependendo do navegador que você está usando.

1. Entrar para a web da interface do usuário do seu dispositivo. A senha padrão é *Senha1*. 
   
    ![Página de entrada do dispositivo Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. No prompt, altere a senha de administrador do dispositivo.  
    A nova senha deve conter de 8 a 16 caracteres. Deve conter três dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.

Você está agora no painel do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
O painel exibe as várias configurações necessárias para configurar e registrar o dispositivo físico com o serviço do Data Box Edge. O **nome do dispositivo**, **configurações de rede**, **configurações de proxy Web**, e **configurações de hora** são opcionais. São as únicas configurações obrigatórias **configurações de nuvem**.
   
![O painel de dispositivo do Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. No painel esquerdo, selecione **Nome do dispositivo** e, em seguida, insira um nome amigável para seu dispositivo.  
    O nome amigável deve conter de 1 a 15 caracteres e conter letras, números e hifens.

    ![A página "Nome do dispositivo"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Opcional) No painel esquerdo, selecione **Configurações de rede** e, em seguida, defina as configurações.  
    No dispositivo físico, você verá seis adaptadores de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORT 3, PORT 4, PORT 5 e PORT 6 são todas interfaces de rede de 25 Gbps. A PORTA 1 é configurada automaticamente como porta somente de gerenciamento, enquanto as PORTA 2 a 6 são portas de dados. A página **Configurações de rede**, conforme mostrado abaixo.
    
    ![A página de "Configurações de rede"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Conforme você define as configurações de rede, tenha em mente:

    - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
    - Você pode configurar sua interface de rede como IPv4.
   
1. (Opcional) No painel esquerdo, selecione **Configurações de proxy Web** e, em seguida, configure seu servidor proxy Web. Embora a configuração do proxy Web seja opcional, se você usar um proxy Web, poderá configurá-lo apenas nesta página.
   
   ![A página "Configurações de proxy Web"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na página **Configurações de proxy Web**, faça o seguinte:
   
    a. Na **URL do proxy Web**, digite a URL neste formato: `http://host-IP address or FDQN:Port number`. Não há suporte para URLs HTTPS.

   b. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**.

   c. Se você estiver usando a autenticação, insira um nome de usuário e uma senha.

   d. Para validar e aplicar as configurações de proxy da Web, selecione **Aplicar configurações**.

1. (Opcional) No painel esquerdo, selecione **Configurações de hora** e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para seu dispositivo.  
    Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![A página "Configurações de hora"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Na página **Configurações de hora**, faça o seguinte:
    
     a. Na lista suspensa **Fuso horário**, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está sendo implantado.  
        O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    b. Na caixa **Servidor NTP primário**, digite o servidor primário para seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    c. Opcionalmente, na caixa **Servidor NTP secundário**, digite um servidor secundário para seu dispositivo.

    d. Para validar e aplicar as configurações de hora configuradas, selecione **Aplicar**.

6. No painel esquerdo, selecione **Configurações de nuvem** e, em seguida, ative seu dispositivo com o serviço do Data Box Edge no portal do Azure.
    
     a. Na caixa **Chave de ativação**, digite a chave de ativação recebida em [Obter chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key) para o Data Box Edge.

    b. Escolha **Aplicar**. 
       
    ![A página "Configurações de nuvem"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Depois que o dispositivo for ativado com sucesso, você verá as opções do modo de conectividade. Essas configurações são definidas se você precisar trabalhar com o dispositivo no modo parcialmente desconectado ou desconectado. 

    ![A confirmação de ativação de "Configurações de nuvem"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

A configuração do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Conectar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico


Para saber como transferir dados com o seu dispositivo Data Box Edge, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de borda](./data-box-edge-deploy-add-shares.md).