<properties
	pageTitle="Como criar e implantar um serviço de nuvem | Microsoft Azure"
	description="Saiba como criar e implantar um serviço de nuvem usando o método de Criação Rápida no Azure. Esses exemplos usam o portal do Azure."
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
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="adegeo"/>




# Como criar e implantar um serviço de nuvem

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-how-to-create-deploy.md)
- [Azure portal](cloud-services-how-to-create-deploy-portal.md)

O Portal do Azure oferece duas maneiras de criar e de implantar um serviço de nuvem: *Criação Rápida* e *Criação Personalizada*.

Este artigo explica como usar o método Criação Rápida para criar um novo serviço de nuvem e usar **Carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Ao usar esse método, o Portal do Azure disponibiliza links convenientes para o cumprimento de todos os requisitos quando você precisar. Se você estiver pronto para implantar o serviço de nuvem ao criá-lo, é possível usar ambos ao mesmo tempo usando a Criação Personalizada.

> [AZURE.NOTE]Se você planeja publicar o serviço de nuvem do VSTS (Visual Studio Team Services), use a Criação Rápida e configure a publicação do VSTS no Início Rápido do Azure ou no painel. Para obter mais informações, veja [Entrega contínua para o Azure usando o Visual Studio Team Services][TFSTutorialForCloudService] ou confira a ajuda da página **Início Rápido**.

## Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

- **Definição de serviço** O arquivo de definição de serviço de nuvem (.csdef) define o modelo de serviço, incluindo o número de funções.

- **Configuração de serviço** O arquivo de configuração de serviço de nuvem (.cscfg) fornece as configurações para o serviço de nuvem e as funções individuais, inclusive o número de instâncias de função.

- **Pacote de serviço** O pacote de serviço (.cspkg) contém o código do aplicativo, as configurações e o arquivo de definição de serviço.

Você pode saber mais sobre eles e sobre como criar um pacote [aqui](cloud-services-model-and-package.md).

## Preparação do aplicativo
Antes de poder implantar um serviço de nuvem, você deve criar o pacote do serviço de nuvem (arquivo .cspkg) do seu código do aplicativo e um arquivo de configuração do serviço de nuvem (.cscfg). O SDK do Azure fornece as ferramentas para preparar os arquivos exigidos para a implantação. Você pode instalar o SDK da página de [downloads de Azure](http://azure.microsoft.com/downloads/) no idioma em que você preferir desenvolver seu código do aplicativo.

Três recursos de serviço de nuvem precisam de configurações especiais antes que você exporte um pacote de serviço:

- Se você desejar implantar um serviço de nuvem que usa protocolo SSL para a criptografia de dados, [configure seu aplicativo](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.

- Se você desejar configurar conexões de área de trabalho remota para as instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop.md) para Área de Trabalho Remota. Isso só pode ser feito no portal clássico.

- Se você desejar configurar o monitoramento detalhado para o seu serviço de nuvem, habilite o Diagnóstico do Azure para o serviço de nuvem. *Monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para as instâncias de função (máquinas virtuais). O *Monitoramento detalhado* coleta métricas adicionais baseadas nos dados de desempenho nas instâncias de função para habilitar uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar o Diagnóstico do Azure, confira [Habilitando o diagnóstico no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço de nuvem com implantações de funções Web ou de Trabalho, você deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## Antes de começar

- Se você não instalou o SDK do Azure, clique em **Instalar SDK do Azure** para abrir a[página de Downloads do Azure](http://azure.microsoft.com/downloads/) e baixar o SDK para o idioma em que você preferir desenvolver seu código. (Você terá uma oportunidade de fazer isso posteriormente).

- Se alguma instância de função precisar de certificados, crie-os. Os serviços de nuvem requerem um arquivo .pfx e uma chave privada. [Você pode carregar os certificados para o Azure]() enquanto cria e implanta o serviço de nuvem.

- Se você planeja implantar o serviço de nuvem em um grupo de afinidade, crie-o. Você pode usar um grupo de afinidade para implantar o serviço de nuvem e outros serviços do Azure no mesmo local em uma região. Você pode criar o grupo de afinidade na área **Redes** do Portal clássico do Azure, na página **Grupos de Afinidade**.


## Etapa 3: criar um serviço de nuvem e carregar o pacote de implantação

1. Faça logon no [Portal do Azure][].
2. Clique em **Novo > Computação**, role para baixo e clique em **Serviço de Nuvem**.

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Na parte inferior da página de informações que é exibida, clique em **Criar**.
4. Na nova folha **Serviço de Nuvem**, insira um valor em **Nome DNS**.
5. Crie um novo **Grupo de Recursos** ou selecione um existente.
6. Selecione um **Local**.
7. Selecione **Pacote** na folha **Carregar um pacote** e preencha os campos obrigatórios.  

     Se alguma das funções contiver uma única instância, verifique se **Implantar mesmo se uma ou mais funções contiverem uma única instância** está marcado.

8. Verifique se a opção **Iniciar implantação** está selecionada.
9. Clique em **OK**.

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## Carregar um certificado

Se o pacote de implantação tiver sido [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), você poderá carregar o certificado agora.

1. Selecione **Certificados** e, na folha **Adicionar certificados**, selecione o arquivo .pfx do certificado SSL e forneça a **Senha** do certificado.
2. Clique em **Anexar certificado** e clique em **OK** na folha **Adicionar certificados**.
3. Clique em **Criar** na folha **Serviço de Nuvem**. Quando a implantação alcançar o status **Pronto**, será possível passar às próximas etapas.

    ![Publicar o serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## Verifique se a implantação foi concluída com êxito

1. Clique na instância do serviço de nuvem.

	O status deve mostrar que o serviço está **Executando**.

2. Em **Essentials**, clique na **URL do Site** para abrir o serviço de nuvem em um navegador da Web.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## Próximas etapas

* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_1210_2015-->