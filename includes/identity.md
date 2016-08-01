O gerenciamento de identidade é tão importante em ambiente de nuvem pública quanto local. Para ajudar nisso, o Azure oferece suporte a várias tecnologias de identidade de nuvem diferentes. Entre elas:

- Você pode executar o Active Directory do Windows Server (normalmente chamado apenas de AD) na nuvem usando máquinas virtuais criadas com Máquinas Virtuais do Azure. Essa abordagem faz sentido quando você estiver usando o Azure para estender seu datacenter local para a nuvem.

- Você pode usar o Active Directory do Azure para fornecer aos usuários um logon único de aplicativos de Software hospedado como serviço (SaaS). Por exemplo, o Office 365 da Microsoft utiliza essa tecnologia, e aplicativos executados no Azure ou outras plataformas na nuvem também podem usá-lo.

- Aplicativos em execução na nuvem ou no local podem usar o Controle de Acesso no Active Directory do Azure para permitir que os usuários façam logon usando identidades do Facebook, da Google, da Microsoft e de outros provedores de identidade.


Este artigo descreve todas essas três opções.

## Sumário

- [Executando o Active Directory do Windows Server em máquinas virtuais](#adinvm)

- [Usando o Active Directory do Azure](#ad)

- [Usando o Controle de Acesso do Active Directory do Azure](#ac)


## <a name="adinvm"></a>Executando o Active Directory do Windows Server em máquinas virtuais

Executar o AD do Windows Server em máquinas virtuais do Azure é muito semelhante a executá-lo localmente. A [Figura 1](#fig1) mostra um exemplo típico de como isso acontece.

![Active Directory do Azure na Máquina Virtual](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figura 1: O Active Directory do Windows Server pode ser executado em máquinas virtuais do Azure conectadas ao datacenter local de uma organização usando a Rede Virtual do Azure.

No exemplo mostrado aqui, o AD do Windows Server está em execução em VMs criadas usando Máquinas Virtuais do Azure, a tecnologia da plataforma IaaS. Essas VMs e algumas outras são agrupadas em uma rede virtual conectada a um datacenter local usando a Rede Virtual do Azure. A rede virtual forma um grupo de máquinas virtuais da nuvem que interage com a rede local por meio de uma conexão VPN (rede virtual privada). Isso permite que essas máquinas virtuais do Azure se pareçam exatamente outra sub-rede para o datacenter local. Como mostra a figura, duas dessas VMs estão executando controladores de domínio do AD do Windows Server. As outras máquinas virtuais na rede virtual podem estar executando aplicativos, como o SharePoint, ou podem estar sendo usadas de alguma outra maneira, como para desenvolvimento e teste. O datacenter local também está executando dois controladores de domínio do AD do Windows Server.

Há várias opções para conectar os controladores de domínio na nuvem com aqueles que estão em execução no local:

- Tornar todas elas parte de um único domínio do Active Directory.

- Criar domínios separados do AD locais e na nuvem que fazem parte da mesma floresta.

- Criar florestas do AD separadas na nuvem e no local e depois conectar as florestas usando relações de confiança entre florestas ou os Serviços de Federação do Active Directory (AD FS) do Windows Server, que também podem ser executados em máquinas virtuais no Azure.

Seja qual for a opção feita, um administrador deve garantir que as solicitações de autenticação de usuários locais vão para controladores de domínio em nuvem somente quando necessário, uma vez que o link para a nuvem provavelmente será mais lento do que para redes locais. Outro fator a ser levado em conta ao conectar controladores de domínio locais e em nuvem é o tráfego gerado pela replicação. Normalmente, os controladores de domínio na nuvem estão em seu próprio site do AD, que permite a um administrador agendar a frequência com a qual a replicação é feita. O Azure cobra pelo tráfego enviado para fora de um datacenter do Azure, embora não seja por bytes enviados, o que pode afetar as opções de replicação do administrador. Também vale a pena salientar que, enquanto o Azure fornece seu próprio suporte do Sistema de Nomes de Domínios (DNS), nesse serviço faltam recursos exigidos pelo Active Directory (como suporte para registros de DNS dinâmico e SRV). Por isso, executar o AD do Windows Server no Azure exige a configuração dos seus próprios servidores DNS na nuvem.

Executar o AD do Windows Server em VMs do Azure pode fazer sentido em várias situações diferentes. Estes são alguns exemplos:

- Se estiver usando as Máquinas Virtuais do Azure como uma extensão do seu próprio datacenter, você pode executar aplicativos em nuvem que precisem de controladores de domínio locais para lidar com coisas como as solicitações de autenticação integrada do Windows ou consultas LDAP. O SharePoint, por exemplo, interage com frequência com o Active Directory, então, embora seja possível executar um farm do SharePoint no Azure usando um diretório local, configurar controladores de domínio na nuvem melhorará o desempenho de forma significativa. (No entanto, é importante perceber que isso não é necessariamente obrigatório. Vários aplicativos podem ser executados com êxito na nuvem usando apenas os controladores de domínio locais).

- Suponha que uma filial longínqua não possua os recursos para executar seus próprios controladores de domínio. Hoje em dia, os usuários devem autenticar em controladores de domínio do outro lado do mundo - os logons são lentos. Executar o Active Directory no Azure em um datacenter Microsoft mais próximo pode acelerar isso sem a necessidade de mais servidores na filial da empresa.

- Uma organização que usa o Azure para recuperação de desastres pode manter um pequeno conjunto de VMs ativas na nuvem, incluindo um controlador de domínio. Em seguida, ela pode ser preparada para expandir este site conforme necessário para assumir o controle de falhas em outro lugar.

Existem também outras possibilidades. Por exemplo, não é necessário que você conecte o AD do Windows Server na nuvem a um datacenter local. Se desejar executar um farm do SharePoint que serviu um conjunto específico de usuários, por exemplo, todos os quais efetuariam logon exclusivamente com identidades baseadas na nuvem, você pode criar uma floresta autônoma no Azure. A maneira como você usa essa tecnologia depende de quais são suas metas. (Para obter orientações mais detalhadas sobre como usar o AD do Windows Server com o Azure, [clique aqui](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Usando o Active Directory do Azure

Conforme os aplicativos SaaS tornam-se cada vez mais comuns, eles geram uma questão óbvia: que tipo de serviço de diretório esses aplicativos baseados em nuvem devem usar? A resposta da Microsoft para essa pergunta é: o Active Directory do Azure.

Existem duas opções principais para usar esse serviço de diretório na nuvem:

- Indivíduos e organizações que usam somente aplicativos SaaS podem depender do Active Directory do Azure como seu único serviço de diretório.

- Organizações que executam o Active Directory do Windows Server podem conectar seu diretório local ao Active Directory do Azure e usá-lo para fornecer aos seus usuários um logon único para aplicativos SaaS.


A [Figura 2](#fig2) ilustra a primeira dessas duas opções, na qual o Active Directory do Azure é tudo que é necessário.

![Active Directory do Azure na Máquina Virtual](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figura 2: O Active Directory do Azure fornece aos usuários da organização um logon único para aplicativos SaaS, incluindo o Office 365.

Como mostra a figura, o AD do Azure é um serviço multilocatário. Isso significa que ele pode suportar simultaneamente várias organizações, armazenando informações de diretório sobre os usuários em cada um deles. Neste exemplo, um usuário em uma organização A está tentando acessar um aplicativo SaaS. Este aplicativo pode ser parte do Office 365, como SharePoint Online, ou pode ser outra coisa — aplicativos não Microsoft também podem usar essa tecnologia. Como o AD do Azure suporta o protocolo SAML 2.0, tudo que é necessário de um aplicativo é a capacidade de interagir com esse padrão da indústria. (De fato, os aplicativos que usam o AD do Azure podem ser executados em qualquer datacenter, não apenas em um datacenter do Azure.)

O processo começa quando o usuário acessa um aplicativo SaaS (etapa 1). Para usar esse aplicativo, o usuário deverá apresentar um token emitido pelo AD do Azure.

Esse token contém informações que identificam o usuário e é assinado digitalmente pelo AD do Azure. Para obter o token, o usuário autentica a si próprio no AD do Azure, fornecendo um nome de usuário e uma senha (etapa 2). Em seguida, o AD do Azure retorna o token necessário (etapa 3).

Esse token é enviado para o aplicativo de SaaS (etapa 4), que valida a assinatura do token e usa seu conteúdo (etapa 5). Normalmente, o aplicativo usará as informações de identidade que o token contém para decidir quais informações o usuário tem permissão para acessar e, talvez, de outras maneiras.

Se o aplicativo precisar de mais informações sobre o usuário do que o que está contido no token, ele pode solicitar isso diretamente do AD do Azure usando a Graph API do AD do Azure (etapa 6). Na versão inicial do Azure AD, o esquema do diretório é muito simples: ele contém apenas os usuários e grupos e as relações entre eles. Aplicativos podem usar essas informações para saber mais sobre as conexões entre os usuários. Por exemplo, suponha que um aplicativo precisa saber quem é o gerente desse usuário para decidir se ele tem permissão de acesso para alguns blocos de dados. Ele pode aprender isso consultando o AD do Azure por meio da Graph API.

A Graph API usa um protocolo RESTful comum, o que o torna fácil de usar da maioria dos clientes, inclusive dispositivos móveis. A API também suporta as extensões definidas pelo OData, adicionando itens como uma linguagem de consulta para permitir que os clientes acessem dados de maneiras mais úteis. (Para obter mais informações sobre OData, consulte [Apresentando o OData (a página pode estar em inglês)](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf).) Como a Graph API pode ser usada para aprender sobre as relações entre os usuários, ela permite que os aplicativos entendam o gráfico social que está incorporado no esquema do AD do Azure de uma determinada organização (que é o motivo pelo qual ela é chamada de Graph API - API gráfica). E para autenticar a si próprio no AD do Azure para solicitações da Graph API, um aplicativo usa o OAuth 2.0.

Se uma organização não usa o Active Directory do Windows Server — ela não possui servidores locais ou domínios — e baseia-se exclusivamente em aplicativos de nuvem que usam o AD do Azure, usar apenas esse diretório em nuvem daria aos usuários da empresa um logon único a todos eles. Apesar de esse cenário se tornar mais comum todos os dias, a maioria das organizações ainda usa domínios locais criados com o Active Directory do Windows Server. O AD do Azure tem uma função útil para desempenhar aqui também, como mostra a [Figura 3](#fig3).

![Active Directory do Azure na Máquina Virtual](./media/identity/identity_03_AD.png) <a id="fig3"></a>Figura 3: uma organização pode federar o Active Directory do Windows Server com o Active Directory do Azure para fornecer a seus usuários um logon único para aplicativos SaaS.

Neste cenário, um usuário em uma organização B deseja acessar um aplicativo SaaS. Antes de fazer isso, os administradores de diretório da organização devem estabelecer um relacionamento de federação com AD do Azure usando o AD FS, como mostra a figura. Os administradores também devem configurar a sincronização de dados entre o AD do Windows Server e o AD do Azure locais da organização. Isso copia automaticamente informações de usuário e grupo do diretório local para AD do Azure. Observe o que isso permite: em vigor, a organização está ampliando seu diretório local para a nuvem. Combinar o AD do Windows Server e o AD do Azure dessa forma oferece à organização um serviço de diretório que pode ser gerenciado como uma entidade única, existindo ainda um volume tanto local quanto na nuvem.

Para usar o AD do Azure, o usuário primeiro faz logon no seu domínio local do Active Directory como de costume (etapa 1). Quando ele tenta acessar o aplicativo de SaaS (etapa 2), os resultados do processo de federação no AD do Azure emitem um token para este aplicativo (etapa 3). (Para obter mais informações sobre o funcionamento da federação, consulte [Identidade baseada em declarações para Windows: tecnologias e cenários](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Tal como antes, esse token contém informações que identificam o usuário e é assinado digitalmente pelo AD do Azure. Esse token é enviado para o aplicativo de SaaS (etapa 4), que valida a assinatura do token e usa seu conteúdo (etapa 5). E está no cenário anterior, o SaaS aplicativo pode usar a Graph API para saber mais sobre este usuário se necessário (etapa 6).

Hoje em dia, o AD do Azure AD não é uma substituição completa do AD do Windows Server local. Como já mencionado, o diretório de nuvem tem um esquema muito mais simples e também não possui itens como diretiva de grupo, a capacidade de armazenar informações sobre computadores e suporte a LDAP. (Na verdade, uma máquina Windows não pode ser configurada para permitir aos usuários fazer logon usando apenas o AD do Azure — este não é um cenário aceito.) Em vez disso, as metas iniciais do AD do Azure incluem permitir que usuários corporativos acessem aplicativos na nuvem sem manter um logon separado e liberar os administradores de diretório local da sincronização manual de seu diretório local com todos os aplicativos SaaS que sua organização usa. No entanto, ao longo do tempo, o esperado é que esse serviço de diretório de nuvem atenda a uma grande variedade de cenários.

## <a name="ac"></a>Usando o Controle de Acesso do Active Directory do Azure

Tecnologias de identidade baseada em nuvem podem ser usadas para resolver uma variedade de problemas. O Active Directory do Azure pode fornecer aos usuários da organização um logon único para múltiplos aplicativos SaaS, por exemplo. Mas tecnologias de identidade na nuvem também podem ser usadas de outras maneiras.

Suponha, por exemplo, que um aplicativo deseja permitir que seus usuários efetuem logon usando tokens emitidos por vários *provedores de identidade (IdPs)*. Existem diversos provedores de identidade diferentes atualmente, incluindo Facebook, Google, Microsoft e outros, e frequentemente os aplicativos permitem que os usuários entrem usando uma dessas identidades. Por que um aplicativo deve se preocupar em manter a sua própria lista de usuários e senhas quando, em vez disso, pode basear-se em identidades que já existem? Aceitar identidades existentes facilita a vida dos usuários, que possuem um nome de usuário e senha a menos para se lembrar, e das pessoas que criam o aplicativo, que não precisam mais manter suas próprias listas de nomes de usuário e senhas.

Mas enquanto cada provedor de identidade emite algum tipo de token, esses tokens não padrão — cada IdP tem seu próprio formato. Além disso, as informações desses tokens também não padrão. Um aplicativo que deseja aceitar tokens emitidos por, digamos, Facebook, Google e Microsoft enfrenta o desafio de escrever código exclusivo para lidar com cada um desses formatos diferentes.

Mas por que fazer isso? Em vez disso, por que não criar um intermediário que possa gerar um único formato de token com uma representação comum de informações de identidade? Essa abordagem tornaria mais simples a vida dos desenvolvedores que criam aplicativos, uma vez que agora eles precisam lidar com apenas um tipo de token. O Controle de Acesso do Active Directory do Azure faz exatamente isso, oferecendo um intermediário na nuvem para trabalhar com vários tokens. A [Figura 4](#fig4) mostra como is funciona

![Active Directory do Azure na Máquina Virtual](./media/identity/identity_04_IdentityProviders.png) <a id="fig4"></a>Figura 4: o Controle de Acesso do Active Directory do Azure torna mais fácil para aplicativos aceitarem os tokens de identidade emitidos por diferentes provedores de identidade.

O processo começa quando um usuário tenta acessar o aplicativo em um navegador. O aplicativo o redireciona para um IdP de sua escolha (no qual o aplicativo também confia). O usuário se autentica por conta própria nesse IdP, digitando um nome de usuário e uma senha (etapa 1), por exemplo, e o IdP retorna um token contendo informações sobre ele (etapa 2).

Como mostra a figura, o Controle de Acesso oferece suporte a uma variedade de diferentes IdPs baseados em nuvem, incluindo contas criadas pelo Google, Yahoo, Facebook, Microsoft (anteriormente conhecido como Windows Live ID) e qualquer provedor de OpenID. Ele também dá suporte a identidades criadas usando o Azure Active Directory e, por meio de federação com o AD FS, o Active Directory do Windows Server. O objetivo é abordar as identidades usadas com mais frequência hoje em dia, sejam elas emitidas por IdPs na nuvem ou no local.

Uma vez que o navegador do usuário tem um token do IdP do IdP escolhido, ele envia esse token para o Controle de Acesso (etapa 3). O Controle de Acesso valida o token, certificando-se de que ele realmente foi emitido por esse IdP, e cria um novo token de acordo com as regras definidas para este aplicativo. Assim como o Active Directory do Azure, o Controle de Acesso é um serviço multilocatário, mas os inquilinos são aplicativos em vez das empresas dos clientes. Cada aplicativo pode obter seu próprio namespace, como mostra a figura, e pode definir várias regras sobre autorização e muito mais.

Essas regras permitem que o administrador de cada aplicativo defina como os tokens de vários IdPs devem ser transformados em um token do Controle de Acesso. Por exemplo, se IdPs diferentes usam tipos diferentes para representar os nomes de usuário, as regras do Controle de Acesso podem transformar todos eles em um tipo de nome de usuário comum. O Controle de Acesso então envia esse novo token de volta para o navegador (etapa 4), que o envia para o aplicativo (etapa 5). Uma vez que ele tem o token do Controle de Acesso, o aplicativo verifica se esse token realmente foi emitido pelo Controle de Acesso e usa as informações que ele contém (etapa 6).

Embora esse processo possa parecer um pouco complicado, ele realmente facilita muito a vida do criador do aplicativo. Em vez de lidar com diversos token que contêm informações diferentes, o aplicativo pode aceitar identidades emitidas por vários provedores de identidade enquanto ainda recebe apenas um único token com informações conhecidas. Além disso, em vez de solicitar que cada aplicativo seja configurado para confiar em vários IdPs, essas relações de confiança são mantidas pelo Controle de Acesso: um aplicativo só precisa confiar nele.

Vale a pena salientar que nada do Controle de Acesso está ligado ao Windows — ele poderia ser usado da mesma forma por um aplicativo Linux que aceite somente identidades Google e Facebook. E mesmo que o Controle de Acesso seja parte da família do Active Directory do Azure, ele pode ser considerado um serviço totalmente distinto do que foi descrito na seção anterior. Embora as duas tecnologias funcionem com identidade, elas abordam problemas completamente diferentes (embora a Microsoft tenha declarado a intenção de integrá-las em algum momento).

Trabalhar com identidade é importante em praticamente todos os aplicativos. O objetivo do Controle de Acesso é tornar mais fácil aos desenvolvedores criar aplicativos que aceitem identidades de provedores de identidade diferentes. Ao colocar esse serviço na nuvem, a Microsoft disponibilizou-o a qualquer aplicativo executado em qualquer plataforma.

##Sobre o autor

David Chappell é diretor da Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) em São Francisco, Califórnia.

<!---HONumber=AcomDC_0720_2016-->