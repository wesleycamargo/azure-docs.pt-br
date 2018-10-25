---
title: Conecte-se, configure e ative o Azure Data Box Edge no portal do Azure | Microsoft Docs
description: O terceiro tutorial para implantar o Data Box Edge instrui você a conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166569"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Tutorial: Conectar, configurar, ativar a Borda da Caixa de Dados do Azure (Visualizar) 

Este tutorial descreve como conectar-se, configurar e ativar seu dispositivo Data Box Edge usando a interface de usuário da web local. 

O processo de configuração e ativação pode levar cerca de 20 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se ao dispositivo físico
> * Configurar e ativar o dispositivo físico

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu Data Box Edge, certifique-se de que:

* Você instalou o dispositivo físico conforme detalhado em [Install Data Box Edge](data-box-edge-deploy-install.md).
* Você tem a chave de ativação do serviço Caixa de Dados que você criou para gerenciar o dispositivo Data Box Edge. Para obter mais informações, vá para [Preparar para implantar a Borda da Caixa de Dados do Azure](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Configure o adaptador Ethernet no computador que você está usando para conectar-se ao dispositivo Edge com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.
2. Conecte o computador à porta 1 do seu dispositivo. 
3. Abrir uma janela do navegador e acessar o local da web da interface do usuário do dispositivo em https://192.168.100.10. Essa ação pode levar alguns minutos depois que você tenha ativado o dispositivo. 
4. Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site. Clique em **Continuar para essa página da Web**. (Essas etapas podem ser diferentes com base no navegador usado.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Entrar para a web da interface do usuário do seu dispositivo. A senha padrão é *Senha1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Você é solicitado a alterar a senha do administrador do dispositivo. Digite uma nova senha que contenha entre 8 e 16 caracteres. A senha deve conter 3 dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.

Você está agora no **painel** do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
1. No painel, você pode acessar várias configurações necessárias para configurar e registrar o dispositivo físico com o serviço Data Box Edge. O **nome do dispositivo**, **configurações de rede**, **configurações de proxy Web**, e **configurações de hora** são opcionais. São as únicas configurações obrigatórias **configurações de nuvem**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. Na página **Nome do dispositivo**, configure um nome amigável para o seu dispositivo. O nome amigável pode ter de 1 a 15 caracteres e pode conter letras, números e hífens.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Opcionalmente) configure suas **configurações de rede**. No seu dispositivo físico, você verá seis interfaces de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORT 3, PORT 4, PORT 5 e PORT 6 são todas interfaces de rede de 25 Gbps. A PORTA 1 é configurada automaticamente como porta somente de gerenciamento, enquanto a PORTA 2 a PORTA 6 são todas as portas de dados. A página **Configurações de rede**, conforme mostrado abaixo.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Ao configurar as configurações de rede, tenha em mente:

    - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
    - Você pode configurar sua interface de rede como IPv4.
   
4. Opcionalmente, configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, se você usar um proxy da Web, só poderá configurá-lo aqui.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na página **Proxy Web** :
   
   1. Forneça a **URL do proxy Web** neste formato: `http://host-IP address or FDQN:Port number`. Não há suporte para URLs HTTPS.
   2. Especifique a **Autenticação** como **Básica** ou **Nenhuma**.
   3. Se estiver usando autenticação, você também precisará fornecer um **Nome de usuário** e **Senha**.
   4. Clique em **aplicar** para validar e aplicar as configurações de proxy da web.

5. Opcionalmente, defina as configurações de hora para seu dispositivo, como o fuso horário e os servidores NTP primários e secundários. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Na página **Configurações de hora** :
    
    1. Na lista suspensa, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.
    2. Especifique um **Servidor NTP primário** para seu dispositivo ou aceite o valor padrão de time.windows.com. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.
    3. Opcionalmente, especifique um **Servidor NTP secundário** para o dispositivo.
    4. Clique em **aplicar** para validar e aplicar as configurações de hora configuradas.

6. Na página **Configurações da nuvem**, ative seu dispositivo com o serviço Caixa de Dados de Borda no portal do Azure.
    
    1. Digite a **Chave de ativação** que você recebeu em [Obtenha a chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key) para o Data Box Edge.

    2. Clique em **Aplicar**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Depois que o dispositivo for ativado com sucesso, você verá as opções do modo de conectividade. Essas configurações são definidas se você precisar trabalhar com o dispositivo no modo parcialmente desconectado ou desconectado. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

A configuração do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Edge, como:

> [!div class="checklist"]
> * Conectar-se ao dispositivo físico
> * Configurar e ativar o dispositivo físico


Avance para o próximo tutorial para saber como transferir dados com a borda da caixa de dados.

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de borda](./data-box-edge-deploy-add-shares.md).