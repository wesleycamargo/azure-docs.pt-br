---
title: Propriedades de um usuário de colaboração B2B do Azure Active Directory | Microsoft Docs
description: As propriedades do usuário de colaboração B2B do Azure Active Directory podem ser configuradas
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/5/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 01693f16b0af59881c22fefb6ec8abe0c4fb3874
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996635"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Propriedades de um usuário de colaboração B2B do Azure Active Directory

Um usuário de colaboração entre empresas (B2B) do Azure Active Directory (AD do Azure) é um usuário com UserType = Convidado. Normalmente, esse usuário convidado representa uma organização parceira e, por padrão, tem privilégios limitados no diretório que convida.

Dependendo das necessidades da organização que convida, um usuário de colaboração B2B do AD do Azure pode estar em um dos seguintes estados de conta:

- Estado 1: hospedado em uma instância externa do Azure AD e representado como usuário convidado na organização que convida. Nesse caso, o usuário B2B entra usando uma conta do Azure AD que pertence ao locatário convidado. Se a organização do parceiro não usar o Azure AD, o usuário convidado no Azure AD ainda será criado. Os requisitos são o resgate do convite e a verificação do endereço de email pelo Azure AD. Essa disposição também é chamada de locação JIT (just-in-time) ou, às vezes, de locação "viral".

- Estado 2: hospedado em uma conta da Microsoft ou outra conta e representado como um usuário convidado na organização host. Nesse caso, o usuário convidado entra com uma conta Microsoft ou uma conta social (google.com ou semelhante). A identidade do usuário convidado é criada como uma conta Microsoft no diretório da organização que fez o convite durante o resgate de oferta.

- Estado 3: hospedado no Active Directory local da organização host e sincronizado com o Azure AD da organização host. É possível usar o Azure AD Connect para sincronizar as contas de parceiros com a nuvem como usuários B2B do Azure AD com UserType = Convidado. Confira [Conceder às contas de parceiros gerenciadas localmente acesso a recursos na nuvem](hybrid-on-premises-to-cloud.md).

- Estado 4: hospedado na organização do Azure AD com UserType = Convidado e com credenciais que a organização host gerencia.

  ![Exibição das iniciais do emissor do convite](media/user-properties/redemption-diagram.png)


Agora, vamos ver a aparência de um usuário de colaboração B2B do Azure AD no Azure AD.

### <a name="before-invitation-redemption"></a>antes do resgate do convite

Contas de Estado 1 e 2 são resultado do convite de usuários a colaborar usando as credenciais dos usuários convidados. Quando o convite é enviado inicialmente para o usuário convidado, uma conta é criada em seu diretório. Essa conta não tem credenciais associadas a ela, porque a autenticação é executada pelo provedor de identidade do usuário convidado. A propriedade **Origem** da conta do usuário convidado em seu diretório é definida como **Usuário convidado**. 

![Antes do resgate de oferta](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>após o resgate do convite

Depois que o usuário convidado aceita o convite, a propriedade **Origem** é atualizada com base no provedor de identidade do usuário convidado.

Para usuários convidados com Estado 1, a **Origem** é **Azure Active Directory Externo**.

![Usuário convidado no Estado 1 após o resgate de oferta](media/user-properties/after-redemption-state1.png)

Para usuários convidados no Estado 2, a **Origem** é **Conta Microsoft**.

![Usuário convidado no Estado 2 após o resgate de oferta](media/user-properties/after-redemption-state2.png)

Para usuários convidados nos Estados 3 e 4, a **Origem** é definida como **Azure Active Directory** ou **Windows Server Active Directory**, conforme descrito na próxima seção.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>As principais propriedades do usuário de colaboração B2B do AD do Azure
### <a name="usertype"></a>UserType
Essa propriedade indica a relação entre o usuário e o locatário do host. Essa propriedade pode assumir dois valores:
- Membro: este valor indica um funcionário da organização host e um usuário na folha de pagamento da organização. Por exemplo, provavelmente esse usuário poderá acessar somente os sites internos. Esse usuário não é considerado um colaborador externo.

- Convidado: esse valor indica um usuário que não é considerado interno à empresa, como um colaborador externo, parceiro ou cliente. Um usuário como esse não deve receber um memorando interno do CEO ou benefícios da empresa, por exemplo.

  > [!NOTE]
  > O UserType não tem nenhuma relação com o tipo de acesso do usuário, nem com a função do diretório do usuário e assim por diante. Essa propriedade só indica a relação do usuário com a organização host, e permite que a organização aplique as políticas que dependem desse atributo.

### <a name="source"></a>Fonte
Essa propriedade indica o tipo de acesso do usuário.

- Usuário convidado: esse usuário foi convidado, mas ainda não resgatou seu convite.

- Active Directory Externo: esse usuário está hospedado em uma organização externa e é autenticado com uma conta do Azure AD que pertence a outra organização. Esse tipo de acesso corresponde ao Estado 1.

- Conta Microsoft: este usuário está hospedado em uma conta Microsoft e é autenticado usando uma conta Microsoft. Esse tipo de acesso corresponde ao Estado 2.

- Active Directory do Windows Server: este usuário faz logon no Active Directory local que pertence a esta organização. Esse tipo de acesso corresponde ao Estado 3.

- Azure Active Directory: este usuário é autenticado usando uma conta do Azure AD que pertence a esta organização. Esse tipo de acesso corresponde ao Estado 4.
  > [!NOTE]
  > A origem e o UserType são propriedades independentes. O valor de origem não envolve um valor específico para o UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Os usuários B2B do AD do Azure podem ser adicionados como membros em vez de convidados?
Normalmente, um usuário B2B do Azure AD e o usuário convidado são sinônimos. Portanto, um usuário de colaboração B2B do AD do Azure é adicionado por padrão como um usuário com UserType = Convidado. No entanto, em alguns casos, a organização parceira é membra de uma organização maior a qual a organização host também pertence. Se esse for o caso, talvez a organização host queira tratar os usuários na organização parceira como membros e não convidados. Use as APIs do gerenciador de convites B2B do AD do Azure para adicionar ou convidar um usuário da organização parceira para a organização host como um membro.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtragem de usuários convidados no diretório

![Como filtrar usuários convidados](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Converter UserType
É possível converter o UserType de Membro para Convidado e vice-versa usando o PowerShell. No entanto, a propriedade UserType representa a relação do usuário com a organização. Portanto, o valor dessa propriedade só deverá ser alterado se a relação entre o usuário e a organização mudar. Se o relacionamento do usuário for alterado, o nome UPN também deverá ser alterado? O usuário poderá acessar os mesmos recursos? Uma caixa de correio deve ser atribuída? Não recomendamos alterar o UserType no PowerShell como uma atividade atômica. Além disso, caso essa propriedade se torne imutável usando o PowerShell, é melhor não assumir uma dependência desse valor.

## <a name="remove-guest-user-limitations"></a>Como remover limitações do usuário convidado
Em alguns casos, talvez você queira dar privilégios mais altos aos usuários convidados. Você pode adicionar um usuário convidado a qualquer função e até mesmo remover as restrições do usuário convidado padrão no diretório a fim de fornecer os mesmos privilégios como membros.

É possível desligar as limitações padrão para que um usuário convidado no diretório da empresa receba as mesmas permissões que um membro.

![Como remover limitações do usuário convidado](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>É possível tornar os usuários convidados visíveis na Lista de Endereços Global do Exchange?
Sim. Por padrão, objetos convidados não são visíveis na lista de endereços global da organização, mas você pode usar o PowerShell do Azure Active Directory para torná-los visíveis. Para obter mais detalhes, consulte **É possível tornar os objetos convidados visíveis na lista de endereços global?** em [Acesso para convidado em Grupos do Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Tokens de usuário de colaboração B2B](user-token.md)
* [Mapeamento de declarações de usuário de colaboração B2B](claims-mapping.md)
