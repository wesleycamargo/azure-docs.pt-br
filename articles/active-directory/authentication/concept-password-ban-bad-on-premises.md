---
title: Versão prévia da proteção por senha do Azure AD
description: Proibir senhas fracas no Active Directory local usando a versão prévia da proteção por senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 816c459ca6edd7204ccdcdf9d402f2d4499d9116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662516"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Visualização: Impor a proteção por senha do Azure AD para o Active Directory do Windows Server

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

A proteção por senha do Azure AD é um novo recurso na visualização pública com base no Azure AD (Azure Active Directory) para aprimorar as políticas de senha em uma organização. A implantação local da proteção por senha do Azure AD usa as listas de senhas proibidas globais e personalizadas armazenadas no Azure AD e realiza as mesmas verificações no local que as alterações baseadas em nuvem do Azure AD.

Há três componentes de software que formam a proteção por senha do Azure AD:

* O serviço de proxy de proteção por senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta atual do Active Directory. Ele encaminha solicitações de controladores de domínio para o Azure AD e retorna a resposta do Azure AD ao controlador de domínio.
* O serviço do agente DC de proteção por senha do Azure AD recebe solicitações de validação de senha da dll do filtro de senha do agente DC, processa-as usando a política de senha atual disponível localmente e retorna o resultado (aprovado/reprovado). Esse serviço é responsável por chamar periodicamente (uma vez por hora) o serviço de proxy de proteção por senha do Azure AD para recuperar novas versões da política de senha. A comunicação entre o serviço do agente DC de proteção de senha do Azure AD e o serviço de proxy de proteção de senha do Azure AD é tratada usando RPC (chamada de procedimento remoto) sobre TCP. Após a recuperação, novas políticas serão armazenadas em uma pasta sysvol, onde poderão ser replicadas para outros controladores de domínio. O serviço do agente DC também monitora a pasta sysvol em busca de alterações caso outros controladores de domínio tenham criado novas políticas de senha, se uma política recente já estiver disponível, então novas solicitações de download de política serão ignoradas.
* A dll do filtro de senha do Agente DC recebe solicitações de validação de senha do sistema operacional e as encaminha para o serviço do agente DC de proteção por senha do Azure AD executado localmente no controlador de domínio.

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

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
* É recomendável instalar o agente DC em todos os DCs para garantir a imposição de proteção de senha.
* A proteção por senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso no tempo entre uma alteração na configuração da política de senha e o tempo que ela atinge e é imposta em todos os controladores de domínio.

## <a name="next-steps"></a>Próximas etapas

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
