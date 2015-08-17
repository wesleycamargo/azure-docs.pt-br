<properties
	pageTitle="Como criar e implantar um serviço de nuvem - Azure"
	description="Saiba como criar e implantar um serviço de nuvem usando o método de Criação Rápida no Azure."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/30/2015"
	ms.author="adegeo"/>




# Como criar e implantar um serviço de nuvem.

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-create-deploy.md)
- [Azure Preview Portal](cloud-services-how-to-create-deploy-portal.md)

O Portal do Azure oferece duas maneiras de criar e implantar um serviço de nuvem: **Criação Rápida** e **Criação Personalizada**.

Este tópico explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao usar esse método, o Portal do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a **Criação Personalizada**.

> [AZURE.NOTE]Se você planeja publicar o serviço de nuvem do VSO (Visual Studio Online), use a criação rápida e configure a publicação do VSO em **Inicialização Rápida** ou no painel. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando a visualização do Visual Studio Online][TFSTutorialForCloudService], ou consulte a ajuda para a página **Início Rápido**.

## Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

- **Definição de serviço** O arquivo de definição de serviço de nuvem (.csdef) define o modelo de serviço, inclusive o número de funções.

- **Configuração de serviço** O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

- **Pacote de serviço** O pacote de serviço (.cspkg) contém o código do aplicativo, as configurações e o arquivo de definição de serviço.

Você pode aprender mais sobre eles e como criar um pacote [aqui](cloud-services-model-and-package.md).

## Preparação do aplicativo
Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure](http://azure.microsoft.com/downloads/) no idioma em que você preferir desenvolver seu código do aplicativo.

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

- Se você desejar implantar um serviço de nuvem que usa protocolo SSL para a criptografia de dados, configure seu aplicativo para SSL. Para obter mais informações, consulte [Como configurar um certificado SSL em um ponto de extremidade HTTPS](http://msdn.microsoft.com/library/azure/ff795779.aspx).

- Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, configure as funções para área de trabalho remota. Para obter mais informações sobre como preparar o arquivo da definição de serviço para acesso remoto, consulte [Configuração de uma conexão de área de trabalho remota para uma função no Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O "monitoramento detalhado"* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar o Diagnóstico do Azure, consulte [Habilitação do Diagnóstico no Azure](cloud-services-dotnet-diagnostics.md) (a página pode estar em inglês).

- Para criar um serviço de nuvem com implantações de funções Web ou de Trabalho, você deve criar o pacote de serviço. Para mais informações sobre os arquivos relacionados ao pacote, consulte [Configuração de um serviço de nuvem para o Azure](http://msdn.microsoft.com/library/hh124108.aspx). Para criar o arquivo de pacote, veja [Empacotar um aplicativo do Microsoft Azure](http://msdn.microsoft.com/library/hh403979.aspx). Se estiver usando o Visual Studio para desenvolver seu aplicativo, consulte [Publicando um serviço de nuvem usando as ferramentas do Azure](http://msdn.microsoft.com/library/ff683672.aspx).

## Antes de começar

- Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a[página de Downloads do Azure](http://azure.microsoft.com/downloads/) e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).

- Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. Você pode carregar os certificados para o Azure enquanto cria e implanta o serviço de nuvem. Para obter mais informações sobre os certificados, consulte [Gerenciar certificados](http://msdn.microsoft.com/library/gg981929.aspx).

- Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie-o. Você pode usar um grupo de afinidade para implantar o serviço de nuvem e outros serviços do Azure no mesmo local em uma região. Você pode criar um grupo de afinidade na área **Redes** do Portal de Gerenciamento, na página **Grupos de Afinidade**. Para obter mais informações, consulte [Criar um grupo de afinidade no Portal de Gerenciamento](http://msdn.microsoft.com/library/jj156209.aspx).


## Etapa 3: criar um serviço de nuvem e carregar o pacote de implantação

1. Entre no [Portal de Visualização do Azure][]. 
2. Clique em **Novo**, **Computação**, role para baixo e clique em **Serviço de Nuvem**.

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Na nova folha **Serviço de Nuvem**, insira um valor para o **Nome DNS**
4. Crie um novo **Grupo de Recursos** ou selecione um existente.
5. Selecione um **Local**.
6. Selecione **Pacote** na folha **Carregar um pacote** e preencha os campos obrigatórios.  
      
     Se alguma das funções contiver uma única instância, verifique se **Implantar mesmo se uma ou mais funções contiverem uma única instância** está marcado.

7. Verifique se **Iniciar implantação** está *marcado*.
8. Clique em **OK**. 

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Carregar um certificado

Se o pacote de implantação foi [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), você pode carregar o certificado agora.

9. Selecione **Certificados** e na folha **Adicionar certificados**, selecione o arquivo .pfx do certificado SSL e forneça a **Senha** do certificado, 
10. Clique em **Anexar certificado** e clique em **OK** na folha **Adicionar certificados**.
11. Clique em **Criar** na folha **Serviço de Nuvem**. Quando a implantação alcançar o status **Pronto**, será possível passar às próximas etapas.

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Verifique se a implantação foi concluída com êxito

1. Clique na instância do serviço de nuvem.

	O status deve mostrar que o serviço está **Executando**.

2. Em **Essentials**, clique na **URL do site** para abrir o serviço de nuvem em um navegador da Web.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
 

<!---HONumber=August15_HO6-->