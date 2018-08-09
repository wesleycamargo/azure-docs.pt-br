---
title: Como habilitar o SSO entre aplicativos no Android usando a ADAL | Microsoft Docs
description: 'Como usar os recursos do SDK do ADAL para habilitar o Logon Único em seus aplicativos. '
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579906"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como habilitar o SSO entre aplicativos no Android usando a ADAL
Atualmente, o padrão da indústria é o recurso de Logon Único (SSO) permitir que os usuários insiram suas credenciais apenas uma vez, e essas credenciais serem automaticamente entre os aplicativos. A dificuldade em digitar o nome de usuário e senha em uma tela pequena, muitas vezes combinada com um fator adicional (2FA) como uma chamada telefônica ou um código enviado via texto, se transforma rapidamente em insatisfação se um usuário tiver que efetuar logon mais de uma vez.

Além disso, se você aplicar uma plataforma de identidade usada por outros aplicativos, como Contas da Microsoft ou uma conta corporativa do Microsoft 365, os clientes esperam que essas credenciais estejam disponíveis em todos os aplicativos, independentemente do publicador.

A plataforma Microsoft Identity, juntamente com nossos SDKs do Microsoft Identity, permite que você agrade aos seus clientes com o SSO dentro de seu próprio pacote de aplicativos ou, como ocorre com a funcionalidade de agente e aplicativos autenticadores, em todo o dispositivo.

Este passo a passo informa como configurar o SDK dentro de seu aplicativo a fim de fornecê-lo aos clientes.

O documento anterior pressupõe que você saiba como integrar seu aplicativo com o [SDK do Android do Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceitos de SSO na Plataforma do Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Agentes do Microsoft Identity
A Microsoft fornece aplicativos para todas as plataformas móveis que permitem o uso das mesmas credenciais em aplicativos de diferentes fornecedores e recursos aprimorados especiais que exigem um único local seguro do qual validar as credenciais. O SDK chama esses **agentes**. No iOS e no Android, os agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push ao dispositivo por uma empresa que gerencia uma parte ou todos os dispositivos para seus funcionários. Esses agentes oferecem suporte ao gerenciamento de segurança apenas para alguns aplicativos, ou para todo o dispositivo, com base no desejo dos administradores de TI. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.

#### <a name="broker-assisted-logins"></a>Logons assistidos por agentes
Os logons assistido por agente são experiências de logon que ocorrem dentro do aplicativo do agente e usam o armazenamento e a segurança do agente para compartilhar as credenciais em todos os aplicativos no dispositivo, e aplicam a plataforma Microsoft Identity. A implicação de que os aplicativos dependerão do agente para fazer logon dos usuários. No iOS e no Android, esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push ao dispositivo por uma empresa que gerencia o dispositivo para o usuário. Um exemplo desse tipo de aplicativo é o Microsoft Authenticator no iOS. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.
A experiência varia de acordo com a plataforma e, às vezes, pode ser perturbador para os usuários se não for gerenciada corretamente. Provavelmente você estará mais familiarizado com esse padrão se tiver instalado o aplicativo do Facebook e usado o Facebook Connect em outro aplicativo. A plataforma Microsoft Identity usa o mesmo padrão.

Em Android, o seletor de conta é exibido na parte superior de seu aplicativo, o que é menos perturbador para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o agente é invocado
Se um agente compatível for instalado no dispositivo, como o aplicativo Microsoft Authenticator, os SDKs do Microsoft Identity farão automaticamente o trabalho de invocar o agente para você quando um usuário indicar que deseja fazer logon usando qualquer conta da plataforma Microsoft Identity. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante que o aplicativo é válido?
 
 A necessidade de garantir a identidade de uma chamada de aplicativo para o agente é fundamental para a segurança fornecida em logons assistidos por agente. Nem o iOS nem Android impõem identificadores exclusivos que sejam válidos somente para um determinado aplicativo, portanto, aplicativos mal-intencionados podem "falsificar" o identificador de um aplicativo legítimo e receber os tokens destinados ao aplicativo legítimo. Para garantir que a Microsoft esteja sempre se comunicando com o aplicativo certo no tempo de execução, é pedido ao desenvolvedor que forneça um redirectURI personalizado ao registrar seu aplicativo com a Microsoft. **O modo como os desenvolvedores devem criar esse URI de redirecionamento será abordado com detalhes logo abaixo.** Este redirectURI personalizado contém a impressão digital do certificado do aplicativo e tem a garantia da Google Play Store de ser exclusivo para o aplicativo. Quando um aplicativo chama o agente, o agente solicita que o sistema operacional Android o forneça com a impressão digital do certificado que chamou o agente. O agente fornece a impressão digital do certificado para a Microsoft na chamada para o sistema de identidade. Se a impressão digital do certificado do aplicativo não corresponder à impressão digital do certificado fornecida para nós pelo desenvolvedor durante o registro, o acesso será negado aos tokens do recurso que o aplicativo está solicitando. Essa verificação garante que apenas o aplicativo registrado pelo desenvolvedor receba tokens.

Os logons SSO de agente têm os seguintes benefícios:

* O usuário desfruta do SSO em todos os aplicativos, independentemente do fornecedor.
* Seu aplicativo pode usar recursos corporativos mais avançados, como o Acesso Condicional, e oferecer suporte a cenários do Intune.
* Seu aplicativo pode oferecer suporte à autenticação baseada em certificado para usuários corporativos.
* Uma experiência de entrada mais segura, pois a identidade do aplicativo e do usuário são verificadas pelo aplicativo agente com criptografia e algoritmos de segurança adicionais.

Veja uma representação de como os SDKs do Microsoft Identity funcionam com os aplicativos agentes a fim de habilitar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

Com essas informações em mãos, você deve ser capaz de entender melhor e implementar o SSO em seu aplicativo usando a plataforma do Microsoft Identity e os SDKs.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO assistido por agente
A capacidade de um aplicativo usar qualquer agente instalado no dispositivo está **desativada por padrão**. Para usar seu aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Execute estas etapas:

1. Habilitar o modo de agente na chamada do código do aplicativo para o SDK do MS
2. Estabelecer um novo URI de redirecionamento e fornecê-lo ao aplicativo e no registro do aplicativo
3. Configurar as permissões corretas no manifesto do Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: habilitar o modo de agente em seu aplicativo
A capacidade de seu aplicativo de usar o agente é ativada quando você cria as “configurações” ou configuração inicial da sua instância de autenticação. Para fazer isso em seu aplicativo:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Etapa 2: Estabelecer um novo URI de redirecionamento com seu esquema de URL
Para garantir que o aplicativo correto retorne os tokens de credencial, é preciso ter certeza de que seu aplicativo seja chamado, de forma que o sistema operacional Android possa verificar. O sistema operacional Android usa o hash do certificado no Google Play Store. Esse hash do certificado não pode ser falsificado por um aplicativo invasor. Juntamente com o URI do nosso aplicativo de agente, a Microsoft garante que os tokens sejam retornados para o aplicativo correto. É necessário que um URI de redirecionamento exclusivo seja registrado no aplicativo.

O URI de redirecionamento deve ser no formato correto de:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

É possível registrar esse URI de redirecionamento no registro de seu aplicativo usando o [Portal do Azure](https://portal.azure.com/). Para saber mais sobre o registro de aplicativo do Azure AD, confira [Integração com o Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Etapa 3: Configurar as permissões corretas em seu aplicativo
O aplicativo de agente no Android usa o recurso de Gerenciador de Contas do sistema operacional Android para gerenciar credenciais nos aplicativos. Para usar o agente no Android, seu manifesto de aplicativo deve ter permissões para usar contas do Gerenciador de Contas. Essas permissões são discutidas detalhadamente na [documentação do Google para o Gerente de Contas](http://developer.android.com/reference/android/accounts/AccountManager.html)

Em particular, essas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Você configurou o SSO!
Agora, o SDK do Microsoft Identity compartilhará automaticamente as credenciais em seus aplicativos e invocará o agente se ele estiver presente em seu dispositivo.

