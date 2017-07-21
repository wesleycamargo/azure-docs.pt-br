---
title: "Criar e usar um Balanceador de Carga Interno com um Ambiente do Serviço de Aplicativo do Azure"
description: "Detalhes sobre como criar e usar um Ambiente do Serviço de Aplicativo do Azure isolado da Internet"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Criar e usar um Balanceador de Carga Interno com um Ambiente do Serviço de Aplicativo #

O ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da VNet (Rede Virtual) do Azure. Há duas maneiras de implantar um ASE: 

- com um VIP em um endereço IP externo, geralmente chamado de _ASE Externo_.
- com o VIP em um endereço IP interno, geralmente chamado de _ASE ILB_ devido ao ponto de extremidade interno ser um ILB (Balanceador de Carga Interno). 

Este artigo mostra como criar um _ASE ILB_.  Para obter uma visão geral do ASE, comece com [Uma introdução aos Ambientes do Serviço de Aplicativo][Intro] e, se desejar saber como criar um ASE Externo, comece com [Criando um ASE Externo][MakeExternalASE].

## <a name="overview"></a>Visão geral ##

Um ASE pode ser implantado com um ponto de extremidade acessível pela Internet ou com um endereço IP na Rede Virtual. Para definir o endereço IP como um endereço de Rede Virtual, o ASE deve ser implantado com um ILB. Ao implantar o ASE com um ILB, você deverá fornecer:

-   seu próprio domínio com os quais os aplicativos são criados
-   o certificado usado para HTTPS
-   gerenciamento de DNS para o subdomínio

Em troca, você pode fazer coisas como:

-   hospedar aplicativos de intranet com segurança na nuvem, que são acessados por meio de uma VPN Site a Site ou VPN do ExpressRoute
-   hospedar aplicativos na nuvem que não estão listados em servidores de DNS públicos
-   criar aplicativos back-end isolados da Internet, aos quais os aplicativos front-end podem ser integrados com segurança

***Funcionalidade desabilitada***

Há algumas coisas que você não pode fazer ao usar um ASE ILB, incluindo:

-   usar o SSL baseado em IP
-   atribuir IP endereços para aplicativos específicos
-   comprar e usar um certificado com um aplicativo por meio do portal. Você ainda pode obter certificados diretamente com uma Autoridade de Certificação e usá-los com os aplicativos, mas não por meio do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar um ASE ILB ##

Para criar um ILB do ASE:

1.  No portal do Azure, selecione **Novo – &gt; Web + Móvel –&gt; Ambiente do Serviço de Aplicativo**.
2.  Selecione sua assinatura.
3.  Selecione ou crie um grupo de recursos.
4.  Selecione ou crie uma Rede Virtual.
5.  Crie uma sub-rede se estiver selecionando uma Rede Virtual. Lembre-se de definir um tamanho de sub-rede grande o suficiente para acomodar qualquer crescimento futuro do ASE. O tamanho recomendado é um `/25`, que tem 128 endereços e pode manipular um ASE com tamanho máximo. Não é possível usar um `/29` ou um tamanho menor de sub-rede.  As necessidades de infraestrutura consomem, pelo menos, 5 endereços.  Mesmo com um `/28`, você terá um dimensionamento máximo de 11 instâncias em uma sub-rede `/28`. Se você achar que um dia precisará ir além do máximo padrão de 100 instâncias nos planos do Serviço de Aplicativo ou precisará dimensionar cerca de 100, mas com um dimensionamento mais rápido de Front-End, use um /24 com 256 endereços.
6.  Selecione **Rede Virtual/Localização –&gt; Configuração de Rede Virtual** e defina o **Tipo de VIP** como **Interno**.
7.  Forneça o nome de domínio. Esse será o domínio usado para os aplicativos criados nesse ASE. Há algumas restrições. Ele não pode ser:
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    Além disso, se você pretende usar um nome de domínio personalizado para os aplicativos que são hospedados com esse ASE, não pode haver uma sobreposição entre o nome de domínio personalizado e o nome de domínio usado pelo ASE. Para um ASE ILB com o nome de domínio _contoso.com_, não é possível usar nomes de domínio personalizados para os aplicativos semelhantes a:
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    Se você souber os nomes de domínio personalizados que desejará usar com os aplicativos que implantar no ASE ILB, selecione um domínio para o ASE ILB durante a criação do ASE ILB que não terá conflito. Neste exemplo, ele poderá ser algo como _contoso-internal.com_.
8.  Selecione **OK** e, depois, **Criar**.

    ![][1]

Na folha Rede Virtual, há uma opção **Configuração de Rede Virtual**. Isso permite a escolha entre um VIP Externo ou um VIP Interno. O padrão é **Externo**. Se você selecionar **Externo**, o ASE usará um VIP acessível pela Internet. Se você selecionar **Interno**, o ASE será configurado com um ILB em um endereço IP na Rede Virtual.

Depois de selecionar **Interno**, a capacidade de adicionar mais endereços IP ao ASE é removida e, em vez disso, você precisa fornecer o domínio do ASE. Em um ASE com um VIP Externo, o nome do ASE é usado no domínio dos aplicativos criados nesse ASE.

Se você definir **Tipo de VIP** como **Interno**, o nome do ASE não será usado no domínio do ASE. Especifique o domínio explicitamente. Se o domínio for ***contoso.corp.net*** e você criar um aplicativo nesse ASE chamado ***timereporting***, a URL desse aplicativo será ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplicativos em um ILB do ASE ##

A criação de um aplicativo em um ILB do ASE é igual à criação normal de um aplicativo em um ASE.

1.  No portal do Azure, selecione **Novo &gt; Web + Móvel &gt; Web**, **Móvel** ou **Aplicativo de API**.
2.  Insira o nome do aplicativo.
3.  Selecione a assinatura.
4.  Selecione ou crie um grupo de recursos.
5.  Selecione ou crie um plano do Serviço de Aplicativo. Se desejar criar um novo plano do Serviço de Aplicativo, selecione o ASE como a localização e selecione o pool de trabalho no qual você deseja que o plano do Serviço de Aplicativo seja criado. Ao criar o plano do Serviço de Aplicativo, selecione o ASE como a localização e o pool de trabalho. Ao especificar o nome do aplicativo, você verá que o domínio sob o nome do aplicativo é substituído pelo domínio do ASE.
6.  Selecione **Criar**. Você deve marcar a caixa de seleção **Fixar ao painel** se quiser que o aplicativo apareça em seu painel.

    ![][2]

Sob o nome do aplicativo, o nome de domínio é atualizado para refletir o domínio do ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validação posterior à criação do ILB do ASE ##

Um ASE com ILB é ligeiramente diferente do ASE que não tem um ILB. Como já observamos, você precisa gerenciar seu próprio DNS e também precisa fornecer seu próprio certificado para conexões HTTPS.

Após a criação do ASE, você observará que o nome de domínio mostra o domínio especificado e que há um novo item no menu **Configuração** chamado **Certificado ILB**. O ASE é criado com um certificado que não especifica o domínio do ASE ILB. Se você usar o ASE com esse certificado, o navegador informará que ele é inválido. Esse certificado facilita o teste do HTTPS, mas a expectativa é que você precisará carregar seu próprio certificado vinculado ao domínio do ASE ILB, independentemente se ele for autoassinado ou adquirido de uma AC (autoridade de certificação).

![][3]

Há várias maneiras de obter um certificado SSL válido, incluindo CAs internos, adquirir certificado de um emissor externo e usar um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado devem ser configurados corretamente:

-   _Entidade_: esse atributo deve ser definido como _\*.your-root-domain-here_
-   _Nome Alternativo da Entidade_: esse atributo deve incluir _\*.your-root-domain-here_ e _\*.scm.your-root-domain-here_. O motivo da segunda entrada é que as conexões SSL para o site do SCM/Kudu associadas a cada aplicativo serão feitas usando um endereço no formato _your-app-name.scm.your-root-domain-here_

Com um certificado SSL válido em mãos, são necessárias mais duas etapas preparatórias. O certificado SSL deve ser convertido/salvo como um arquivo.pfx. Lembre-se de que o arquivo .pfx deve incluir todos os certificados intermediários e raiz e também precisa ser protegido com uma senha.

Se desejar criar seu próprio certificado usando o PowerShell, use os comandos mostrados abaixo.  Lembre-se de usar o nome de domínio do ASE ILB, em vez de *internal.contoso.com*. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

O certificado gerado por esses comandos do PowerShell ainda será sinalizado por navegadores, porque ele não foi criado por uma AC que está na cadeia de confiança usada pelo navegador. A única maneira de obter um certificado que não será um problema para o navegador é adquiri-lo de uma AC comercial que está na cadeia de confiança do navegador.  

![][4]

Para carregar seus próprios certificados e testar o acesso:

1.  Acesse a interface do usuário do ASE após a criação do ASE (**ASE > Configurações > Certificados ILB**).
2.  Defina o certificado do ILB selecionando o arquivo pfx do certificado e forneça a senha. Esta etapa demora um pouco mais para ser processada e a mensagem de que uma operação de upload está em andamento será mostrada.
3.  Obtenha o endereço do ILB para o ASE (**ASE > Propriedades > Endereço IP Virtual**).
4.  Crie um aplicativo Web no ASE após a criação.
5.  Crie uma VM, caso você não tenha uma nessa Rede Virtual.

    > [!NOTE] 
    > Não crie essa VM na mesma sub-rede do ASE. Caso contrário, ela interromperá a configuração.
    >
    >

6.  Defina o DNS para o domínio do ASE. Use um curinga com o domínio no DNS ou, se desejar realizar alguns testes simples, edite o arquivo dos hosts na VM para definir o nome do aplicativo Web como o endereço IP VIP. Se o ASE tiver o nome de domínio _.ilbase.com_ e você criar o aplicativo Web chamado _mytestapp_, ele será endereçado em _mytestapp.ilbase.com_. Em seguida, você definirá _mytestapp.ilbase.com_ para resolver o endereço do ILB. (No Windows, o arquivo dos hosts está em _C:\Windows\System32\drivers\etc\_.) Se você desejar testar a publicação da implantação da Web ou acessar o console avançado, também precisará criar um registro para _mytestapp.scm.ilbase.com_.
7.  Use um navegador nessa VM e acesse ***http://mytestapp.ilbase.com*** (ou o nome do aplicativo Web com o domínio).
8.  Use um navegador nessa VM e acesse ***https://mytestapp.ilbase.com***. Você precisará aceitar a falta de segurança se usar um certificado autoassinado.

O endereço IP do ILB está listado em **Endereços IP**. Isso também tem os endereços IP usados pelo VIP externo e para o tráfego de gerenciamento de entrada.

![][5]

### <a name="functions-and-the-ilb-ase"></a>Functions e o ASE ILB

Ao usar o Functions em um ASE ILB, você poderá receber um erro que informa: *“Não é possível recuperar as funções no momento.  Tente novamente mais tarde”.*  O motivo disso é que a interface do usuário do Functions utiliza o site SCM por HTTPS.  Se você estiver usando um certificado HTTP para o ASE que não tem um certificado raiz, encontre isso no navegador.  Além disso, os navegadores IE\Edge não compartilham a configuração *accept-invalid-cert* entre as guias. Portanto, você pode:

- adicionar o certificado ao repositório de certificados confiáveis 
- ou usar o Chrome, mas precisará acessar o site SCM primeiro, aceitar o certificado não confiável e, em seguida, acessar o portal

## <a name="dns-configuration"></a>Configuração de DNS ##

Ao usar um VIP Externo, o DNS é gerenciado pelo Azure. Qualquer aplicativo criado no ASE é automaticamente adicionado ao DNS do Azure, que é um DNS público. Em um ASE ILB, é necessário gerenciar seu próprio DNS. Para um domínio específico, como _contoso.net_, você precisa criar registros DNS A no seu DNS que apontam para o endereço do ILB de:

- *.contoso.net
- *.scm.contoso.net

Se o domínio do ASE ILB for usado para várias tarefas fora desse ASE, talvez seja necessário gerenciar o DNS por nome de aplicativo. Isso é muito mais difícil, pois você precisará adicionar cada novo nome de aplicativo ao DNS durante sua criação. Por esse motivo, recomenda-se o uso de um domínio dedicado.

## <a name="publishing-with-an-ilb-ase"></a>Publicando com um ASE ILB ##

Para cada aplicativo criado, há dois pontos de extremidade. Em um ASE ILB, você tem *&lt;nome do aplicativo>.&lt;Domínio do ASE ILB>*, bem como *&lt;nome do aplicativo>.scm.&lt;Domínio do ASE ILB>*. 

O nome do site SCM leva você para o console do Kudu, que é chamado de **Portal Avançado** no portal do Azure. O console do Kudu permite realizar várias tarefas, incluindo exibir variáveis de ambiente, explorar o disco, usar um console e muito mais. Para obter mais informações, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. 

No Serviço de Aplicativo multilocatário e em um ASE Externo, há logon único entre o portal do Azure e o console do Kudu. No entanto, para o ASE ILB, você precisa usar suas credenciais de publicação para entrar no console do Kudu.

Sistemas de CI baseados na Internet, como GitHub e VSTS, não funcionam com um ASE ILB, pois o ponto de extremidade de publicação não é acessível pela Internet. Em vez disso, você precisa usar um sistema de CI que usa um modelo pull, como o Dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Isso pode ser visto no perfil de publicação do aplicativo e na folha do portal do aplicativo (em **Visão Geral** > **Informações Básicas** e também em **Propriedades**). Se você tiver um ASE ILB com o subdomínio *contoso.net* e um aplicativo chamado *mytest*, você usará FTP para *mytest.contoso.net* e executará a implantação da Web para *mytest.scm.contoso.net*.

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>Acoplando um ASE ILB com um dispositivo WAF ##

O Serviço de Aplicativo do Azure fornece várias de medidas de segurança para proteger o sistema e ajudar a determinar se um aplicativo foi invadido por um hacker. A melhor proteção para um aplicativo Web é acoplar uma plataforma de hospedagem, como o Serviço de Aplicativo do Azure, com um WAF (Firewall do Aplicativo Web). Como o ASE ILB tem um ponto de extremidade do aplicativo isolado da rede, ele é perfeito para esse uso.  

Para saber mais sobre como configurar o ASE ILB com um dispositivo WAF, consulte [Configurar um firewall do aplicativo Web com o Ambiente do Serviço de Aplicativo][ASEWAF]. Este artigo mostra como usar uma solução de virtualização do Barracuda com o ASE. Outra opção é usar o Gateway de Aplicativo do Azure. O Gateway de Aplicativo usa as regras básicas do OWASP para proteger os aplicativos colocados atrás dele. Para obter mais informações sobre o Gateway de Aplicativo do Azure, consulte [Introdução ao Firewall do Aplicativo Web do Azure][AppGW].

### <a name="getting-started"></a>Introdução ###

Todos os artigos e instruções dos Ambientes do Serviço de Aplicativo estão disponíveis no [LEIAME dos Ambientes do Serviço de Aplicativo][ASEReadme].

Para se familiarizar com os Ambientes do Serviço de Aplicativo, consulte [Introdução aos Ambientes do Serviço de Aplicativo][Intro].

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/).
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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

