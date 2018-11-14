---
title: Histórico de lançamento de versão do agente de proteção de senha do Azure Active Directory no local
description: Lançamento de versão de documentos e o comportamento de histórico de alterações
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913629"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Versão prévia: Histórico de versão do agente de proteção de senha do Microsoft Azure Active Directory

|     |
| --- |
| A proteção de senha do Azure Active Directory é uma versão prévia do recurso do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12250"></a>1.2.25.0

Data do lançamento: 11/01/2018

Correções:

* Serviço de agente e o proxy do controlador de domínio não deve falhar devido a falhas de relação de confiança de certificado.
* Serviço de agente e o proxy do controlador de domínio têm correções adicionais para computadores em conformidade com FIPS.
* Serviço de proxy agora funcionará corretamente em um ambiente de chamadas rede somente 1.2 TLS.
* Menor de desempenho e correções de robustez
* Log aprimorado

Alterações:

* O nível de sistema operacional mínimo exigido para o serviço Proxy é agora o Windows Server 2012 R2. O nível de sistema operacional mínimo exigido para o serviço do agente DC permanece no Windows Server 2012.
* O algoritmo de validação de senha usa uma tabela de normalização de caracteres expandida. Isso pode resultar na rejeição de senhas aceitas em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data do lançamento: 17/08/2018

Correções:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora dão suporte a autenticação multifator
* Register-AzureADPasswordProtectionProxy requer um WS2012 ou domínio posterior no domínio para evitar erros de criptografia.
* O serviço de agente do controlador de domínio é mais confiável sobre como solicitar uma nova política de senha do Azure na inicialização.
* O serviço de agente do DC solicitará uma nova política de senha do Azure a cada hora se necessário, mas agora vai fazer isso em uma hora de início selecionada aleatoriamente.
* O serviço de agente do controlador de domínio não fará com que um atraso indefinido no novo anúncio do controlador de domínio quando instalado em um servidor antes da sua promoção como uma réplica.
* O serviço de agente do controlador de domínio agora respeitará a definição de configuração "Habilitar a proteção por senha no Active Directory do Windows Server"
* Ambos os instaladores de agente e o proxy do controlador de domínio agora serão compatível com a atualização in-loco ao atualizar para versões futuras.

> [!WARNING]
> Atualização in-loco de versão 1.1.10.3 não tem suporte e resultará em um erro de instalação. Para atualizar para a versão 1.2.10 ou posterior, você deve primeiro completamente desinstalar o software de serviço de agente e o proxy de controlador de domínio, e instalar a nova versão a partir do zero. Instalar e configurar o serviço proxy de proteção por senha do Azure Active Directory.  Não é necessário registrar novamente a floresta.

> [!NOTE]
> Atualizações in-loco do software agente DC exigirá uma reinicialização.

* Serviço de agente e o proxy do controlador de domínio agora oferecem suporte em execução em um servidor configurado para usar somente os algoritmos compatíveis com FIPS.
* Menor de desempenho e correções de robustez
* Log aprimorado

## <a name="11103"></a>1.1.10.3

Data do lançamento: 15/06/2018

Versão prévia pública inicial

## <a name="next-steps"></a>Próximas etapas

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
