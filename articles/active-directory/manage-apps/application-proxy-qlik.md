---
title: Proxy de Aplicativo Azure AD e Qlik Sense | Microsoft Docs
description: Ative o Proxy de Aplicativo no Portal do Azure e instale os Conectores para o proxy reverso.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 855cf81dbf24b0662048f69061ca85b72ff5f670
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171240"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de Aplicativo e Qlik Sense 
O Proxy de Aplicativo do Azure Active Directory e a Qlik Sense fizeram uma parceria para garantir que você possa facilmente usar o Proxy de Aplicativo para fornecer acesso remoto para sua implantação do Qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O restante deste cenário pressupõe que você tenha feito o seguinte:
 
- Configurado o [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Instalado um conector de Proxy de Aplicativo](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicar seus aplicativos no Azure 
Para publicar o QlikSense, você precisará publicar dois aplicativos no Azure.  

### <a name="application-1"></a>Aplicativo nº 1: 
Siga estas etapas para publicar seu aplicativo. Para um passo a passo mais detalhado das etapas 1 a 8, veja [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md). 


1. Entre no Portal do Azure como um administrador global. 
2. Selecione **Azure Active Directory** > **Aplicativos empresariais**. 
3. Selecione **Adicionar** na parte superior da folha. 
4. Selecione **Aplicativo local**. 
5.       Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes a seguir para as configurações: 
    - **URL interna**: este aplicativo deve ter uma URL interna que é a própria URL do QlikSense. Por exemplo, **https&#58;//demo.qlikemm.com:4244** 
    - **Método de pré-autenticação**: Azure Active Directory (recomendado, mas não obrigatório) 
1.       Selecione **Adicionar** na parte inferior da folha. Seu aplicativo é adicionado e abre o menu de início rápido. 
2.       No menu de início rápido, selecione **Atribuir um usuário para teste** e adicione pelo menos um usuário para o aplicativo. Verifique se essa conta de teste tem acesso ao aplicativo local. 
3.       Selecione **Atribuir** para salvar a atribuição do usuário de teste. 
4.       (Opcional) Na folha de gerenciamento do aplicativo, selecione Logon único. Escolha **Delegação restrita de Kerberos** no menu suspenso e preencha os campos obrigatórios, com base na sua configuração do Qlik. Clique em **Salvar**. 

### <a name="application-2"></a>Aplicativo nº 2: 
Siga as mesmas etapas do aplicativo nº 1, com as seguintes exceções: 

**Etapa 5**: a URL interna agora deve ser a URL do QlikSense com a porta de autenticação usada pelo aplicativo. O padrão é **4244** para HTTPS e 4248 para HTTP. Exemplo: **https&#58;//demo.qlik.com:4244**</br></br> 
**Etapa 10:** não configure o SSO e deixe o **Logon único desabilitado**
 
 
## <a name="testing"></a>Testando 
Seu aplicativo agora está pronto para teste. Acesse a URL externa usada para publicar o QlikSense no aplicativo nº 1 e faça logon como um usuário atribuído aos dois aplicativos.  

## <a name="additional-references"></a>Referências adicionais
Para saber mais sobre a publicação de Qlik Sense com o Proxy de aplicativo, consulte o artigo da comunidade do Qlik: [Azure AD com autenticação integrada do Windows usando uma delegação restrita de Kerberos com Qlik Sense](https://community.qlik.com/docs/DOC-20183).

## <a name="next-steps"></a>Próximas etapas

- [Publique aplicativos com proxy de aplicativo](application-proxy-add-on-premises-application.md)
- [Trabalhando com conectores de Proxy de aplicativo](application-proxy-connector-groups.md)

