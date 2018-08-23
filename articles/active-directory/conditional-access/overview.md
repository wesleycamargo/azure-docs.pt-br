---
title: O que é o acesso condicional no Azure Active Directory? | Microsoft Docs
description: Saiba como o acesso condicional no Azure Active Directory ajuda você a implementar decisões de acesso automatizado que não são baseadas apenas em quem tenta acessar um recurso, mas também em como um recurso é acessado.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3e96e251e655c7c7ad862fbf875111e08c0dfbdf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022802"
---
# <a name="what-is-conditional-access-in-azure-active-directory"></a>O que é o acesso condicional no Azure Active Directory?

Segurança é uma grande preocupação para organizações que usam a nuvem. Um aspecto importante da segurança em nuvem é a identidade e o acesso quando o assunto é gerenciar os recursos em nuvem. Em um mundo primeiro o dispositivo móvel e a nuvem, os usuários podem acessar os recursos da organização usando uma grande variedade de dispositivos e aplicativos de qualquer lugar. Por causa disso, concentrar-se apenas em quem pode acessar um recurso não é mais suficiente. Para dominar o equilíbrio entre segurança e produtividade, você também precisa considerar como um recurso é acessado em uma decisão de controle de acesso. Com acesso condicional do Azure AD, você pode atender a esse requisito. O acesso condicional é um recurso do Azure Active Directory. Com o acesso condicional, você pode implementar decisões de controle de acesso automatizado para o acesso a aplicativos de nuvem com base em condições. 

![Controle](./media/overview/81.png)

Este artigo apresenta uma visão geral conceitual do acesso condicional no Azure AD.



## <a name="common-scenarios"></a>Cenários comuns

Em um mundo móvel e em nuvem, o Azure Active Directory permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Com a proliferação de dispositivos (incluindo BYOD), trabalho fora de redes corporativas e aplicativos de SaaS de terceiros, você tem duas metas opostas:

- Capacitar os usuários para serem produtivos sempre e em qualquer lugar
- Proteger os ativos corporativos a qualquer momento

Usando políticas de acesso condicional, você pode aplicar os controles de acesso certos nas condições necessárias. O acesso condicional do AD Azure oferece segurança adicional quando necessário e fica fora do caminho do usuário quando não o é. 

A seguir, são apresentadas algumas preocupações de acesso comuns em que o acesso condicional pode ajudar:



- **[Risco de entrada](conditions.md#sign-in-risk)**: o Azure AD Identity Protection detecta riscos de entrada. Como você restringe o acesso caso um risco de entrada detectado indique um mau ator? E se você quisesse obter prova mais sólida de que uma entrada foi realizada pelo usuário legítimo? E se a dúvida é forte o suficiente para até mesmo impedir que usuários específicos acessem um aplicativo?  

- **[Local de rede](location-condition.md)**: o Azure AD é acessível de qualquer lugar. E se uma tentativa de acesso for realizada em um local de rede que não esteja sob o controle do departamento de TI? O uso de uma combinação de nome de usuário e senha pode ser bom o suficiente como prova de identidade para tentativas de acesso aos recursos da rede corporativa. E se você exigir uma prova de identidade mais forte para tentativas de acesso iniciadas de outros países ou regiões do mundo inesperados? E se você ainda quiser bloquear tentativas de acesso de determinados locais?  

- **[Gerenciamento de dispositivos](conditions.md#device-platforms)**: no Azure AD, os usuários podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. E se você exigir que as tentativas de acesso somente sejam realizadas utilizando dispositivos gerenciados pelo departamento de TI? E se você ainda quiser bloquear determinados tipos de dispositivo de acessar aplicativos na nuvem no ambiente? 

- **[Aplicativo cliente](conditions.md#client-apps)**: atualmente, você pode acessar muitos aplicativos na nuvem usando tipos de aplicativos diferentes, como aplicativos baseados na Web, aplicativos móveis ou aplicativos da área de trabalho. E se uma tentativa de acesso for realizada usando-se um tipo de aplicativo cliente que causa problemas conhecidos? E se você precisar de um dispositivo gerenciado pelo departamento de TI para determinados tipos de aplicativo? 

Essas perguntas e as respostas relacionadas representam cenários de acesso comuns para acesso condicional do Azure AD. Acesso condicional é um recurso do Azure Active Directory que permite processar cenários de acesso usando uma abordagem baseada em política.


## <a name="conditional-access-policies"></a>Políticas de acesso condicional

Política de acesso condicional é a definição de um cenário de acesso que usa o seguinte padrão:

![Controle](./media/overview/10.png)

**Em seguida, isso** define a resposta da política. É importante observar que o objetivo de uma política de acesso condicional não é conceder acesso a um aplicativo de nuvem. No Azure AD, a concessão do acesso a aplicativos de nuvem é assunto de atribuições do usuário. Com uma política de acesso condicional, você controla como usuários autorizados (usuários que receberam acesso a um aplicativo de nuvem) podem acessar aplicativos de nuvem em condições específicas. Na resposta, você impõe requisitos adicionais, como autenticação multifator, um dispositivo gerenciado e outros. No contexto de acesso condicional do Azure AD, os requisitos que a política impõe são chamados de controles de acesso. Na formulário mais restritiva, a política pode bloquear o acesso. Para obter mais informações, consulte [Controles de acesso no acesso condicional do Azure Active Directory](controls.md).
     

**Quando isso acontece** define o motivo de disparar a política. Esse motivo é caracterizado por um grupo de condições que foram atendidas. No acesso condicional do Azure AD, as duas condições de atribuição desempenham um papel especial:

- **[Usuários](conditions.md#users-and-groups)**: os usuários que realizam uma tentativa de acesso (**Quem**). 

- **[Aplicativos de nuvem](conditions.md#cloud-apps)**: os destinos de uma tentativa de acesso (**O quê**).    

Essas duas condições são obrigatórias em uma política de acesso condicional. Além das duas condições obrigatórias, você também pode incluir condições adicionais que descrevam como a tentativa de acesso é realizada. Os exemplos comuns estão usando dispositivos móveis ou locais que estejam fora da rede corporativa. Para saber mais, confira [Condições no acesso condicional do Azure Active Directory](conditions.md).   

A combinação de condições e seus controles de acesso representa uma política de acesso condicional. 

![Controle](./media/overview/51.png)

Com o acesso condicional do Azure AD, você pode controlar como os usuários autorizados podem acessar seus aplicativos na nuvem. O objetivo de uma política de acesso condicional é impor controles de acesso adicionais em uma tentativa de acesso a um aplicativo de nuvem controlado por como uma tentativa de acesso é realizada.

Uma vantagem de usar uma abordagem baseada em política para proteger o acesso aos aplicativos de nuvem é poder começar esboçando os requisitos de política para o ambiente usando a estrutura descrita neste artigo, sem se preocupar com a implementação técnica. 


## <a name="license-requirements-for-using-conditional-access"></a>Requisitos de licença para usar o acesso condicional

O uso do acesso condicional requer uma licença do Microsoft Azure AD Premium. Para localizar a licença correta para os requisitos, consulte [Comparar recursos geralmente disponíveis nas edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber mais sobre:
    - Condições, consulte [Condições no acesso condicional do Azure Active Directory](conditions.md).

    - Controles de acesso, consulte [Controles de acesso no acesso condicional do Azure Active Directory](controls.md).

- Se você deseja obter alguma experiência com a configuração de políticas de acesso condicional, confira [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 

- Se você deseja um plano de implantação passo a passo com as políticas recomendadas, consulte o [plano de implantação de acesso condicional](http://aka.ms/conditionalaccessdeploymentplan)
