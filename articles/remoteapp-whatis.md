<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# O que é o RemoteApp do Azure?

O RemoteApp permite com que você faça os programas que são acessados remotamente através do Azure que aparecem se eles estiverem em execução no computador local do usuário final. Esses programas são referidos como programas do RemoteApp. Ao invés de serem apresentados ao usuário na área de trabalho do servidor do Host da Sessão da Área de Trabalho Remota (RD Session Host), os programas do RemoteApp são integrados com a área de trabalho do cliente. O programa RemoteApp executa em sua própria janela ajustável, que pode ser arrastada para vários monitores e tem a sua própria entrada na barra de tarefa. Se um usuário estiver executando mais de um programa RemoteApp no mesmo servidor do RD Session Host, o programa irá compartilhar a mesma sessão de Serviços de Área de Trabalho Remota.

O RemoteApp pode reduzir a sobrecarga da complexidade administrativa em muitas situações, inclusive as seguintes:

-   Filiais, em que pode haver suporte local de TI limitado e largura de banda de rede limitada.
-   Situações em que os usuários precisam acessar os programas remotamente.
-   implantação dos programas de linha de negócios (LOB), especialmente os programas LOB personalizados.
-   Ambientes, como os espaços de trabalho "hot desk" ou “hoteling”, em que os usuários não possuem computadores designados.
-   implantação de várias versões do programa, principalmente se a instalação local de várias versões puder causar conflitos.

Diferente do Serviços de Área de Trabalho Remota tradicional, o RemoteApp do Azure é executado no Portal de Gerenciamento do Azure. É necessário acesso aos programas através do portal, enquanto todo o gerenciamento dos programas e usuários é feito através do portal Admin.

Há dois tipos de implantação do RemoteApp:

-   Uma implantação de nuvem é hospedada nos e armazena todos os dados para programas na nuvem do Azure.
-   Uma implantação híbrida é hospedada na nuvem do Azure, mas permite que os usuários acessem os dados armazenados em sua rede local.

Independente do tipo de implantação escolhida, após a criação do serviço, publique os programas que deseja compartilhar com os seus usuários para os comentários do usuário final. Esta é a lista de programas disponíveis que os seus usuários acessam através do portal do Azure. Esteja ciente de que os comentários mostram todos os programas de todos os serviços associados à sua assinatura.

