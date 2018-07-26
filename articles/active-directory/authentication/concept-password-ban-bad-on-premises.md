---
title: Versão prévia da proteção por senha do Azure AD
description: Proibir senhas fracas no Active Directory local usando a versão prévia da proteção por senha do Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5d4062ff2c21deeb56eb4ae6e8386452608f85fa
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158797"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Versão prévia: impor proteção por senha do Azure AD ao Windows Server Active Directory

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

A proteção por senha do Azure AD é um novo recurso na visualização pública com base no Azure AD (Azure Active Directory) para aprimorar as políticas de senha em uma organização. A implantação local da proteção por senha do Azure AD usa as listas de senhas proibidas globais e personalizadas armazenadas no Azure AD e realiza as mesmas verificações no local que as alterações baseadas em nuvem do Azure AD.

Há três componentes de software que formam a proteção por senha do Azure AD:

* O serviço de proxy de proteção por senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta atual do Active Directory. Ele encaminha solicitações de controladores de domínio para o Azure AD e retorna a resposta do Azure AD ao controlador de domínio.
* O serviço do agente DC de proteção por senha do Azure AD recebe solicitações de validação de senha da dll do filtro de senha do agente DC, processa-as usando a política de senha atual disponível localmente e retorna o resultado (aprovado/reprovado). Esse serviço é responsável por chamar periodicamente (uma vez por hora) o serviço de proxy de proteção por senha do Azure AD para recuperar novas versões da política de senha. A comunicação para chamadas de e para o serviço de proxy de proteção por senha do Azure AD é tratada pela RPC (Chamada de Procedimento Remoto) sobre TCP. Após a recuperação, novas políticas serão armazenadas em uma pasta sysvol, onde poderão ser replicadas para outros controladores de domínio. O serviço do agente DC também monitora a pasta sysvol em busca de alterações caso outros controladores de domínio tenham criado novas políticas de senha, se uma política recente já estiver disponível, a verificação do serviço de proxy de proteção por senha do Azure AD será ignorada.
* A dll do filtro de senha do Agente DC recebe solicitações de validação de senha do sistema operacional e as encaminha para o serviço do agente DC de proteção por senha do Azure AD executado localmente no controlador de domínio.

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Requisitos

* Todas os computadores nos quais os componentes de proteção por senha do Azure AD estão instalados, incluindo os controladores de domínio, devem estar executando o Windows Server 2012 ou posterior.
* A conectividade de rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospeda o serviço de proxy de proteção por senha do Azure AD.
* Qualquer domínio do Active Directory que esteja executando o software de serviço do agente DC deve usar DFSR para replicação sysvol.
* Uma conta de administrador global para registrar o serviço de proxy de proteção por senha do Azure AD com o Azure AD.
* Uma conta com privilégios de administrador de domínio do Active Directory no domínio raiz da floresta.

### <a name="license-requirements"></a>Requisitos de licença

Os benefícios da lista global de senhas proibidas aplicam-se a todos os usuários do Azure AD (Active Directory do Azure).

A lista de senhas proibidas personalizada requer licenças do Azure AD Basic.

A proteção por senha do Azure AD para o Windows Server Active Directory requer licenças do Azure AD Premium. 

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Baixar

Há dois instaladores necessários para a proteção por senha do Azure AD que podem ser baixados do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Respostas a perguntas comuns

* Não é necessária conectividade com a Internet dos controladores de domínio. Os computadores que executam o serviço de proxy de proteção por senha do Azure AD são os únicos computadores que exigem conectividade com a Internet.
* Nenhuma porta de rede é aberta em controladores de domínio.
* Nenhuma alteração de esquema do Active Directory é necessária.
   * O software usa o contêiner do Active Directory existente e os objetos de esquema serviceConnectionPoint.
* Não há requisitos mínimos de Domínio do Active Directory ou nível Funcional da Floresta (DFL\FFL).
* O software não cria nem exige nenhuma conta nos domínios do Active Directory que ele protege.
* A implantação incremental tem suporte com a compensação de que a política de senha somente é aplicada quando o agente do controlador de domínio é instalado.
* A proteção por senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso no tempo entre uma alteração na configuração da política de senha e o tempo que ela atinge e é imposta em todos os controladores de domínio.

## <a name="next-steps"></a>Próximas etapas

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises.md)