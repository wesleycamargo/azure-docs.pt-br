<properties 
	pageTitle="Azure Multi-Factor Authentication - O que vem a seguir" 
	description="Esta é a página do Azure Multi-Factor Authentication que descreve o que fazer depois com o MFA. Isso inclui relatórios, alerta de fraude, desvio único, mensagens de voz personalizadas, cache, senhas de ips e aplicativos confiáveis." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="billmath"/>

# Configurando o Azure Multi-Factor Authentication

O artigo a seguir lhe ajudará a gerenciar o Azure Multi-Factor Authentication, agora que você já o conhece bem. Ele cobre uma variedade de tópicos que lhe permitirão aproveitar ao máximo o Azure Multi-Factor Authentication. Esteja ciente de que nem todos estes recursos estão disponíveis em todas as versões do Azure Multi-Factor Authentication.

A configuração de alguns dos recursos a seguir pode ser encontrada no Portal de Gerenciamento do Azure Multi-Factor Authentication. Há duas maneiras diferentes de acessar esse portal, ambas pelo portal do Azure. A primeira é gerenciando um Provedor de Autenticação Multifator, se estiver usando um MFA baseado em consumo. A segunda é por configurações do serviço MFA. A segunda opção exige um Provedor de Autenticação Multifator ou uma licença do Azure MFA, do Azure AD Premium ou do Enterprise Mobility Suite.

Para acessar o Portal de Gerenciamento de MFA por meio de um Provedor do Azure Multi-Factor Auth, entre no portal do Azure como administrador e selecione a opção Active Directory. Clique na guia **Provedores de Autenticação Multifator**, selecione o diretório e clique no botão **Gerenciar** na parte inferior.

Para acessar o Portal de Gerenciamento de MFA por meio da página Configurações de Serviço de MFA, entre no Portal do Azure como administrador e selecione a opção Active Directory. Clique em seu diretório e depois na guia **Configurar**. Na seção da autenticação multifator, escolha **Gerenciar configurações de serviço**. Na parte inferior da página Configurações do Serviço do MFA, clique no link **Ir para o portal**.


Recurso| Descrição| O que é coberto
:------------- | :------------- | :------------- | 
[Alerta de fraude](#fraud-alert)|O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos.|Como instalar, configurar e relatar fraude
[Desvio único](#one-time-bypass) |Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator.|Como instalar e configurar um desvio único
[Mensagens de voz personalizadas](#custom-voice-messages) |As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator. |Como instalar e configurar as mensagens e saudações personalizadas
[Cache](#caching)|O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. |Como instalar e configurar o cache de autenticação.
[IPs Confiáveis](#trusted-ips)|IPs Confiáveis é um recurso de autenticação multifator que permite que os administradores de um inquilino gerenciado ou federado possam ignorar a autenticação multifator para usuários que estão entrando pela intranet local da empresa.|Instalar e configurar endereços IP que estão isentos para autenticação multifator	
[Senhas de aplicativo](#app-passwords)|As senhas de aplicativo permitem que um aplicativo que não tenha reconhecimento de MFA possa se desviar da autenticação multifator e continuar trabalhando.|Informações sobre senhas de aplicativo.
[Suspender a autenticação multifator para dispositivos e navegadores lembrados (visualização pública)](#suspend-multi-factor-authentication-for-remembered-devices-and-browsers-public-preview)|Permite suspender a MFA por um determinado número de dias depois que um usuário se conectou com êxito usando MFA.|Informações sobre como habilitar esse recurso e configurar o número de dias.




## Alerta de fraude
O alerta de fraude pode ser instalado e configurado para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. Os usuários podem relatar fraude com o aplicativo móvel ou pelo telefone.

### Para instalar e configurar o alerta de fraude

1.	Faça logon no http://azure.microsoft.com
2.	Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3.	No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Configurações na seção Configurar.
4.	Na seção Alerta de Fraude da página Configurações, maque a caixa de seleção Permitir que os usuários enviem alertas de fraudes.
5.	Se você quiser que os usuários sejam bloqueados quando a fraude for relatada, marque Bloquear usuário quando fraude for relatada.
6.	Na caixa de texto **Código para relatar fraude durante a saudação inicial**, digite um código numérico que possa ser usado durante a verificação de chamada. Se um usuário digitar esse código mais #, em vez de apenas o sinal #, um alerta de fraude será relatado. 
7.	Na parte inferior, clique em Salvar.

>[AZURE.NOTE]
As saudações de voz padrão da Microsoft instruem os usuários a pressionar 0# para enviar um alerta de fraude. Se você usar um código diferente de 0, será necessário gravar e carregar suas próprias saudações de voz personalizadas com instruções apropriadas.


<center>![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)</center>

### Para relatar um alerta de fraude
O alerta de fraude pode ser informado de duas maneiras. Seja por meio do aplicativo móvel ou telefone.

### Para relatar o alerta de fraude com o aplicativo móvel



1. Quando uma verificação for enviada ao seu telefone, clique nela e o aplicativo Multi-Factor Authentication será iniciado.
2. Para relatar fraudes, clique em Cancelar e Relatar fraude. Isso abrirá uma caixa informando que a equipe de Suporte de TI de suas organizações será notificada. 
3. Clique em Relatar fraude.
4. No aplicativo, clique em Fechar.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report1.png)</center>

### Para relatar o alerta de fraude com o telefone

1. Ao receber uma chamada de verificação em seu telefone, atenda-a.</li>
2. Para relatar fraudes, digite o código que foi configurado para corresponder ao relato de fraude pelo telefone e o sinal #. Você será notificado de que um alerta de fraude foi enviado.
3. Encerre a chamada.

### Para exibir o relatório de fraude

1. Faça logon em [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione os Provedores de autenticação multifator. Isso trará uma lista de seus provedores de autenticação multifator.
4. Se você tiver mais de um Provedor de autenticação multifator, selecione aquele em que você deseja ver o relatório de alertas de fraude e clique em Gerenciar na parte inferior da página. Se você tiver apenas um, clique em Gerenciar. Isso abrirá o Portal de Gerenciamento do Azure Multi-Factor Authentication.
5. No Portal de Gerenciamento do Azure Multi-Factor Authentication, à esquerda, em Exibir um relatório, clique em Alerta de fraude.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários específicos, números de telefone e o status de usuários.
7. Clique em Executar. Isso abrirá um relatório semelhante ao mostrado abaixo. Você também pode clicar em Exportar para CSV se quiser exportar o relatório.

## Desvio único

Um desvio único permite que um usuário se autentique uma única vez ao "desviar" a autenticação multifator. O desvio é temporário e expira após o número especificado de segundos. Portanto, em situações em que o aplicativo móvel ou telefone não está recebendo uma notificação ou chamada telefônica, você pode habilitar um desvio único para que o usuário possa acessar o recurso desejado.

### Para criar um desvio único

1.	Faça logon no http://azure.microsoft.com
2.	Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3.	No Portal de Gerenciamento de Azure Multi-Factor Authentication, se você vir o nome do locatário ou Provedor de Azure MFA à esquerda com um + ao lado dele, clique no + para ver grupos diferentes de replicação do Servidor MFA e o grupo Padrão do Azure. Clique no grupo apropriado.
4.	Em Administração de Usuário, clique em **Bypass Avulso**. ![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)
5.	Na página de Bypass Avulso, clique em **Novo Bypass Avulso**.
6.	Digite o nome do usuário, o número de segundos durante os quais o bypass vai existir, o motivo do bypass e clique em **Bypass**.![Nuvem](./media/multi-factor-authentication-whats-next/create2.png)
7.	Neste ponto, o usuário deve se conectar antes que o desvio único expire.



### Para exibir o relatório de desvio único

1. Faça logon em [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione os Provedores de autenticação multifator. Isso trará uma lista de seus provedores de autenticação multifator.
4. Se você tiver mais de um Provedor de autenticação multifator, selecione aquele em que você deseja ver o relatório de alertas de fraude e clique em Gerenciar na parte inferior da página. Se você tiver apenas um, clique em Gerenciar. Isso abrirá o Portal de Gerenciamento do Azure Multi-Factor Authentication.
5. No Portal de Gerenciamento do Azure Multi-Factor Authentication, à esquerda, em Exibir um relatório, clique em Desvio Único.
6. Especifique o intervalo de datas em que você deseja exibir no relatório. Também é possível especificar nomes de usuários específicos, números de telefone e o status de usuários.
7. Clique em Executar. Isso abrirá um relatório semelhante ao mostrado abaixo. Você também pode clicar em Exportar para CSV se quiser exportar o relatório.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## Mensagens de voz personalizadas

As mensagens de voz personalizadas permitem que você use suas próprias gravações ou saudações com a autenticação multifator. Elas podem ser usadas com os registros da Microsoft ou para substituí-los.

Antes de começar, esteja ciente das seguintes informações:

- Os formatos de arquivo compatíveis atualmente são .wav e .mp3.
- O limite de tamanho de arquivo é de 5 MB.
- É recomendável que as mensagens de Autenticação não sejam maiores que 20 segundos. Se forem maiores, a verificação pode falhar, porque o usuário pode não responder antes do fim da mensagem e a verificação expira.



### Para configurar mensagens de voz personalizadas no Azure Multi-Factor Authentication
1.	Crie uma mensagem de voz personalizada usando um dos formatos de arquivo compatíveis.
2.	Faça logon no http://azure.microsoft.com
3.	Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
4.	No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Mensagens de Voz na seção Configurar.
5.	Na seção Mensagens de Voz, clique em **Nova Mensagem de Voz**.![Nuvem](./media/multi-factor-authentication-whats-next/custom1.png)
6.	Na página Configurar: Novas Mensagens de Voz, clique em **Gerenciar Arquivos de Som**.![Nuvem](./media/multi-factor-authentication-whats-next/custom2.png)
7.	Na página Configurar: Arquivos de Som, clique em **Carregar Arquivo de Som**.![Nuvem](./media/multi-factor-authentication-whats-next/custom3.png)
8.	Na página Configurar: Carregar Arquivo de Som, clique em **Procurar** e navegue até a mensagem de voz e clique em **Abrir**.![Nuvem](./media/multi-factor-authentication-whats-next/custom4.png)
9.	Adicione uma Descrição e clique em Carregar.
10.	Quando isso for concluído, você verá uma mensagem de que carregou com êxito o arquivo.
11.	À esquerda, clique em Mensagens de voz.
12.	Na seção Mensagens de voz, clique em Nova mensagem de voz.
13.	No menu suspenso Idioma, selecione um idioma.
14.	Se esta mensagem for para um aplicativo específico, especifique-o na caixa de Aplicativo.
15.	No Tipo de mensagem, selecione o tipo de mensagem que será substituído pela nossa nova mensagem personalizada.
16.	No menu suspenso Arquivo de som, selecione seu arquivo de som.
17.	Clique em **Criar**. Você verá uma mensagem que diz que você criou com êxito uma mensagem de voz.![Nuvem](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## Cache no Azure Multi-Factor Authentication

O cache permite que você defina um momento específico período para que tentativas de autenticação subsequentes tenham êxito automaticamente. Isso é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Isso permite que as solicitações subsequentes tenham êxito automaticamente após a conclusão bem-sucedida da verificação em andamento. Observe que o cache não deve ser usado para entrar no AD do Azure.


### Para instalar um cache no Azure Multi-Factor Authentication

1.	Faça logon no http://azure.microsoft.com
2.	Navegue até o Portal de Gerenciamento do MFA de acordo com as instruções na parte superior desta página.
3.	No Portal de Gerenciamento do Azure Multi-Factor Authentication, clique em Cache na seção Configurar.
4.	Na página Configurar cache, clique em Novo cache
5.	Selecione o tipo de Cache e os segundos de cache. Clique em Criar.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## IPs Confiáveis

IPs Confiáveis é um recurso de autenticação multifator que permite que os administradores de um inquilino gerenciado ou federado possam ignorar a autenticação multifator para usuários que estão entrando pela intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças do Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

 
Tipo de locatário do Azure AD| Opções disponíveis de IPs confiáveis
:------------- | :------------- | 
Gerenciada|Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem se desviar da autenticação multifator para usuários que estejam entrando pela intranet da empresa.
Federado|<li>Todos os usuários federados - todos os usuários federados que estão entrando de dentro da organização se desviarão da autenticação multifator utilizando uma declaração emitida pelo AD FS.</li><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem se desviar da autenticação multifator para usuários que estejam entrando pela intranet da empresa.

Esse desvio só funciona dentro da intranet da empresa. Por exemplo, se você selecionou apenas todos os usuários federados e um usuário entrar de fora da intranet da empresa, esse usuário terá de se autenticar usando a autenticação multifator, mesmo se apresentar uma declaração do AD FS. A tabela a seguir descreve quando as senhas de aplicativo e a autenticação multifator são necessárias dentro de sua rede corporativa e fora de sua rede corporativa quando IPs confiáveis estiver habilitado.


|IPs confiáveis habilitados| IPs confiáveis desabilitados
:------------- | :------------- | :------------- | 
Dentro da rede corporativa|Para fluxos de navegador, a autenticação multifator NÃO é necessária.|Para fluxos de navegador, a autenticação multifator é necessária.
|Para aplicativos cliente avançados, senhas comuns funcionarão se o usuário não tiver criado nenhuma senha de aplicativo. Quando uma senha de aplicativo for criada, as senhas de aplicativo são necessárias.|Para aplicativos cliente avançados, as senhas de aplicativo são necessárias
Fora da rede corporativa|Para fluxos de navegador, a autenticação multifator é necessária.|Para fluxos de navegador, a autenticação multifator é necessária.
|Para aplicativos cliente avançados, as senhas de aplicativo são necessárias.|Para aplicativos cliente avançados, as senhas de aplicativo são necessárias.

### Para habilitar IPs confiáveis

1. Entre no Portal de Gerenciamento do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório em que deseja habilitar IPs confiáveis.
4. No Diretório que você selecionou, clique em Configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações do Serviço, em IPs Confiáveis, selecione:

	- Para solicitações de usuários federados provenientes da minha intranet – todos os usuários federados que estiverem entrando pela rede corporativa ignorarão a autenticação multifator usando uma declaração emitida pelo AD FS. 
	- Para solicitações de um intervalo específico de IPs públicos – insira os endereços IP nas caixas fornecidas usando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP no intervalo xxx.xxx.xxx.1 – xxx.xxx.xxx.254, ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Você pode inserir até 12 intervalos de endereços IP.

7. Clique em Salvar.
8. Depois que as atualizações forem aplicadas, clique em Fechar.



![Nuvem](./media/multi-factor-authentication-whats-next/trustedips2.png)



 
## Senhas de aplicativo

Em alguns aplicativos, como o Office 2010 ou mais antigo e o Apple Mail, você não pode usar autenticação multifator. Para usar esses aplicativos, você precisará usar as "senhas de aplicativo" no lugar de sua senha tradicional. A senha de aplicativo permite que um aplicativo se desvie da autenticação multifator e continue funcionando.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication. Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013. Para saber mais, confira [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### Coisas importantes a saber sobre senhas de aplicativo

Veja a seguir uma lista de importante das coisas que você deve saber sobre senhas de aplicativo.

Experiência de autenticação|Para aplicativos baseados em navegador|Para aplicativos não baseados em navegador
:------------- | :------------- | :------------- 
|<ul><li>O fator primário de autenticação é realizado no local</li><li>O fator secundário é um método baseado em telefone realizado pela Identidade na Nuvem.</li>|<ul><li>Os administradores e usuários podem usar senhas de aplicativo para entrar.

- Os usuários podem ter várias senhas de aplicativo, o que aumenta a área de superfície para roubo. Como senhas de aplicativo são difíceis de lembrar, isso pode incentivar as pessoas anotá-las. Isso não é recomendável e deve ser desencorajado, porque apenas um fator é necessário para fazer logon com a senha de aplicativo.
- Os aplicativos que armazenam senhas em cache e a usam em cenários locais podem começar a falhar, pois a senha do aplicativo não será conhecida fora da ID organizacional. Um exemplo é o de emails do Exchange que estão no local, mas o email arquivado está na nuvem. A mesma senha não funcionará.
- A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
- Atualmente, há um limite de 40 senhas por usuário. Você será solicitado a excluir uma de suas senhas de aplicativo existente para criar uma nova.
- Assim que a multi-factor authentication é habilitada na conta de um usuário, as senhas do aplicativo podem ser usadas com a maioria dos clientes sem navegador, como Outlook e Lync, mas as ações administrativas não podem ser executadas usando senhas do aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell, mesmo se o usuário tiver uma conta administrativa. Certifique-se de criar uma conta de serviço com uma senha segura para executar scripts do PowerShell e não habilite essa conta para multi-factor authentication.

>[AZURE.WARNING]  As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem.
>
>Esteja ciente de que as senhas de aplicativos não funcionarão em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática locais e na nuvem, já que as senhas de domínio são necessárias para autenticação local e as senhas de aplicativo são necessárias para autenticação na nuvem.


### Nomeando orientação para senhas de aplicativo
É recomendável que os nomes de senha de aplicativo reflitam o dispositivo no qual eles serão usados. Por exemplo, se você tiver um laptop que tem aplicativos sem navegador, como Outlook, Word e Excel, basta criar uma senha de aplicativo chamada Laptop e usar essa senha em todos esses aplicativos. Embora você possa criar senhas separadas para todos esses aplicativos, isso não é recomendável. A maneira recomendada é usar uma senha de aplicativo por dispositivo.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### Senhas de aplicativo federado (SSO)
O Azure AD oferece suporte à federação com Serviços de Domínio do Active Directory (AD DS) local do Windows Server. Se sua organização for federada (SSO) com o Azure AD e você pretende usar o Azure Multi-Factor Authentication, é importante que você tenha as seguinte informações ao usar senhas de aplicativo. Isso se aplica apenas aos clientes federados (SSO).

- A Senha de aplicativo é verificada pelo Azure AD e, portanto, se desvia da federação. A federação só é usada ativamente ao configurar a Senha de aplicativo.
- Para usuários federados (SSO), nós nunca iremos ao Provedor de Identidade (IdP), ao contrário do fluxo passivo. As senhas serão armazenadas na ID organizacional. Se o usuário sair da empresa, essa informação deve ir para a ID organizacional usando o DirSync em tempo real. A desabilitação/exclusão da conta pode levar até 3 horas para sincronizar, atrasando a desabilitação/exclusão da Senha de aplicativo no Azure AD.
- As configurações do Controle de Acesso do Cliente local não são consideradas pela Senha de aplicativo
- Nenhum recurso de registro de autenticação local/auditoria recurso está disponível para a Senha de aplicativo
- É preciso que haja mais treinamento do usuário final é necessário para o cliente Microsoft Lync 2013. Para as etapas obrigatórias, consulte Como alterar a senha em seu email para a senha de aplicativo.
- Alguns projetos arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas da organização e senhas de aplicativo ao usar a autenticação multifator com os clientes, dependendo de onde eles se autenticam. Para clientes que se autenticam em uma infraestrutura local, você usaria um nome e senha da organização. Para clientes que se autenticam no Azure AD, você usaria a senha de aplicativo.

Por exemplo, suponha que você tenha uma arquitetura que consiste no seguinte:

- Você está associando sua instância local do Active Directory com o Azure AD
- Você está usando o Exchange online
- Você está usando o Lync que está especificamente no local
- Você está usando o Azure Multi-Factor Authentication


<center>![Proofup](./media/multi-factor-authentication-whats-next/federated.png)</center>

 Nesses casos, você deve fazer o seguinte:

- Ao entrar no Lync, use o nome de usuário e a senha de suas organizações.
- Ao tentar acessar o catálogo de endereços por meio de um cliente Outlook que se conecte ao Exchange online, use uma senha de aplicativo.

### Permitindo a criação de senha de aplicativo
Por padrão, os usuários não podem criar senhas de aplicativo. Esse recurso deve ser ativado. Para permitir aos usuários possam criar senhas de aplicativo, use o procedimento a seguir.

#### Para permitir que os usuários criem senhas de aplicativo



1. Entre no Portal de Gerenciamento do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário que deseja habilitar.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Na parte superior da página de autenticação multifator, clique em Configurações de serviço.
7. Certifique-se de que o botão de opção ao lado de Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores esteja selecionado.

<center>![Cloud](./media/multi-factor-authentication-whats-next/trustedips.png)</center>

### Criando senhas de aplicativo
Os usuários podem criar senhas de aplicativo durante o registro inicial. É fornecida uma opção no final do processo de registro que lhes permite criá-las.

Além disso, eles também podem criar senhas de aplicativo posteriormente alterando suas configurações no Portal do Azure, o portal do Office 365 ou

### Para criar senhas de aplicativo no portal do Office 365
--------------------------------------------------------------------------------


1. Entre no portal do Office 365
2. No canto superior direito, selecione o widget de configurações
3. À esquerda, selecione Verificação de Segurança Adicional
4. À direita, selecione **Atualizar meus números de telefone usados para segurança da conta**
5. Na parte superior da página de prova, selecione as senhas de aplicativo
6. Clique em **Criar**
7. Insira um nome para a senha de aplicativo e clique em **Avançar**
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### Para criar senhas de aplicativo no portal do Azure
--------------------------------------------------------------------------------
1. Entre no Portal de Gerenciamento do Azure
3. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
5. Na parte superior da página de prova, selecione as senhas de aplicativo
6. Clique em **Criar**
7. Insira um nome para a senha de aplicativo e clique em **Avançar**
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.


<center>![Cloud](./media/multi-factor-authentication-whats-next/app2.png)</center>

### Para criar senhas de aplicativo se você não tiver uma assinatura do Office 365 ou do Azure
--------------------------------------------------------------------------------
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Na parte superior, selecione Perfil.
3. Clique no nome de usuário e selecione Verificação de Segurança Adicional.
5. Na parte superior da página de prova, selecione as senhas de aplicativo
6. Clique em **Criar**
7. Insira um nome para a senha de aplicativo e clique em **Avançar**
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

<center>![Cloud](./media/multi-factor-authentication-whats-next/myapp.png)</center>

## Suspender a autenticação multifator para dispositivos e navegadores lembrados (visualização pública)

A suspensão de autenticação multifator para navegadores e dispositivos lembrados é um recurso que lhe permite dar aos usuários a opção de suspender a MFA por um determinado número de dias após a entrada bem-sucedida usando a MFA. Esse é um recurso gratuito para todos os usuários MFA e aprimora o uso para os usuários. No entanto, como os usuários têm permissão para suspender a MFA, esse recurso pode reduzir a segurança da conta.

Para garantir que as contas de usuário sejam protegidas, você deve restaurar a autenticação multifator para seus dispositivos para qualquer um dos seguintes cenários:

- Se sua conta corporativa estiver comprometida
- Se um dispositivo lembrado for perdido ou roubado

> [AZURE.NOTE] Esse recurso é implementado como um cache de cookie do navegador. Ele não funcionará se os cookies do navegador não estiverem habilitados.

### Como habilitar/desabilitar Suspender MFA para dispositivos lembrados e definir

<ol>
<li>Entre no Portal de Gerenciamento do Azure.</li>
<li>À esquerda, clique no Active Directory.</li>
<li>No Active Directory, clique no diretório em que deseja configurar Suspender o Multi-Factor Authentication.</li>
<li>No Diretório que você selecionou, clique em Configurar.</li>
<li>Na seção autenticação multifator, clique em Gerenciar configurações de serviço.</li>
<li>Na página Configurações de serviço, em Gerenciar configurações de usuário do dispositivo, marque/desmarque elar seleção de **Permite aos usuários suspender o Multi-Factor Authentication, fazendo com que um dispositivo seja lembrado**.</li>
![Suspender dispositivos](./media/multi-factor-authentication-manage-users-and-devices/suspend.png) <li>Defina o número de dias que você deseja permitir a suspensão. O padrão é 14 dias.</li> <li>Clique em Salvar.</li> <li>Clique em Fechar.</li>

<!---HONumber=AcomDC_0128_2016-->