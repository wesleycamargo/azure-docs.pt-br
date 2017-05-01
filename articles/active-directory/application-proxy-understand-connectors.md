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
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5780c56a05ce1c40500927dec9df7906b02a1d13
ms.lasthandoff: 04/07/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD

Os conectores são o que torna o Proxy de Aplicativo do Azure AD possível. Eles são simples, fáceis de implantar e manter, além de superpotentes. Este artigo aborda o que são conectores, como eles funcionam, além de algumas das melhores práticas para aproveitar ao máximo sua implantação. 

## <a name="connector-deployment"></a>Implantação do conector

O Proxy de Aplicativo funciona depois que você instala o serviço do Windows Server, chamado de conector, em sua rede. Você pode instalar vários conectores de acordo com suas necessidades de escalabilidade e alta disponibilidade. Comece com um e adicione mais conforme a necessidade. Toda vez que um conector é instalado, ele é adicionado ao pool de conectores que atende ao seu locatário.

Recomendamos que você não instale os conectores nos mesmos servidores que hospedam os aplicativos.

Não é necessário excluir manualmente os conectores que não foram utilizados. Quando um conector está em execução, ele permanece ativo, pois se conecta ao serviço. Os conectores que não estão sendo usados são marcados como _inativos_ e serão removidos depois de 10 dias de inatividade. 

## <a name="connector-maintenance"></a>Manutenção do conector
Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Eles podem ser adicionados ou removidos dinamicamente. Sempre que uma nova solicitação chega, ela é roteada para um dos conectores que está disponível no momento. Se um conector estiver temporariamente indisponível, ele não responderá a esse tráfego.

Os conectores não têm estado e nenhum dado de configuração no computador, a não ser a conectividade com as configurações do serviço e o certificado que autentica esse conector. Quando se conectam ao serviço, eles extraem todos os dados de configuração necessários e os atualizam a cada dois minutos.
Eles também sondam o servidor para saber se há uma versão mais recente do conector. Se for encontrada, os próprios conectores se atualizam.

É possível monitorar os conectores no computador em que eles estão em execução, usando o log de eventos e os contadores de desempenho ou a página Proxy de Aplicativo do portal do Azure.

 ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Toda a rede é de saída
Os conectores enviam somente solicitações de saída. Portanto, a conexão sempre é iniciada pelo(s) conector(es). Não é necessário abrir portas de entrada, pois o tráfego fluirá de ambos os lados assim que uma sessão for estabelecida.

O tráfego de saída é enviado ao serviço de Proxy de Aplicativo e aos aplicativos publicados. O tráfego para o serviço é enviado aos datacenters do Azure para vários números de porta diferentes. Para obter mais informações sobre quais portas são usadas, consulte [Habilitar o Proxy de Aplicativo no portal do Azure](active-directory-application-proxy-enable.md).

Como consequência de ter apenas o tráfego de saída, não há necessidade de configurar o balanceamento de carga entre os conectores nem de configurar o acesso de entrada por meio dos firewalls.

Para saber mais sobre como configurar regras de firewall de saída, confira [Work with existing on-premise Proxy servers](application-proxy-working-with-proxy-servers.md) (Trabalhar com servidores proxy locais existentes).

Use a [Ferramenta de Teste de Portas do Conector de Proxy de Aplicativo do Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar se o conector pode alcançar o serviço Proxy de Aplicativo. No mínimo, verifique se a região EUA Central e a região mais próxima de você tem todas as marcas de seleção verdes. Além disso, um número maior de marcas de seleção verdes significa maior resiliência. 

## <a name="network-security"></a>Segurança de rede

Os conectores podem ser instalados em qualquer lugar na rede que os permita enviar solicitações ao serviço e aos aplicativos de back-end. Eles funcionarão bem se você instalá-los dentro da rede corporativa, dentro de uma DMZ (zona desmilitarizada) ou até mesmo em uma máquina virtual. O importante é que o computador que executa o conector também tenha acesso aos aplicativos.

As implantações em uma DMZ são mais complicadas. No entanto, um motivo para uma possível implantação de conectores em uma DMZ é o uso de outra infraestrutura como balanceadores de carga do aplicativo de back-end ou sistemas de detecção de intrusões.

## <a name="domain-joining"></a>Ingresso no domínio

Os conectores podem ser executados em um computador que não foi ingressado no domínio. Entretanto, se você desejar efetuar SSO (logon único) em aplicativos que usam a IWA (Autenticação Integrada do Windows), precisará de um computador ingressado no domínio. Nesse caso, os computadores do conector devem ser adicionados a um domínio que possa executar a Delegação Restrita de [Kerberos](https://web.mit.edu/kerberos) em nome dos usuários relevantes para os aplicativos publicados.

Os conectores também podem ser ingressados em domínios ou florestas que têm uma relação de confiança parcial ou em controladores de domínio somente leitura.

## <a name="connectors-on-hardened-environments"></a>Conectores em ambientes protegidos

Normalmente, a implantação do conector é simples e não exige nenhuma configuração especial. No entanto, há algumas condições exclusivas que devem ser consideradas:

* As organizações que limitam o tráfego de saída devem [abrir as portas necessárias](active-directory-application-proxy-enable.md#open-your-ports).
* Os computadores em conformidade com FIPS podem precisar alterar sua configuração para permitir o serviço do conector, o serviço de atualizador do conector e seu instalador para gerar e armazenar um certificado nesse computador.
* As organizações que bloqueiam o ambiente de acordo com os processos que emitem as solicitações de rede precisam ter certeza de que os serviços do conector estão habilitados para acessar todas as portas e IPs necessários.
* Em alguns casos, os proxies de encaminhamento de saída podem interromper a autenticação de certificado bidirecional e causar falha na comunicação.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, os conectores devem fazer a autenticação no serviço e o serviço deve fazer a autenticação no conector. Isso é feito usando certificados de cliente e servidor quando os conectores iniciam a conexão. Dessa forma, o nome de usuário e a senha do administrador não são armazenados no computador do conector.

Os certificados usados são específicos ao serviço de Proxy de Aplicativo. Eles são criados durante o registro inicial e renovados automaticamente pelos conectores no intervalo de alguns meses. 

Se um conector não estiver conectado ao serviço por vários meses, seus certificados poderão estar desatualizados. Nesse caso, desinstale e reinstale o conector para disparar o registro. É possível executar os seguintes comandos do PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Desempenho e escala

Mesmo que a escala do serviço online seja transparente, ela é um fator para os conectores. Você precisa ter conectores suficientes para tratar do pico de tráfego. Como os conectores não têm estado, eles não são afetados pelo número de usuários ou sessões. Em vez disso, eles respondem ao número de solicitações e a seu tamanho do conteúdo. Com o tráfego padrão da Web, um computador médio pode manipular milhares de solicitações por segundo. A capacidade específica depende das características exatas do computador.

O desempenho do conector está vinculado à CPU e rede. O desempenho da CPU é necessário para a criptografia e descriptografia SSL, enquanto a rede é importante para obter conectividade rápida com os aplicativos e com o serviço online no Azure. 

Por outro lado, a memória é uma questão menos significativa para os conectores. O serviço online cuida de grande parte do processamento e de todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é feito na nuvem.

Outro fator que afeta o desempenho é a qualidade da rede entre os conectores, incluindo:

* **O serviço online:** conexões lentas ou de alta latência influenciam o serviço do conector. É melhor se sua organização estiver conectada ao Azure por meio da Rota Expressa. Caso contrário, faça com que a equipe de rede garanta que as conexões com o Azure são tratadas de maneira eficiente.  
* **Os aplicativos de back-end:** em alguns casos, há outros proxies entre o conector e os aplicativos de back-end. Solucione o problema desse cenário abrindo um navegador no computador do conector e acessando esses aplicativos. Se você executar os conectores no Azure e os aplicativos forem locais, a experiência poderá não ser como esperada pelos usuários.
* **Os controladores de domínio:** se os conectores estiverem executando o SSO usando a KCD (Delegação Restrita de Kerberos), eles contatarão os controladores de domínio antes de enviarem a solicitação ao back-end. Os conectores têm um cache de tíquetes Kerberos, mas em um ambiente ocupado, a capacidade de resposta dos controladores de domínio pode afetar a experiência. Esse problema é mais comum em conectores executados no Azure, enquanto os controladores de domínio são locais.

## <a name="automatic-updates-to-the-connector"></a>Atualizações automáticas para o conector

Com o serviço de Atualizador do Conector, oferecemos uma maneira automatizada de manter-se atualizado. Dessa forma, você tem a vantagem contínua de todos os recursos novos, bem como de aprimoramentos de segurança e desempenho.

O Azure AD dá suporte a atualizações automáticas para todos conectores que você implanta. Desde que o serviço Atualizador do Conector de Proxy de Aplicativo esteja em execução, os conectores são atualizados automaticamente. Caso você não veja o serviço Atualizador do Conector no servidor, precisará [reinstalar o conector](active-directory-application-proxy-enable.md) para obter todas as atualizações.

Poderá ocorrer tempo de inatividade quando o conector for atualizado se:

- Você só tiver um conector. Como não há nenhum outro conector para redirecionar o tráfego, o serviço ficará indisponível durante a atualização. Para evitar esse tempo de inatividade e melhorar a alta disponibilidade, recomendamos instalar um segundo conector e [criar um grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).

- Um conector estava no meio de uma transação quando a atualização foi iniciada. O navegador deverá repetir a operação automaticamente ou você poderá atualizar a página. Quando a solicitação é enviada novamente, o tráfego é direcionado para um conector de backup.

## <a name="under-the-hood"></a>Nos bastidores

Os conectores baseiam-se no Proxy de Aplicativo Web do Windows Server e, portanto, têm a maioria das mesmas ferramentas de gerenciamento, incluindo os Logs de Eventos do Windows

 ![Gerenciar logs de eventos com o Visualizador de Eventos](./media/application-proxy-understand-connectors/event-view-window.png)

e contadores de desempenho do Windows. 

 ![Adicionar contadores ao conector com o Monitor de Desempenho](./media/application-proxy-understand-connectors/performance-monitor.png)

Os conectores têm logs de administrador e sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. 

Para ver os logs, acesse o Visualizador de Eventos, abra o menu **Exibir** e habilite **Mostrar logs analíticos e de depuração**. Em seguida, habilite-os para iniciar a coleta de eventos. Esses logs não aparecem no Proxy de Aplicativo Web no Windows Server 2012 R2, pois os conectores são baseados em uma versão mais recente.

Você pode examinar o estado do serviço na janela Serviços. O conector é composto por dois Serviços do Windows: o conector real e o atualizador. Ambos precisam ser executados o tempo todo.

 ![Local dos Serviços do Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Próximas etapas
[Solucionar erros do conector e do Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

[Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md)

[Como fazer uma instalação silenciosa do Conector de Proxy de Aplicativo do Azure AD](active-directory-application-proxy-silent-installation.md)


