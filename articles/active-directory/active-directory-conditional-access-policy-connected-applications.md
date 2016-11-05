---
title: Como definir a política de acesso condicional com base no dispositivo do Azure Active Directory para controle de acesso dos aplicativos conectados no Azure Active Directory
description: Explica como os administradores de TI podem configurar políticas de acesso condicional com base no dispositivo para aplicativos conectados do Azure AD.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: markvi

---
# Como definir a política de acesso condicional com base no dispositivo do Azure Active Directory para controle de acesso dos aplicativos conectados no Azure Active Directory
O acesso condicional com base no dispositivo do Azure Active Directory é a capacidade que você tem para proteger os recursos organizacionais de:

* Acesso proveniente de dispositivos desconhecidos / não gerenciados
* De dispositivos que não atendem às políticas de segurança, conforme definido pela sua organização.

Para obter uma visão geral sobre o acesso condicional, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).

Você pode definir as políticas de acesso condicional com base no dispositivo para proteger o seguinte:

* Office 365 SharePoint Online para proteger sites e documentos organizacionais.
* Office 365 Exchange Online para proteger emails organizacionais.
* Aplicativos de SaaS conectados ao Azure AD para autenticação.
* Aplicativos locais publicados por meio do Proxy de aplicativo do Azure AD.

No Portal de Gerenciamento do Azure, você pode definir esta política ao visitar o aplicativo específico no diretório.

  ![Aplicativos](./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicativos")

Após selecionar o aplicativo, clique na guia **Configurar** para definir a política de acesso condicional.

  ![Regras de acesso com base no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/02.png "Regras de acesso com base no dispositivo")

Para habilitar uma política de acesso condicional com base no dispositivo, na seção **Regras de acesso com base no dispositivo**, para **Habilitar as regras de acesso**, selecione **Ativado**.

Essa política consiste em três componentes:

1. **Aplicar a** - O escopo de usuários para os quais esta política é aplicável ao acessarem o aplicativo.
2. **Regras do Dispositivo** - Os requisitos exigidos que os dispositivos devem atender antes de acessar o aplicativo.
3. **Imposição do Aplicativo** - Os aplicativos do cliente (nativo versus navegador) para os quais a política deve ser avaliada.
   
   ![Regras de acesso com base no dispositivo](./media/active-directory-conditional-access-policy-connected-applications/03.png "Regras de acesso com base no dispositivo")

## Seleção dos usuários para os quais a política se aplica
Na seção **Aplicar a**, você pode selecionar o escopo de usuários para os quais essa política se aplica.

Você tem duas opções para o escopo:

* **Todos os usuários** - Para todas as pessoas que acessarem o aplicativo
* **Grupos** - Para limitar o escopo aos usuários que são membros de um ou mais grupos.

![Aplicar a](./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")

Ao selecionar **Exceto**, você pode excluir usuários dessa política enquanto acessam o aplicativo. Isso é útil quando você precisa permitir que usuários específicos acessem o aplicativo temporariamente. Selecione esta opção, por exemplo, se alguns dos seus usuários tiverem dispositivos que não estão prontos para o acesso condicional (ainda não registrados, fora de conformidade, etc.).

## Selecionando as condições que os dispositivos devem atender
Com as **Regras de Dispositivo**, defina os requisitos para que os dispositivos possam acessar o aplicativo.

Para o acesso condicional com base no dispositivo, há suporte para os seguintes dispositivos:

* Atualização de Aniversário do Windows 10, Windows 7 e Windows 8.1.
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
* Dispositivos iOS (iPad, iPhone)
* Dispositivos Android

O suporte para Mac será disponibilizado em breve.

  ![Dispositivos](./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicativos")

> [!NOTE]
> Para obter informações sobre as diferenças entre o ingresso no domínio e o ingresso do Azure AD, confira [Usando dispositivos com Windows 10 no local de trabalho](active-directory-azureadjoin-windows10-devices.md).
> 
> 

Você tem duas opções para as regras de dispositivo:

* **Todos os dispositivos devem ser compatíveis** - Isso exige que todas as plataformas de dispositivo que acessam o aplicativo sejam compatíveis. Para plataformas que não dão suporte para o acesso condicional com base no dispositivo, os dispositivos têm o acesso negado.
* **Apenas dispositivos selecionados devem ser compatíveis** - Isso exige que somente as plataformas de dispositivo selecionadas sejam compatíveis. As plataformas não selecionadas ou outras plataformas que acessam o aplicativo têm permissão de acesso.
  
  ![Aplicativos](./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicativos")

Os dispositivos do Azure AD associados são compatíveis quando estão marcados como **compatíveis** no diretório pelo Microsoft Intune ou por um sistema de MDM (gerenciamento de dispositivo móvel) de terceiros que se integra ao Azure AD.

Os dispositivos ingressados no domínio são compatíveis em uma das duas maneiras:

* Se eles forem registrados com o Azure AD, o fato de eles serem ingressados no domínio poderá fazer com que muitas organizações os tratem como dispositivos confiáveis.
* Se eles estiverem marcados como 'compatíveis' no Azure AD pelo System Center Configuration Manager 2016.
  
  ![Regras de Dispositivo](./media/active-directory-conditional-access-policy-connected-applications/06.png "Regras de Dispositivo")

Os dispositivos pessoais do Windows são compatíveis quando estão marcados como **compatíveis** no diretório pelo Microsoft Intune ou por um sistema de MDM (gerenciamento de dispositivo móvel) de terceiros que se integra ao Azure AD.

Os dispositivos que não são do Windows são compatíveis quando estão marcados como **compatíveis** no diretório pelo Microsoft Intune.

> [!NOTE]
> Para obter mais informações sobre como configurar o Azure AD para fins de conformidade do dispositivo pelo sistema de gerenciamento, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).
> 
> 

Ao selecionar as plataformas de dispositivo específicas, você pode escolher uma ou várias opções, incluindo Android, iOS, Windows Mobile (telefones e tablets Windows 8.1) e Windows (todos os outros dispositivos Windows, incluindo todos os dispositivos Windows 10). Com esta opção, a avaliação da política ocorre apenas em plataformas selecionadas. Se houver uma tentativa de acesso de um dispositivo que não faz parte da seleção, nenhuma política de dispositivo será avaliada e o dispositivo será permitido se o usuário também for.

![Regras de Dispositivo](./media/active-directory-conditional-access-policy-connected-applications/07.png "Regras de Dispositivo")

## Seleção do tipo de aplicativos de cliente sob a política em que serão avaliados
Na seção **Imposição do Aplicativo**, você define o tipo de aplicativos no qual a política deverá ser avaliada.

Você tem duas opções de aplicativos:

* Para aplicativos de navegador e nativos
* Somente para aplicativos nativos.

![Aplicativos](./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicativos")

Selecionar a opção **Para aplicativos de navegador e nativos** impõe a política de acesso a aplicativos por:

* Navegadores (por exemplo, o Edge no Windows 10, o Safari no iOS, etc.)
* Aplicativos que usam a ADAL (Biblioteca de Autenticação do Active Directory) em qualquer plataforma ou na API WAM (Gerenciador de Contas da Web) no Windows 10

> [!NOTE]
> Para obter mais informações sobre o suporte ao navegador e outras considerações para o usuário final que está acessando os aplicativos protegidos da AC com base no dispositivo, confira [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md).
> 
> 

## Protegendo o acesso a email do Exchange Active Sync com base em aplicativos
Nos aplicativos do Office 365 Exchange Online, você tem uma seção adicional chamada **Exchange Activesync**. Esta seção permite que você bloqueie o acesso ao email para aplicativos de email com base no Exchange Active Sync.

![Aplicativos](./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicativos")

![Aplicativos](./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicativos")

## Tópicos adicionais
* [Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0914_2016-->