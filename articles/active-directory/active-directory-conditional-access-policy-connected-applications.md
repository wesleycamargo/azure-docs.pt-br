---
title: "Definir a política de acesso condicional com base em dispositivo para aplicativos conectados ao Azure Active Directory | Microsoft Docs"
description: "Definir políticas de acesso condicional com base no dispositivo para aplicativos conectados do Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133


---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Definir a política de acesso condicional com base em dispositivo para aplicativos conectados ao Azure Active Directory
O acesso condicional com base no dispositivo do Azure AD (Azure Active Directory) ajuda-o a proteger os recursos organizacionais de:

* Tentativas de acesso de dispositivos desconhecidos ou não gerenciados.
* Dispositivos que não satisfaçam as políticas de segurança da sua organização.

Para obter uma visão geral sobre o acesso condicional, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).

Você pode definir as políticas de acesso condicional com base no dispositivo para proteger estes aplicativos:

* Office 365 SharePoint Online, para proteger sites e documentos organizacionais
* Office 365 Exchange Online, para proteger emails organizacionais
* Aplicativos de software como um serviço (SaaS) conectados ao Azure AD para autenticação
* Aplicativos locais publicados usando os serviços de Proxy de Aplicativo do Azure AD

Para definir uma política de acesso condicional com base no dispositivo, no portal do Azure, vá para o aplicativo específico no diretório.

  ![Lista de aplicativos no diretório do portal do Azure](./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicativos")

Selecione o aplicativo e clique na guia **Configurar** para definir a política de acesso condicional.  

  ![Configurar o aplicativo](./media/active-directory-conditional-access-policy-connected-applications/02.png "Regras de acesso baseadas em dispositivo")

Para definir uma política de acesso condicional com base no dispositivo, na seção **Regras de acesso com base no dispositivo**, em **Habilitar as regras de acesso**, selecione **Ativado**.

Uma política de acesso condicional com base no dispositivo tem três componentes:

* **Aplicar a**. O escopo de usuários ao qual a política se aplica.
* **Regras do Dispositivo**. As condições às quais um dispositivo deve atender antes que ele possa acessar o aplicativo.
* **Imposição de Aplicativo**. Os aplicativos cliente (nativo versus navegador) aos quais a política se aplica.
  
  ![Os três componentes de uma política de acesso baseada no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/03.png "Regras de acesso baseadas no dispositivo")

## <a name="select-the-users-the-policy-applies-to"></a>Selecionar os usuários aos quais a política se aplica
Na seção **Aplicar a** , você pode selecionar o escopo de usuários para os quais essa política se aplica.

Você tem duas opções ao criar um escopo de política de acesso para usuários:

* **Todos os Usuários**. Aplicar a política a todos os usuários que acessarem o aplicativo.
* **Grupos**. Limitar a política aos usuários membros de um grupo específico.

![Aplicar a política a todos os usuários ou a um grupo](./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")

 Para excluir um usuário de uma política, marque a caixa de seleção **Exceto**. Isso será útil quando você precisar conceder permissões para que usuários específicos acessem temporariamente o aplicativo. Selecione esta opção, por exemplo, se alguns dos seus usuários tiverem dispositivos que não estão prontos para o acesso condicional. Os dispositivos talvez não estejam registrados ou estejam prestes a perder a conformidade.

## <a name="select-the-conditions-that-devices-must-meet"></a>Selecionar as condições que os dispositivos devem atender
Use **Regras do Dispositivo** para definir as condições que um dispositivo deve atender para acessar o aplicativo.

Você pode definir o acesso condicional baseado no dispositivo para estes tipos de dispositivo:

* Atualização de Aniversário do Windows 10, Windows 8.1 e Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
* Dispositivos iOS (iPad, iPhone)
* Dispositivos Android

O suporte para Mac será disponibilizado em breve.

  ![Aplicar política a dispositivos](./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicativos")

> [!NOTE]
> Para saber mais sobre as diferenças entre o ingresso no domínio e o ingresso do Azure AD, confira [Usando dispositivos do Windows 10 no local de trabalho](active-directory-azureadjoin-windows10-devices.md).
> 
> 

Você tem duas opções para as regras de dispositivo:

* **Todos os dispositivos devem ser compatíveis**. Todas as plataformas de dispositivos que acessam o aplicativo devem ser compatíveis. Os dispositivos executados em plataformas que não dão suporte ao acesso condicionado baseado em dispositivo terão o acesso negado.
* **Somente os dispositivos selecionados devem ser compatíveis**. Apenas as plataformas de dispositivo específicas devem ser compatíveis. Outras plataformas, ou outras plataformas que podem acessar o aplicativo, têm acesso.
  
  ![Definir o escopo para as regras de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicativos")

Os dispositivos do Azure AD associados são compatíveis quando estão marcados como **compatíveis** no diretório pelo Intune ou por um sistema de gerenciamento de dispositivo móvel de terceiros que se integra ao Azure AD.

Um dispositivo de domínio estará em conformidade se:

* Estiver registrado no Azure AD. Muitas organizações tratam os dispositivos ingressados no domínio como dispositivos confiáveis.
* Se eles estiverem marcados como **compatíveis** no Azure AD pelo System Center Configuration Manager.
  
  ![Dispositivos ingressados no domínio que são compatíveis](./media/active-directory-conditional-access-policy-connected-applications/06.png "Regras de Dispositivo")

Os dispositivos pessoais do Windows são compatíveis quando estão marcados como **compatíveis** no diretório pelo Microsoft Intune ou por um sistema de MDM (gerenciamento de dispositivo móvel) de terceiros que se integra ao Azure AD.

Os dispositivos que não são do Windows são compatíveis quando estão marcados como **compatíveis** no diretório pelo Intune.

> [!NOTE]
> Para saber mais sobre como configurar o Azure AD para conformidade do dispositivo em diferentes sistemas de gerenciamento, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).
> 
> 

Você pode selecionar uma ou várias plataformas de dispositivo para uma política de acesso baseada no dispositivo. Isso inclui Android, iOS, Windows Mobile (telefones e tablets Windows 8.1) e Windows (todos os dispositivos com Windows, incluindo todos os dispositivos com Windows 10).
A avaliação de política só ocorre em plataformas selecionadas. Se um dispositivo que tenta acessar não estiver executando uma das plataformas selecionadas, o dispositivo poderá acessar o aplicativo se o usuário tiver acesso. Nenhuma política de dispositivo é avaliada.

![Selecionar plataformas para as regras de dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Regras de Dispositivo")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>Definir a avaliação da política para um tipo de aplicativo
Na seção **Imposição do Aplicativo**, você define o tipo de aplicativos que a política avaliará para o acesso do usuário ou do dispositivo.

Você tem duas opções para o tipo de aplicativo a ser incluído:

* Navegador e aplicativos nativos
* Somente aplicativos nativos

![Escolher navegador e aplicativos nativos](./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicativos")

Para impor a política de acesso para aplicativos, selecione **Para navegador e aplicativos nativos**. Em seguida, você pode incluir:

* Navegadores (por exemplo, Microsoft Edge no Windows 10 ou o Safari no iOS).
* Aplicativos que usam a ADAL (Biblioteca de Autenticação do Active Directory) em qualquer plataforma ou que usam a API WAM (Gerenciador de Contas da Web) no Windows 10.

> [!NOTE]
> Para saber mais sobre o suporte ao navegador e outras considerações para o usuário que está acessando um aplicativo protegido pela autoridade de certificação com base em dispositivo, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Ajudar a proteger o acesso a email de aplicativos baseados no Exchange ActiveSync
Em aplicativos do Office 365 Exchange Online, você pode usar o Exchange ActiveSync para bloquear o acesso de email para aplicativos de email baseados no Exchange ActiveSync.

![Opções de conformidade do Exchange ActiveSync](./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicativos")

![Exigir um dispositivo compatível para acessar emails](./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicativos")

## <a name="next-steps"></a>Próximas etapas
* [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO5-->


