---
title: "Considerações de segurança para o Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Aborda considerações de segurança para usar o Proxy de Aplicativo do Azure AD"
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
ms.date: 04/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 52704b3259ddad85db12b8bb81c4992ece2f37a4
ms.lasthandoff: 04/21/2017


---

# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Considerações de segurança para acessar aplicativos remotamente com o Proxy de Aplicativo do Azure AD

Este artigo explica como o Proxy de Aplicativo do Azure AD (Azure Active Directory) fornece um serviço seguro para publicar e acessar aplicativos remotamente.

O diagrama a seguir mostra como o Azure AD permite acesso remoto seguro aos aplicativos locais.

 ![Diagrama de acesso remoto por meio do Proxy de Aplicativo do Azure AD](./media/application-proxy-security-considerations/secure-remote-access.png)

O Proxy de Aplicativo do Azure AD oferece os seguintes benefícios de segurança:

**Acesso autenticado:** somente as conexões autenticadas podem acessar sua rede.

* Proxy de Aplicativo do Azure AD depende do Azure AD serviço de token de segurança (STS) para todas as autenticações.  A pré-autenticação, por sua própria natureza, bloqueia um número significativo de ataques anônimos, pois somente identidades autenticadas podem acessar o aplicativo de back-end.

**Acesso condicional:** aplique controles de política mais rígidos antes que as conexões com sua rede sejam estabelecidas.

* Com [acesso condicional](active-directory-conditional-access-azuread-connected-apps.md), você pode definir restrições de qual tráfego é permitido para acessar seus aplicativos back-end, com base na localização, no nível de autenticação e no perfil de risco do usuário.

**Encerramento de tráfego:** todo o tráfego é encerrado na nuvem.

* Como o Proxy de Aplicativo do Azure AD é um proxy reverso, todo o tráfego para os aplicativos de back-end é encerrado no serviço. A sessão pode obter restabelecida apenas com o servidor de back-end, o que significa que seus servidores back-end não são expostos para o tráfego HTTP direto. Por exemplo, você pode reduzir ataques direcionados com mais facilidade.

**Todo o acesso é de saída:** não é necessário abrir as conexões de entrada para a rede corporativa.

* Conectores de Azure AD mantêm conexões de saída para o serviço de Proxy de Aplicativo do Azure AD, o que significa que não há necessidade de abrir portas de firewall para conexões de entrada. As abordagens tradicionais exigiam uma rede de perímetro (também conhecida como *DMZ*, *zona desmilitarizada*, ou *sub-rede filtrada*) e permitiam acesso a conexões não autenticadas na borda da rede. Esse cenário resultou na necessidade de muitos investimentos adicionais em produtos WAF (firewall de aplicativo Web) para analisar o tráfego e oferecer proteções extras ao ambiente. Com o Proxy de Aplicativo, você não precisa de uma rede de perímetro porque todas as conexões são de saída e ocorrem por um canal seguro.

**Inteligência baseada na linguagem de máquina e análise de segurança:** proteção com segurança de ponta.

* O [Azure AD Identity Protection](active-directory-identityprotection.md) com inteligência baseada em aprendizado de máquina com os dados é alimentado de nossa Unidade de Crimes Digitais e do Microsoft Security Response Center. Juntos, identificamos proativamente contas comprometidas e oferecemos proteção em tempo real em conexões de alto risco. Levamos em consideração vários fatores, como acesso de dispositivos infectados por meio de redes que mantêm o anonimato, bem como de locais atípicos e improváveis.
* Muitos desses relatórios e eventos já estão disponíveis por meio de uma API para integração com as informações de segurança e sistemas de gerenciamento (SIEM) do evento.

**Acesso remoto como um serviço:** você não precisa se preocupar com a manutenção e a aplicação de patches em servidores locais.

* A não aplicação de patches no software ainda é responsável por um grande número de ataques. O Proxy de Aplicativo do Azure AD é um serviço de escala de Internet da Microsoft. Sendo assim, você pode ter a certeza de que sempre obterá os patches e atualizações de segurança mais recentes.
* Para melhorar a segurança dos aplicativos publicados pelo Proxy de Aplicativo do Azure AD, bloquearemos robôs do rastreador da Web de indexação e o arquivamento de seus aplicativos. Cada vez que um robô rastreador da Web tentar recuperar as configurações de robôs para um aplicativo publicado, o Proxy de Aplicativo responderá com um arquivo robots.txt que inclui `User-agent: * Disallow: /`.

## <a name="under-the-hood"></a>Nos bastidores

O Proxy de Aplicativo do Azure AD consiste em duas partes:

* O serviço baseado em nuvem: este serviço é onde as conexões de cliente/usuário externo são feitas.
* O conector de Proxy de Aplicativo do Azure AD: um componente de local, o conector escuta solicitações das conexões de serviço e identificadores de Proxy de Aplicativo do Azure AD para aplicativos internos. 

Um fluxo entre o conector e o serviço de Proxy de Aplicativo é estabelecido quando:

* O conector é configurado pela primeira vez.
* O conector extrai informações de configuração do serviço Proxy de Aplicativo, incluindo o grupo de conectores do qual cada conector é um membro.
* Um usuário acessa um aplicativo publicado.

>[!NOTE]
>Todas as comunicações ocorrem por SSL e sempre originam-se no conector para o serviço de Proxy de Aplicativo. O serviço é apenas de saída.

O conector usa um certificado de cliente para autenticar o serviço de Proxy de Aplicativo para quase todas as chamadas. A única exceção deste processo é a etapa de configuração inicial em que o certificado de cliente é estabelecido.

### <a name="installing-the-connector"></a>Instalação do conector

Quando o conector é configurado pela primeira vez, ocorrem os seguintes eventos de fluxo:

1. O registro do conector para o serviço ocorre como parte da instalação do conector. Atualmente, os usuários são solicitados a digitar suas credenciais de administrador do Azure AD. O token adquirido é então apresentado ao serviço de Proxy de Aplicativo do Azure AD.
2. O Proxy de Aplicativo avalia o token para garantir que o usuário é um membro da função de administrador da empresa no locatário para o qual o token foi emitido. Se o usuário não é um membro da função de administrador, o processo é encerrado.
3. O conector gera uma solicitação de certificado de cliente e a passa com o token para o serviço de Proxy de Aplicativo que, por sua vez, verifica o token e assina a solicitação de certificado de cliente.
4. O conector usa o certificado de cliente para comunicação futura com o serviço de Proxy de Aplicativo.
5. O conector realiza uma extração inicial dos dados de configuração do sistema do serviço usando o respectivo certificado de cliente e está pronto para receber solicitações.

### <a name="updating-the-configuration-settings"></a>Atualização das definições da configuração

Sempre que o serviço de Proxy de Aplicativo atualiza as definições de configuração, ocorrem os seguintes eventos de fluxo:

1. O conector se conecta ao ponto de extremidade de configuração no serviço de Proxy de Aplicativo usando seu certificado de cliente.
2. Depois que o certificado de cliente tiver sido validado, o serviço de Proxy de Aplicativo retorna dados de configuração ao conector (por exemplo, o grupo de conectores do qual esse conector deve fazer parte).
3. Se o certificado atual tiver mais de 30 dias, o conector gerará uma nova solicitação de certificado, que atualiza efetivamente o certificado de cliente a cada 30 dias.

### <a name="accessing-published-applications"></a>Acesso a aplicativos publicados

Quando os usuários acessam um aplicativo publicado, ocorrem estes eventos de fluxo:

#### <a name="the-application-proxy-service-checks-the-configuration-settings-for-the-app"></a>O serviço de Proxy de Aplicativo verifica as definições de configuração para o aplicativo. 

Se o aplicativo estiver configurado para usar a Pré-autenticação com o Azure AD, os usuários são redirecionados para o STS do Azure AD para autenticação. Se você configurar o aplicativo para usar passagem, esta etapa será ignorada.

1. O Proxy de Aplicativo verifica todos os requisitos da política de acesso condicional para o aplicativo específico. Esta etapa garante que o usuário foi atribuído ao aplicativo. Se uma verificação em duas etapas for necessária, a sequência de autenticação solicitará ao usuário um segundo método de autenticação.

2. Depois de realizadas todas as verificações, o STS do Azure AD emite um token assinado para o aplicativo e redireciona o usuário de volta ao serviço de Proxy de Aplicativo.

3. O Proxy de Aplicativo verifica se o token foi emitido para corrigir o aplicativo. Ele também faz outras verificações, como ao garantir que o token foi assinado pelo Azure AD, e que ainda está dentro da janela de validade.

4. O Proxy de Aplicativo define um cookie de autenticação criptografado para indicar que a autenticação para o aplicativo ocorreu. Esse cookie inclui um carimbo de data/hora de expiração com base no token do Azure AD e outros dados, como o nome do usuário na qual se baseia a autenticação. O cookie é criptografado com uma chave privada conhecida apenas pelo serviço de Proxy de Aplicativo.

5. O Proxy de Aplicativo redireciona o usuário de volta para a URL originalmente solicitada.

Se qualquer parte das etapas da pré-autenticação falhar, a solicitação do usuário será negada e o usuário verá uma mensagem indicando a origem do problema.


#### <a name="application-proxy-places-a-request-in-the-appropriate-queue-for-an-on-premises-connector-to-handle"></a>O Proxy de Aplicativo coloca uma solicitação na fila apropriada para ser manipulada por um conector local. 

Todas as solicitações do conector são emitidas para o serviço de Proxy de Aplicativo. Os conectores mantêm uma conexão de saída aberta com o Proxy de Aplicativo. Quando uma solicitação chega, o Proxy de Aplicativo enfileira a solicitação em uma das conexões abertas para escolha do conector.

A solicitação inclui itens do aplicativo, como os cabeçalhos da solicitação, os dados do cookie criptografado, o usuário que faz a solicitação e a ID de solicitação. Entretanto, o cookie de autenticação criptografado não é enviado ao conector.

#### <a name="the-connector-receives-the-request-from-the-queue"></a>O conector recebe a solicitação da fila. 

Com base na solicitação, o Proxy de Aplicativo executa uma das seguintes ações:

* O conector confirma se ele pode identificar o aplicativo. Se ele não é possível identificar o aplicativo, o conector estabelece uma conexão para o serviço de Proxy de Aplicativo para obter detalhes sobre o aplicativo e armazena em cache o aplicativo localmente.

* Se a solicitação for uma operação simples (por exemplo, não há dados no corpo como acontece com uma solicitação *GET* RESTful), o conector fará uma conexão com o recurso interno de destino e aguardará uma resposta.

* Se a solicitação tiver dados associados a ela no corpo, por exemplo, uma operação *POST* RESTful, o conector fará uma conexão de saída usando o certificado de cliente com a instância do Proxy de Aplicativo. Ele faz essa conexão para solicitar os dados e abrir uma conexão com o recurso interno. No recebimento da solicitação do conector, o serviço de Proxy de Aplicativo começa aceitando o conteúdo do usuário e encaminha os dados ao conector. O conector, por sua vez, encaminha os dados para o recurso interno.

#### <a name="after-the-request-and-transmission-of-all-content-to-the-back-end-is-complete-the-connector-waits-for-a-response"></a>Depois de concluída a solicitação/transmissão de todo o conteúdo ao back-end, o conector espera uma resposta.

Depois de receber a resposta, o conector faz uma conexão de saída com o serviço de Proxy de Aplicativo para retornar os detalhes do cabeçalho e iniciar a transmissão dos dados de retorno.

#### <a name="application-proxy-streams-the-data-to-the-user"></a>O Proxy de Aplicativo "transmite" os dados ao usuário. 

Parte do processamento dos cabeçalhos pode ocorrer aqui, conforme a necessidade e definição do aplicativo.


## <a name="next-steps"></a>Próximas etapas

[Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD](application-proxy-network-topology-considerations.md)

[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)

