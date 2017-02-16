---
title: "O que é a visualização de colaboração B2B do Azure Active Directory? | Microsoft Docs"
description: "A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/01/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8fdd2993e6b3f0345948f35753ebb7a20ed174e3
ms.openlocfilehash: d5764ee2a8447de70798c24a3152a854327ae89d


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>O que é a visualização da colaboração B2B do Azure AD?

Os recursos de colaboração B2B do Azure AD permitem que os profissionais de TI e os profissionais de informações trabalhem em conjunto com seus parceiros de qualquer outra organização do planeta. Eles podem fornecer acesso a documentos, aplicativos e recursos enquanto mantêm o controle total sobre seus dados internos. Os desenvolvedores podem usar as APIs entre empresas do Azure AD para escrever aplicativos que reúnem as duas organizações de uma maneira segura que parece perfeita para os profissionais de informações e é intuitiva para eles navegarem.

Os recursos de colaboração B2B do Azure AD permitem que as organizações de todos os tamanhos e em todos os setores, independentemente de seus requisitos de conformidade e governança trabalhem de forma fácil e segura com colaboradores em todo o mundo. Essa é a meta dessa Atualização da Visualização Pública da colaboração B2B.

## <a name="how-does-it-work"></a>Como ele funciona?

Na versão atual (no [Portal Clássico do Azure](https://manage.windowsazure.com)), para estabelecer uma relação com uma organização, os profissionais de TI e os profissionais de informações podem adicionar usuários de outra organização um de cada vez ou alguns de cada vez por meio do portal ou da API do Gerenciador de Convite. Agora, os administradores podem usar as novas experiências do portal no Azure (https://portal.azure.com) e o PowerShell para isso. E os profissionais de informações podem usar as experiências do Painel de Acesso em http://myapps.microsoft.com. Os desenvolvedores podem criar aplicativos usando a API do Gerenciador de Convite B2B do Azure AD para adicionar usuários de colaboração B2B e personalizar os fluxos de trabalho de convite e integração.

Os usuários de colaboração B2B normalmente são integrados por meio de um processo de convite + resgate. Veja como ele funciona.

1. Márcio Alvez da WoodGrove deseja adicionar Sam Oogle usando seu email do Gmail (gsamoogle@gmail.com)

2. Márcio vai até o portal da WoodGrove (portal.azure.com) ou painel de acesso (myapps.microsoft.com), entra e adiciona o usuário ao aplicativo, grupo ou diretório da WoodGrove.

3. Márcio especifica um email de convite personalizado para enviar ao Sam.

4. Assim que ele terminar, o seguinte usuário será criado no AD da WoodGrove (a captura de tela é do UX de administração no portal.azure.com):

  ![o usuário é adicionado](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Assim que terminarmos de adicionar esse usuário, o Azure AD enviará um email de convite para Sam:

  ![email de convite enviado para Sam](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. Agora o Sam seleciona **Introdução** e entra. Neste ponto, o Azure AD atualiza seu objeto de usuário no diretório com informações do seu token (a captura de tela é do UX de administração no [Portal do Azure](https://portal.azure.com)):

  ![o perfil do usuário é populado](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. Agora que o convite de Sam foi resgatado, ele pode obter acesso aos recursos da WoodGrove e certamente pode ser gerenciado, como qualquer outro usuário no diretório, pelo administrador (a captura de tela é do UX de administração no [Portal do Azure](https://portal.azure.com)):

  ![Agora o Sam é um usuário do Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Recursos da Visualização Pública
Vocês têm usado os recursos de colaboração B2B que tínhamos na visualização pública e têm nos fornecido muitos comentários excelentes. E nós ouvimos! Estamos empacotamento todos os aprimoramentos que fizemos nessa Atualização da Visualização Pública. Estes são os principais recursos na Atualização da Visualização Pública da colaboração B2B:

1. Aprimoramentos de UX de administração para experiência de B2B
  - chegando ao https://portal.azure.com
  - Capacidade dos administradores convidarem usuários B2B para o diretório ou em qualquer aplicativo ou grupo

2. Recursos de convite de autoatendimento de colaboração B2B para profissionais de informações no Painel de Acesso: https://myapps.microsoft.com. Os profissionais de informações podem convidar usuários de colaboração B2B para qualquer grupo ou aplicativo de autoatendimento que eles gerenciam.

3. Agora você pode convidar um usuário com qualquer endereço de email. Se o usuário tem um endereço de email do Office365 ou Microsoft Exchange local, um endereço de email outlook.com ou qualquer endereço de email social (Gmail, Yahoo e assim por diante), ele pode acessar diretamente a organização convidada com a criação leve e embutida de uma conta do Azure AD ou uma conta da Microsoft.

4. Beneficie-se de um email de convite profissional com a maraca do locatário.

5. Ampla capacidade de personalizar a integração usando as APIs de convite.

6. Autenticação Multifator para usuários de colaboração B2B na organização que está convidando.

7. Capacidade de delegar convites para não administradores.

8. Suporte do PowerShell para colaboração B2B.

9. Recursos de auditoria e relatório.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


