---
title: Configurar o Azure MFA| Microsoft Docs
description: "Esta é a página da Autenticação Multifator do Azure que descreve o que fazer depois com o MFA.  Isso inclui relatórios, alerta de fraude, desvio único, mensagens de voz personalizadas, cache, senhas de ips e aplicativos confiáveis."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: alexwe
ms.openlocfilehash: 65a149b0d291bc650e0937c7ef3f90600f54c669
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="configure-azure-multi-factor-authentication-settings---public-preview"></a>Definir as configurações de Autenticação Multifator do Azure – visualização pública

Este artigo ajuda a gerenciar o Autenticação Multifator do Azure, agora que tudo está funcionando.  Ele aborda diversos tópicos que ajudarão você a aproveitar ao máximo a Autenticação Multifator do Azure.  Nem todos esses recursos estão disponíveis em todas as [versões da Autenticação Multifator do Azure](/multi-factor-authentication-get-started.md#what-features-do-i-need).

>[!NOTE]
>Essas configurações estão em visualização pública no portal do Azure. Para obter a documentação sobre como gerenciar configurações de Autenticação Multifator do Azure no portal pfweb, consulte [Definir configurações de Autenticação Multifator do Azure](multi-factor-authentication-whats-next-pfweb.md).

| Recurso | Descrição | 
|:--- |:--- |
| [Alerta de fraude](#fraud-alert) |O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. |
| [Desvio único](#one-time-bypass) |Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator. |
| [Mensagens de voz personalizadas](#custom-voice-messages) |As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator. |
| [Cache](#caching-in-azure-multi-factor-authentication) |O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. |
| [IPs Confiáveis](#trusted-ips) |Os administradores de um locatário gerenciado ou federado podem usar os IPs Confiáveis para ignorar a verificação em duas etapas de usuários que se conectam pela intranet local da empresa. |
| [Senhas de aplicativo](#app-passwords) |A senha de aplicativo permite que um aplicativo que não tenha reconhecimento de MFA possa se desviar da autenticação multifator e continuar trabalhando. |
| [Lembrar da Autenticação Multifator para dispositivos e navegadores lembrados](#remember-multi-factor-authentication-for-devices-that-users-trust) |Permite lembrar dispositivos por um número de dias específico após a entrada bem-sucedida de um usuário usando o MFA. |
| [Métodos de verificação selecionáveis](#selectable-verification-methods) |Permite que você escolha os métodos de autenticação disponíveis para os usuários usarem. |

## <a name="fraud-alert"></a>Alerta de fraude
O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos.  Os usuários podem relatar fraude com o aplicativo móvel ou pelo telefone.

### <a name="turn-on-fraud-alert"></a>Ativar o alerta de fraude
1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Alerta de fraude**.

   ![Alerta de fraude](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Defina **Permitir aos usuários enviar alertas de fraudes** como **Ligado**.
4. Selecione **Salvar**.

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear usuário quando fraude for relatada** - se de um usuário relatar uma fraude, sua conta será bloqueada.
- **Código para relatar fraude durante a saudação inicial** – quando os usuários recebem um telefonema para realizar a verificação em duas etapas, eles normalmente pressionam # para confirmar sua conexão. Se quiserem relatar uma fraude, eles devem inserir um código antes de pressionar #. Esse código é **0** por padrão, mas você pode personalizá-lo.

> [!NOTE]
> As saudações de voz padrão da Microsoft instruem os usuários a pressionar 0# para enviar um alerta de fraude. Se você quiser usar um código diferente de 0, deverá gravar e carregar suas próprias saudações de voz personalizadas com instruções apropriadas.

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Na parte inferior da página Configurações de serviço, selecione **Ir para o portal**.
7. No Portal de Gerenciamento da Autenticação Multifator do Azure, em Exibir um relatório, clique em **Alerta de Fraude**.
8. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários, números de telefone e o status de usuários.
9. Clique em **Executar**. Isso abre um relatório de alertas de fraude. Você também pode clicar em **Exportar para CSV** se quiser exportar o relatório.

## <a name="one-time-bypass"></a>Desvio único
Um bypass avulso permite que um usuário se autentique uma única vez ao sem executar uma verificação em duas etapas. O bypass é temporário e expira após um número de segundos especificado. Quando o aplicativo móvel ou o telefone não estiver recebendo notificações ou chamadas telefônicas, você poderá habilitar um bypass avulso para que o usuário possa acessar o recurso desejado.

### <a name="create-a-one-time-bypass"></a>Criar um bypass avulso

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Bypass avulso**.

   ![Desvio único](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para esse bypass.
5. Forneça o nome do usuário (na forma de username@domain.com), o número de segundos da duração do bypass e o motivo do bypass. 
6. Selecione **Adicionar**. Como o limite de tempo começa a contar imediatamente, o usuário precisa entrar antes que o bypass avulso expire. 

### <a name="view-the-one-time-bypass-report"></a>Para exibir o relatório de bypass avulso
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Na parte inferior da página Configurações de serviço, selecione **Ir para o portal**.
7. No Portal de Gerenciamento da Autenticação Multifator do Azure, em Exibir um relatório, clique em **Bypass avulso**.
8. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários, números de telefone e o status de usuários.
9. Clique em **Executar**. Isso abre um relatório de bypasses. Você também pode clicar em **Exportar para CSV** se quiser exportar o relatório.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a verificação em duas etapas. Elas podem ser usadas com os registros da Microsoft ou para substituí-los.

Antes de começar, esteja ciente das seguintes informações:

* Os formatos de arquivo compatíveis são .wav e .mp3.
* O limite de tamanho de arquivo é de 5 MB.
* As mensagens de autenticação devem ter menos de 20 segundos. Se forem mais longas, a verificação pode falhar, porque o usuário não terá tempo de responder antes do fim da mensagem, pois a verificação expira.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Configurações de chamada telefônica**.

   ![Configurações de chamada telefônica](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Selecione **Adicionar saudação**.
4. Escolha o tipo de saudação e o idioma.
5. Selecione um arquivo de som .mp3 ou .wav para carregar.
6. Selecione **Adicionar**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Cache na Autenticação Multifator do Azure
O cache permite que você defina um período específico para que as próximas tentativas de autenticação subsequentes nesse período automaticamente tenham êxito. Isso é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações automaticamente tenham êxito depois que o usuário concluir a primeira verificação em andamento. 

Observe que o cache não deve ser usado para acessar o Azure AD.

### <a name="set-up-caching"></a>Configurar o cache 
1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Servidor MFA** > **Regras de cache**.

   ![Regras de cache](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. Selecione **Adicionar**.
5. Selecione o tipo de cache na lista suspensa e especifique o número de segundos de cache máximo. 
6. Se necessário, selecione um tipo de autenticação e especifique um aplicativo. 
7. Selecione **Adicionar**.


## <a name="trusted-ips"></a>IPs confiáveis
Os administradores de um locatário gerenciado ou federado podem usar este recurso do Azure MFA para ignorar a verificação em duas etapas de usuários que fizerem logon pela intranet local da empresa. Esse recurso está disponível na versão completa da Autenticação Multifator do Azure, não na versão gratuita para administradores. Para saber como obter a versão completa da Autenticação Multifator do Azure, consulte [Autenticação Multifator do Azure](multi-factor-authentication.md).

| Tipo de locatário do Azure AD | Opções disponíveis de IPs confiáveis |
|:--- |:--- |
| Gerenciada |<li>Intervalos de endereços IP específicos – os administradores podem definir um intervalo de endereços IP que tem permissão para ignorar a autenticação multifator de usuários que se conectam pela intranet da empresa.</li> |
| Federado |<li>Todos os usuários federados - todos os usuários federados que estiverem fazendo logon de dentro da organização ignorarão a verificação em duas etapas usando uma declaração emitida pelo AD FS.</li><br><li>Intervalos de endereços IP específicos – os administradores podem definir um intervalo de endereços IP que tem permissão para ignorar a autenticação multifator de usuários que se conectam pela intranet da empresa. |

Esse desvio só funciona dentro da intranet da empresa. Por exemplo, se você selecionou a opção Todos os usuários federados e um usuário fizer logon de fora da intranet da empresa, esse usuário precisará se autenticar com a verificação em duas etapas, mesmo se apresentar uma declaração do AD FS. 

**Experiência do usuário final dentro da rede corporativa:**

Quando o recurso IPs confiáveis estiver desabilitado, será necessário utilizar a verificação em duas etapas para fluxos de navegador e senhas de aplicativo para antigos aplicativos clientes avançados. 

Quando o recurso IPs confiáveis estiver habilitado, a verificação em duas etapas *não* será necessária para fluxos de navegador e as senhas de aplicativo *não* serão necessárias para antigos aplicativos clientes avançados, desde que o usuário não tenha criado uma senha de aplicativo. Se uma senha de aplicativo for usada, ela será necessária. 

**Experiência do usuário final fora da rede corporativa:**

Não importa se o recurso IPs confiáveis está habilitado ou não, será necessário utilizar a verificação em duas etapas para fluxos de navegador e senhas de aplicativo para antigos aplicativos clientes avançados. 

### <a name="to-enable-trusted-ips"></a>Para habilitar IPs confiáveis
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Na página Configurações do Serviço, em IPs Confiáveis, há duas opções:
   
   * **Para solicitações de usuários federados originárias da minha intranet** – marque a caixa de seleção. Todos os usuários federados que estiverem fazendo logon de dentro da organização ignorarão a verificação em duas etapas usando uma declaração emitida pelo AD FS.
   * **Para solicitações de um intervalo específico de IPs públicos** – insira os endereços IP nas caixas fornecidas usando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP no intervalo xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Você pode inserir até 50 intervalos de endereços IP. Os usuários que acessam desses endereços IP ignoram verificação em duas etapas.
7. Clique em **Salvar**.
8. Depois que as atualizações forem aplicadas, clique em **Fechar**.

![IPs confiáveis](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Senhas de aplicativo
Alguns aplicativos, como o Office 2010 ou anterior e o Apple Mail, não oferecem suporte à verificação em duas etapas. Eles não estão configurados para aceitar uma segunda verificação. Para usar esses aplicativos, você precisará usar as "senhas de aplicativo" no lugar de sua senha tradicional. A senha de aplicativo permite que um aplicativo ignore a verificação em duas etapas e continue funcionando.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo o Outlook) e mais recente são compatíveis com protocolos da autenticação moderna e podem ser usados com a verificação em duas etapas. Depois de habilitada, não será mais necessário fornecer as senhas de aplicativo para esses clientes.  Para saber mais, veja [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Coisas importantes a saber sobre senhas de aplicativo
Veja a seguir uma lista de importante das coisas que você deve saber sobre senhas de aplicativo.

* As senhas de aplicativo só precisam ser fornecidas uma vez por aplicativo. Os usuários não precisam decorá-las e fornecê-las toda vez.
* A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso acontece porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Há um limite de 40 senhas por usuário. 
* Os aplicativos que armazenam senhas em cache e a usam em cenários locais podem começar a falhar, pois a senha do aplicativo não será conhecida fora da ID organizacional. Um exemplo é o de emails do Exchange que estão no local, mas o email arquivado está na nuvem. A mesma senha não funciona.
* Assim que a autenticação multifator é habilitada na conta de um usuário, as senhas do aplicativo podem ser usadas com a maioria dos clientes sem navegador, como Outlook e Lync, mas as ações administrativas não podem ser executadas usando senhas do aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell, mesmo se o usuário tiver uma conta administrativa.  Quando você criar uma conta de serviço, atribua a ela uma senha forte para executar scripts do PowerShell e não habilite essa conta para a verificação em duas etapas.

> [!WARNING]
> As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. Isso ocorre porque as senhas de domínio são necessárias para autenticar no local e as senhas de aplicativo são necessárias para autenticar com a nuvem.

### <a name="naming-guidance-for-app-passwords"></a>Nomeando orientação para senhas de aplicativo
Os nomes das senhas de aplicativo devem refletir o dispositivo no qual as senhas serão usadas. Por exemplo, se você tiver um laptop com aplicativos que não são navegadores, como Outlook, Word e Excel, basta criar uma senha de aplicativo chamada Laptop e usar essa senha em todos esses aplicativos. Em seguida, crie outra senha de aplicativo chamada Desktop para os mesmos aplicativos em um computador desktop. 

A Microsoft recomenda criar uma senha de aplicativo por dispositivo, e não uma senha de aplicativo por aplicativo.

### <a name="federated-sso-app-passwords"></a>Senhas de aplicativo federado (SSO)
O Azure AD oferece suporte à federação (logon único) com os Serviços de Domínio do Active Directory Domain Services (AD DS) locais do Windows Server. Se sua organização for federada com o Azure AD e você quiser usar a Autenticação Multifator do Azure, é importante saber o seguinte sobre as senhas de aplicativo. Esta seção se aplica apenas aos clientes federados.

* As senhas de aplicativo são verificadas Azure AD e, portanto, ignoram a federação. A federação só é usada ativamente na configuração das senhas de aplicativo.
* Para usuários federados (SSO), nós nunca consultamos o Provedor de Identidade (IdP), diferentemente do fluxo passivo. As senhas são armazenadas na ID organizacional. Se o usuário sair da empresa, essa informação deve ir para a ID organizacional usando o DirSync em tempo real. A desabilitação/exclusão da conta pode levar até 3 horas para sincronizar, atrasando a desabilitação/exclusão da Senha de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela senha de aplicativo.
* Nenhum recurso de registro de autenticação/auditoria local está disponível para a Senha de aplicativo.
* Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo com a verificação em duas etapas em clientes, dependendo de onde eles são autenticados. Para clientes que fazem a autenticação em uma infraestrutura local, você usaria um nome de usuário e senha organizacional. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

  Por exemplo, suponha que você tenha uma arquitetura que consiste no seguinte:

  * Você está federando sua instância local do Active Directory com o Azure AD
  * Você está usando o Exchange online
  * Você está usando o Lync que é especificamente local
  * Você está usando a Autenticação Multifator do Azure

  ![Prova](./media/multi-factor-authentication-whats-next/federated.png)

  Nesses casos, você deve fazer o seguinte:

  * Ao entrar no Lync, use o nome de usuário e a senha de suas organizações.
  * Ao tentar acessar o catálogo de endereços por meio de um cliente Outlook que se conecte ao Exchange online, use uma senha de aplicativo.

### <a name="allow-app-password-creation"></a>Permitir a criação de senha de aplicativo
Por padrão, os usuários não podem criar senhas de aplicativo. Esse recurso deve ser ativado. Para permitir que os usuários criem senhas de aplicativo, faça o seguinte:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Selecione o botão de opção ao lado de **Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores**.

![Criar senhas de aplicativo](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Criar senhas de aplicativo
Os usuários podem criar senhas de aplicativo durante o registro inicial. Essa opção é oferecida a eles ao final do processo de registro, permitindo que eles criem senhas de aplicativo.

Os usuários também podem criar senhas de aplicativo após o registro alterando suas configurações no Portal do Azure ou no portal do Office 365. Para obter mais informações e etapas detalhadas para os usuários, consulte [O que são senhas de aplicativo na Autenticação Multifator do Azure](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Lembrar a autenticação multifator em dispositivos de confiança dos usuários
Lembrar Autenticação Multifator para dispositivos e navegadores de confiança dos usuários é um recurso gratuito para todos os usuários do MFA. Ele permite fornecer aos usuários a opção de ignorar o MFA para um número de dias específico após a entrada bem-sucedida usando o MFA. Isso pode melhorar a usabilidade, pois minimiza o número de vezes que um usuário executa a verificação em duas etapas no mesmo dispositivo.

No entanto, se um dispositivo ou a conta for comprometido, lembrar a autenticação multifator em dispositivos confiáveis pode colocar sua segurança em risco. Caso uma conta corporativa seja comprometida ou um dispositivo confiável seja perdido ou roubado, você deve [restaurar a Autenticação Multifator em todos os dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Essa ação revoga o status de confiável de todos os dispositivos, e o usuário precisará executar a verificação em duas etapas novamente. Você também pode instruir seus usuários a restaurar o MFA em seus próprios dispositivos com as instruções em [Gerenciar as configurações de verificação em duas etapas](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Como ele funciona

Lembre-se que a autenticação multifator funciona com a definição de um cookie persistente no navegador quando um usuário seleciona a caixa "Não perguntar novamente por **X** dias" ao entrar. O usuário não será solicitado a fornecer o MFA novamente neste navegador até que o cookie expire. Se o usuário abrir um navegador diferente no mesmo dispositivo ou limpar os cookies, ele será solicitado a verificar novamente. 

A caixa de seleção "Não perguntar novamente por **X** dias" não é exibida em aplicativos que não usam navegador, independentemente de eles suportarem a autenticação moderna ou não. Esses aplicativos usam tokens de atualização que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica se a última verificação de duas etapas foi realizada dentro do número de dias configurado. 

Portanto, lembrar o MFA em dispositivos confiáveis reduz o número de autenticações em aplicativos Web (que normalmente solicitam sempre), mas aumenta o número de autenticações para clientes de autenticação moderna (que normalmente solicita a cada 90 dias).

> [!NOTE]
>Esse recurso não é compatível com o recurso "Manter-me conectado" do AD FS quando os usuários executam uma verificação em duas etapas para o AD FS por meio do Servidor MFA do Azure ou uma solução MFA de terceiros. Se os usuários selecionarem "Manter-me conectado" no AD FS e também marcarem seus dispositivos como confiáveis para o MFA, eles não poderão verificar depois que o número de dias de "Lembrar do MFA" expirar. O Azure AD solicita uma nova verificação em duas etapas, mas o AD FS retorna um token com a declaração e a data originais do MFA em vez de realizar a verificação em duas etapas novamente. Isso define um loop de verificação entre o Azure AD e AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar a opção Lembrar autenticação multifator
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Na página Configurações do Serviço, em Gerenciar configurações do dispositivo do usuário, marque a caixa de seleção **Permitir que os usuários lembrem a autenticação multifator em dispositivos que eles confiam**.
   ![Lembrar dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
7. Defina o número de dias que os dispositivos confiáveis podem ignorar a verificação em duas etapas. O padrão é 14 dias.
8. Clique em **Salvar**.
9. Clique em **fechar**

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar esse recurso, os usuários poderão marcar um dispositivo como confiável quando fizerem logon e marcarem a opção **Não perguntar novamente**.

![Não perguntar novamente - captura de tela](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionáveis
Você pode escolher quais métodos de verificação estarão disponíveis para os usuários. A tabela a seguir fornece uma visão geral de cada método.

Quando os usuários registram suas contas na MFA, eles escolhem o método de verificação preferido das opções que você habilitou. As diretrizes para o processo de registro são abordadas em [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

| Método | Descrição |
|:--- |:--- |
| Ligue para o telefone |Faz uma chamada de voz automatizada para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. Esse número de telefone não está sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. O usuário é solicitado a responder à mensagem de texto com o código de verificação ou a inserir o código de verificação na interface de entrada. |
| Notificação pelo aplicativo móvel |Envia uma notificação por push para o telefone ou o dispositivo registrado. O usuário vê a notificação e seleciona **Verificar** para concluir a verificação. <br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação de aplicativo móvel |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de acesso.<br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Como habilitar/desabilitar métodos de autenticação
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório que você deseja gerenciar. 
4. Selecione **Configurar**
5. Em Autenticação Multifator, selecione **Gerenciar configurações de serviço**.
6. Na página Configurações do Serviço, em Opções de verificação, marque/desmarque as opções que deseja usar.
   ![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Clique em **Salvar**.
8. Clique em **fechar**

