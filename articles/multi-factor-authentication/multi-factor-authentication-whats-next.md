---
title: Azure Multi-Factor Authentication - O que vem a seguir
description: "Esta é a página do Azure Multi-Factor Authentication que descreve o que fazer depois com o MFA.  Isso inclui relatórios, alerta de fraude, desvio único, mensagens de voz personalizadas, cache, senhas de ips e aplicativos confiáveis."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d904de6370c1c6bd5116471465ec63fd8999a7ee


---
# <a name="configuring-azure-multi-factor-authentication"></a>Configurando o Azure Multi-Factor Authentication
Este artigo ajuda a gerenciar o Autenticação Multifator do Azure, agora que tudo está funcionando.  Ele cobre uma variedade de tópicos que ajudarão você a aproveitar ao máximo a Autenticação Multifator do Azure.  Nem todas estas funcionalidades estão disponíveis em todas as versões da Autenticação Multifator do Azure.

A configuração de alguns dos recursos a seguir pode ser encontrada no Portal de Gerenciamento do Azure Multi-Factor Authentication. Há duas maneiras diferentes de acessar o portal de gerenciamento do MFA, ambas pelo portal do Azure. A primeira é gerenciando um Provedor de Autenticação Multifator, se estiver usando um MFA baseado em consumo. A segunda é por configurações do serviço MFA. A segunda opção exige um Provedor de Autenticação Multifator ou uma licença do Azure MFA, do Azure AD Premium ou do Enterprise Mobility Suite.

Para acessar o Portal de Gerenciamento de MFA por meio de um Provedor do Azure Multi-Factor Auth, entre no portal do Azure como administrador e selecione a opção Active Directory. Clique na guia **Provedores de Autenticação Multifator**, selecione o diretório e clique no botão **Gerenciar** na parte inferior.

Para acessar o Portal de Gerenciamento de MFA por meio da página Configurações de Serviço de MFA, entre no Portal do Azure como administrador e selecione a opção Active Directory. Clique em seu diretório e depois na guia **Configurar** . Na seção da autenticação multifator, escolha **Gerenciar configurações de serviço**. Na parte inferior da página Configurações do Serviço do MFA, clique no link **Ir para o portal** .

| Recurso | Descrição | O que é coberto |
|:--- |:--- |:--- |
| [Alerta de fraude](#fraud-alert) |O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. |Como instalar, configurar e relatar fraude |
| [Desvio único](#one-time-bypass) |Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator. |Como instalar e configurar um desvio único |
| [Mensagens de voz personalizadas](#custom-voice-messages) |As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator. |Como instalar e configurar as mensagens e saudações personalizadas |
| [Cache](#caching-in-azure-multi-factor-authentication) |O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. |Como instalar e configurar o cache de autenticação. |
| [IPs Confiáveis](#trusted-ips) |IPs Confiáveis é um recurso de autenticação multifator que permite que os administradores de um inquilino gerenciado ou federado possam ignorar a autenticação multifator para usuários que estão entrando pela intranet local da empresa. |Instalar e configurar endereços IP isentos da autenticação multifator |
| [Senhas de aplicativo](#app-passwords) |A senha de aplicativo permite que um aplicativo que não tenha reconhecimento de MFA possa se desviar da autenticação multifator e continuar trabalhando. |Informações sobre senhas de aplicativo. |
| [Lembrar da Autenticação Multifator para dispositivos e navegadores lembrados](#remember-multi-factor-authentication-for-devices-users-trust) |Permite lembrar dispositivos por um número de dias específico após a entrada bem-sucedida de um usuário usando o MFA. |Informações sobre como habilitar esse recurso e configurar o número de dias. |
| [Métodos de verificação selecionáveis](#selectable-verification-methods) |Permite que você escolha os métodos de autenticação disponíveis para os usuários usarem. |Informações sobre como habilitar ou desabilitar os métodos de autenticação específicos, como mensagens de texto ou chamada. |

## <a name="fraud-alert"></a>Alerta de fraude
O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos.  Os usuários podem relatar fraude com o aplicativo móvel ou pelo telefone.

### <a name="to-set-up-and-configure-fraud-alert"></a>Para instalar e configurar o alerta de fraude
1. Faça logon em http://azure.microsoft.com
2. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3. No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Configurações na seção Configurar.
4. Na seção Alerta de Fraude da página Configurações, maque a caixa de seleção Permitir que os usuários enviem alertas de fraudes.
5. Se você quiser que os usuários sejam bloqueados quando a fraude for relatada, marque Bloquear usuário quando fraude for relatada.
6. Na caixa de texto **Código para relatar fraude durante a saudação inicial** , digite um código numérico que possa ser usado durante a verificação de chamada. Se um usuário digitar esse código mais #, em vez de apenas o sinal #, um alerta de fraude será relatado.
7. Na parte inferior, clique em Salvar.

> [!NOTE]
> As saudações de voz padrão da Microsoft instruem os usuários a pressionar 0# para enviar um alerta de fraude. Se você quiser usar um código diferente de 0, deverá gravar e carregar suas próprias saudações de voz personalizadas com instruções apropriadas.
> 
> 

![Nuvem](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Para relatar um alerta de fraude
O alerta de fraude pode ser informado de duas maneiras.  Seja por meio do aplicativo móvel ou telefone.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Para relatar o alerta de fraude com o aplicativo móvel
1. Quando uma verificação é enviada ao seu telefone, selecione-o para iniciar o aplicativo Microsoft Authenticator.
2. Para relatar fraudes, clique em Cancelar e Relatar fraude. Isso abrirá uma caixa informando que a equipe de Suporte de TI de suas organizações será notificada.
3. Clique em Relatar fraude.
4. No aplicativo, clique em Fechar.

![Nuvem](./media/multi-factor-authentication-whats-next/report1.png)

![Nuvem](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Para relatar o alerta de fraude com o telefone
1. Ao receber uma chamada de verificação em seu telefone, atenda-a.  
2. Para relatar fraudes, digite o código que foi configurado para corresponder ao relato de fraude pelo telefone e o sinal #. Você será notificado de que um alerta de fraude foi enviado.
3. Encerre a chamada.

### <a name="to-view-the-fraud-report"></a>Para exibir o relatório de fraude
1. Faça logon em [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione os Provedores de autenticação multifator. Isso trará uma lista de seus provedores de autenticação multifator.
4. Se você tiver mais de um Provedor de autenticação multifator, selecione aquele em que você deseja ver o relatório de alertas de fraude e clique em Gerenciar na parte inferior da página. Se você tiver apenas um, clique em Gerenciar. Isso abrirá o Portal de Gerenciamento da Autenticação Multifator do Azure.
5. No Portal de Gerenciamento do Azure Multi-Factor Authentication, à esquerda, em Exibir um relatório, clique em Alerta de fraude.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários específicos, números de telefone e o status de usuários.
7. Clique em Executar. Isso abrirá um relatório semelhante ao mostrado abaixo. Você também pode clicar em Exportar para CSV se quiser exportar o relatório.

## <a name="one-time-bypass"></a>Desvio único
Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator. O desvio é temporário e expira após o número especificado de segundos.  Portanto, em situações em que o aplicativo móvel ou telefone não está recebendo uma notificação ou chamada telefônica, você pode habilitar um desvio único para que o usuário possa acessar o recurso desejado.

### <a name="to-create-a-one-time-bypass"></a>Para criar um desvio único
1. Faça logon em http://azure.microsoft.com
2. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3. No Portal de Gerenciamento de Azure Multi-Factor Authentication, se você vir o nome do locatário ou Provedor de Azure MFA à esquerda com um + ao lado dele, clique no + para ver grupos diferentes de replicação do Servidor MFA e o grupo Padrão do Azure. Clique no grupo apropriado.
4. Em Administração de Usuário, clique em **Bypass Avulso**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)
5. Na página de Bypass Único, clique em **Novo Bypass Único**.
6. Insira o nome do usuário, o número de segundos durante os quais o bypass existirá, o motivo do bypass e clique em **Bypass**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/create2.png)
7. Neste ponto, o usuário deve se conectar antes que o desvio único expire.

### <a name="to-view-the-one-time-bypass-report"></a>Para exibir o relatório de desvio único
1. Faça logon em [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione os Provedores de autenticação multifator. Isso trará uma lista de seus provedores de autenticação multifator.
4. Se você tiver mais de um Provedor de autenticação multifator, selecione aquele em que você deseja ver o relatório de alertas de fraude e clique em Gerenciar na parte inferior da página. Se você tiver apenas um, clique em Gerenciar. Isso abrirá o Portal de Gerenciamento da Autenticação Multifator do Azure.
5. No Portal de Gerenciamento do Azure Multi-Factor Authentication, à esquerda, em Exibir um relatório, clique em Desvio Único.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários específicos, números de telefone e o status de usuários.
7. Clique em Executar. Isso abrirá um relatório semelhante ao mostrado abaixo. Você também pode clicar em Exportar para CSV se quiser exportar o relatório.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Mensagens de voz personalizadas
As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator.  Elas podem ser usadas com os registros da Microsoft ou para substituí-los.

Antes de começar, esteja ciente das seguintes informações:

* Os formatos de arquivo compatíveis atualmente são .wav e .mp3.
* O limite de tamanho de arquivo é de 5 MB.
* É recomendável que as mensagens de Autenticação não sejam maiores que 20 segundos. Se forem maiores, a verificação pode falhar, porque o usuário pode não responder antes do fim da mensagem e a verificação expira.

### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Para configurar mensagens de voz personalizadas na Autenticação Multifator do Azure
1. Crie uma mensagem de voz personalizada usando um dos formatos de arquivo compatíveis.
2. Faça logon em http://azure.microsoft.com
3. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
4. No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Mensagens de Voz na seção Configurar.
5. Na seção Mensagens de voz, clique em **Nova mensagem de voz**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom1.png)
6. Na página Configurar: Novas Mensagens de Voz, clique em **Gerenciar Arquivos de Som**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom2.png)
7. Na página Configurar: Arquivos de Som, clique em **Carregar Arquivo de Som**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom3.png)
8. Na página Configurar: Carregar Arquivo de Som, clique em **Procurar** e navegue até a mensagem de voz e clique em **Abrir**.
   ![Nuvem](./media/multi-factor-authentication-whats-next/custom4.png)
9. Adicione uma Descrição e clique em Carregar.
10. Quando isso for concluído, uma mensagem confirmará que carregou com êxito o arquivo.
11. À esquerda, clique em Mensagens de voz.
12. Na seção Mensagens de voz, clique em Nova mensagem de voz.
13. No menu suspenso Idioma, selecione um idioma.
14. Se esta mensagem for para um aplicativo específico, especifique-o na caixa de Aplicativo.
15. No Tipo de mensagem, selecione o tipo de mensagem para ser substituído pela nossa nova mensagem personalizada.
16. No menu suspenso Arquivo de som, selecione seu arquivo de som.
17. Clique em **Criar**. Uma mensagem confirma que você criou com êxito uma mensagem de voz.
    ![Nuvem](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Cache no Azure Multi-Factor Authentication
O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. Isso é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Isso permite que as solicitações subsequentes tenham êxito automaticamente após a conclusão bem-sucedida da verificação em andamento. Observe que o cache não deve ser usado para entrar no AD do Azure.

### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Para instalar um cache na Autenticação Multifator do Azure
1. Faça logon em http://azure.microsoft.com
2. Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3. No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Cache na seção Configurar.
4. Na página Configurar cache, clique em Novo cache
5. Selecione o tipo de Cache e os segundos de cache. Clique em Criar.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IPs Confiáveis
IPs Confiáveis é um recurso de autenticação multifator que permite que os administradores de um inquilino gerenciado ou federado possam ignorar a autenticação multifator para usuários que estão entrando pela intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças do Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

| Tipo de locatário do Azure AD | Opções disponíveis de IPs confiáveis |
|:--- |:--- |
| Gerenciada |Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem se desviar da autenticação multifator para usuários que estejam entrando pela intranet da empresa. |
| Federado |<li>Todos os usuários federados - todos os usuários federados que estiverem entrando pela organização ignorarão a autenticação multifator usando uma declaração emitida pelo AD FS.</li><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem se desviar da autenticação multifator para usuários que estejam entrando pela intranet da empresa. |

Esse desvio só funciona dentro da intranet da empresa. Por exemplo, se você selecionou apenas todos os usuários federados e um usuário entrar de fora da intranet da empresa, esse usuário terá de se autenticar usando a autenticação multifator, mesmo se apresentar uma declaração do AD FS. A tabela a seguir descreve quando as senhas de aplicativo e a autenticação multifator são necessárias dentro de sua rede corporativa e fora de sua rede corporativa quando IPs confiáveis estiver habilitado.

| IPs confiáveis habilitados | IPs confiáveis desabilitados |
|:--- |:--- |:--- |
| Dentro da rede corporativa |Para fluxos de navegador, a autenticação multifator NÃO é necessária. |
| Para aplicativos cliente avançados, senhas comuns funcionarão se o usuário não tiver criado nenhuma senha de aplicativo. Quando uma senha de aplicativo for criada, as senhas de aplicativo são necessárias. |Para aplicativos cliente avançados, as senhas de aplicativo são necessárias |
| Fora da rede corporativa |Para fluxos de navegador, a autenticação multifator é necessária. |
| Para aplicativos cliente avançados, as senhas de aplicativo são necessárias. |Para aplicativos cliente avançados, as senhas de aplicativo são necessárias. |

### <a name="to-enable-trusted-ips"></a>Para habilitar IPs confiáveis
1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório em que deseja habilitar IPs confiáveis.
4. No Diretório que você selecionou, clique em Configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações do Serviço, em IPs Confiáveis, selecione:
   
   * Para solicitações de usuários federados provenientes da minha intranet – todos os usuários federados que estiverem entrando pela rede corporativa ignorarão a autenticação multifator usando uma declaração emitida pelo AD FS.
   * Para solicitações de um intervalo específico de IPs públicos – insira os endereços IP nas caixas fornecidas usando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP no intervalo xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Você pode inserir até 50 intervalos de endereços IP.
7. Clique em Salvar.
8. Depois que as atualizações forem aplicadas, clique em Fechar.

![IPs Confiáveis](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Senhas de aplicativo
Em alguns aplicativos, como o Office 2010 ou mais antigo e o Apple Mail, você não pode usar autenticação multifator.  Para usar esses aplicativos, você precisará usar as "senhas de aplicativo" no lugar de sua senha tradicional.  A senha de aplicativo permite que um aplicativo se desvie da autenticação multifator e continue funcionando.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication.  Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013.  Para saber mais, veja [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
> 
> 

### <a name="important-things-to-know-about-app-passwords"></a>Coisas importantes a saber sobre senhas de aplicativo
Veja a seguir uma lista de importante das coisas que você deve saber sobre senhas de aplicativo.

* Os usuários podem ter várias senhas de aplicativo, o que aumenta a área de superfície para roubo. Como senhas de aplicativo são difíceis de lembrar, isso pode incentivar as pessoas anotá-las. Isso não é recomendável e deve ser desencorajado, porque apenas um fator é necessário para fazer logon com a senha de aplicativo.
* Os aplicativos que armazenam senhas em cache e a usam em cenários locais podem começar a falhar, pois a senha do aplicativo não será conhecida fora da ID organizacional. Um exemplo é o de emails do Exchange que estão no local, mas o email arquivado está na nuvem. A mesma senha não funciona.
* A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Atualmente, há um limite de 40 senhas por usuário. Você será solicitado a excluir uma de suas senhas de aplicativo existente para criar uma nova.
* Assim que a multi-factor authentication é habilitada na conta de um usuário, as senhas do aplicativo podem ser usadas com a maioria dos clientes sem navegador, como Outlook e Lync, mas as ações administrativas não podem ser executadas usando senhas do aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell, mesmo se o usuário tiver uma conta administrativa.  Certifique-se de criar uma conta de serviço com uma senha segura para executar scripts do PowerShell e não habilite essa conta para multi-factor authentication.

> [!WARNING]
> As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. Isso ocorre porque as senhas de domínio são necessárias para autenticar no local e as senhas de aplicativo são necessárias para autenticar com a nuvem.
> 
> 

### <a name="naming-guidance-for-app-passwords"></a>Nomeando orientação para senhas de aplicativo
É recomendável que os nomes de senha de aplicativo reflitam o dispositivo no qual eles serão usados. Por exemplo, se você tiver um laptop que tem aplicativos sem navegador, como Outlook, Word e Excel, basta criar uma senha de aplicativo chamada Laptop e usar essa senha em todos esses aplicativos. Embora você possa criar senhas separadas para todos esses aplicativos, isso não é recomendável. A maneira recomendada é usar uma senha de aplicativo por dispositivo.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Senhas de aplicativo federado (SSO)
O Azure AD oferece suporte à federação com Serviços de Domínio do Active Directory (AD DS) local do Windows Server. Se sua organização for federada (SSO) com o Azure AD e você pretende usar o Azure Multi-Factor Authentication, é importante que você tenha as seguinte informações ao usar senhas de aplicativo. Isso se aplica apenas aos clientes federados (SSO).

* A Senha de aplicativo é verificada pelo Azure AD e, portanto, se desvia da federação. A federação só é usada ativamente ao configurar a Senha de aplicativo.
* Para usuários federados (SSO), nós nunca iremos ao Provedor de Identidade (IdP), ao contrário do fluxo passivo. As senhas são armazenadas na ID organizacional. Se o usuário sair da empresa, essa informação deve ir para a ID organizacional usando o DirSync em tempo real. A desabilitação/exclusão da conta pode levar até 3 horas para sincronizar, atrasando a desabilitação/exclusão da Senha de aplicativo no Azure AD.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela Senha de aplicativo
* Nenhum recurso de registro de autenticação local/auditoria recurso está disponível para a Senha de aplicativo
* É preciso que haja mais treinamento do usuário final é necessário para o cliente Microsoft Lync 2013. Para as etapas obrigatórias, consulte Como alterar a senha em seu email para a senha de aplicativo.
* Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a autenticação multifator com os clientes, dependendo de onde eles se autenticam. Para clientes que se autenticam em uma infraestrutura local, você usaria um nome e senha da organização. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

Por exemplo, suponha que você tenha uma arquitetura que consiste no seguinte:

* Você está associando sua instância local do Active Directory com o Azure AD
* Você está usando o Exchange online
* Você está usando o Lync que está especificamente no local
* Você está usando o Azure Multi-Factor Authentication

![Prova](./media/multi-factor-authentication-whats-next/federated.png)

 Nesses casos, você deve fazer o seguinte:

* Ao entrar no Lync, use o nome de usuário e a senha de suas organizações.
* Ao tentar acessar o catálogo de endereços por meio de um cliente Outlook que se conecte ao Exchange online, use uma senha de aplicativo.

### <a name="allowing-app-password-creation"></a>Permitindo a criação de senha de aplicativo
Por padrão, os usuários não podem criar senhas de aplicativo.  Esse recurso deve ser ativado.  Para permitir aos usuários possam criar senhas de aplicativo, use o procedimento a seguir.

#### <a name="to-enable-users-to-create-app-passwords"></a>Para permitir que os usuários criem senhas de aplicativo
1. Entre no portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário que deseja habilitar.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth.  
6. Na parte superior da página de autenticação multifator, clique em Configurações de serviço.
7. Certifique-se de que o botão de opção ao lado de Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores esteja selecionado.

![Criar senhas de aplicativo](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Criando senhas de aplicativo
Os usuários podem criar senhas de aplicativo durante o registro inicial.  É fornecida uma opção no final do processo de registro que lhes permite criá-las.

Além disso, eles também podem criar senhas de aplicativo posteriormente alterando suas configurações no Portal do Azure, o portal do Office 365 ou

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para criar senhas de aplicativo no portal do Office 365
- - -
1. Entre no portal do Office 365
2. No canto superior direito, selecione o widget de configurações
3. À esquerda, selecione Verificação de Segurança Adicional
4. À direita, selecione **Atualizar meus números de telefone usados para segurança da conta**
5. Na parte superior da página de prova, selecione as senhas de aplicativo
6. Clique em **Criar**
7. Insira um nome para a senha de aplicativo e clique em **Avançar**
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar senhas de aplicativo no portal do Azure
- - -
1. Entre no portal clássico do Azure.
2. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior da página de prova, selecione as senhas de aplicativo
4. Clique em **Criar**
5. Insira um nome para a senha de aplicativo e clique em **Avançar**
6. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

![Senhas de aplicativo](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Para criar senhas de aplicativo se você não tiver uma assinatura do Office 365 ou Azure
- - -
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione Perfil.
3. Clique no nome de usuário e selecione Verificação de Segurança Adicional.
4. Na parte superior da página de prova, selecione as senhas de aplicativo
5. Clique em **Criar**
6. Insira um nome para a senha de aplicativo e clique em **Avançar**
7. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

![Senhas de aplicativo](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Lembrar Multi-Factor Authentication para dispositivos de confiança dos usuários
Lembrar Multi-Factor Authentication para dispositivos e navegadores de confiança dos usuários é um recurso gratuito para todos os usuários do MFA.  Ele permite fornecer aos usuários a opção de ignorar o MFA para um número de dias específico após a entrada bem-sucedida usando o MFA. Isso pode melhorar a usabilidade de seus usuários.

No entanto, já que os usuários têm permissão para lembrar o MFA para dispositivos confiáveis, esse recurso pode reduzir a segurança da conta. Para garantir que as contas de usuário sejam protegidas, você deve restaurar o Multi-Factor Authentication para seus dispositivos para qualquer um dos seguintes cenários:

* Se sua conta corporativa estiver comprometida
* Se um dispositivo lembrado for perdido ou roubado

> [!NOTE]
> Esse recurso é implementado como um cache de cookie do navegador. Ele não funcionará se os cookies do navegador não estiverem habilitados.
> 
> 

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Como habilitar/desabilitar Lembrar Multi-Factor Authentication
1. Entre no portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. No Active Directory, clique no diretório em que deseja configurar Lembrar da Autenticação Multifator para dispositivos.
4. No Diretório que você selecionou, clique em Configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações do Serviço, em Gerenciar configurações do dispositivo do usuário, marque/desmarque **Permitir que os usuários se lembrem da autenticação multifator nos dispositivos confiáveis**.
   ![Lembrar dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
7. Defina o número de dias que você deseja permitir a suspensão. O padrão é 14 dias.
8. Clique em Salvar.
9. Clique em Fechar.

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionáveis
Nas versões de nuvem e local, você pode escolher quais métodos de verificação estão disponíveis para os usuários. A tabela a seguir fornece uma visão geral de cada método.

Quando os usuários registram suas contas na MFA, eles escolhem o método de verificação preferido das opções que você habilitou. As diretrizes para o processo de registro são abordadas em [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

| Método | Descrição |
|:--- |:--- |
| Ligue para o telefone |Faz uma chamada de voz automática para o telefone de Autenticação. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. Esse número de telefone não está sincronizado com o Active Directory local. |
| Mensagem de texto para telefone |Envia para o usuário uma mensagem de texto que contém um código de verificação. O usuário é solicitado a responder à mensagem de texto com o código de verificação ou a inserir o código de verificação na interface de entrada. |
| Notificação pelo aplicativo móvel |Nesse modo, o aplicativo Microsoft Authenticator impede o acesso não autorizado a contas e impede transações fraudulentas. Isso é feito usando uma notificação por push para seu telefone ou dispositivo registrado. Basta exibir a notificação e, se ela for legítima, você tocar em Verificar. Caso contrário, você pode escolher Negar ou optar por negar e reportar a notificação fraudulenta. Para obter informações sobre o relatório de notificações fraudulentas, consulte Como usar o recurso Negar e Denunciar fraude para autenticação multifator.</br></br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação de aplicativo móvel |Nesse modo, o aplicativo Microsoft Authenticator pode ser usado como um token de software para gerar um código de verificação OATH. Esse código de verificação pode ser inserido juntamente com o nome de usuário e a senha, a fim de fornecer a segunda forma de autenticação.</li><br><p> O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Como habilitar/desabilitar métodos de autenticação
1. Entre no portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. No Active Directory, clique no diretório que você deseja habilitar ou desabilitar os métodos de autenticação.
4. No Diretório que você selecionou, clique em Configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações do Serviço, em Opções de verificação, marque/desmarque as opções que deseja usar.</br></br>
   ![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Clique em Salvar.
8. Clique em Fechar.




<!--HONumber=Nov16_HO3-->


