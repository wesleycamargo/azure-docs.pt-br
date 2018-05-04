---
title: Sincronizar contas de usuários de usuários convidados que usam email para entrar no Azure | Microsoft Docs
description: Este artigo explica como sincronizar contas de usuários convidados que usam uma ID alternativa para entrar nos aplicativos.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: d21f124858a7f98227eb301a97b9837e3adbba68
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Sincronizar contas de usuários convidados que usam email para entrar (versão prévia)

>[!NOTE]
> Esse recurso está atualmente em visualização pública.

O cenário a seguir aborda a situação em que é possível ter usuários externos no ambiente do AD local, como parceiros, que usam um método de entrada alternativo.

No exemplo anterior, Nina Morin trabalha para a Fabrikam e ela tem o seguinte endereço de email: nmorin@fabrikam.com. A Nina é parceira da Contoso e precisa acessar determinados aplicativos que a Contoso possui. A Contoso criou uma conta para Nina e a orientou a utilizar o endereço de email para entrar nos aplicativos.

Para os aplicativos locais, esse cenário tem funcionado muito bem. Mas agora, a Contoso está movendo esses aplicativos para a nuvem e quer ter a mesma experiência para os parceiros.

Esse cenário resolve essa situação.


## <a name="pre-requisites-and-assumptions"></a>Pré-requisitos e suposições
Esta seção contém uma lista de pré-requisitos e suposições que você precisa conhecer antes de tentar configurar o cenário.

### <a name="pre-requisites"></a>Pré-requisitos
- Credenciais de administrador global para configurar o Azure AD Connect, verificar domínios e definir configurações de federação de domínio
- Azure AD Connect versão 1.1.524.0 ou superior
- Domínio verificado para definir o UPN da nuvem de usuários externos (por exemplo: bmcontoso.com).
- Serviço de Federação para autenticar os usuários externos. Se você usa o AD FS, ele deverá ser 2012 R2 ou superior
- O PowerShell v1.1 do MSOL é instalado em um computador para verificar as configurações de federação. Para obter mais informações, consulte [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Suposições 
- O Azure AD Connect já foi configurado e instalado com êxito. Para obter informações sobre como instalar o Azure AD Connect, consulte [Azure AD Connect e federação](active-directory-aadconnectfed-whatis.md).
Este documento pressupõe o seguinte:
- que há um serviço de federação configurado e está autenticando usuários com êxito.
- usuários externos podem autenticar usando o endereço de email externo.
- - O uso de uma ID alternativa para entrar foi definido e configurado. Os usuários podem autenticar usando a ID alternativa. Para obter informações adicionais sobre como configurar uma ID alternativa com AD FS, consulte [Configurar ID de Logon Alternativa](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Tarefa 1: Preparar o ambiente
A tarefa a seguir é mais informativa, de modo que você esteja pronto a começar a sincronizar as contas externas e elas possam entrar usando um i alternativo, como o atributo de email.

Defina os itens na tabela abaixo antes de prosseguir para a segunda tarefa.

![Arquitetura](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspecto do ambiente|Para que é utilizado?|Implementação no ambiente|
|-----|-----|-----|
|Atributo UPN da nuvem|O atributo que preenche o UPN dos objetos de usuários externos na nuvem. O sufixo UPN para as contas externas deve ser o definido nos pré-requisitos. Esse é o domínio verificado.|* Exemplo: UserPrincipalName (nmorin@bmcontoso.com)|
|Endereço de inscrição|O atributo que os usuários externos digitarão ao fazer logon. Esse atributo deve ter um formato de endereço de email e, na maioria dos casos, coincidir com o endereço de email real do usuário externo.|* Exemplo: email (nmorin@fabrikam.com)|
|Filtro com escopo do Azure AD Connect|O filtro que permite direcionar as identidades externas para o escopo das regras de sincronização definidas posteriormente neste guia. As formas típicas de escopo incluem: uma Unidade Organizacional predefinida na organização, uma determinada convenção de nomenclatura, um domínio específico e etc.|* Exemplo: a UO contém elementos externos|
|Locatário do Azure AD|O nome do locatário do Microsoft Azure AD como aparece para o Azure AD Connect.|Exemplo:  contoso.onmicrosoft.com|

A captura de tela a seguir tem três caixas contornadas.
- A UO **Externos**, que é usada no filtro de escopo definido do Azure AD Connect e é o local dos usuários externos.
- O atributo de **email**, que é usado pelos usuários externos para entrar.
- O atributo **userPrincipalName**, que é o domínio verificado no qual o ambiente local é federado.

![Usuário](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Tarefa 2: Configurar o Azure AD Connect
Após definidas as informações acima, será possível prosseguir com a configuração das regras de sincronização do Azure AD Connect. Para configurar as regras, use o editor de regras de sincronização do Azure AD Connect. Para mais informações sobre o editor, consulte [Provisionamento Declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Como configurar a regra de sincronização
Use o procedimento a seguir para configurar o Azure AD Connect.

1. Abra o editor de regras de sincronização do Azure AD Connect, acessando **Iniciar - Azure AD Connect - Editor de regras de sincronização**.
2. Na tela **Editor de Regras de Sincronização**, verifique se a direção é **entrada** e, à direita, clique em **Adicionar nova regra**.
3. Na página **Descrição**, configure o seguinte e clique em **Avançar**.
    - **Nome** - insira um nome para a regra 
    - **Sistema conectado:** - o ambiente do AD local
    - **Tipo de objeto do sistema conectado:** - usuário
    - **Tipo de objeto Metaverse:** - pessoa
    - **Tipo de vínculo:** - Junção
    - **Precedência:** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Na tela **Filtro de Escopo**, clique em **Adicionar grupo**.
5. Use os menus suspensos para configurar o filtro. Insira o seguinte e clique em **Avançar**. Esta ação cria um filtro que aplica-se apenas a objetos localizados na UO externa.
    - **Atributo** - dn
    - **Operador** - CONTAINS
    - **Valor** - Externos
 
 ![Filter](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Na tela **Regras de Junção**, clique em **Avançar**.
7. Na tela **Transformações**, clique em **Adicionar Transformação**. Insira as seguintes informações:
    - **FlowType** - Constante
    - **Atributo de Destino** - userType
    - **Origem** - Convidado
8. Na tela **Transformações**, clique em **Adicionar Transformação**. Insira as seguintes informações:
    - **FlowType** - Direto
    - **Atributo de Destino** - onPremisesUserPrincipalName
    - **Origem** - email
9. Na tela **Transformações**, clique em **Adicionar Transformação**. Insira as seguintes informações:
    - **FlowType** - Direto
    - **Atributo de Destino** - userPrincipalName
    - **Origem** - userPrincipalName ![Transformação](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Clique em **Adicionar**. 
11. Na tela **Editor de Regras de Sincronização**, verifique se a direção é **saída** e, à direita, clique em **Adicionar novo valor**.
12. Na página **Descrição**, configue o seguinte e clique em **Avançar**.
    - **Nome** - insira um nome para a regra 
    - **Sistema conectado:** - o locatário do Microsoft Azure AD
    - **Tipo de objeto do sistema conectado:** - usuário
    - **Tipo de objeto Metaverse:** - pessoa
    - **Tipo de vínculo:** - Junção
    - **Precedência:** - 90
    - 
![Regra](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Na tela **Filtro de escopo**, clique em **Avançar**.
14. Na tela **Regras de Junção**, clique em **Avançar**.
15. Na tela **Transformações**, clique em **Adicionar Transformação**.  Insira as seguintes informações:
    - **FlowType** - Direto
    - **Atributo de Destino** - userType
    - **Origem** -userType
9. Na tela **Transformações**, clique em **Adicionar Transformação**. Insira as seguintes informações:
    - **FlowType** - Direto
    - **Atributo de Destino** - onPremisesUserPrincipalName
    - **Origem** - onPremisesUserPrincipalName ![Transformação](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Clique em **Adicionar**. 
11. Após configurar essas regras, será necessário executar uma sincronização completa. Use o PowerShell para iniciar uma sincronização completa. Quando a sincronização concluir, você poderá prosseguir para a próxima etapa.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Tarefa 3:  Federação
A tarefa a seguir são informações sobre o que é necessário ter no local para que o cenário funcione.

É possível verificar as configurações de federação com o Azure usando o PowerShell do Microsoft Azure AD. Este documento usa a v1.1 do PowerShell do MSOL. É possível instalar essa versão [aqui](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Verifique as configurações da federação
Use o procedimento a seguir para verificar as configurações da federação.
1. Abra o Windows PowerShell e conecte ao Microsoft Azure AD usando o comando a seguir:
``` powershell
      Connect-MSOLservice
```
2.  Insira as credenciais de administrador global
3.  Agora, insira o comando a seguir:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Observe que as informações da federação devem ser retornadas. Observe que o **ActiveLogonUri** é a URL do servidor de federação.

  ![Federação](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Verificar a ID de logon alternativa
Este documento usa o AD FS como o Idp (provedor de identidade). Se estiver utilizando um Idp diferente, essas etapas poderão variar.

1. Abra o Windows PowerShell e insira o comando a seguir:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Você deve ver as informações do AD FS.  Anote o **AlternateLoginID** e **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Tarefa 4: Testar
Para verificar se está funcionando corretamente, é necessário entrar em um ponto de extremidade que tenha sido configurado para autenticar usando o Idp. Para testar isso, implantamos um site no Azure e estamos usando a URL a seguir: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Verifique se é possível entrar com a ID alternativa
1. Entre no ponto de extremidade.</br>
![Ponto de extremidade](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Insira o nome de usuário e você será redirecionado para a página de entrada da federação.
![Federação](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Insira suas credenciais.
2. Agora, você deve entrar com êxito.
![Êxito](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Próximas etapas
- [Propriedades de um usuário de colaboração B2B do Azure Active Directory](../../active-directory/active-directory-b2b-user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Configurar ID de logon alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: histórico de lançamento de versão](active-directory-aadconnect-version-history.md)
