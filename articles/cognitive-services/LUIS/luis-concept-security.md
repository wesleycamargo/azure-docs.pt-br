---
title: Entender o acesso a aplicativos LUIS – Azure | Microsoft Docs
description: Saiba como acessar a criação do LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fddffbcabba753e9ef214f924d5ff2cee38427a5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301686"
---
# <a name="authoring-and-endpoint-user-access"></a>Acesso de criação e do usuário de ponto de extremidade
O acesso de criação está disponível para proprietários e colaboradores. Para um aplicativo privado, o acesso de ponto de extremidade está disponível para proprietários e colaboradores. Para um aplicativo público, o acesso de ponto de extremidade está disponível a todos que têm sua conta LUIS e que tem a ID do aplicativo público. 

## <a name="access-to-authoring"></a>Acesso à criação
O acesso ao aplicativo do site do [LUIS](luis-reference-regions.md#luis-website) ou às [APIs de criação](https://aka.ms/luis-authoring-apis) é controlado pelo proprietário do aplicativo. 

O proprietário e todos os colaboradores têm acesso para criar o aplicativo. 

|O acesso de criação inclui|Observações|
|--|--|
|Adicionar ou remover chaves de ponto de extremidade||
|Versão de exportação||
|Exportar logs de ponto de extremidade||
|Versão de importação||
|Tornar um aplicativo público|Quando um aplicativo for público, qualquer pessoa com uma chave de criação ou de ponto de extremidade poderá consultá-lo.|
|Modificar modelo|
|Publicar|
|Examinar declarações de ponto de extremidade para [aprendizado ativo](luis-how-to-review-endoint-utt.md)|
|Treinar|

## <a name="access-to-endpoint"></a>Acesso ao ponto de extremidade
O acesso ao ponto de extremidade para consulta LUIS é controlado pela configuração **Pública** do aplicativo na página **Configurações**. Uma consulta de ponto de extremidade de um aplicativo privado é verificada para uma chave autorizada com ocorrências de cota restantes. A consulta de ponto de extremidade de um aplicativo público também precisa fornecer uma chave do ponto de extremidade (de quem quer que esteja realizando a consulta) que também é verificada para ocorrências de cota restantes. 

A chave do ponto de extremidade é passada na querystring da solicitação GET ou o cabeçalho da solicitação POST.

![Definir aplicativo para público](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Segurança de ponto de extremidade de aplicativo privado
O ponto de extremidade de um aplicativo privado está disponível para o seguinte:

|Chave e usuário|Explicação|
|--|--|--|
|Chave de criação do proprietário| Até 1000 ocorrências de ponto de extremidade|
|Chaves de criação dos colaboradores| Até 1000 ocorrências de ponto de extremidade|
|Chaves de ponto de extremidade adicionadas da página **[Publicar](luis-how-to-publish-app.md)**|O proprietário e os colaboradores podem adicionar chaves de ponto de extremidade|

Outras chaves de criação ou de ponto de extremidade **não** têm acesso.

### <a name="public-app-endpoint-access"></a>Acesso de ponto de extremidade do aplicativo público
Configure o aplicativo como **público** na página **Configurações** do aplicativo. Depois que um aplicativo for configurado como público, _qualquer_ chave de criação LUIS válida ou chave do ponto de extremidade LUIS poderá consultar seu aplicativo, desde que a chave não tenha usado toda a cota de ponto de extremidade.

Um usuário que não é proprietário ou colaborador só poderá acessar um aplicativo público se tiver recebido a ID do aplicativo. O LUIS não tem um _mercado_ público ou outra maneira de pesquisar um aplicativo público.  

## <a name="microsoft-user-accounts"></a>Contas de usuário Microsoft
Autores e colaboradores podem adicionar chaves ao LUIS na página de publicação. A conta de usuário Microsoft que cria a chave de LUIS no portal do Azure precisa ser o proprietário do aplicativo ou um colaborador do aplicativo. 

Consulte [Usuário do locatário do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre as contas de usuário do Active Directory. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Protegendo o ponto de extremidade 
É possível controlar quem pode ver sua chave do ponto de extremidade LUIS chamando-a em um ambiente servidor a servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já estará segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar a API do servidor (como uma [função](https://azure.microsoft.com/services/functions/) do Azure) com acesso controlado (como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Embora essa estratégia não impeça ataques "man-in-the-middle", ela oculta seu ponto de extremidade dos usuários, permite que você controle o acesso e que você adicione um log de resposta de ponto de extremidade (como o [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Conformidade de segurança
O LUIS concluiu com sucesso a auditoria ISO 27001:2013 e ISO 27018:2014 com ZERO não conformidades (descobertas) no relatório de auditoria. Além disso, o LUIS também obteve o Certificado CSA STAR com o Prêmio Ouro mais alto possível para a avaliação de funcionalidade de maturidade. O Azure é o único grande provedor de serviços de nuvem pública para ganhar essa certificação. Para obter mais detalhes, é possível encontrar o LUIS incluído na instrução de escopo atualizada no principal documento de [visão geral de conformidade](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) do Azure referenciado nas páginas ISO da [Central de Confiabilidade](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001).  

## <a name="next-steps"></a>Próximas etapas

Confira [Melhores práticas](luis-concept-best-practices.md) para saber como usar intenções e entidades para obter as melhores previsões.
