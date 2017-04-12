---
title: "Publicar a Área de Trabalho Remota com o Proxy de Aplicativo do Azure Active Directory | Microsoft Docs"
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd0ecc62fd3cfc860423acd02108648e99f44753
ms.lasthandoff: 04/03/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicar a Área de Trabalho Remota com o Proxy de Aplicativo do Azure AD

Este artigo discute como disponibilizar as implantações de Área de Trabalho Remota do Windows para usuários remotos. As implantações de Área de Trabalho Remota podem residir localmente ou em redes privadas, como implantações de IaaS.

> [!NOTE]
> O Proxy de Aplicativo é um recurso que estará disponível somente se você tiver atualizado para a edição Premium ou Basic do Azure Active Directory (Azure AD). Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

O tráfego de RDP (Protocolo de Área de Trabalho Remota) pode ser publicado por meio do Proxy de Aplicativo do Azure AD como um aplicativo de proxy de passagem. Essa solução resolve o problema de conectividade e fornece proteção de segurança básica, como buffer de rede, front-end seguro de Internet e proteção contra DDoS (ataque de negação de serviço distribuído).

## <a name="remote-desktop-deployment"></a>Implantação de Área de Trabalho Remota

Dentro da implantação de Área de Trabalho Remota, Gateway de Área de Trabalho Remota é publicado para que ele pode converter a chamada de procedimento remoto (RPC) sobre o tráfego HTTPS para RDP sobre o tráfego de protocolo de datagrama de usuário (UDP).

Você pode configurar os clientes para usar clientes de Área de Trabalho Remota, como MSTSC.exe, para acessar o Proxy de Aplicativo do Azure AD. Dessa forma, você pode criar uma nova conexão HTTPS para o Gateway de Área de Trabalho Remota usando seus conectores. Como resultado, o gateway não é diretamente exposto à Internet, e todas as solicitações HTTPS serão encerradas primeiro na nuvem.

A topologia é mostrada neste diagrama:

 ![Diagrama de Locais de Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurar a URL do Gateway de Área de Trabalho Remota

Quando os usuários configuram a URL de Gateway de Área de Trabalho Remota e disparam o tráfego de RDP normalmente, podem acessar arquivos e outros métodos.

Você pode publicar usando o nome de domínio fornecido pelo Proxy de Aplicativo (msappproxy.net) ou usando um nome de domínio personalizado configurado no Azure AD (por exemplo, rdg.contoso.com).

Se os dispositivos de cliente e o arquivo RDP já estiverem configurados com uma URL de Gateway de Área de Trabalho Remota, você poderá usar o mesmo nome de domínio e, portanto, evitar a alteração. Nesse caso, o certificado que cobre esse domínio deve ser fornecido ao Proxy de Aplicativo, e sua CRL (lista de revogação de certificado) deve estar acessível pela Internet.

Se nenhuma URL de Gateway de Área de Trabalho Remota estiver configurada, os usuários ou administradores poderão especificá-la nos clientes de Área de Trabalho Remota (MSTSC) usando a caixa de diálogo Conexão de Área de Trabalho Remota, conforme mostrado aqui.

 ![Caixa de diálogo Conexão de Área de Trabalho Remota](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

A caixa de diálogo **Configurações da Conexão** é exibida quando você clica em **Configurações** na guia **Avançado**.

 ![Janela de configurações de Conexão na caixa de diálogo Conexão de Área de Trabalho Remota](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Acesso via Web da Área de Trabalho Remota

Se sua organização usar o portal de RDWA (Acesso via Web da Área de Trabalho Remota), você também poderá publicar usando o Proxy de Aplicativo do Azure AD. Você pode publicar nesse portal com pré-autenticação e logon único (SSO).

A topologia do cenário RDWA é mostrada no diagrama a seguir:

 ![Diagrama do cenário RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

No caso anterior, os usuários são autenticados no Azure AD antes de acessar RDWA. Se eles já tiverem sido autenticados no Azure AD (por exemplo, se estiverem usando o Office 365), não será necessário autenticar novamente no RDWA.

Quando os usuários iniciarem a sessão de RDP, precisarão autenticar novamente no canal da RDP. Isso acontece porque o SSO do RDWA para o Gateway de Área de Trabalho Remota tem base no armazenamento de credenciais de usuário no cliente usando o ActiveX. Esse processo é disparado a partir da autenticação baseada em formulário do RDWA. Quando a autenticação do RDWA estiver usando Kerbros, nenhuma autenticação baseada em formulário será apresentada e, portanto, o RDWA para SSO de RDP não funcionará.

Se o RDWA precisar de SSO para o tráfego de RDP, ou a autenticação baseada em formulário de RDWA tiver sido muito personalizada, você poderá publicar o RDWA sem pré-autenticação.

A topologia desse cenário é mostrada no diagrama a seguir:

 ![Diagrama do cenário RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

No caso anterior, os usuários devem autenticar RDWA usando a autenticação baseada em formulário, mas eles não precisam autenticar o RDP.

>[!NOTE]
>Em ambos os casos anteriores, nenhum pré-autenticação é necessária no tráfego RDP. Portanto, os usuários podem acessá-lo sem passar primeiro pelo RDWA.

## <a name="next-steps"></a>Próximas etapas

[Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Habilitar o Proxy de Aplicativo no portal do Azure](active-directory-application-proxy-enable.md)

