---
title: Conecte-se, configure e ative um dispositivo do Azure Data Box Edge no portal do Azure | Microsoft Docs
description: O terceiro tutorial para implantar o Data Box Edge instrui você a conectar, configurar e ativar seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: ad288067fdd40dbfa84352916e84fa1e3ab9af8e
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629152"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Tutorial: Conectar-se, configurar e ativar a borda de caixa de dados do Azure 

Este tutorial descreve como conectar-se, configurar e ativar seu dispositivo Azure Data Box Edge usando a interface do usuário Web local.

O processo de configuração e ativação pode levar cerca de 20 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar seu dispositivo Data Box Edge, verifique o seguinte:

* Você instalou o dispositivo físico conforme detalhado em [Instalar Data Box Edge](data-box-edge-deploy-install.md).
* Você tem a chave de ativação do serviço Caixa de Dados que você criou para gerenciar o dispositivo Data Box Edge. Para obter mais informações, vá para [Preparar para implantar a Borda da Caixa de Dados do Azure](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Conectar-se para a configuração de interface do usuário da web local 

1. Configure o adaptador Ethernet no computador para conectar-se para o dispositivo de borda da caixa de dados com um endereço IP estático de 192.168.100.5 e a sub-rede 255.255.255.0.

2. Conecte o computador à porta 1 do seu dispositivo. Use a ilustração a seguir para identificar a porta 1 em seu dispositivo.

    ![Backplane de um dispositivo cabeado](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Abrir uma janela do navegador e acessar o local da web da interface do usuário do dispositivo em https://192.168.100.10.  
    Essa ação pode levar alguns minutos depois que você tenha ativado o dispositivo. 

    Você verá um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro de certificado de segurança do site](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Selecione **Continuar para essa página da Web**.  
    Essas etapas podem variar dependendo do navegador que você está usando.

5. Entrar para a web da interface do usuário do seu dispositivo. A senha padrão é *Senha1*. 
   
    ![Página de entrada do dispositivo Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. No prompt, altere a senha de administrador do dispositivo.  
    A nova senha deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúscula, minúscula, numérica e caracteres especiais.

Você está agora no painel do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
O painel exibe as várias configurações necessárias para configurar e registrar o dispositivo físico com o serviço do Data Box Edge. O **nome do dispositivo**, **configurações de rede**, **configurações de proxy Web**, e **configurações de hora** são opcionais. São as únicas configurações obrigatórias **configurações de nuvem**.
   
![Web local da interface do usuário "Painel" página](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. No painel esquerdo, selecione **Nome do dispositivo** e, em seguida, insira um nome amigável para seu dispositivo.  
    O nome amigável deve conter de 1 a 15 caracteres e ter letras, números e hifens.

    ![Página de "Nome do dispositivo" de interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Opcional) No painel esquerdo, selecione **Configurações de rede** e, em seguida, defina as configurações.  
    Em seu dispositivo físico, há seis interfaces de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. A porta 3, 4 de porta, porta 5 e 6 de porta são todas as interfaces de rede de 25 Gbps também podem servir como interfaces de rede de 10 Gbps. A PORTA 1 é configurada automaticamente como porta somente de gerenciamento, enquanto as PORTA 2 a 6 são portas de dados. O **as configurações de rede** página é conforme mostrado abaixo.
    
    ![Página de "Configurações de rede" de interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Conforme você define as configurações de rede, tenha em mente:

   - Se o DHCP estiver ativado em seu ambiente, as interfaces de rede serão configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são atribuídos automaticamente.
   - Se o DHCP não estiver ativado, você poderá atribuir IPs estáticos, se necessário.
   - Você pode configurar sua interface de rede como IPv4.

     >[!NOTE] 
     > É recomendável que você não alterne o endereço IP local do adaptador de rede estático para DCHP, a menos que tenha outro endereço IP para se conectar ao dispositivo. Se estiver usando um adaptador de rede e alternar para DHCP, não haverá como determinar o endereço DHCP. Se você quiser alterar para um endereço DHCP, espere até o dispositivo ser registrado com o serviço e então altere. Posteriormente, você pode visualizar os IPs de todos os adaptadores em **Propriedades do dispositivo** no portal do Azure para seu serviço.

3. (Opcional) No painel esquerdo, selecione **Configurações de proxy Web** e, em seguida, configure seu servidor proxy Web. Embora a configuração do proxy Web seja opcional, se você usar um proxy Web, poderá configurá-lo apenas nesta página.
   
   ![Página de "Configurações de proxy da Web" de interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na página **Configurações de proxy Web**, faça o seguinte:
   
    a. Na **URL do proxy Web**, digite a URL neste formato: `http://host-IP address or FQDN:Port number`. Não há suporte para URLs HTTPS.

   b. Em **Autenticação**, selecione **Nenhuma** ou **NTLM**.

   c. Se você estiver usando a autenticação, insira um nome de usuário e uma senha.

   d. Para validar e aplicar as configurações de proxy da Web, selecione **Aplicar configurações**.

4. (Opcional) No painel esquerdo, selecione **Configurações de hora** e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para seu dispositivo.  
    Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem.
       
    Na página **Configurações de hora**, faça o seguinte:
    
    1. Na lista suspensa **Fuso horário**, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está sendo implantado.
        O fuso horário padrão para o seu dispositivo é PST. Seu dispositivo usará esse fuso horário para todas as operações agendadas.

    2. Na caixa **Servidor NTP primário**, digite o servidor primário para seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet.

    3. Opcionalmente, na caixa **Servidor NTP secundário**, digite um servidor secundário para seu dispositivo.

    4. Para validar e aplicar as configurações de tempo configurado, selecione **aplicar configurações**.

        ![Página de "Configurações de tempo" de interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Opcional) No painel esquerdo, selecione **configurações de armazenamento** para configurar a resiliência de armazenamento em seu dispositivo. Esse recurso está atualmente na visualização. Por padrão, o armazenamento no dispositivo não é resiliente e há perda de dados se um disco de dados falhar no dispositivo. Quando você habilita a opção resiliente, o armazenamento do dispositivo será reconfigurado e o dispositivo pode suportar a falha de um disco de dados sem perda de dados. Configurando o armazenamento como resiliente reduzirá a capacidade utilizável do seu dispositivo.

    > [!IMPORTANT] 
    > A resiliência só pode ser configurada antes de ativar o dispositivo. 

    ![Página de "Configurações de armazenamento" de interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. No painel esquerdo, selecione **Configurações de nuvem** e, em seguida, ative seu dispositivo com o serviço do Data Box Edge no portal do Azure.
    
    1. Na caixa **Chave de ativação**, digite a chave de ativação recebida em [Obter chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key) para o Data Box Edge.
    2. Escolha **Aplicar**.
       
        ![Página de "Configurações de nuvem" da interface do usuário da web local](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. O dispositivo é ativado pela primeira vez. O dispositivo é então verificado para todas as atualizações críticas e se estiver disponível, as atualizações são aplicadas automaticamente. Você verá uma notificação para esse efeito.

        A caixa de diálogo também tem uma chave de recuperação que você deve copiar e salve-o em um local seguro. Essa chave é usada para recuperar os dados no caso do dispositivo não é inicializado.

        ![Página de "Configurações de nuvem" de interface do usuário da web local atualizada](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Talvez você precise aguardar alguns minutos após a atualização for concluída com êxito. A página é atualizada para indicar que o dispositivo é ativado com êxito.

        ![Página de "Configurações de nuvem" de interface do usuário da web local atualizada](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

A configuração do dispositivo foi concluída. Agora você pode adicionar compartilhamentos em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Conectar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico

Para saber como transferir dados com o seu dispositivo Data Box Edge, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de borda](./data-box-edge-deploy-add-shares.md).
