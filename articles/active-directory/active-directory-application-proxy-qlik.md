---
title: Proxy de Aplicativo Azure AD e Qlik Sense | Microsoft Docs
description: Ative o Proxy de Aplicativo no Portal do Azure e instale os Conectores para o proxy reverso.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 331f8ed2e77a076dd8969dc37add1cdeafc852dc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de Aplicativo e Qlik Sense 
O Proxy de Aplicativo do Azure Active Directory e a Qlik Sense fizeram uma parceria para garantir que você possa facilmente usar o Proxy de Aplicativo para fornecer acesso remoto para sua implantação do Qlik Sense.  

## <a name="prerequisites"></a>pré-requisitos 
O restante deste cenário pressupõe que você tenha feito o seguinte:
 
- Configurado o [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- Instalado um conector do Proxy de Aplicativo 

## <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de Aplicativo 
Se você já tiver o Proxy de Aplicativo habilitado, além de um conector instalado, poderá ignorar essa seção e ir para [Adicionar seu aplicativo ao Azure AD com o Proxy de Aplicativo](application-proxy-ping-access.md). 

O conector do Proxy de Aplicativo é um serviço do Windows Server que direciona o tráfego de seus funcionários remotos para seus aplicativos publicados. Para obter informações mais detalhadas, confira [Habilitar o Proxy de Aplicativo no Portal do Azure](active-directory-application-proxy-enable.md). 


1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador global. 
2. Selecione Azure Active Directory > Proxy de Aplicativo. 
3. Selecione Baixar Conector para iniciar o download do conector do Proxy de Aplicativo. Siga as instruções de instalação. 
 
>[!NOTE]
>O download do conector deve habilitar automaticamente o Proxy de Aplicativo para seu diretório, mas se isso não acontecer, selecione **Habilitar Proxy de Aplicativo**. 
 
## <a name="publish-your-applications-in-azure"></a>Publicar seus aplicativos no Azure 
Para publicar o QlikSense, você precisará publicar dois aplicativos no Azure.  

### <a name="application-1"></a>Aplicativo nº 1: 
Siga estas etapas para publicar seu aplicativo. Para um passo a passo mais detalhado das etapas 1 a 8, veja [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-publish-azure-portal.md). 


1. Se você não fez isso na última seção, entre no Portal do Azure como um administrador global. 
2. Selecione **Azure Active Directory** > **Aplicativos empresariais**. 
3. Selecione **Adicionar** na parte superior da folha. 
4. Selecione **Aplicativo local**. 
5.       Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes a seguir para as configurações: 
    - **URL interna**: este aplicativo deve ter uma URL interna que é a própria URL QlikSense. Por exemplo, **https&#58;//demo.qlikemm.com** 
    - **Método de Pré-autenticação**: Azure Active Directory (recomendado, mas não obrigatório) 
1.       Selecione **Adicionar** na parte inferior da folha. Seu aplicativo é adicionado e abre o menu de início rápido. 
2.       No menu de início rápido, selecione **Atribuir um usuário para teste** e adicione pelo menos um usuário para o aplicativo. Verifique se essa conta de teste tem acesso ao aplicativo local. 
3.       Selecione **Atribuir** para salvar a atribuição do usuário de teste. 
4.       (Opcional) Na folha de gerenciamento do aplicativo, selecione Logon único. Escolha **Delegação restrita de Kerberos** no menu suspenso e preencha os campos obrigatórios, com base na sua configuração do Qlik. Selecione **Salvar**. 

### <a name="application-2"></a>Aplicativo nº 2: 
Siga as mesmas etapas do aplicativo nº 1, com as seguintes exceções: 

**Etapa nº 5**: a URL interna agora deve ser a URL do QlikSense com a porta de autenticação usada pelo aplicativo. O padrão é **4244** para HTTPS e 4248 para HTTP. Ex.: **https&#58;//demo.qlik.com:4244** 
**Etapa nº 10:** não configure o SSO e deixe o **Logon único desabilitado**
 
 
## <a name="testing"></a>Testando 
Seu aplicativo agora está pronto para teste. Acesse a URL externa usada para publicar o QlikSense no aplicativo nº 1 e faça logon como um usuário atribuído aos dois aplicativos.  

## <a name="next-steps"></a>Próximas etapas

- [Publique aplicativos com proxy de aplicativo](application-proxy-publish-azure-portal.md)
- [Trabalhar com conectores de Proxy de Aplicativo](active-directory-application-proxy-connectors-azure-portal.md).
