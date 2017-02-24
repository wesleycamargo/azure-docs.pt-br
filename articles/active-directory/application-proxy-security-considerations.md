---
title: "Considerações de segurança ao acessar aplicativos remotamente usando o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Aborda considerações de segurança ao usar o Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 67650676117fef834ea819da2fc45ca66ed15493
ms.openlocfilehash: 1b713e015f48b044c6c9d8ff6a0d8669d4532a9f


---

# <a name="security-considerations-when-accessing-apps-remotely-using-azure-ad-application-proxy"></a>Considerações de segurança ao acessar aplicativos remotamente usando o Proxy de Aplicativo do Azure AD

>[!NOTE]
>O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
>  

Este artigo explica como o Proxy de Aplicativo do Azure AD fornece um serviço seguro para publicar e acessar aplicativos remotamente. 

O Proxy de Aplicativo do Azure AD oferece os seguintes benefícios de segurança:

**Acesso autenticado:** somente conexões autenticadas podem acessar sua rede

* O Proxy de Aplicativo do Azure AD conta com o STS do Azure AD para todas as autenticações. Para aplicativos publicados com pré-autenticação, não é permitido que nenhum tráfego passe para seu ambiente pelo serviço de proxy de aplicativo sem um token STS válido.
* A pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques anônimos, pois somente identidades autenticadas podem acessar o aplicativo de back-end.

**Acesso condicional:** aplicação de controles de política mais rígidos antes que as conexões com sua rede sejam estabelecidas

* Com acesso condicional, é possível definir mais restrições em relação ao que o tráfego pode acessar nos seus aplicativos de back-end. É possível definir restrições com base no local, na força da autenticação e no perfil de risco do usuário.
* Esse recurso permite estabelecer barreiras adicionais contra os invasores. Você pode ler mais sobre acesso condicional em [Introdução ao Acesso Condicional ao Azure Active Directory](https://azure.microsoft.com/en-us/documentation/articles/active-directory-conditional-access-azuread-connected-apps).

**Encerramento de tráfego:** todo o tráfego é encerrado na nuvem

* O Proxy de Aplicativo do Azure AD é um proxy reverso, portanto, todo o tráfego para os aplicativos de back-end é encerrado no serviço. A sessão pode ser restabelecida somente com o servidor back-end. Isso significa que os servidores back-end não são expostos ao tráfego HTTP direto. Por exemplo, você pode reduzir ataques direcionados com mais facilidade.

**Todo acesso é de saída:** nenhuma conexão de entrada precisa ser aberta para a rede corporativa

* Os conectores do Azure AD mantêm conexões de saída com o serviço de Proxy de Aplicativo do Azure AD. Isso significa que não é necessário abrir portas de firewall para conexões de entrada. 
* As abordagens tradicionais exigiam uma DMZ e abertura de acesso para conexões não autenticadas na extremidade da rede. Isso resultou na necessidade de muitos investimentos adicionais em produtos WAF para analisar o tráfego e oferecer proteções extras ao ambiente. Com o Proxy de Aplicativo, você pode evitar esse cenário. Você até pode pensar em não usar a DMZ, pois todas as conexões são de saída e feitas por um canal seguro.

**Inteligência baseada na linguagem de máquina e análise de segurança:** proteção com segurança de ponta

* Azure AD tem um recurso de proteção de identidade com inteligência orientada ao aprendizado de máquina com feeds de dados da nossa Unidade de Crimes Digitais e do Microsoft Security Response Center. Juntos, identificamos proativamente contas comprometidas e oferecemos proteção em tempo real em conexões de alto risco. Levamos em consideração vários fatores, como acesso de dispositivos infectados e por meio de redes que mantêm o anonimato, bem como de locais atípicos e improváveis.
* Muitos desses relatórios e eventos já estão disponíveis por meio de uma API para integração aos sistemas SIEM.
* Você pode ler mais sobre o Azure AD Identity Protection em [Azure Active Directory Identity Protection](https://azure.microsoft.com/en-us/documentation/articles/active-directory-identityprotection).
!
**Acesso remoto como um serviço:** você não precisa se preocupar com a manutenção e a aplicação de patches em servidores locais

* O Proxy de Aplicativo do Azure AD é um serviço de escala de Internet que temos. Sendo assim, você pode ter a certeza de que sempre obterá os patches e atualizações de segurança mais recentes. Portanto, cuidamos da segurança da sua rede.
* A não aplicação de patches no software ainda é responsável por um grande número de ataques. Com nosso modelo de serviço, você não tem mais que carregar o peso de gerenciar servidores de borda. 

Os serviços de acesso remoto fornecidos com o Azure AD operam de acordo com as diretrizes e os padrões descritos na [Central de Confiabilidade do Azure](https://azure.microsoft.com/en-us/support/trust-center). 

O diagrama abaixo mostra como o Azure AD permite acesso remoto seguro aos aplicativos locais.

 ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

>[!NOTE] 
>Para melhorar a segurança dos aplicativos publicados pelo Proxy de Aplicativo do Azure AD, bloquearemos robôs do rastreador da Web de indexação e o arquivamento de seus aplicativos. Cada vez que um robô de rastreador da Web tentar recuperar as configurações de robôs para um aplicativo publicado, o Proxy de Aplicativo responderá com um arquivo robots.txt que inclui o seguinte: <br>
>   _Agente de usuário: *_<br>
>   _Não permitir: /_
>

## <a name="components-of-the-azure-ad-app-proxy-solution"></a>Componentes da solução de Proxy de Aplicativo do Azure AD

O Proxy de Aplicativo do Azure AD consiste em duas partes:

* O serviço baseado em nuvem (AAD AP). É onde as conexões externas de cliente/usuário são feitas.
* Um componente local chamado Conector do Proxy de Aplicativo do AAD.  O conector escuta solicitações do serviço de AAD AP e trata das conexões com os aplicativos internos, incluindo o cuidado com os itens, como a KCD (Delegação Restrita de Kerberos) para SSO.

### <a name="traffic-flowsand-how-they-are-secured"></a>Fluxos de tráfego — e como eles são protegidos
Esta seção inclui detalhes sobre como os fluxos são protegidos. Um fluxo entre o conector e o serviço de Proxy de Aplicativo é estabelecido quando: 

* O Conector é configurado pela primeira vez.
* O Conector extrai informações de configuração do serviço de Proxy de Aplicativo, incluindo o grupo de conectores do qual cada conector é um membro.
* Um usuário acessa um aplicativo publicado.

>[!NOTE]
>Todas as comunicações ocorrem por SSL e sempre originam-se no Conector para o serviço de Proxy de Aplicativo. Esse serviço é apenas de saída.
>

O conector usa um certificado de cliente para autenticar o serviço de Proxy de Aplicativo para todas as chamadas. A única exceção é a etapa de configuração inicial em que o certificado de cliente é estabelecido.

#### <a name="install-the-connector"></a>Instalar o Conector

O fluxo a seguir ocorre quando o conector é configurado pela primeira vez.

1. O registro do Conector para o serviço ocorre como parte da instalação do Conector. Nesse momento, o usuário é solicitado a inserir suas credenciais de administrador do Azure AD. O token adquirido é então apresentado ao serviço de Proxy de Aplicativo do Azure AD.
2. O Proxy de Aplicativo avalia o token para garantir que o usuário é um membro da função de administrador da empresa no locatário para o qual o token foi emitido. Se não for, o processo será encerrado.
3. O conector gera uma solicitação de certificado de cliente e passa isso com o token para o serviço de Proxy de Aplicativo que, por sua vez, verifica o token e assina a solicitação de certificado de cliente. 
4. O conector usa esse certificado de cliente para comunicação futura com o serviço de Proxy de Aplicativo.
4. O conector realiza uma extração inicial dos dados de configuração do sistema do serviço usando o respectivo certificado de cliente e está pronto para receber solicitações.

#### <a name="periodic-configuration-updates"></a>Atualizações periódicas de configuração

O fluxo a seguir ocorre periodicamente, sempre que o serviço de Proxy de Aplicativo atualiza as definições de configuração.

1. O conector se conecta ao ponto de extremidade de configuração no serviço de Proxy de Aplicativo usando seu certificado de cliente.
2. Depois que o certificado de cliente tiver sido validado, o serviço de Proxy de Aplicativo retorna dados de configuração ao conector. Por exemplo, o grupo de conectores do qual esse conector deve fazer parte.
3. O conector vai gerar uma nova solicitação de certificado se o certificado atual tiver mais de 30 dias, rotacionando efetivamente o certificado de cliente a cada 30 dias.

#### <a name="accesss-published-applications"></a>Acessar aplicativos publicados

O fluxo a seguir ocorre quando o usuário acessa um aplicativo publicado.

1. Quando um usuário acessa um aplicativo publicado, o serviço de Proxy de Aplicativo verifica as definições de configuração para o aplicativo.  Se o aplicativo estiver configurado para usar a Pré-autenticação com o Azure AD, o usuário é redirecionado para o STS do Azure AD para autenticação.  Isso será ignorado se você publicar o aplicativo usando a passagem.
 * Durante a autenticação com o Azure AD, verificaremos todos os requisitos da política de acesso condicional para o aplicativo específico. Isso é para garantir que o usuário foi atribuído ao aplicativo; (por exemplo, se o MFA for exigido, a sequência de autenticação solicitará do usuário uma autenticação de segundo fator).
 * Depois de realizadas todas as verificações, o STS do Azure AD emite um token assinado para o aplicativo e redireciona o usuário de volta ao serviço de Proxy de Aplicativo.
 * O Proxy de Aplicativo valida o token para garantir que ele foi emitido para o aplicativo ao qual o usuário estava solicitando acesso. Ele faz isso com outras verificações, como ao garantir que o token foi assinado pelo Azure AD, e que ainda está dentro da janela de validade, etc.
 * O Proxy de Aplicativo define um cookie de autenticação criptografado (como um cookie não persistente) para indicar que a autenticação para o aplicativo ocorreu.  Esse cookie inclui um carimbo de data/hora de expiração com base no token do Azure AD e outros dados, como o nome do usuário na qual se baseia a autenticação.  Esse cookie é criptografado com uma chave privada conhecida apenas pelo serviço de Proxy de Aplicativo.
 * O Proxy de Aplicativo redireciona o usuário de volta para a URL originalmente solicitada.
 >[!NOTE] 
 >Se qualquer parte das etapas da pré-autenticação falhar, a solicitação do usuário será negada e o usuário verá uma mensagem indicando a origem do problema.
 >

2. O Proxy de Aplicativo, ao receber a solicitação do cliente, valida que a condição de pré-autenticação foi atendida e que o cookie ainda é válido (conforme necessário). Ele coloca uma solicitação na fila apropriada para que um conector local trate dela. 

 >[!NOTE]
 >Todas as solicitações do conector são emitidas para o serviço de Proxy de Aplicativo. Os conectores mantêm uma conexão de saída aberta com o Proxy de Aplicativo. Quando uma solicitação chega, o Proxy de Aplicativo enfileira a solicitação em uma das conexões abertas para escolha do conector.
 >

 * A solicitação inclui itens do aplicativo, como os cabeçalhos da solicitação e os dados do cookie criptografado, o usuário que faz a solicitação e a ID de solicitação.  Mas, o cookie de autenticação criptografado não é enviado ao conector.
3. O conector recebe a solicitação da fila, com base em uma conexão de saída de vida longa. O Proxy de Aplicativo tomará uma das seguintes medidas com base na solicitação:
 * O conector confirma se ele pode identificar o aplicativo.  Se não puder, o conector estabelece uma conexão com o serviço de Proxy de Aplicativo para coletar detalhes sobre o aplicativo, e os armazena em cache localmente.
 * Se a solicitação for uma operação simples, como não haver dados no corpo como acontece com uma solicitação "GET" RESTful, o conector fará uma conexão com o recurso interno de destino e aguardará uma resposta.
 * Se a solicitação tiver dados associados a ela no corpo, por exemplo, uma operação "POST" RESTful, o conector fará uma conexão de saída usando o certificado de cliente com a instância do Proxy de Aplicativo. Ele faz isso para solicitar os dados e abrir uma conexão com o recurso interno.  No recebimento da solicitação do conector, o serviço de Proxy de Aplicativo começa aceitando o conteúdo do usuário e o encaminha ao conector.  O conector, por sua vez, encaminha os dados para o recurso interno.
4. Depois de concluída a solicitação/transmissão de todo o conteúdo ao back-end, o conector espera uma resposta.
5. Assim que uma resposta é recebida, o conector faz uma conexão de saída com o serviço de Proxy de Aplicativo para retornar os detalhes do cabeçalho e iniciar a transmissão dos dados de retorno.
6. O Proxy de Aplicativo "transmite" os dados ao usuário.  Parte do processamento dos cabeçalhos pode ocorrer aqui, conforme a necessidade e definição do aplicativo.

Se precisar de ajuda na comunicação de um aplicativo Web do Azure por meio de um navegador cliente com um ponto de extremidade SOAP local autenticado pelo Windows, confira este [Blog de anotações em campo do Azure](http://www.azurefieldnotes.com/2016/12/02/claims-to-windows-identity-translation-solutions-and-its-flaws-when-using-azure-ad-application-proxy). 

##<a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)




<!--HONumber=Feb17_HO2-->


