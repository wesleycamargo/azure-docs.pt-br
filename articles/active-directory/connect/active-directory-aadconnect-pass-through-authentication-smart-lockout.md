---
title: "Azure AD Connect: autenticação de passagem – bloqueio inteligente | Microsoft Docs"
description: "Este artigo descreve como a autenticação de passagem do Azure Active Directory (Azure AD) protege suas contas locais contra ataques de senha de força bruta na nuvem"
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 2e1468c6a576ab71b85e3f69e5914df6ee9e4d5a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticação de passagem do Azure Active Directory: bloqueio inteligente

## <a name="overview"></a>Visão geral

O Azure Active Directory (Azure AD) protege contra ataques de senha de força bruta e impede que usuários reais sejam impedidos de acessar seus aplicativos de SaaS e do Office 365. Essa capacidade, chamada *Bloqueio Inteligente*, tem suporte quando você usa a Autenticação de Passagem como seu método de entrada. O Bloqueio Inteligente está habilitado por padrão para todos os locatários e protege continuamente suas contas de usuário.

O Bloqueio Inteligente controla as tentativas de logon que falharam. Após um certo *limite de bloqueio*, ele inicia uma *duração do bloqueio*. O Bloqueio Inteligente recusa as tentativas de logon do invasor durante a duração do bloqueio. Se o ataque persistir, as tentativas posteriores de logon com falha, após o final da duração do bloqueio, levarão a durações de bloqueio mais longas.

>[!NOTE]
>O limite de bloqueio padrão é de 10 tentativas falhas. A duração de bloqueio padrão é de 60 segundos.

O Bloqueio Inteligente também faz a distinção entre entradas de usuários reais e de invasores. Na maioria dos casos, ele bloqueia somente os invasores. Essa funcionalidade impede que invasores bloqueiem usuários reais de forma mal-intencionada. O Bloqueio Inteligente usa o comportamento de entrada anterior, os dispositivos e navegadores dos usuários, entre outros sinais, para distinguir entre invasores e usuários originais. Os algoritmos são aperfeiçoando constantemente.

A Autenticação de Passagem encaminha solicitações de validação de senha para o AD (Active Directory) local, portanto, você precisa impedir que invasores bloqueiem contas de usuários do Active Directory. O Active Directory tem suas próprias políticas de bloqueio de conta, especificamente, as políticas de [Limite de bloqueios de conta](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [Redefinir contador de bloqueios de conta após](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx). Configure os valores de limite de bloqueio do Azure AD e duração de bloqueio adequadamente para filtrar os ataques na nuvem antes que eles atinjam o Active Directory no local.

>[!NOTE]
>O recurso de Bloqueio inteligente é gratuito e está _ativado_ por padrão para todos os clientes. Entretanto, se você quiser modificar os valores de limite de bloqueio e de duração de bloqueio do Azure AD usando a API do Graph, seu locatário precisa ter pelo menos uma licença do Azure AD Premium P2. Você não precisa de uma licença do Azure AD Premium P2 _por usuário_ para obter o recurso de bloqueio inteligente com Autenticação de Passagem.

Para garantir que as contas do Active Directory locais dos seus usuários também sejam protegidas, você precisa garantir que:

   * O limite de bloqueio do Azure AD seja _menor_ que o limite de bloqueio da conta do Active Directory. Defina os valores de forma que o limite de bloqueio da conta do Active Directory seja pelo menos duas ou três vezes maior do que o limite de bloqueio do Azure AD.
   * A duração de bloqueio do Azure AD (representada em segundos) seja _maior_ que a duração de redefinir contador de bloqueios de conta após do Active Directory (representado em minutos).

>[!IMPORTANT]
>No momento um administrador não pode desbloquear contas de nuvem dos usuários se eles foram bloqueados fora da funcionalidade do Bloqueio Inteligente. O administrador deve aguardar a duração do bloqueio expirar.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Verifique as políticas de bloqueio de conta do Active Directory

Use as instruções a seguir para verificar suas políticas de bloqueio de conta do Active Directory:

1.  Abra a ferramenta de Gerenciamento de Política de Grupo.
2.  Edite a política de grupo aplicada a todos os usuários, por exemplo, a **Política de Domínio Padrão**.
3.  Navegue até **Configuração do Computador** > **Políticas** > **Configurações do Windows** > **Configurações de Segurança** > **Políticas de Conta** > **Política de Bloqueio de Conta**.
4.  Verifique os valores de **Limite de bloqueio de conta** e **Redefinir contador de bloqueios de conta após**.

![Políticas de bloqueio de conta do Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Utilize a API do Graph para gerenciar os valores de Bloqueio Inteligente do locatário (é necessário uma licença Premium)

>[!IMPORTANT]
>Modificar os valores de limite de bloqueio e de duração de bloqueio do Azure AD usando a API do Graph é um recurso do Azure AD Premium P2. Também é necessário que você seja um administrador global em seu locatário.

Você pode usar o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer) para ler, definir e atualizar os valores do Bloqueio Inteligente do Azure AD. Você também pode fazer essas operações de forma programática.

### <a name="view-smart-lockout-values"></a>Exibir valores de Bloqueio Inteligente

Siga estas etapas para visualizar os valores do Bloqueio Inteligente de seu locatário:

1. Entre no Explorador do Graph como um administrador global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Selecione **Modificar permissões** e, em seguida, selecione a permissão **Directory.ReadWrite.All**.
3. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como **BETA**, o tipo de solicitação como **GET** e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Selecione **Executar Consulta** para ver os valores do Bloqueio Inteligente de seu locatário. Se não tiver configurado os valores de seu locatário antes, você verá um conjunto vazio.

### <a name="set-smart-lockout-values"></a>Definir valores de Bloqueio Inteligente

Siga estas etapas para definir os valores de do Bloqueio Inteligente de seu locatário (necessário somente pela primeira vez):

1. Entre no Explorador do Graph como um administrador global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Selecione **Modificar permissões** e, em seguida, selecione a permissão **Directory.ReadWrite.All**.
3. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como **BETA**, o tipo de solicitação como **POST** e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie e cole a seguinte solicitação JSON no campo **Corpo da Solicitação**.
5. Selecione **Executar Consulta** para ver os valores do Bloqueio Inteligente de seu locatário.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Se não usá-los, deixe os valores de **BannedPasswordList** e **EnableBannedPasswordCheck**, como (**""**) e **false**, respectivamente.

Verifique se você definiu os valores do Bloqueio Inteligente de seu locatário corretamente usando as etapas em [Visualizar valores do Bloqueio Inteligente](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Atualizar valores de Bloqueio Inteligente

Siga estas etapas para atualizar os valores do Bloqueio Inteligente de seu locatário (se você configurou esses valores antes):

1. Entre no Explorador do Graph como um administrador global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Selecione **Modificar permissões** e, em seguida, selecione a permissão **Directory.ReadWrite.All**.
3. [Siga estas etapas para exibir os valores do Bloqueio Inteligente de seu locatário](#view-smart-lockout-values). Copie o valor de `id` (um GUID) do item com **displayName** como **PasswordRuleSettings**.
4. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como **BETA**, o tipo de solicitação como **PATCH** e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Use o GUID da etapa 3 para `<id>`.
5. Copie e cole a seguinte solicitação JSON no campo **Corpo da Solicitação**. Altere os valores do Bloqueio Inteligente conforme apropriado.
6. Selecione **Executar consulta** para atualizar os valores de Bloqueio Inteligente do seu locatário.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Verifique se você carregou os valores do Bloqueio Inteligente de seu locatário corretamente usando as etapas em [Visualizar valores do Bloqueio Inteligente](#view-smart-lockout-values).

## <a name="next-steps"></a>Próximas etapas
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.
