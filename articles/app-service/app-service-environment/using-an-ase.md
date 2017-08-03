---
title: "Usando um Ambiente do Serviço de Aplicativo do Azure"
description: "Como criar, publicar e dimensionar aplicativos em um Ambiente do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>Usando um Ambiente do Serviço de Aplicativo #

## <a name="overview"></a>Visão geral ##

Um ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da VNet (Rede Virtual) do Azure de um cliente. Ele consiste em:

- Front-Ends – é neles em que o HTTP/HTTPS termina em um ASE
- Funções de Trabalho – são os recursos que hospedam os aplicativos
- Banco de Dados – contém informações que definem o ambiente
- Armazenamento – o armazenamento é usado para hospedar os aplicativos publicados pelo cliente

> [!NOTE]
> Há duas versões do Ambiente do Serviço de Aplicativo: ASEv1 e ASEv2. No ASEv1, é necessário gerenciar os recursos antes de usá-los. Para saber como configurar e gerenciar um ASEv1, consulte [Configurar um Ambiente do Serviço de Aplicativo v1][ConfigureASEv1]. O restante deste documento tem como foco o ASEv2.
>
>

Implante um ASE (ASEv1 e ASEv2) com um VIP externo para acesso ao aplicativo ou um VIP interno para o acesso ao aplicativo. A implantação com um VIP externo é geralmente chamada de ASE Externo e a versão interna é chamada de ASE ILB, porque usa um ILB (Balanceador de Carga Interno). Para saber mais sobre o ASE ILB, consulte [Criando e usando um ASE ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Criar um aplicativo Web em um ASE ##

Criar um aplicativo Web em um ASE é o mesmo processo que criá-lo com o método normal, com apenas algumas pequenas diferenças. Ao criar um novo plano do Serviço de Aplicativo:

- Em vez de escolher uma localização geográfica para implantar o aplicativo, escolha um ASE como a localização
- Todos os planos do Serviço de Aplicativo criados em um ASE devem estar em um tipo de preço Isolado.

Se você não tiver um ASE, crie um seguindo as instruções em [Criar um Ambiente do Serviço de Aplicativo][MakeExternalASE].

Para criar um aplicativo Web em um ASE:

1. Clique em **Novo &gt; Web e Móvel** e selecione **Aplicativo Web**.
2. Forneça um nome para o aplicativo Web. Se você já selecionou um plano do Serviço de Aplicativo em um ASE, o nome de domínio do aplicativo refletirá o nome de domínio do ASE.

    ![][1]

1. Selecione uma assinatura.
2. Forneça um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.
3. Selecione um plano do Serviço de Aplicativo existente no ASE ou crie um novo com as seguintes etapas:
    1. Selecione **Criar Novo**.
    2. Forneça o nome do plano do Serviço de Aplicativo.
    3. Selecione o ASE na lista suspensa **Localização**.
    4. Selecione o tipo de preço **Isolado**. Clique em **Selecionar**.
    5. Clique em **OK**.
    
    ![][2]
1. Clique em **Criar**.

## <a name="how-scale-works"></a>Como funciona a escala ##

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. O modelo de contêiner é: os ambientes mantêm os planos do Serviço de Aplicativo e os planos do Serviço de Aplicativo mantêm os aplicativos. Ao dimensionar um aplicativo, você dimensiona o plano do Serviço de Aplicativo e, portanto, dimensiona todos os aplicativos do mesmo plano.

Em um ASEv2, quando você dimensiona um plano do Serviço de Aplicativo, a infraestrutura necessária é adicionada automaticamente. Isso é diferente do ASEv1, no qual a infraestrutura necessária precisa ser adicionada antes de criar ou escalar horizontalmente o plano do Serviço de Aplicativo. No ASEv2, isso significa que há um atraso de tempo nas operações de escala, conforme a infraestrutura é adicionada.

No Serviço de Aplicativo multilocatário, geralmente, o dimensionamento é imediato, porque há um pool de recursos imediatamente disponível que pode ser usado para dar suporte à expansão. Em um ASE, não há nenhum buffer desse tipo e os recursos são alocados conforme a necessidade.

Em um ASE, é possível escalar verticalmente até 100 instâncias. Essas 100 instâncias podem estar todas em um único plano do Serviço de Aplicativo ou ser distribuídas entre vários planos do Serviço de Aplicativo.

## <a name="ip-addresses"></a>Endereços IP ##

O Serviço de Aplicativo tem a capacidade de alocar um endereço IP dedicado a um aplicativo. Essa funcionalidade está disponível ao configurar um SSL baseado em IP, conforme descrito aqui: [Associar um certificado SSL personalizado existente aos Aplicativos Web do Azure][ConfigureSSL]. No entanto, em um ASE, há uma exceção notável: não é possível adicionar endereços IP adicionais a serem usados para o SSL baseado em IP em um ASE ILB.

No ASEv1, você precisa alocar os endereços IP como recursos antes de poder usá-los. No ASEv2, basta usá-lo no aplicativo como usaria no Serviço de Aplicativo multilocatário. Sempre há um endereço extra em um ASEv2, com até 30 endereços IP. Sempre que você usar um, outro será adicionado, de forma que sempre haja um endereço imediatamente disponível para uso. Há um atraso de tempo necessário para alocar outro endereço IP, o que impede a adição de endereços IP em sucessão rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

Em um ASEv2, ao escalar horizontalmente os planos do Serviço de Aplicativo, as funções de trabalho são adicionadas automaticamente para dar suporte a eles. Além dos dois Front-Ends com os quais cada ASE é criado, os Front-Ends são automaticamente escalados horizontalmente em uma taxa de um Front-End para cada 15 instâncias no plano do Serviço de Aplicativo. Isso significa que se você tiver 15 instâncias, terá três Front-Ends. Se você dimensionar para 30 instâncias, terá quatro Front-Ends e assim por diante.

Isso deve ser mais do que suficiente para a maioria dos cenários, mas se houver uma necessidade de escalar horizontalmente a uma taxa mais rápida, é possível alterar a taxa para o mínimo, com um Front-End para cada 5 instâncias no plano do Serviço de Aplicativo. Há um encargo para a alteração da taxa, conforme descrito em [Preços do Serviço de Aplicativo do Azure][Pricing].

Os recursos do Front-End são o ponto de extremidade HTTP/HTTPS do ASE. Com a configuração de front-end padrão, o uso de memória por Front-End é consistentemente cerca de 60%. Cargas de trabalho de cliente não são executadas em um Front-End. O fator chave para um Front-End com relação à escala é a CPU, que é controlada principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso ao aplicativo ##

Em um ASE Externo, o domínio usado durante a criação de aplicativos é diferente do Serviço de Aplicativo multilocatário e inclui o nome do ASE. Para obter mais informações sobre como criar um ASE Externo, consulte [Criando um Ambiente do Serviço de Aplicativo][MakeExternalASE]. O nome de domínio em um ASE Externo é semelhante a *.&lt;asename&gt;.p.azurewebsites.net*. Isso significa que, se o ASE for chamado _external-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o acessaria nas seguintes URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

A URL *contoso.scm.external-ase.p.azurewebsites.net* é usada para acessar o console do Kudu ou para publicar o aplicativo usando a implantação da Web. Para obter informações sobre o console do Kudu, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

Em um ASE ILB, você determina o domínio no momento da implantação. Para obter mais informações sobre como criar um ASE ILB, consulte [Criar e usar um ASE ILB][MakeILBASE]. Se você especificar o nome de domínio _ilb-ase.info_, os aplicativos nesse ASE usarão esse domínio durante a criação do aplicativo. Para o aplicativo chamado _contoso_, as URLs serão:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publicação ##

Assim como ocorre com o Serviço de Aplicativo multilocatário, em um ASE, é possível publicar com:

- Implantação da Web
- FTP
- Integração contínua
- Operação de arrastar e soltar no console do Kudu
- Um IDE como o Visual Studio, Eclipse ou Intellij IDEA

Com um ASE Externo, tudo isso tem o mesmo comportamento. Para obter informações, consulte [Implantação no Serviço de Aplicativo do Azure][AppDeploy]. 

A grande diferença na publicação é em relação a um ASE ILB. Com um ASE ILB, os pontos de extremidade de publicação estão disponíveis apenas por meio do ILB. O ILB está em um IP privado na sub-rede do ASE, na Rede Virtual. Se você não tiver acesso à rede ao ILB, não poderá publicar nenhum aplicativo nesse ASE. Conforme observado em [Criar e usar um ASE ILB][MakeILBASE], você precisa configurar o DNS para os aplicativos no sistema. Isso inclui o ponto de extremidade SCM. Se eles não forem definidos corretamente, você não conseguirá fazer a publicação. Os IDEs também precisam ter acesso à rede ao ILB para publicar diretamente nele.

Sistemas de CI baseados na Internet, como GitHub e VSTS, não funcionam com um ASE ILB, pois o ponto de extremidade de publicação não é acessível pela Internet. Em vez disso, você precisa usar um sistema de CI que usa um modelo pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Isso pode ser visto no perfil de publicação do aplicativo e na folha do portal do aplicativo (em **Visão Geral** > **Informações Básicas** e também em **Propriedades**). 

## <a name="pricing"></a>Preços ##

Com o ASEv2, há um novo SKU de preços que é usado somente com o ASEv2 chamado **Isolado**. Todos os planos do Serviço de Aplicativo que são hospedados em um ASEv2 estão no SKU de preços Isolado. Além do preço dos planos do Serviço de Aplicativo, há um valor fixo para o ASE em si. Esse preço não é alterado com o tamanho do ASE. 

Os outros valores potenciais são referentes ao ajuste da taxa de escala ou do tamanho do Front-End. Se você ajustar a taxa de escala para que os Front-Ends sejam adicionados mais rapidamente, você pagará por todos os núcleos adicionais que não seriam adicionados automaticamente ao sistema. Da mesma forma, se você selecionar um tamanho maior para os Front-Ends, pagará por todos os núcleos que não estavam sendo alocados automaticamente. Por exemplo, se você ajustar a taxa de escala para 10, isso significará que um Front-End é adicionado para cada 10 instâncias nos planos do Serviço de Aplicativo. O valor fixo abrange uma taxa de escala de 1 Front-End para cada 15 instâncias. Com a taxa de escala 10, você pagará uma taxa pelo terceiro Front-End que é adicionado às 10 instâncias do ASP, mas não precisará pagar por ele quando chegar a 15 instâncias, pois ele teria sido adicionado automaticamente.

Para obter mais informações, consulte [Preços do Serviço de Aplicativo do Azure][Pricing].

## <a name="deleting-an-ase"></a>Excluindo um ASE ##

Se desejar excluir um Ambiente do Serviço de Aplicativo, basta usar a ação **Excluir** na parte superior da folha Ambiente do Serviço de Aplicativo. Quando você fizer isso, será solicitada a inserção do nome do seu Ambiente do Serviço de Aplicativo para confirmar a operação. Observe que quando você exclui um Ambiente do Serviço de Aplicativo, exclui todo o conteúdo dentro dele também. 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

