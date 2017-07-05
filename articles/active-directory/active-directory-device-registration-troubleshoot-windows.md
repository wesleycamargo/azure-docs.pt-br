---
title: "Solucionando problemas do registro automático de computadores ingressados no domínio do Azure AD para Windows 10 e Windows Server 2016 | Microsoft Docs"
description: "Solucionando problemas do registro automático de computadores ingressados no domínio do Azure AD para Windows 10 e Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.contentlocale: pt-br
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Solução de problemas de registro automático de computadores ingressados no domínio do Azure AD – Windows 10 e Windows Server 2016

Este tópico é aplicável aos seguintes clientes:

-   Windows 10
-   Windows Server 2016

Para outros clientes Windows, confira [Solução de problemas de registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows](active-directory-device-registration-troubleshoot-windows-legacy.md).

Este tópico pressupõe que você tenha configurado o registro automático de dispositivos ingressados no domínio conforme descrito em [Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory](active-directory-device-registration-get-started.md) para dar suporte aos seguintes cenários:

- [Acesso condicional com base em dispositivo](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Roaming corporativo de configurações](active-directory-windows-enterprise-state-roaming-overview.md)

- [Configurar o Hello for Business](active-directory-azureadjoin-passport-deployment.md)


Este documento fornece diretrizes de solução de problemas sobre como resolver os problemas potenciais. 

O registro tem suporte na Atualização do Windows de 10 de novembro de 2015 e superior.  
É recomendável usar a Atualização de Aniversário para habilitar os cenários acima.

## <a name="step-1-retrieve-the-registration-status"></a>Etapa 1: Recuperar o status do registro 

**Para recuperar o status do registro:**

1. Abra o prompt de comando como administrador.

2. Digite **dsregcmd /status**



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>Etapa 2: Avaliar o status do registro 

Examine os seguintes campos e garanta que eles tenham os valores esperados:

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

Esse campo mostra se o dispositivo está registrado no Azure AD. Se o valor aparecer como ‘NO’, o registro não foi concluído. 

**Possíveis causas:**

- Falha na autenticação do computador para o registro.

- Há um proxy HTTP na organização que não pode ser descoberto pelo computador

- O computador não pode alcançar o Azure AD para autenticação ou o Azure DRS para registro

- O computador pode não estar na rede interna da organização ou em VPN com a linha de visão direta para um controlador de domínio do AD local.

- Se o computador tiver um TPM, ele poderá estar em um estado inválido.

- Pode haver um erro de configuração nos serviços observado anteriormente no documento que você precisará verificar novamente. Alguns exemplos comuns são:

    - O servidor de Federação não tem pontos de extremidade WS-Trust habilitados

    - O servidor de federação pode não permitir a autenticação de entrada de computadores na rede usando a Autenticação Integrada do Windows.

    - Não há nenhum objeto de Ponto de Conexão de Serviço que aponta para o seu nome de domínio verificado no Azure AD na floresta do AD à qual o computador pertence

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

Esse campo mostra se o dispositivo ingressou em um Active Directory local ou não. Se o valor aparecer como **NO**, o dispositivo não poderá se registrar automaticamente no Azure AD. Certifique-se primeiro de que o dispositivo ingresse no Active Directory local para que ele possa se registrar no Azure AD. Se você estiver buscando ingressar o computador no Azure AD diretamente, vá até Learn about capabilities of Azure Active Directory Join (Saiba mais sobre os recursos do Ingresso do Azure Active Directory).

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Esse campo mostra se o dispositivo está registrado no Azure AD, mas como um dispositivo pessoal (marcado como “Ingressado no Espaço de Trabalho”). Se esse valor deve aparecer como 'NO' para um computador ingressado no domínio registrado no Azure AD, mas aparecer como YES, isso significa que uma conta corporativa ou de estudante foi adicionada antes de o computador concluir o registro. Nesse caso a conta será ignorada se a versão de Atualização de Aniversário do Windows 10 (1607 ao executar o comando WinVer na janela 'Executar' ou em uma janela de prompt de comando) estiver sendo usada.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES e AzureADPrt : YES
  
Esses campos mostram que o usuário foi autenticado com êxito no Azure AD ao se conectar ao dispositivo. Se eles mostrarem ‘NO’, os itens a seguir serão possíveis causas:

- STK (chave de armazenamento) inválida no TPM associada ao dispositivo no registro (verifique o KeySignTest durante a execução com privilégios elevados).

- ID de logon alternativo

- Proxy HTTP não encontrado

## <a name="next-steps"></a>Próximas etapas

Para saber mais, veja [Perguntas frequentes sobre o registro de dispositivo automático](active-directory-device-registration-faq.md) 
