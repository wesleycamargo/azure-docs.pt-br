<properties linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Como criar e implantar um serviço de nuvem.

[WACOM.INCLUDE [isenção de responsabilidade](../includes/disclaimer.md)]

O Portal de Gerenciamento do Azure fornece duas maneiras para criar e implantar um serviço de nuvem: **Criação Rápida** e **Criação Personalizada**.

Este tópico explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao você usar esse método, o Portal de Gerenciamento do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a **Criação Personalizada**.

**Observação** Se você planeja publicar o serviço de nuvem do Team Foundation Services (TFS) do Windows, use a criação rápida e configure a publicação do TFS em **Inicialização Rápida** ou no painel. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando a visualização do Visua Studio Online][TFSTutorialForCloudService], ou consulte a ajuda para a página **Início Rápido**.

## Sumário

-   [Conceitos](#concepts)
-   [Preparação do aplicativo](#prepare)
-   [Antes de começar](#begin)
-   [Como: criar um serviço de nuvem com a Criação Rápida](#quick)
-   [Como: carregar um certificado para um serviço de nuvem](#uploadcertificate)
-   [Como: Implantar um serviço de nuvem](#deploy)
  
## <span id="concepts"></span></a>Conceitos

Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

> -   **arquivo da definição de serviço** O arquivo da definição de serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.

> -   **arquivo de configuração de serviço** O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

> -   **pacote de serviço** O pacote de serviço (arquivo .cspkg) contém o código do aplicativo e o arquivo de definição do serviço.

## <span id="prepare"></span></a>Preparação do aplicativo

Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). Cada pacote do serviço de nuvem contém arquivos de aplicativo e configurações. O arquivo de configuração do serviço fornece as configurações.

O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure](http://www.windowsazure.com/pt-br/develop/downloads/) no idioma em que você preferir desenvolver seu código do aplicativo.

Se você é nos serviços de nuvem, pode baixar um pacote de serviço de nuvem (.cspkg) de exemplo e o arquivo de configuração de serviço (.cscfg) para trabalhar dos [exemplos de código do Azure](http://code.msdn.microsoft.com/windowsazure/).

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

-   Se você desejar implantar um serviço de nuvem que usa protocolo SSL para a criptografia de dados, configure seu aplicativo para SSL. Para obter mais informações, consulte [Como configurar um certificado SSL em um ponto de extremidade HTTPS](http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx).

-   Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, configure as funções para área de trabalho remota. Para obter mais informações sobre como preparar o arquivo da definição de serviço para acesso remoto, consulte [Visão geral da configuração de uma conexão de área de trabalho remota para uma função](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433010.aspx).

-   Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O "monitoramento detalhado"\* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar o Diagnóstico do Azure, consulte [Habilitação do Diagnóstico no Azure](http://www.windowsazure.com/pt-br/develop/net/common-tasks/diagnostics/) (a página pode estar em inglês).

## <span id="begin"></span></a>Antes de começar

-   Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a [página de Downloads do Azure](http://www.windowsazure.com/pt-br/develop/downloads/) e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).

-   Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. Você pode carregar os certificados para o Azure enquanto cria e implanta o serviço de nuvem. Para obter mais informações sobre a criação de certificados, consulte [Como configurar um certificado SSL em um ponto de extremidade HTTPS](http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx).

-   Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie-o. Você pode usar um grupo de afinidade para implantar o serviço de nuvem e outros serviços do Azure no mesmo local em uma região. Você pode criar um grupo de afinidade na área **Redes** do Portal de Gerenciamento, na página **Grupos de Afinidade**. Para obter mais informações, consulte a ajuda da página **Grupos de Afinidade**.

## <span id="quick"></span></a>Como: Criar um serviço de nuvem com a Criação Rápida

1.  No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Novo**, **Computar**, **Serviço de Nuvem** e em **Criação Rápida**.

    ![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  Em **URL**, digite um nome do subdomínio para usar na URL pública para acessar o serviço de nuvem em implantações de produção. O formato da URL para implantações de produção é: http://*myURL*.cloudapp.net.

3.  Em **Grupo de afinidade/região**, selecione a região geográfica ou o grupo de afinidade no qual você implantará o serviço de nuvem. Selecione um grupo de afinidade se desejar implantar um serviço de nuvem no mesmo local de outros serviços do Azure em uma região.

    > [WACOM.NOTE]
    > Para criar um grupo de afinidade, abra a área **Redes** do Portal de Gerenciamento, clique em **Grupos de Afinidade** e em **Criar um novo grupo de afinidade** ou em **Criar**. Você pode usar os grupos de afinidade criados anteriormente no Portal de Gerenciamento do Azure. Além disso, você pode criar e gerenciar os grupos de afinidade que usam a API do Gerenciamento de Serviços do Azure. Para obter mais informações, consulte [Operações em grupos de afinidade](http://msdn.microsoft.com/pt-br/library/windowsazure/ee460798.aspx).

4.  Clique em **Criar Serviço de Nuvem**.

    Você pode monitorar o status do processo na área de mensagem na parte inferior da janela.

    A área **Serviços de Nuvem** é aberta, com o novo serviço de nuvem sendo exibido. Quando o status mudar para Criado, a criação do serviço de nuvem terá sido concluída com êxito.

    ![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

    Se uma função no serviço de nuvem precisar de um certificado para a criptografia de dados SSL e o certificado não tiver sido carregado para o Azure, você deverá carregá-lo antes de implantar o serviço de nuvem. Após carregar um certificado, todos os aplicativos do Windows em execução nas instâncias de função poderão acessar o certificado.

## <span id="uploadcertificate"></span></a>Como: Carregar um certificado para um serviço de nuvem

1.  No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.

    ![CloudServices\_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  Clique em **Certificados** para abrir a página **Certificados** mostrada abaixo.

    ![CloudServices\_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Clique em **Carregar um Certificado**.
    **Carregar Certificado** abre.

    ![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  Em **Arquivo de Certificado**, use **Procurar** para selecionar o certificado (arquivo .pfx ou .cer) a usar.

5.  Em **Senha**, insira a chave privada para o certificado.

6.  Clique em OK (marca de seleção).

    Você pode observar o progresso do carregamento na área de mensagem, mostrada abaixo. Quando o carregamento for concluído, o certificado será adicionado à tabela. Na área de mensagem, clique na seta para baixo para fechar a mensagem ou em X para removê-la.

    ![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

    Você pode implantar seu serviço de nuvem pelo painel ou pelo **Início Rápido**.

## <span id="deploy"></span></a>Como: Implantar um serviço de nuvem

1.  Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a [página de Downloads do Azure](http://www.windowsazure.com/pt-br/develop/downloads) e baixar o SDK para o idioma em que você preferir desenvolver seu código.

    Na página de downloads, você também pode instalar bibliotecas e código-fonte do cliente para desenvolver aplicativos de Web em Node.js, Java, PHP e outras linguagens, que podem ser implantado como serviços de nuvem escalonáveis do Azure.

    > [WACOM.NOTE]
    > Para a serviços de nuvem criados anteriormente (conhecidos anteriormente como *serviços hospedados*), você precisará certificar-se de que se os sistemas operacionais convidados nas máquinas virtuais (instâncias de função) são compatíveis com a versão do SDK do Azure instalada. Para obter mais informações, consulte [Notas de versão do SDK do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/hh552718.aspx).

2.  No [Portal de Gerenciamento](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**. Então, clique no nome do Serviço de Nuvem para abrir o painel e, em seguida, clique em **Painel**.

3.  Clique em **Produção** ou **Preparo**.

    Se você quiser testar o serviço de nuvem no Azure antes de implantá-lo em produção, você pode implantar em preparo. No ambiente de preparo, o identificador global exclusivo (GUID) do serviço de nuvem identifica o serviço de nuvem nas URLs (*GUID*.cloudapp.net). No ambiente de produção, o prefixo de DNS amigável que você atribui é usado (por exemplo, *meuserviço*.cloudapp.net). Quando você estiver pronto para colocar o serviço de nuvem em produção, use **Permutar** para redirecionar solicitações do cliente para essa implantação.

    ![CloudServices\_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

4.  Clique em **Carregar** no painel para implantar seu serviço de nuvem.

    ![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  Em **Etiqueta da implantação**, insira um nome para a nova implantação como, por exemplo, MyCloudServicev1.

6.  Em **Pacote**, use **Procurar** para selecionar o arquivo do pacote de serviço (.cspkg) para o uso.

7.  Em **Configuração**, use **Procurar** para selecionar o arquivo de configuração do serviço (.cscfg) para o uso.

8.  Se o serviço de nuvem incluir funções com apenas uma instância, marque a caixa de seleção **Implantar mesmo se uma ou mais funções contiverem uma única instância** para continuar a implantação.

    O Azure só pode garantir 99,95 por cento de acesso ao serviço de nuvem durante a manutenção e atualizações do serviço se cada função tiver pelo menos duas instâncias. Se necessário, você pode adicionar instâncias de função adicionais na página **Escala** após implantar o serviço de nuvem. Para obter mais informações, consulte [Contratos de Nível de Serviço](http://www.windowsazure.com/pt-br/support/legal/sla/).

9.  Clique em OK (marca de seleção) para iniciar a implantação do serviço de nuvem.

    Você pode monitorar o status da implantação na área de mensagem. Clicar a seta para baixo para ocultar a mensagem.

    ![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### Para verificar se sua implantação foi concluída com êxito

1.  Clique em **Painel**.

2.  Em **visão rápida**, clique na URL do site para abrir o serviço de nuvem em um navegador.



    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [isenção de responsabilidade]: ../includes/disclaimer.md
  [Fornecimento contínuo para o Azure usando a visualização do Visua Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Conceitos]: #concepts
  [Preparação do aplicativo]: #prepare
  [Antes de começar]: #begin
  [Como: criar um serviço de nuvem com a Criação Rápida]: #quick
  [Como: carregar um certificado para um serviço de nuvem]: #uploadcertificate
  [Como: Implantar um serviço de nuvem]: #deploy
  [downloads de Azure]: http://www.windowsazure.com/pt-br/develop/downloads/
  [exemplos de código do Azure]: http://code.msdn.microsoft.com/windowsazure/
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx
  [Visão geral da configuração de uma conexão de área de trabalho remota para uma função]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433010.aspx
  [Habilitação do Diagnóstico no Azure]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/diagnostics/
  [Portal de Gerenciamento]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [Operações em grupos de afinidade]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee460798.aspx
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [CloudServices\_EmptyDashboard]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_CertificatesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Notas de versão do SDK do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh552718.aspx
  [CloudServices\_QuickStartPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [Contratos de Nível de Serviço]: http://www.windowsazure.com/pt-br/support/legal/sla/
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
