<properties
   pageTitle="Pré-requisitos de Catálogo de Dados do Azure"
   description="O que é necessário para começar a usar o Catálogo de Dados do Azure?"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/11/2015"
   ms.author="maroche"/>

# O que é necessário para começar a usar o Catálogo de Dados do Azure?

## Pré-requisitos de Catálogo de Dados do Azure

Há algumas coisas que você precisará cuidar antes de instalar o **Catálogo de Dados do Azure**. Não se preocupe – isso não vai demorar!

## Azure Active Directory

O Active Directory do Azure (AD do Azure) fornece uma maneira fácil para a sua empresa gerenciar identidades e acesso, tanto na nuvem quanto local. Os usuários podem usar uma única conta de trabalho ou da escola para logon único em qualquer nuvem e aplicativo Web local. O Catálogo de Dados do Azure usa o AD do Azure para autenticar o logon. Para obter mais informações, consulte [Como começar usando o AD do Azure](active-directory-get-started.md).

## Configuração de política do Active Directory

Em algumas situações, os usuários podem encontrar uma situação em que podem acessar o portal do Catálogo de Dados do Azure, mas quando tentam fazer logon na ferramenta de registro da fonte de dados encontram uma mensagem de erro que impede o logon. Esse comportamento de problema pode ocorrer apenas quando o usuário está na rede da empresa, ou quando está se conectando de fora da rede da empresa.

A ferramenta de registro de fonte de dados usa a Autenticação de Formulários para validar logons de usuário no Active Directory. Para um logon bem-sucedido, a autenticação de formulários deve ser habilitada na Política de Autenticação Global por um administrador do Active Directory.

A Política de Autenticação Global permite que os métodos de autenticação sejam habilitados separadamente para conexões intranet e extranet, conforme ilustrado abaixo. Erros de logon poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede por meio da qual o usuário está se conectando.

 ![Política de Autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/pt-BR/library/dn486781.aspx).


## Assinatura do Azure
As assinaturas do Azure ajudam a organizar o acesso aos recursos de serviço de nuvem como o Catálogo de Dados do Azure. Eles também ajudam a controlar como o uso de recursos é reportado, cobrado e pago. Cada assinatura pode ter uma configuração diferente de cobrança e pagamento, assim você pode ter diferentes assinaturas e planos diferentes por departamento, projeto, escritório regional, etc. Cada serviço de nuvem pertence a uma assinatura, e você precisa ter uma assinatura antes de configurar o Catálogo de Dados do Azure. Para saber mais, consulte [Gerenciar contas, assinaturas e funções administrativas](https://msdn.microsoft.com/library/azure/hh531793.aspx).

<!---HONumber=Sept15_HO3-->