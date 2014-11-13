<properties urlDisplayName="How to create and deploy" pageTitle="Como criar e implantar um servi&ccedil;o de nuvem - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Saiba como criar e implantar um servi&ccedil;o de nuvem usando o m&eacute;todo de Cria&ccedil;&atilde;o R&aacute;pida no Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Como criar e implantar um servi&ccedil;o de nuvem." authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Como criar e implantar um serviço de nuvem.

O Portal de Gerenciamento do Azure fornece duas maneiras para criar e implantar um serviço de nuvem: **Criação Rápida** e **Criação Personalizada**.

Este tópico explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao você usar esse método, o Portal de Gerenciamento do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a **Criação Personalizada**.

**Observação** Se você planeja publicar o serviço de nuvem do Team Foundation Services (TFS) do Windows, use a criação rápida e configure a publicação do TFS em **Inicialização Rápida** ou no painel. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando a visualização do Visua Studio Online][Fornecimento contínuo para o Azure usando a visualização do Visua Studio Online], ou consulte a ajuda para a página **Início Rápido**.

## Sumário

-   [Conceitos][Conceitos]
-   [Preparação do aplicativo][Preparação do aplicativo]
-   [Antes de começar][Antes de começar]
-   [Como: criar um serviço de nuvem com a Criação Rápida][Como: criar um serviço de nuvem com a Criação Rápida]
-   [Como: carregar um certificado para um serviço de nuvem][Como: carregar um certificado para um serviço de nuvem]
-   [Como: Implantar um serviço de nuvem][Como: Implantar um serviço de nuvem]

## <span id="concepts"></span></a>Conceitos

Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

> -   **arquivo da definição de serviço** O arquivo da definição de serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.

> -   **arquivo de configuração de serviço** O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

> -   **pacote de serviço** O pacote de serviço (arquivo .cspkg) contém o código do aplicativo e o arquivo de definição do serviço.

## <span id="prepare"></span></a>Preparação do aplicativo

Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). Cada pacote do serviço de nuvem contém arquivos de aplicativo e configurações. O arquivo de configuração do serviço fornece as configurações.

O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure][downloads de Azure] no idioma em que você preferir desenvolver seu código do aplicativo.

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

-   Se você desejar implantar um serviço de nuvem que usa protocolo SSL para a criptografia de dados, configure seu aplicativo para SSL. Para obter mais informações, consulte [Como configurar um certificado SSL em um ponto de extremidade HTTPS][Como configurar um certificado SSL em um ponto de extremidade HTTPS].

-   Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, configure as funções para área de trabalho remota. Para obter mais informações sobre como preparar o arquivo da definição de serviço para acesso remoto, consulte [Configuração de uma conexão de área de trabalho remota para uma função no Azure][Configuração de uma conexão de área de trabalho remota para uma função no Azure].

-   Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O "monitoramento detalhado"\* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar o Diagnóstico do Azure, consulte [Habilitação do Diagnóstico no Azure][Habilitação do Diagnóstico no Azure] (a página pode estar em inglês).

-   Para criar um serviço de nuvem com implantações de funções Web ou de Trabalho, você deve criar o pacote de serviço. Para mais informações sobre os arquivos relacionados ao pacote, consulte [Configuração de um serviço de nuvem para o Azure][Configuração de um serviço de nuvem para o Azure]. Para criar o arquivo de pacote, veja [Empacotamento de um aplicativo do Microsoft Azure][Empacotamento de um aplicativo do Microsoft Azure]. Se estiver usando o Visual Studio para desenvolver seu aplicativo, consulte [Publicando um serviço de nuvem usando as ferramentas do Azure][Publicando um serviço de nuvem usando as ferramentas do Azure].

## <span id="begin"></span></a>Antes de começar

-   Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a[página de Downloads do Azure][downloads de Azure] e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).

-   Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. Você pode carregar os certificados para o Azure enquanto cria e implanta o serviço de nuvem. Para obter mais informações sobre os certificados, consulte [Gerenciar certificados][Gerenciar certificados].

-   Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie-o. Você pode usar um grupo de afinidade para implantar o serviço de nuvem e outros serviços do Azure no mesmo local em uma região. Você pode criar um grupo de afinidade na área **Redes** do Portal de Gerenciamento, na página **Grupos de Afinidade**. Para obter mais informações, consulte [Criar um grupo de afinidade no Portal de Gerenciamento][Criar um grupo de afinidade no Portal de Gerenciamento].

## <span id="quick"></span></a>Como: Criar um serviço de nuvem com a Criação Rápida

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Novo**\>**Computar**\>**Serviço de Nuvem**\>**Criação Rápida**.

    ![CloudServices\_QuickCreate][CloudServices\_QuickCreate]

2.  Em **URL**, digite um nome do subdomínio para usar na URL pública para acessar o serviço de nuvem em implantações de produção. O formato da URL para implantações de produção é: http://*myURL*.cloudapp.net.

3.  Em **Grupo de afinidade/região**, selecione a região geográfica ou o grupo de afinidade no qual você implantará o serviço de nuvem. Selecione um grupo de afinidade se desejar implantar um serviço de nuvem no mesmo local de outros serviços do Azure em uma região.

4.  Clique em **Criar Serviço de Nuvem**.

    ![CloudServices\_Region][CloudServices\_Region]

    Você pode monitorar o status do processo na área de mensagem na parte inferior da janela.

    A área **Serviços de Nuvem** é aberta, com o novo serviço de nuvem sendo exibido. Quando o status mudar para Criado, a criação do serviço de nuvem terá sido concluída com êxito.

    ![CloudServices\_CloudServicesPage][CloudServices\_CloudServicesPage]

## <span id="uploadcertificate"></span></a>Como: Carregar um certificado para um serviço de nuvem

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem** e no nome do serviço de nuvem e depois em **Certificados**.

    ![CloudServices\_QuickCreate][1]

2.  Clique em **Carregar um certificado** ou **Carregar**.

3.  Em **Arquivo**, use **Procurar** para selecionar o certificado (arquivo .pfx).

4.  Em **Senha**, insira a chave privada para o certificado.

5.  Clique em **OK** (marca de seleção).

    ![CloudServices\_AddaCertificate][CloudServices\_AddaCertificate]

    Você pode observar o progresso do carregamento na área de mensagem, mostrada abaixo. Quando o carregamento for concluído, o certificado será adicionado à tabela. Na área de mensagem, clique em OK para fechar a mensagem.

    ![CloudServices\_CertificateProgress][CloudServices\_CertificateProgress]

## <span id="deploy"></span></a>Como: Implantar um serviço de nuvem

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços de Nuvem**, no nome do serviço de nuvem e depois em **Painel**.

    O painel é aberto no ambiente de Produção. Nesse momento, você pode selecionar Preparar para implantar seu aplicativo no ambiente de preparo. Para obter mais informações, consulte [Gerenciar implantações no Azure][Gerenciar implantações no Azure].

2.  Clique em **Carregar uma nova implantação de produção** ou em **Carregar**.

3.  Em **Rótulo de implantação**, insira um nome para a nova implantação como, por exemplo, MyCloudServicev4.

4.  Em **Pacote**, use **Procurar** para selecionar o arquivo do pacote de serviço (.cspkg) para o uso.

5.  Em **Configuração**, use **Procurar** para selecionar o arquivo de configuração do serviço (.cscfg) para o uso.

6.  Se o serviço de nuvem incluir funções com apenas uma instância, marque a caixa de seleção **Implantar mesmo se uma ou mais funções contiverem uma única instância** para continuar a implantação.

O Azure só pode garantir 99,95 por cento de acesso ao serviço de nuvem durante a manutenção e atualizações do serviço se cada função tiver pelo menos duas instâncias. Se necessário, você pode adicionar instâncias de função adicionais na página **Escala** após implantar o serviço de nuvem. Para obter mais informações, consulte [Contratos de Nível de Serviço][Contratos de Nível de Serviço].

1.  Clique em **OK** (marca de seleção) para iniciar a implantação do serviço de nuvem.

    ![CloudServices\_UploadaPackage][CloudServices\_UploadaPackage]

    Você pode monitorar o status da implantação na área de mensagem. Clique em OK para ocultar a mensagem.

    ![CloudServices\_UploadProgress][CloudServices\_UploadProgress]

### Para verificar se sua implantação foi concluída com êxito

1.  Clique em **Painel**.

    O status deve mostrar que o serviço está **Executando**.

2.  Em **visão rápida**, clique na URL do site para abrir o serviço de nuvem em um navegador.

<!-- -->

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [Fornecimento contínuo para o Azure usando a visualização do Visua Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Conceitos]: #concepts
  [Preparação do aplicativo]: #prepare
  [Antes de começar]: #begin
  [Como: criar um serviço de nuvem com a Criação Rápida]: #quick
  [Como: carregar um certificado para um serviço de nuvem]: #uploadcertificate
  [Como: Implantar um serviço de nuvem]: #deploy
  [downloads de Azure]: http://www.windowsazure.com/pt-br/develop/downloads/
  [Como configurar um certificado SSL em um ponto de extremidade HTTPS]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff795779.aspx
  [Configuração de uma conexão de área de trabalho remota para uma função no Azure]: http://msdn.microsoft.com/pt-br/library/hh124107.aspx
  [Habilitação do Diagnóstico no Azure]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/diagnostics/
  [Configuração de um serviço de nuvem para o Azure]: http://msdn.microsoft.com/pt-br/library/hh124108.aspx
  [Empacotamento de um aplicativo do Microsoft Azure]: http://msdn.microsoft.com/pt-br/library/hh403979.aspx
  [Publicando um serviço de nuvem usando as ferramentas do Azure]: http://msdn.microsoft.com/pt-br/library/ff683672.aspx
  [Gerenciar certificados]: http://msdn.microsoft.com/pt-br/library/gg981929.aspx
  [Criar um grupo de afinidade no Portal de Gerenciamento]: http://msdn.microsoft.com/pt-br/library/jj156209.aspx
  [Portal de Gerenciamento]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [CloudServices\_Region]: ./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [1]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Gerenciar implantações no Azure]: http://msdn.microsoft.com/pt-br/library/gg433027.aspx
  [Contratos de Nível de Serviço]: http://www.windowsazure.com/pt-br/support/legal/sla/
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
