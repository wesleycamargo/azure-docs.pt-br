---
title: Pesquisa de nome de usuário durante a autenticação de entrada - Azure Active Directory | Microsoft Docs
description: Como na tela de mensagens refletem a pesquisa de nome de usuário durante a entrada
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007477"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Descoberta de realm inicial durante o logon para serviços do Microsoft 365

Estamos alterando nosso comportamento de entrada do Azure Active Directory (Azure AD) para liberar espaço para novos métodos de autenticação e melhorar a usabilidade. Durante a entrada, AD do Azure determina em que um usuário precisa se autenticar. Azure AD toma decisões inteligentes, lendo as configurações de usuário e organização para o nome de usuário inserido na página de entrada. Essa é uma etapa em direção um futuro sem senha que permite que as credenciais adicionais, como FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de descoberta de realm inicial

Historicamente, descoberta de realm inicial foi controlada por domínio que é fornecido na entrada ou por uma política de descoberta de Realm inicial para alguns aplicativos herdados. Por exemplo, no antigo comportamento de descoberta um usuário do Azure Active Directory poderia digitar incorretamente o nome de usuário, mas ainda seria chegam na tela de coleção de credenciais da sua organização. Isso ocorre quando o usuário fornece corretamente o nome de domínio "contoso.com" da organização. Esse comportamento não permite a granularidade personalizar as experiências para um usuário individual.

Para dar suporte a uma variedade de credenciais e aumentar a usabilidade, comportamento de pesquisa de nome de usuário do Azure Active Directory durante o processo está atualizado. O novo comportamento faz com que decisões inteligentes pela leitura de Inquilino e usuário configurações de nível com base no nome de usuário na página de entrada. Para tornar isso possível, o Azure Active Directory verificará para ver se o nome de usuário que é inserida na página de entrada existe em seu domínio especificado ou redireciona o usuário para fornecer suas credenciais. 

Um benefício adicional desse trabalho é aprimorado de erro no sistema de mensagens. Aqui estão alguns exemplos de aprimorado de erro no sistema de mensagens ao entrar em um aplicativo que suporta somente a usuários do Active Directory do Azure.

1. O nome de usuário for digitado incorretamente ou o nome de usuário ainda não foram sincronizada com o Azure AD:
  
    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/signin-realm-discovery/typo-username.png)
  
2. O nome de domínio for digitado incorretamente:
  
    ![o nome de domínio é digitado incorretamente ou não encontrado](./media/signin-realm-discovery/typo-domain.png)
  
3. Usuário tenta entrar com um domínio de consumidor conhecidos:
  
    ![entrar com um domínio de consumidor conhecidos](./media/signin-realm-discovery/consumer-domain.png)
  
4. A senha for digitado incorretamente, mas o nome de usuário é preciso:  
  
    ![senha for digitado incorretamente com um bom nome de usuário](./media/signin-realm-discovery/incorrect-password.png)
  
## <a name="additional-info"></a>Informações adicionais

Além da experiência do usuário aprimorada de entrada, essa alteração inclui mecanismos que podem ajudar a reduzir o abuso de enumeração de nome de usuário em larga escala.

Essa alteração destina-se inicialmente para domínios gerenciados e começa a implantar em maio de 2019, mas não será iniciado distribuindo para domínios federados no final de 2019. As datas de disponibilização exatas para domínios federados depende de comentários do cliente.

> [!IMPORTANT]
> Esse recurso terá um impacto significativo em domínios federados no nível do domínio antigo Home Realm Discovery para forçar a federação de terceira parte confiável. Algumas organizações treinar seus funcionários a entrar com um nome de usuário que não existe no Active Directory do Azure, mas contém o nome de domínio adequado, porque os nomes de domínio encaminha os usuários no momento ao ponto de extremidade de domínio da sua organização. O novo comportamento de entrada não permite isso. O usuário é notificado para corrigir o nome de usuário, e eles não são permitidos para entrar com um nome de usuário que não existe no Azure Active Directory.
>
> Se você ou sua organização tiver práticas que dependem do comportamento antigo, é importante que os administradores da organização para atualizar a documentação de entrada e autenticação de funcionários e para treinar os funcionários usem seu nome de usuário do Active Directory do Azure para entrar.
  
Se você tiver preocupações com o novo comportamento, deixe seus comentários na **comentários** seção deste artigo.  

## <a name="next-steps"></a>Próximas etapas

[Personalizar a entrada identidade Visual](../fundamentals/add-custom-domain.md)