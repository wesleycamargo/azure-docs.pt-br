---
title: Habilitar o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Habilite o Proxy de Aplicativo no portal clássico do Azure e instale os Conectores para o proxy reverso."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 45ecd5b16b874dcf0ddf16c58f6d9dea6f4afb00


---
# <a name="enable-application-proxy-in-the-azure-portal"></a>Habilitar o Proxy de aplicativo no portal do Azure
Este artigo orienta você pelas etapas para habilitar o Proxy de Aplicativo do Microsoft Azure AD para seu diretório de nuvem no Azure AD.

Se você estiver familiarizado com o que o Proxy de Aplicativo pode ajudá-lo a fazer, saiba mais sobre [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos de Proxy de aplicativo
Antes de habilitar e usar os serviços de Proxy de aplicativo, você precisa ter:

* Uma [assinatura premium ou básica](active-directory-editions.md) do Microsoft Azure AD e um diretório do Azure AD do qual você seja administrador global.
* Um servidor executando o Windows Server 2012 R2 ou Windows 8.1 ou posterior em que você possa instalar o Conector do Proxy de Aplicativo. O servidor envia solicitações aos serviços de Proxy de Aplicativo na nuvem e precisa de uma conexão HTTP ou HTTPS para os aplicativos que você está publicando.
  
  * Para logon único para seus aplicativos publicados, essa máquina deve ser unida ao mesmo domínio do AD que os aplicativos que você está publicando.
* Se houver um firewall no caminho, verifique se o ele está aberto para que o Conector possa fazer solicitações HTTPS (TCP) para o Proxy de Aplicativo. O Conector usa essas portas com subdomínios que fazem parte dos domínios de alto nível: msappproxy.net e servicebus.windows.net. Não esqueça de abrir as seguintes portas para o tráfego de **saída** :
  
  | Número da porta | Descrição |
  | --- | --- |
  | 80 |Habilite o tráfego HTTP de saída para a validação de segurança. |
  | 443 |Habilite a autenticação de usuário no Azure AD (necessário somente para o processo de registro do conector) |
  | 10100–10120 |Habilitar respostas HTTP LOB enviadas de volta para o proxy |
  | 9352, 5671 |Habilite a comunicação entre o conector de serviço do Azure para solicitações de entrada. |
  | 9350 |Opcional, para permitir o melhor desempenho em solicitações de entrada. |
  | 8080 |Habilite a sequência de inicialização do Conector e habilite a atualização automática do Conector |
  | 9090 |Habilite o registro do Conector (necessário somente para o processo de registro do Conector) |
  | 9091 |Habilitar a renovação automática de certificados de confiança do Conector |
  
    Se o firewall reforça o tráfego de acordo com os usuários de origem, abra essas portas para o tráfego proveniente de serviços do Windows em execução como um serviço de rede. Além disso, certifique-se de habilitar a porta 8080 para Autoridade NT\Sistema.
* Se sua organização usa servidores proxy para se conectar à Internet, confira a postagem do blog [Trabalhar com servidores proxy locais existentes](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) para obter detalhes sobre como configurá-los.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Etapa 1: habilite o Proxy de aplicativo no AD do Azure
1. Entre como administrador no [portal clássico do Azure](https://manage.windowsazure.com/).
2. Vá para o Active Directory e selecione o diretório no qual você deseja habilitar o Proxy de aplicativo.
   
    ![Active Directory - ícone](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Selecione **Configurar** na página de diretório e role para baixo até **Proxy de Aplicativo**.
4. Alterne **Habilitar Serviços de Proxy de Aplicativo para este Diretório** para **Habilitado**.
   
    ![Habilitar Proxy de aplicativo](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Selecione **Baixar agora**. Isso leva você para a **Download do Conector de Proxy de Aplicativo do Azure AD**. Leia e aceite os termos de licença e clique em **Download** para salvar o arquivo do Windows Installer (.exe) do conector.

## <a name="step-2-install-and-register-the-connector"></a>Etapa 2: Instalar e registrar o conector
1. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que você preparou de acordo com os pré-requisitos.
2. Siga as instruções no Assistente para instalar.
3. Durante a instalação, será solicitado que você registre o Conector com o Proxy de Aplicativo do seu locatário do Azure AD.
   
   * Forneça suas credenciais de administrador global do AD do Azure. Seu locatário de administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * Verifique se o administrador que registra o Conector está no mesmo diretório onde você habilitou o serviço Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
   * Se a **Configuração de Segurança Aprimorada do Internet Explorer** estiver **Ativada** no servidor em que você estiver instalando o conector, a tela de registro poderá ser bloqueada. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que a Segurança Melhorada do Internet Explorer está desativada.
   * Se o registro do conector não for bem-sucedido, confira [Solucionar problemas de Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md).  
4. Quando a instalação for concluída, dois novos serviços são adicionados ao seu servidor:
   
   * **Conector de Proxy de Aplicativo do Microsoft AAD** habilita a conectividade
     
     * **Atualizador do Conector de Proxy de Aplicativo do Microsoft AAD** é um serviço de atualização automatizada que verifica periodicamente se há novas versões do conector e o atualiza conforme necessário.
     
     ![Serviços do Conector de Proxy de Aplicativo - captura de tela](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Clique em **Concluir** na janela de instalação.

Para fins de alta disponibilidade, você deve implantar pelo menos dois conectores. Para implantar mais conectores, repita as etapas 2 e 3 acima. Cada conector deve ser registrado separadamente.

Se você deseja desinstalar o Conector, desinstale o serviço Conector e o serviço Atualizador. Reinicie o computador para remover completamente o serviço.

## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).

Se tem aplicativos que estão em redes separadas ou em locais diferentes, você pode usar grupos de conector para organizar os conectores diferentes em unidades lógicas. Saiba mais sobre como [Trabalhar com conectores de Proxy de Aplicativo](active-directory-application-proxy-connectors.md).




<!--HONumber=Dec16_HO2-->


