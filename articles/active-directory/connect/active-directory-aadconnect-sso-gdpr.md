---
title: 'Azure AD Connect: Logon Único Contínuo - conformidade com GDPR | Microsoft Docs'
description: Este artigo trata do SSO Contínuo do Microsoft Azure AD (Azure Active Directory) e conformidade com GDPR.
services: active-directory
keywords: o que é o Azure AD Connect, GDPR, componentes necessários para o Microsoft Azure AD, SSO, Logon Único
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Logon Único Contínuo do Microsoft Azure AD: conformidade com GDPR

## <a name="overview"></a>Visão geral

A partir de maio de 2018, entra em vigor uma legislação de privacidade europeia, o [Regulamento Geral de Proteção de Dados (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). O GDPR impõe novas regras às empresas, órgãos governamentais, organizações sem fins lucrativos e outras organizações que oferecem bens e serviços para pessoas da União Europeia (UE) ou que coletam e analisam dados vinculados a residentes da UE. O GDPR se aplica independentemente de onde você está localizado. 

Os produtos e serviços da Microsoft estão disponíveis atualmente para ajudá-lo a atender aos requisitos do GDPR. Leia mais sobre a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).

O SSO Contínuo do Microsoft Azure AD cria o seguinte tipo de log, que pode conter EUII:

- Arquivos de log de rastreamento do Azure AD Connect.

A conformidade com GDPR para SSO Contínuo pode ser alcançada de duas maneiras:

1.  Mediante solicitação, extraia dados de uma pessoa e remova os dados dessa pessoa das instalações.
2.  Certifique-se de que nenhum dado é retido além de 48 horas.

É altamente recomendável a segunda opção, pois é mais fácil de implementar e manter. Consulte as instruções a seguir para cada tipo de log:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Arquivos de log de rastreamento do Azure AD Connect

Verifique o conteúdo da pasta **%ProgramData%\AADConnect** e exclua o conteúdo do log de rastreamento (arquivos **trace-\*.log**) dessa pasta dentro de 48 horas após instalar ou atualizar o Azure AD Connect ou modificar a configuração do SSO Contínuo, pois essa ação pode criar dados cobertos pelo GDPR.

>[!IMPORTANT]
>Não exclua o arquivo **PersistedState.xml** nessa pasta, pois esse arquivo será utilizado para manter o estado da instalação anterior do Azure AD Connect e quando uma instalação de upgrade for feita. Esse arquivo nunca conterá dados sobre uma pessoa e nunca deverá ser excluído.

É possível revisar e excluir esses arquivos de log de rastreamento utilizando o Windows Explorer ou usar o script a seguir do PowerShell para executar as ações necessárias:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Salve o script em um arquivo com a extensão ".PS1". Execute esse script, conforme necessário.

Para saber mais sobre os requisitos do GDPR do Azure AD Connect relacionados, consulte [este artigo](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Observação sobre os logs do Controlador de Domínio

Se o log de auditoria estiver habilitado, esse produto poderá gerar logs de segurança para os Controladores de Domínio. Para saber mais sobre como configurar políticas de auditoria, leia este [artigo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Próximas etapas

- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
