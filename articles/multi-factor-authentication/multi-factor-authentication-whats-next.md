---
title: "Configurar a Autenticação Multifator do Azure | Microsoft Docs"
description: "Este artigo descreve como definir as configurações da Autenticação Multifator do Azure para relatórios, alertas de fraude, bypasses avulsos, mensagens de voz personalizadas, cache, IPs confiáveis e senhas de aplicativo."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4421b995e69e115fbb6c7379af79aaef537aed0d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar a Autenticação Multifator do Azure

Este artigo ajuda você a gerenciar a Autenticação Multifator do Azure, agora que tudo está funcionando. Ele aborda diversos tópicos que ajudarão você a aproveitar ao máximo a Autenticação Multifator do Azure. Nem todas as funcionalidades estão disponíveis em todas as [versões da Autenticação Multifator do Azure](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Recurso | DESCRIÇÃO | 
|:--- |:--- |
| [Bloquear e desbloquear usuários](#block-and-unblock-users) |Use o recurso bloquear/desbloquear usuários para impedir que os usuários recebam solicitações de autenticação. |
| [Alerta de fraude](#fraud-alert) |Configure o recurso de alerta de fraude para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. |
| [Desvio único](#one-time-bypass) |Use o recurso bypass avulso para permitir que usuários se autentiquem uma única vez _ignorando_ a Autenticação Multifator. |
| [Mensagens de voz personalizadas](#custom-voice-messages) |Use o recurso das mensagens de voz personalizadas para realizar a Autenticação Multifator com suas próprias gravações ou saudações. |
| [Cache](#caching-in-azure-multi-factor-authentication) |Use o recurso de cache para definir um período específico de tempo para que as tentativas de autenticação subsequentes tenham êxito automaticamente. |
| [IPs Confiáveis](#trusted-ips) |Os administradores de um locatário gerenciado ou federado podem usar o recurso de IPs Confiáveis para ignorar a verificação em duas etapas de usuários que se conectam pela intranet da empresa. |
| [Senhas de aplicativo](#app-passwords) |Use o recurso de senha de aplicativo para permitir que um aplicativo ignore a Autenticação Multifator e continue funcionando. |
| [Lembrar a Autenticação Multifator para dispositivos e navegadores confiáveis](#remember-multi-factor-authentication-for-trusted-devices) |Use este recurso para lembrar de dispositivos e navegadores confiáveis durante um determinado número de dias depois que um usuário tiver se conectado com êxito usando a Autenticação Multifator. |
| [Métodos de verificação selecionáveis](#selectable-verification-methods) |Use este recurso para selecionar a lista de métodos de autenticação que os usuários podem usar. |

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear usuários

Use o recurso _bloquear e desbloquear usuários_ para impedir que os usuários recebam solicitações de autenticação. Qualquer tentativa de autenticação de usuários bloqueados é negada automaticamente. Os usuários permanecem bloqueados por 90 dias a contar do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um usuário

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Bloquear/desbloquear usuários**.
3. Selecione **Adicionar** para bloquear um usuário.
4. Selecione o **Grupo de Replicação**. Digite o nome de usuário do usuário bloqueado como **username<span></span>@domain.com**. Insira um comentário no campo **Motivo**.
5. Selecione **Adicionar** para concluir o bloqueio do usuário.

### <a name="unblock-a-user"></a>Desbloquear um usuário

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Bloquear/desbloquear usuários**.
3. Selecione **Desbloquear** na coluna **Ação** ao lado do usuário a ser desbloqueado.
4. Insira um comentário no campo **Motivo do desbloqueio**.
5. Selecione **Desbloquear** para concluir o desbloqueio do usuário.

## <a name="fraud-alert"></a>Alerta de fraude

Configure o recurso de _alerta de fraude_ para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. Os usuários podem relatar tentativas de fraude usando o aplicativo móvel ou pelo telefone.

### <a name="turn-on-fraud-alerts"></a>Ativar alertas de fraude

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Alerta de fraude**.

   ![Ativar alertas de fraude](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Defina a configuração **Permitir que os usuários enviem alertas de fraude** como **Ativada**.
4. Selecione **Salvar**.

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear usuário quando fraude for relatada**: se um usuário relatar uma fraude, a respectiva conta será bloqueada por 90 dias ou até que um administrador a desbloqueie. Um administrador pode examinar as entradas usando o relatório de entrada e tomar as devidas ações para evitar futuras fraudes. Um administrador pode, então, [desbloquear](#unblock-a-user) a conta do usuário.
- **Código para relatar fraude durante a saudação inicial**: quando os usuários recebem um telefonema para realizar a verificação em duas etapas, eles normalmente pressionam **#** para confirmar sua conexão. Para relatar fraude, o usuário insere um código antes de pressionar **#**. Esse código é **0** por padrão, mas você pode personalizá-lo.

  >[!NOTE]
  >As saudações de voz padrão da Microsoft instruem os usuários a pressionar **0#** para enviar um alerta de fraude. Se você quiser usar um código diferente de **0**, grave e carregue suas próprias saudações de voz personalizadas com instruções aos usuários.
  >

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **Entradas**. O relatório de fraudes agora faz parte do relatório de entradas padrão do Azure AD.

## <a name="one-time-bypass"></a>Desvio único

O recurso _bypass avulso_ permite que um usuário se autentique uma única vez sem executar a verificação em duas etapas. O bypass é temporário e expira após um número de segundos especificado. Quando o aplicativo móvel ou o telefone não estiver recebendo notificações ou chamadas telefônicas, você poderá permitir um bypass avulso para que o usuário possa acessar o recurso desejado.

### <a name="create-a-one-time-bypass"></a>Criar um bypass avulso

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Bypass avulso**.

   ![Criar o bypass avulso](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para o bypass.
5. Insira o nome de usuário como **username<span></span>@domain.com**. Insira o número de segundos que o bypass deve durar. Insira o motivo do bypass. 
6. Selecione **Adicionar**. O tempo limite entra em vigor imediatamente. O usuário precisa entrar antes que o bypass avulso se expire. 

### <a name="view-the-one-time-bypass-report"></a>Para exibir o relatório de bypass avulso

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até **Active Directory** > **Servidor MFA** > **Bypass avulso**.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
Você pode usar suas próprias gravações ou saudações para a verificação em duas etapas com o recurso _mensagens de voz personalizadas_. Essas mensagens podem ser usadas com as gravações da Microsoft ou para substituí-las.

Antes de começar, esteja ciente das seguintes restrições:

* Os formatos de arquivo compatíveis são .wav e .mp3.
* O limite de tamanho de arquivo é de 5 MB.
* As mensagens de autenticação devem ter menos de 20 segundos. As mensagens que têm mais de 20 segundos podem causar falha na verificação. O usuário poderá não responder antes da conclusão da mensagem e a verificação atingirá o tempo limite.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Configurações de ligação telefônica**.

   ![Gravar mensagens de telefone personalizadas](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selecione **Adicionar saudação**.
4. Escolha o tipo de saudação. Escolha o idioma.
5. Selecione um arquivo de som .mp3 ou .wav para carregar.
6. Selecione **Adicionar**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Cache na Autenticação Multifator do Azure

Você pode definir um período de tempo para permitir tentativas de autenticação depois que um usuário é autenticado usando o recurso _cache_. As tentativas de autenticação subsequentes do usuário, dentro do período de tempo especificado, ocorrerão automaticamente. O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações ocorram automaticamente depois que o usuário conclui a primeira verificação em andamento. 

>[!NOTE]
>O recurso de cache não se destina a ser usado para entradas no Azure AD (Azure Active Directory).

### <a name="set-up-caching"></a>Configurar o cache 

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Regras de cache**.

   ![Configurar as regras de cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** na lista suspensa. Insira o número máximo de **segundos de cache**. 
5. Se necessário, selecione um tipo de autenticação e especifique um aplicativo. 
6. Selecione **Adicionar**.


## <a name="trusted-ips"></a>IPs confiáveis

O recurso _IPs confiáveis_ da Autenticação Multifator do Azure é usado por administradores de um locatário gerenciado ou federado. O recurso ignora a verificação em duas etapas para os usuários que se conectam pela intranet da empresa. O recurso está disponível na versão completa da Autenticação Multifator do Azure e não na versão gratuita para administradores. Para saber como obter a versão completa da Autenticação Multifator do Azure, consulte [Autenticação Multifator do Azure](multi-factor-authentication.md).

| Tipo de locatário do Azure AD | Opções do recurso IPs Confiáveis |
|:--- |:--- |
| Gerenciada |**Intervalos específicos de endereços IP**: os administradores especificam um intervalo de endereços IP que tem permissão para ignorar a verificação em duas etapas de usuários que se conectam pela intranet da empresa.|
| Federado |**Todos os usuários federados**: todos os usuários federados que se conectam de dentro da organização tem permissão para ignorar a verificação em duas etapas. Os usuários ignoram a verificação usando uma declaração que é emitida pelos Serviços de Federação do Active Directory (AD FS).<br/>**Intervalos específicos de endereços IP**: os administradores especificam um intervalo de endereços IP que tem permissão para ignorar a verificação em duas etapas de usuários que se conectam pela intranet da empresa. |

O bypass dos IPs Confiáveis funciona somente dentro da intranet da empresa. Se você selecionar a opção **Todos os usuários federados** e um usuário se conectar de fora da intranet da empresa, ele deverá se autenticar usando a verificação em duas etapas. O processo será o mesmo, ainda que o usuário apresente uma declaração do AD FS. 

**Experiência do usuário final dentro da rede corporativa**

Quando o recurso IPs Confiáveis é desabilitado, a verificação em duas etapas é obrigatória para fluxos do navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos. 

Quando o recurso IPs Confiáveis é habilitado, a verificação em duas etapas *não* é obrigatória para fluxos do navegador. As senhas de aplicativo *não* são necessárias para aplicativos cliente avançados mais antigos, desde que o usuário não tenha criado uma senha de aplicativo. Uma vez que a senha de aplicativo esteja em uso, ela permanecerá necessária. 

**Experiência do usuário final fora da rede corporativa**

Independentemente se o recurso IPs Confiáveis estiver habilitado, a verificação em duas etapas será necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Habilitar locais nomeados usando o acesso condicional

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Acesso condicional** > **Locais nomeados**.
3. Selecione **Novo local**.
4. Insira um nome para o local.
5. Selecione **Marcar como local confiável**.
6. Insira o intervalo de IP em notação CIDR, como **192.168.1.1/24**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Habilitar o recurso IPs Confiáveis usando o acesso condicional

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Acesso condicional** > **Locais nomeados**.
3. Selecione **Configurar IPs confiáveis de MFA**.
4. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir:
   
   * **Para solicitações de usuários federados originárias da minha intranet**: para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectarem pela rede corporativa ignorarão a verificação em duas etapas usando uma declaração que é emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Para solicitações de um intervalo específico de IPs públicos**: para escolher essa opção, insira os endereços IP na caixa de texto, usando a notação CIDR.
   
     * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
     * Para um único endereço IP, use a notação como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Você pode inserir até 50 intervalos de endereço IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.

5. Selecione **Salvar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Habilitar o recurso IPs Confiáveis, usando as configurações do serviço

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configuração do Serviço**, em **IPs Confiáveis**, escolha uma das duas opções a seguir:
   
   * **Para as solicitações de usuários federados na minha intranet**: para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que se conectarem pela rede corporativa ignorarão a verificação em duas etapas usando uma declaração que é emitida pelo AD FS. Verifique se o AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Para solicitações de um intervalo especificado de sub-redes de endereço IP**: para escolher essa opção, insira os endereços IP na caixa de texto, usando a notação CIDR. 
     
     * Para endereços IP que estejam no intervalo xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use a notação como **xxx.xxx.xxx.0/24**.
     * Para um único endereço IP, use a notação como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Você pode inserir até 50 intervalos de endereço IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.

6. Selecione **Salvar**.

![Habilitar IPs Confiáveis com configurações do serviço](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Senhas de aplicativo

Alguns aplicativos, como o Office 2010 ou anterior e o Apple Mail, não são compatíveis com a verificação em duas etapas. Os aplicativos não estão configurados para aceitar uma segunda verificação. Para usar esses aplicativos, use o recurso _senhas de aplicativo_. Você pode usar uma senha de aplicativo em vez da sua senha tradicional para permitir que um aplicativo ignore a verificação em duas etapas e continue funcionando.

>[!NOTE]
>Autenticação moderna para os clientes do Microsoft Office 2013 e posterior
> 
>Os clientes do Office 2013 e posterior (incluindo o Outlook), são compatíveis com protocolos de autenticação modernos e podem ser habilitados para funcionar com a verificação em duas etapas. Assim que o cliente estiver habilitado, as senhas de aplicativo não serão necessárias para o cliente. Para saber mais, confira [Office 2013 modern authentication public preview announcement (Anúncio da visualização pública da autenticação moderna do Office 2013)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>

### <a name="considerations-about-app-passwords"></a>Considerações sobre senhas de aplicativo

Ao usar senhas de aplicativo, considere os seguintes pontos importantes:

* As senhas de aplicativo são inseridas apenas uma vez por aplicativo. Os usuários não precisam manter o controle das senhas nem as inserir toda vez.
* A senha real é gerada automaticamente e não é fornecida pelo usuário. A senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Há um limite de 40 senhas por usuário. 
* Os aplicativos que armazenam as senhas em cache e as usam em cenários locais podem começar a falhar porque a senha de aplicativo não é conhecida fora da conta corporativa ou de estudante. Um exemplo dessa situação é o dos emails do Exchange que estão locais, mas os emails arquivado que estão na nuvem. Nessa situação, a mesma senha não funciona.
* Depois que a Autenticação Multifator é habilitada na conta de um usuário, as senhas de aplicativo podem ser usadas com a maioria dos clientes não usados em navegador, como Outlook e Microsoft Skype for Business. Ações administrativas não podem ser executadas com o uso de senhas de aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell. As ações não podem ser executadas, mesmo que o usuário tenha uma conta administrativa. Para executar scripts do PowerShell, crie uma conta de serviço com uma senha forte e não habilite a conta para a verificação em duas etapas.

>[!WARNING]
>As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. As senhas de domínio são necessárias para autenticar localmente. As senhas de aplicativo são necessárias para autenticar na nuvem.
>

### <a name="guidance-for-app-password-names"></a>Diretrizes para nomes de senha de aplicativo

Os nomes das senhas de aplicativo devem refletir o dispositivo em que elas serão usadas. Se você tem um laptop que tem aplicativos não usados em navegador, como o Outlook, o Word e o Excel, crie uma senha de aplicativo chamada **Laptop** para esses aplicativos. Crie outra senha de aplicativo chamada **Desktop** para os mesmos aplicativos que são executados em um computador desktop. 

>[!NOTE]
>É recomendável que você crie uma senha de aplicativo por dispositivo, em vez de senha de aplicativo por aplicativo.

### <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicativo federado ou de logon único

O Azure AD é compatível com federação ou SSO (logon único), com AD DS (Active Directory Domain Services) local do Windows Server. Se sua organização for federada com o Azure AD e você estiver usando a Autenticação Multifator do Azure, considere os seguintes pontos sobre as senhas de aplicativo.

>[!NOTE]
>Os pontos a seguir se aplicam somente a clientes federados (SSO).

* As senhas de aplicativo são verificadas pelo Azure AD e, portanto, ignoram a federação. A federação é usada ativamente apenas na configuração das senhas de aplicativo.
* O IdP (Provedor de Identidade) não é contatado para usuários federados (SSO), diferentemente do fluxo passivo. As senhas de aplicativo são armazenadas na conta corporativa ou de estudante. Se um usuário sai da empresa, as informações do usuário fluem para a conta corporativa ou de estudante usando o **DirSync** em tempo real. A desabilitação/exclusão da conta de pode levar até três horas para ser sincronizada, o que pode ocasionar atraso ao desabilitar/excluir a senha de aplicativo no Azure AD.
* As configurações do Controle de Acesso de cliente local não são respeitadas pelo recurso senhas de aplicativo.
* Nenhuma funcionalidade de registro em log/auditoria de autenticação local está disponível para ser usada com o recurso senhas de aplicativo.
* Algumas arquiteturas avançadas exigem uma combinação de credenciais para a verificação em duas etapas com clientes. Essas credenciais podem incluir um nome de usuário e senhas de conta corporativa ou de estudante, além das senhas de aplicativo. Os requisitos dependem de como a autenticação é realizada. Para clientes que se autenticam em uma infraestrutura local, um nome de usuário e uma senha de conta corporativa ou de estudante são necessários. Para clientes que se autenticam no Azure AD, é necessária uma senha de aplicativo.

  Por exemplo, suponha que você tem a seguinte arquitetura:

  * Sua instância local do Active Directory é federada com o Azure AD.
  * Você está usando o Exchange online.
  * Você está usando o Skype for Business localmente.
  * Você está usando a Autenticação Multifator do Azure.

  ![Uso de senhas de aplicativo em uma organização federada](./media/multi-factor-authentication-whats-next/federated.png)

  Nesse cenário, você usa as seguintes credenciais:

  * Para entrar no Skype for Business, use seu nome de usuário e senha da conta corporativa ou de estudante.
  * Para acessar o catálogo de endereços de um cliente Outlook que se conecta com o Exchange online, use uma senha de aplicativo.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os usuários criem senhas de aplicativo

Por padrão, os usuários não podem criar senhas de aplicativo. O recurso de senhas de aplicativo deve ser habilitado. Para permitir que os usuários criem senhas de aplicativo, faça o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, selecione a opção **Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores**.

   ![Permitir que os usuários criem senhas de aplicativo](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Criar senhas de aplicativo

Os usuários podem criar senhas de aplicativo durante o registro inicial. O usuário tem a opção de criar senhas de aplicativo no final do processo de registro.

Os usuários também podem criar senhas de aplicativo após o registro. As senhas de aplicativo podem ser alteradas por meio das configurações no Portal do Azure ou no portal do Office 365. Para obter mais informações e etapas detalhadas para os usuários, consulte [O que são senhas de aplicativo na Autenticação Multifator do Azure?](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Lembrar a Autenticação Multifator para dispositivos confiáveis
O recurso _Lembrar a Autenticação Multifator_ para dispositivos e navegadores que são confiáveis para o usuário é um recurso gratuito para todos os usuários da Autenticação Multifator. Os usuários podem ignorar as verificações subsequentes durante um número especificado de dias, depois de se conectarem com êxito a um dispositivo usando a Autenticação Multifator. O recurso melhora a usabilidade, minimizando o número de vezes que um usuário tem que realizar a verificação em duas etapas no mesmo dispositivo.

>[!IMPORTANT]
>Se um dispositivo ou uma conta for comprometida, a lembrança da Autenticação Multifator para dispositivos confiáveis poderá afetar a segurança. Caso uma conta corporativa seja comprometida ou um dispositivo confiável seja perdido ou roubado, você deve [restaurar a Autenticação Multifator em todos os dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A ação de restaurar revoga o status de confiável de todos os dispositivos, e o usuário precisará executar a verificação em duas etapas novamente. Você também pode instruir seus usuários a restaurar a Autenticação Multifator em seus próprios dispositivos com as instruções disponíveis em [Gerenciar as configurações da verificação em duas etapas](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Como o recurso funciona

O recurso Lembrar a Autenticação Multifator define um cookie persistente no navegador quando um usuário seleciona a opção **Não perguntar novamente durante X dias** ao entrar. O usuário não será solicitado novamente a realizar a Autenticação Multifator nesse mesmo navegador até que o cookie se expire. Caso o usuário abra um navegador diferente no mesmo dispositivo ou limpe os cookies, ele será solicitado a verificar novamente. 

A opção **Não perguntar novamente durante X dias** não é mostrada em aplicativos sem navegador, independentemente se o aplicativo é compatível com a autenticação moderna. Esses aplicativos usam _tokens de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica se a última verificação em duas etapas ocorreu dentro do número especificado de dias. 

O recurso reduz o número de autenticações em aplicativos Web, que normalmente solicitam todas as vezes. O recurso aumenta o número de autenticações de clientes de autenticação moderna, que normalmente solicitam a cada 90 dias.

>[!IMPORTANT]
>O recurso **Lembrar a Autenticação Multifator** não é compatível com o recurso **Mantenha-me conectado** do AD FS, em que os usuários realizam a verificação em duas etapas no AD FS por meio do Servidor de Autenticação Multifator do Azure ou de uma solução de autenticação multifator de terceiros.
>
>Se os usuários selecionarem **Mantenha-me conectado** no AD FS e também marcarem seus dispositivos como confiáveis para a Autenticação Multifator, eles não serão verificados automaticamente após o fim do número de dias da **Lembrar a Autenticação Multifator**. O Azure AD solicitará uma nova verificação em duas etapas, mas o AD FS retornará um token com a declaração e a data originais da Autenticação Multifator em vez de realizar a verificação em duas etapas novamente. Essa reação dá início a um loop de verificação entre o Azure AD e o AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar a opção Lembrar a Autenticação Multifator

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, em **Gerenciar Lembrar a Autenticação Multifator**, selecione a opção **Permite aos usuários lembrar a autenticação multifator em dispositivos que eles confiam**.

   ![Lembrar a Autenticação Multifator para dispositivos confiáveis](./media/multi-factor-authentication-whats-next/remember.png)

6. Defina o número de dias a permitir que os dispositivos confiáveis ignorem a verificação em duas etapas. O padrão é 14 dias.
7. Selecione **Salvar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar o recurso Lembrar a Autenticação Multifator, os usuários poderão marcar um dispositivo como confiável ao entrarem e selecionarem **Não perguntar novamente**.

![Selecionar "Não perguntar novamente" para dispositivos confiáveis](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionáveis

Você pode escolher os métodos de verificação que estarão disponíveis para os usuários usando o recurso _métodos de verificação selecionáveis_. A tabela a seguir fornece uma visão geral dos métodos.

Quando os usuários registram suas contas na Autenticação Multifator do Azure, eles escolhem o método de verificação preferido nas opções que você habilitou. As diretrizes para o processo de registro são fornecidas em [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

| Método | DESCRIÇÃO |
|:--- |:--- |
| Ligue para o telefone |Faz uma chamada de voz automatizada para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. O número de telefone não é sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. É solicitado que o usuário digite o código de verificação na interface de acesso. Esse processo é chamado de SMS unidirecional. SMS bidirecional significa que o usuário deve retornar um determinado código por SMS. O SMS bidirecional foi preterido e não terá compatibilidade depois de 14 de novembro de 2018. Os usuários configurados para o SMS bidirecional serão mudados automaticamente para verificação _Ligar para o telefone_ nessa oportunidade.|
| Notificação pelo aplicativo móvel |Envia uma notificação por push para o telefone ou o dispositivo registrado. O usuário vê a notificação e seleciona **Verificar** para concluir a verificação. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação de aplicativo móvel |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Habilitar e desabilitar métodos de verificação

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, em **Opções de verificação**, selecione/cancele a seleção dos métodos a serem fornecidos aos usuários.

   ![Selecionar os métodos de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Clique em **Salvar**.
