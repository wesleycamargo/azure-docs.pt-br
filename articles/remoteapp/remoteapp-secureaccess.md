
<properties 
    pageTitle="Proteger o acesso ao RemoteApp do Azure e além | Microsoft Azure"
	description="Saiba como proteger o acesso ao RemoteApp do Azure por meio do acesso condicional no Active Directory do Azure"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/05/2016" 
    ms.author="elizapo" />

# Proteger o acesso ao RemoteApp do Azure e além

Neste artigo, oferecemos uma visão geral de como um administrador pode configurar um canal de acesso seguro começando no usuário final por meio do RemoteApp do Azure e terminando com um recurso seguro, como um banco de dados SQL ou outro aplicativo de back-end. O objetivo é garantir que somente os usuários autorizados que atendem as condições desejadas possam acessar os aplicativos remotos e que o back-end seguro só possa ser acessado a partir do ambiente do RemoteApp do Azure controlado e não de outros locais.

Há 3 áreas principais que o administrador precisa examinar:

![Considerações de acesso condicional do RemoteApp do Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Leia para obter informações e respostas para essas perguntas.

## Quem pode acessar a coleção?
O administrador escolhe os usuários que podem acessar os aplicativos remotos na coleção. Você pode usar as contas corporativas ou de estudante do Active Directory do Azure (AD do Azure) (anteriormente chamadas de "contas organizacionais") ou as contas da Microsoft (por exemplo, @outlook.com). A maioria dos cenários empresariais usa as contas do AD do Azure. Elas permitem que você use os recursos de acesso condicional analisados posteriormente e também são a única opção para as coleções de domínio. O restante do artigo pressupõe que você esteja usando as contas do AD do Azure com o RemoteApp do Azure.

**O que conseguimos:**

Usar as contas do AD do Azure para controlar o acesso ao RemoteApp do Azure fornece duas coisas:

1.	Sempre sabemos quem pode acessar os aplicativos que publicamos e acessar quaisquer back-ends aos quais esses aplicativos se conectam.
2.	Controlamos o AD do Azure subjacente para que possamos criar e excluir as contas de usuário, definir políticas de senha, usar a autenticação multifator, etc. 

## Como a coleção é acessada? De onde?
Normalmente, os administradores querem definir políticas para acessar um ambiente de acesso à Internet público, como o RemoteApp do Azure. Por exemplo, eles querem garantir que os usuários que acessam o ambiente de fora da rede corporativa tenham que usar a autenticação multifator (MFA) para conseguirem acesso ou, talvez, que sejam bloqueados completamente.

Os administradores do RemoteApp do Azure podem usar a funcionalidade disponível por meio do AD do Azure Premium para definir políticas de acesso condicional para seu ambiente do RemoteApp do Azure. Eles também podem usar relatórios avançados e recursos de alerta para monitorar como o ambiente está sendo acessado.

### Como configurar o acesso condicional para o RemoteApp do Azure
Vamos examinar um cenário de exemplo: o administrador do RemoteApp do Azure deseja bloquear o acesso ao ambiente quando os usuários estão fora da rede corporativa.

>[AZURE.NOTE] Supomos que você tenha atualizado o AD do Azure para a camada Premium e criado pelo menos uma coleção do RemoteApp do Azure.

1.	No portal do Azure, clique na guia **Active Directory**. Em seguida, clique no diretório que você deseja configurar.

	Lembre-se: o acesso condicional é uma propriedade do seu diretório e não do RemoteApp do Azure, portanto, toda a configuração é feita no nível do diretório. Isso também significa que você precisa ser o administrador do diretório para fazer essas alterações.

2.	Clique em **Aplicativos** e, em seguida, clique em **RemoteApp do Microsoft Azure** para configurar o acesso condicional. Observe que você pode definir o acesso condicional para cada aplicativo do "software como serviço" no diretório separadamente.
![Configurar o acesso condicional para o RemoteApp do Azure](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	Na guia **Configurar**, defina **Habilitar Regras de Acesso** para ON.
![Habilitar regras de acesso para o RemoteApp do Azure](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	Agora, você pode configurar várias regras e escolher em quem aplicá-las:

	1. Escolha **Bloquear o acesso quando não estiver no trabalho** para impedir completamente que os usuários acessem o RemoteApp do Azure fora do ambiente de rede especificado.
	2. Clique na opção abaixo para definir os intervalos de endereços IP que constituem sua "rede confiável". Tudo que estiver fora será rejeitado.

5.	Teste sua configuração iniciando o cliente RemoteApp do Azure a partir de um endereço IP fora do intervalo especificado. Depois de entrar com as suas credenciais do AD do Azure, você verá uma mensagem como esta:

![Acesso negado ao RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### Recursos de acesso condicional futuros 
A equipe do Active Directory do Azure está trabalhando em novas funcionalidades do Acesso Condicional. Os administradores poderão criar novos tipos de regras além das regras baseadas na localização da rede. Uma visualização pública da nova funcionalidade deve estar disponível em breve.

### Como monitorar o acesso ao RemoteApp do Azure
Um ótimo recurso para usar junto com o acesso condicional é a funcionalidade de relatórios do Active Directory Premium do Azure. Você pode usar os relatórios para monitorar quem está acessando seu ambiente e detectar qualquer atividade suspeita.

Por exemplo, você pode ver os nomes dos usuários que acessaram o RemoteApp do Azure, quantas vezes eles acessaram e quando.

1.	No portal do Azure, clique em **Active Directory** e em seu diretório.

2.	Vá para a guia **Relatórios**.

3.	Na lista de relatórios, escolha **Uso do aplicativo** em **Aplicativos integrados**.

	Você verá algumas estatísticas agregadas para o RemoteApp do Azure.
![Estatísticas de acesso agregadas do RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	Clique no aplicativo para mostrar as informações sobre os usuários que acessam o RemoteApp do Azure.![Estatísticas de acesso do usuário para o RemoteApp do Azure](./media/remoteapp-secureaccess/ra-userstats.png)
 
### Resumo
Com o Active Directory Premium do Azure, você pode configurar regras de acesso para o RemoteApp do Azure (e outro software como um aplicativo de serviço disponível por meio do AD do Azure). As regras estão limitadas às políticas baseadas na localização da rede, mas no futuro, serão estendidas a outros aspectos do gerenciamento empresarial.

O AD Premium do Azure também oferece as funcionalidades de relatório e monitoramento que estendem ainda mais o controle que o administrador tem sobre seu ambiente do RemoteApp do Azure.

## Como garanto que meu recurso seguro seja acessível somente a partir de meu ambiente do RemoteApp do Azure?
Nas seções anteriores deste artigo, focamos em proteger o acesso ao ambiente do RemoteApp do Azure. Fizemos isso escolhendo os usuários que têm permissão de acesso e configurando regras de acesso para controlar ainda mais como eles podem usar o serviço.

Um cenário comum para as implantações do RemoteApp do Azure é que os aplicativos remotos precisam comunicar-se com um recurso de back-end, por exemplo, um banco de dados SQL. Esse recurso é hospedado no local (por exemplo, em uma rede corporativa) ou na nuvem (por exemplo, na IaaS do Azure). Os administradores geralmente desejam assegurar que o recurso de back-end só possa ser acessado pelos aplicativos implantados por meio do RemoteApp do Azure, e não, por exemplo, por um aplicativo executado diretamente no PC do usuário e acessado na Internet pública. O RemoteApp do Azure é normalmente visto como o ambiente protegido e gerenciado centralmente, portanto, o único caminho por meio do qual os usuários devem interagir com o recurso de back-end.

A solução é colocar o ambiente do RemoteApp do Azure e o recurso seguro na mesma Rede Virtual (VNET) do Azure. Se o recurso estiver em um site diferente, você poderá estabelecer uma conexão VPN site a site, por exemplo, para criar uma rede virtual que abranja o data center do Azure e o ambiente local do cliente.

O RemoteApp do Azure oferece suporte a dois tipos de implantações de coleção onde você pode fornecer sua própria VNET:

-	Não no domínio: os aplicativos terão uma "linha de visão" dos outros recursos na VNET. Por exemplo, isso pode ser usado para conectar os aplicativos a um banco de dados SQL que usa a autenticação SQL (os aplicativos autenticam o usuário diretamente no banco de dados)

-	No domínio: as máquinas virtuais usadas pelo RemoteApp do Azure fazem parte de um controlador de domínio na VNET. Isso é útil quando os aplicativos precisam se autenticar em um Controlador de Domínio do Windows para obter acesso a um recurso de back-end.
![Uma coleção de domínios do RemoteApp do Azure](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### Como criar uma conexão segura entre o Azure e meu ambiente local
Há várias opções de configuração para conectar seus ambientes do Azure e local. Uma boa visão geral das opções está disponível aqui.

Com o RemoteApp do Azure, primeiro você precisa configurar sua VNET e, em seguida, usá-la durante o processo de criação de sua coleção.

## A solução completa
O diagrama a seguir mostra a solução completa na qual criamos um canal de acesso seguro a partir do usuário final, por meio do RemoteApp do Azure (ARA), até o recurso de back-end.
![Proteger o RemoteApp do Azure](./media/remoteapp-secureaccess/ra-secureoverview.png) No Estágio 1, escolhemos os usuários e criamos regras de acesso que determinam como o ARA pode ser acessado. No exemplo abaixo, apenas permitimos o acesso para os usuários que trabalham na rede corporativa. Os usuários não compatíveis não poderão acessar o ambiente ARA. No "Estágio 2", mostramos o recurso de back-end somente por meio da configuração da VNET/VPN que controlamos. O RemoteApp do Azure foi colocado na mesma VNET. O resultado final é que o recurso só pode ser acessado através do ambiente ARA.

<!----HONumber=AcomDC_0406_2016-->