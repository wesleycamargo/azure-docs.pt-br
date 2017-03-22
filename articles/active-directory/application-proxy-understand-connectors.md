---
title: "Noções básicas sobre conectores do Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
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
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 41d6f678dba769cf7f949751da8cacf3df7f88c1
ms.lasthandoff: 03/03/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD

Este artigo aborda os conectores, que são o ingrediente secreto do Proxy de Aplicativo do Azure AD. Eles são simples, fáceis de implantar e manter, além de superpotentes.

> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

## <a name="what-are-azure-ad-application-proxy-connectors"></a>O que são os conectores do Proxy de Aplicativo do Azure AD?
O Proxy de Aplicativo funciona depois que você instala o serviço do Windows Server, chamado de conector, em sua rede. Você pode instalar os conectores com base em suas necessidades de escalabilidade e alta disponibilidade. Comece com um e adicione mais conforme a necessidade. Toda vez que um conector é instalado, ele é adicionado ao pool de conectores que atende ao seu locatário.

É recomendável não instalar os conectores nos próprios servidores de aplicativos; embora isso seja possível, especialmente para implantações pequenas.

Não é necessário excluir conectores que você não está usando mais. Quando um conector está em execução, ele permanece ativo, pois se conecta ao serviço. Os conectores que não estão sendo usados são marcados como _inativos_ e serão removidos depois de 10 dias de inatividade. 

Para saber mais sobre como resolver problemas de conectividade do Azure AD, confira [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Como solucionar problemas de conectividade do Proxy de Aplicativo do Azure AD). 

## <a name="what-are-the-cloud-rules-for-connectors"></a>Quais são as regras de nuvem para conectores?
Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chegar, ela será roteada para um dos conectores que estiver disponível no momento. Se um conector estiver temporariamente indisponível, ele não responderá a esse tráfego.

Os conectores não têm estado e nenhum dado de configuração no computador, a não ser a conectividade com as configurações do serviço e o certificado que autentica esse conector. Quando se conectam ao serviço, eles extraem todos os dados de configuração necessários e os atualizam a cada dois minutos.
Eles também sondam o servidor para saber se há uma versão mais recente do conector. Se for encontrada, os próprios conectores se atualizam.

É possível monitorar os conectores no computador em que eles estão em execução, usando o log de eventos e os contadores de desempenho, ou na nuvem, usando a página Status do Conector, conforme mostrado abaixo.

 ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Toda a rede é de saída
Os conectores enviam somente solicitações de saída. Portanto, a conexão sempre é iniciada pelo(s) conector(es). Não é necessário abrir portas de entrada, pois assim que uma sessão for estabelecida, o tráfego fluirá de ambos os lados.

O tráfego de saída é enviado ao serviço de Proxy de Aplicativo e aos aplicativos publicados. O tráfego para o serviço é enviado aos datacenters do Azure para vários números de porta diferentes. Para obter mais informações, consulte [Habilitar o Proxy de Aplicativo no portal do Azure](active-directory-application-proxy-enable.md).

Como consequência de ter apenas o tráfego de saída, não há a necessidade de configurar o balanceamento de carga entre os conectores nem de configurar o acesso de entrada por meio dos firewalls.

Para saber mais sobre como configurar regras de firewall de saída, confira [Work with existing on-premise Proxy servers](application-proxy-working-with-proxy-servers.md) (Trabalhar com servidores proxy locais existentes).

Use a [Ferramenta de Teste de Portas do Conector de Proxy de Aplicativo do Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar se o conector pode alcançar o serviço Proxy de Aplicativo. No mínimo, verifique se a região EUA Central e a região mais próxima de você tem todas as marcas de seleção verdes. Além disso, um número maior de marcas de seleção verdes significa maior resiliência. 

## <a name="network-security"></a>Segurança de rede

Os conectores podem ser instalados em qualquer lugar na rede que os permita enviar solicitações ao serviço e aos aplicativos de back-end. Eles funcionarão bem se você instalá-los dentro da rede corporativa, em uma DMZ (zona desmilitarizada) ou mesmo na máquina virtual que está em execução na nuvem que tem acesso aos aplicativos.

As implantações de DMZ geralmente são mais complicadas. Porém, um dos motivos para implantar conectores na DMZ é usar outra infraestrutura que esteja disponível para componentes em execução nela, por exemplo, balanceadores de carga do aplicativo de back-end e/ou controles de segurança, como sistemas de detecção de intrusão.

## <a name="domain-joining"></a>Ingresso no domínio

Os conectores podem ser executados em um computador que não foi adicionado ao domínio. No entanto, um computador adicionado ao domínio será necessário se você optar por usar o SSO (logon único) para aplicativos que usam IWA (Autenticação Integrada do Windows). 

Nesse caso, os computadores do conector devem ser adicionados a um domínio que possa executar a Delegação Restrita de [Kerberos](https://web.mit.edu/kerberos) em nome dos usuários relevantes para os aplicativos publicados.

Os conectores também podem ser adicionados a domínios ou florestas que tenham uma relação de confiança parcial, ou a RODC (controladores de domínio somente leitura).

## <a name="connectors-on-hardened-environments"></a>Conectores em ambientes protegidos

Na maioria dos casos, a implantação do conector é bastante direta e não requer nenhuma configuração especial. Mas, há várias condições exclusivas que devem ser levadas em consideração:

* As organizações que limitam o tráfego de saída devem seguir as instruções descritas aqui para abrirem as portas necessárias.
* Os computadores em conformidade com o padrão FIPS podem ter que alterar sua configuração para permitir o serviço do conector, o serviço de atualizador do conector e seu instalador para gerar e armazenar um certificado.
* As organizações que bloqueiam o ambiente com base nos processos que emitem as solicitações de rede têm que ter certeza de que os serviços de conector estão habilitados para acessar todas as portas e IPs necessários.
* Em alguns casos, os proxies de encaminhamento de saída podem romper a autenticação de certificado e causar falha na comunicação.

## <a name="all-connectors-are-created-almost-equal"></a>Todos os conectores são criados quase iguais

É pressuposto que todos os conectores sejam idênticos e que cada solicitação de entrada possa chegar em cada um dos conectores. Isso significa que todos eles devem ter a mesma conectividade de rede e configurações [Kerberos](https://web.mit.edu/kerberos).

Toda comunicação entre conector e serviço é protegida por um certificado de cliente que é emitido e depois instalado no computador do conector. Para saber mais sobre como renovar certificados de conector, confira [Habilitar o Proxy de Aplicativo no Portal do Azure](active-directory-application-proxy-enable.md).

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, os conectores precisam fazer a autenticação no serviço, e o serviço tem que fazer a autenticação no conector. Isso é feito usando certificados de cliente e servidor quando os conectores iniciam a conexão. Dessa forma, o nome de usuário e a senha do administrador não são armazenados no computador do conector.

Os certificados usados são específicos ao serviço de Proxy de Aplicativo. Eles são criados durante o registro inicial e renovados automaticamente pelos conectores a cada dois meses. 

Se um conector não estiver conectado ao serviço por um período de vários meses, ele poderá ter certificados desatualizados. Nesse caso, o registro é obrigatório, de modo que você precisa desinstalar e reinstalar o conector para disparar o registro. É possível executar os seguintes comandos do PowerShell:

```
* Import-module AppProxyPSModule
* Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Desempenho e escala

Mesmo que a escala para o serviço online seja transparente, ela é um fator quando se trata de conectores. Você precisa ter conectores suficientes para tratar do pico de tráfego. Uma vez que os conectores não têm estado, eles não dependem do número de usuários ou sessões. Em vez disso, eles dependem do número de solicitações e do respectivo tamanho do conteúdo. Para o tráfego padrão da Web, temos visto que um computador lida com uma média de duas mil solicitações por segundo. Isso depende das características exatas do computador.

O desempenho do conector está vinculado à CPU e rede. O desempenho da CPU é necessário para a criptografia e descriptografia SSL, enquanto a rede é importante para obter conectividade rápida com os aplicativos e com o serviço online no Azure. Por outro lado, a memória é uma questão menos significativa para os conectores.

Para o serviço de conector, fazemos um esforço para descarregar os conectores o máximo possível. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem.

Outro fator sobre o desempenho é a qualidade da rede entre os conectores, incluindo:

* _O serviço online:_ se a conexão estiver lenta ou tiver alta latência, ela influenciará o nível de serviço. É melhor se sua organização estiver conectada ao Azure por meio da Rota Expressa. Caso contrário, verifique se a equipe de rede garante que as conexões com o Azure são tratadas de maneira eficiente.

* _Os aplicativos de back-end:_ em alguns casos, há outros proxies entre o conector e os aplicativos de back-end. É fácil solucionar esse problema abrindo um navegador no computador do conector e acessando esses aplicativos. Se você executar os conectores no Azure, e os aplicativos forem locais, a experiência pode não ser como esperada pelos usuários.
* _Os controladores de domínio:_ se os conectores estiverem executando o SSO usando a KCD (Delegação Restrita de Kerberos), eles contatarão os controladores de domínio antes de enviarem a solicitação ao back-end. Os conectores têm um cache de tíquetes Kerberos, mas em ambientes ocupados, a capacidade de resposta dos controladores de domínio pode retardar a experiência. Isso é mais comum para conectores que são executados no Azure, enquanto os controladores de domínio são locais.

## <a name="automatic-updates-to-the-connector"></a>Atualizações automáticas para o conector

Com o serviço de Atualizador do Conector, oferecemos uma maneira automatizada de manter-se atualizado. Dessa forma, você tem a vantagem contínua de todos os recursos novos, bem como de aprimoramentos de segurança e desempenho.

O Azure AD dá suporte a atualizações automáticas para todos conectores que você implanta. Desde que o serviço de Atualizador do Conector do Proxy de Aplicativo seja executado, os conectores serão atualizados automaticamente sem tempo de inatividade nem necessidade de etapas manuais. Caso você não veja o serviço de Atualizador de Conector em seu servidor, será preciso reinstalar o conector para obter as atualizações. Se quiser aprender mais sobre como instalar conectores, confira a [documentação de configuração](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-enable.md).

### <a name="updater-impact"></a>Impacto do Atualizador

_Locatários com um conector:_ se você tiver apenas um conector, esse conector será atualizado como parte do grupo mais recente. Como não há outro conector para reencaminhar o tráfego, o serviço estará indisponível durante a atualização. Para evitar esse tempo de inatividade e garantir alta disponibilidade mais facilmente, é recomendável instalar um segundo conector e criar um grupo de conectores. Para obter detalhes sobre como fazer isso, confira a [documentação sobre grupos de conectores](https://azure.microsoft.com/en-us/documentation/articlesactive-directory-application-proxy-connectors.md).

_Outros locatários:_ durante a atualização do conector, o tráfego é reencaminhado para outros conectores para interrupção mínima. No entanto, quando a atualização começa, todas as transações em andamento podem ser descartadas. O navegador deve recuperar a operação automaticamente, tornando esse possível descarte transparente para você. Caso contrário, convém atualizar sua página para solucionar esse problema.

Sabemos que até mesmo um minuto de tempo de inatividade pode ser difícil, mas essas atualizações nos permitem fornecer um conector ainda melhor com inúmeros aprimoramentos, que acreditamos que valem a pena.

Se quiser obter mais detalhes sobre as alterações em nossa atualização recente do Conector, confira a [atualização mais recente](https://azure.microsoft.com/en-us/updates/app-proxy-connector-12sept2016). Revisamos essa página em cada atualização.

## <a name="under-the-hood"></a>Nos bastidores

Fornecemos muitas ferramentas úteis para você no Azure. Os conectores, especialmente, têm muitas funcionalidades úteis. Como os conectores são baseados no Proxy de Aplicativo Web do Windows Server, grande parte das ferramentas de gerenciamento são iguais; incluindo o rico conjunto de Logs de Eventos do Windows e os Contadores de desempenho do Windows, conforme mostrado abaixo no Visualizador de Eventos:

 ![Visualizador de Eventos do Azure AD](./media/application-proxy-understand-connectors/event-view-window.png)

E o Monitor de Desempenho:

 ![Monitor de Desempenho do Azure AD](./media/application-proxy-understand-connectors/performance-monitor.png)

Os conectores têm logs de administrador e sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. 

Para vê-los, você precisa habilitar "Mostrar logs analíticos e de depuração" no menu "Exibir" do Visualizador de Eventos. Em seguida, você precisa habilitá-los para iniciar a coleta de eventos. Esses logs não aparecem no Proxy de Aplicativo Web no Windows Server 2012 R2, pois os conectores são baseados em uma versão mais recente.

 ![Sessão do Visualizador de Eventos do Azure AD](./media/application-proxy-understand-connectors/event-view-window-session.png)

Você pode examinar o estado do serviço na janela Serviços. O conector é composto por dois Serviços do Windows; um é o conector real e o outro é o que cuida da atualização. Ambos precisam ser executados o tempo todo.

 ![Local dos Serviços do Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

Para saber mais sobre como resolver erros do Conector do Proxy de Aplicativo, confira [Solucionar problemas de Proxy de Aplicativo](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Próximas etapas
[Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md)

[Como instalar silenciosamente o Conector de Proxy de Aplicativo do Azure AD ](active-directory-application-proxy-silent-installation.md)


