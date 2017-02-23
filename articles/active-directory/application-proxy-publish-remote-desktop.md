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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>Publicar a Área de Trabalho Remota com o Proxy de Aplicativo do Azure Active Directory

Este artigo discute como disponibilizar as implantações de Área de Trabalho Remota para usuários remotos. Essas implantações de Área de Trabalho Remota podem residir localmente ou em redes privadas, como implantações de IaaS. 

> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 
 

O tráfego de protocolo de Área de Trabalho Remota pode ser publicado por meio do Proxy de Aplicativo como um aplicativo de proxy de passagem. Essa solução resolve o problema de conectividade e fornece proteção de segurança básica, como buffer de rede, front-end seguro de Internet e proteção de DDoS. 

##<a name="remote-desktop-deployment"></a>Implantação de Área de Trabalho Remota

Na implantação de Área de Trabalho Remota, o Gateway de Área de Trabalho Remota é publicado para que possa converter o tráfego de RPC sobre HTTPS para tráfego de RDP sobre UDP.

Você pode configurar os clientes para usar clientes de Área de Trabalho Remota, como MSTSC.exe, para acessar o Proxy de Aplicativo do Azure AD. Isso permite que você crie uma nova conexão HTTPS para o Gateway de Área de Trabalho Remota usando seus conectores. Fazendo isso, o Gateway de Área de Trabalho Remota não será exposto diretamente à Internet, e todas as solicitações HTTPS serão finalizadas primeiro na nuvem. 

O diagrama a seguir mostra essa topologia.

 ![Local dos Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurar a URL do Gateway de Área de Trabalho Remota

Contanto que seus usuários configurem a URL de Gateway de Área de Trabalho Remota, quando eles dispararem o tráfego de RDP normalmente, poderão acessar arquivos e outros métodos.

A publicação pode ser realizada usando o nome de domínio fornecido pelo Proxy de Aplicativo (msappproxy.net) ou usando um nome de domínio personalizado configurado no Azure AD; por exemplo, rdg.contoso.com. 

Se os dispositivos de cliente e o arquivo RDP já estiverem configurados com uma URL de Gateway de Área de Trabalho Remota, você poderá usar o mesmo nome de domínio e, portanto, evitar a alteração. Nesse caso, o certificado que cobre esse domínio deve ser fornecido ao Proxy de Aplicativo, e seu CRL deve ser acessível pela Internet.

Se nenhuma URL de Gateway de Área de Trabalho Remota estiver configurada, os usuários ou administradores poderão especificá-la nos clientes de Área de Trabalho Remota (MSTSC) usando a caixa de Conexão de Área de Trabalho Remota, conforme mostrado abaixo.

 ![Local dos Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

A caixa Configurações da Conexão é exibida quando você clica em **Configurações** na guia **Avançado**.

 ![Local dos Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Acesso via Web da Área de Trabalho Remota

Se sua organização usar o portal de RDWA (Acesso via Web da Área de Trabalho Remota), você também poderá publicar usando o Proxy de Aplicativo do Azure AD. Você pode publicar nesse portal com pré-autenticação e logon único (SSO).

O diagrama a seguir mostra a topologia para esse cenário.

 ![Local dos Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

No caso acima, os usuários serão autenticados no Azure AD antes de acessar o RDWA. Se eles já tiverem sido autenticados no Azure AD (por exemplo, se estiverem usando o Office 365), não será necessário autenticar novamente no RDWA.

Quando os usuários iniciarem a sessão de RDP, precisarão autenticar novamente no canal da RDP. Isso acontece porque o SSO do RDWA para o Gateway de Área de Trabalho Remota tem base no armazenamento de credenciais de usuário final no cliente usando o ActiveX. Esse processo é disparado a partir da autenticação baseada em formulário do RDWA. Quando a autenticação do RDWA estiver usando Kerbros, nenhuma autenticação baseada em formulário será apresentada e, portanto, o RDWA para SSO de RDP não funcionará.

Se o RDWA precisar de SSO para o tráfego de RDP, ou a autenticação baseada em formulário de RDWA tiver sido muito personalizada, será possível publicar o RDWA sem pré-autenticação.

O diagrama a seguir mostra a topologia para esse cenário.

 ![Local dos Serviços do Azure AD](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

No caso acima, os usuários precisam autenticar no RDWA usando a autenticação baseada em formulário, mas não precisarão autenticar na RDP. 

É importante observar em ambos os casos que não há a necessidade de pré-autenticação no tráfego de RDP. Portanto, os usuários podem acessá-lo sem passar primeiro pelo RDWA.

##<a name="next-steps"></a>Próximas etapas

[Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Habilitar o Proxy de aplicativo no Portal do Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


