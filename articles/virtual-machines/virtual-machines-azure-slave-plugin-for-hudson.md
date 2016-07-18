<properties
	pageTitle="Como usar o plug-in subordinado do Azure com a Integração contínua do Hudson | Microsoft Azure"
	description="Descreve como usar o plug-in subordinado do Azure com a Integração contínua do Hudson."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="robmcm"/>

# Como usar o plug-in subordinado do Azure com a Integração contínua do Hudson

O plug-in subordinado do Azure para Hudson permite provisionar nós subordinados no Azure ao executar compilações distribuídas.

## Instalar o plug-in subordinado do Azure

1. No painel do Hudson, clique em **Manage Hudson**.

1. Na página **Manage Hudson**, clique em **Manage Plugins**.

1. Clique na guia **Disponível**.

1. Clique em **Pesquisar** e digite **Azure** para limitar a lista de plug-ins relevantes.

	Se optar por percorrer a lista de plug-ins disponíveis, você encontrará o plug-in subordinado do Azure na seção **Cluster Management and Distributed Build** (Gerenciamento de Cluster e Build Distribuído) da guia **Outros**.

1. Marque a caixa de seleção **Azure Slave Plugin**.

1. Clique em **Instalar**.

1. Reinicie o Hudson.

Agora que o plug-in está instalado, as próximas etapas são a configuração do plug-in com seu perfil de assinatura do Azure e criação de um modelo que será usado na criação da VM para o nó subordinado.

## Configurar o plug-in subordinado do Azure com seu perfil de assinatura

Um perfil de assinatura, também conhecido como configurações de publicação, é um arquivo XML que contém credenciais seguras e informações adicionais, das quais você precisará para trabalhar com o Azure no ambiente de desenvolvimento. Para configurar o plug-in subordinado do Azure, você precisa dos seguintes itens:

* A ID de sua assinatura
* Um certificado de gerenciamento da sua assinatura

Eles podem ser encontrados em seu [perfil de assinatura]. Veja a seguir um exemplo de perfil de assinatura.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Após obter seu perfil de assinatura, siga estas etapas para configurar o plug-in subordinado do Azure.

1. No painel do Hudson, clique em **Manage Hudson**.

1. Clique em **Configure System**.

1. Role a página para encontrar a seção **Cloud**.

1. Clique em **Add new cloud > Microsoft Azure**.

    ![adicionar nova nuvem][add new cloud]

    Isso mostrará os campos em que você precisa inserir os detalhes da sua assinatura.

    ![configurar perfil][configure profile]

1. Copie o certificado de gerenciamento e a ID de assinatura do seu perfil de assinatura e cole-os nos campos apropriados.

    Ao copiar o certificado de gerenciamento e a ID de assinatura, **não** inclua as aspas que contêm os valores.

1. Clique em **Verify configuration**.

1. Quando a configuração for verificada com êxito, clique em **Save**.

## Configurar um modelo de máquina virtual para o plug-in subordinado do Azure

Um modelo de máquina virtual define os parâmetros que o plug-in usará para criar um nó subordinado no Azure. Nas etapas a seguir, criaremos o modelo para uma VM do Ubuntu.

1. No painel do Hudson, clique em **Manage Hudson**.

1. Clique em **Configure System**.

1. Role a página para encontrar a seção **Cloud**.

1. Dentro da seção **Cloud**, encontre **Add Azure Virtual Machine Template** e clique no botão **Add**.

    ![adicionar modelo de vm][add vm template]

1. Especifique um nome de serviço de nuvem no campo **Name**. Se o nome especificado se referir a um serviço de nuvem existente, a VM será provisionada nesse serviço. Caso contrário, o Azure criará um novo.

1. No campo **Description**, especifique o texto que descreve o modelo que você está criando. Essas informações são apenas para fins de documentação e não são usadas no provisionamento de uma VM.

1. No campo **Labels**, digite **linux**. Esse rótulo é usado para identificar o modelo que você está criando e, mais à frente, é usado para referenciar o modelo ao criar um trabalho do Hudson.

1. Selecione uma região onde a VM será criada.

1. Selecione o tamanho adequado da VM.

1. Selecione uma conta de armazenamento onde a VM será criada. Verifique se ela está na mesma região do serviço de nuvem que será usado. Caso deseje criar um novo armazenamento, deixe esse campo em branco.

1. O tempo de retenção especifica o número de minutos antes de o Hudson excluir um subordinado ocioso. Deixe o valor padrão de 60.

1. Em **Usage**, selecione a condição apropriada quando esse nó subordinado será usado. Por enquanto, selecione **Utilize this node as much as possible**.

    Neste momento, seu formulário será semelhante a este:

    ![configuração de modelo][template config]

1. Em **Image Family or Id**, você precisa especificar qual imagem do sistema será instalada em sua VM. Você pode selecionar em uma lista de famílias de imagens ou especificar uma imagem personalizada.

    Caso deseje selecionar em uma lista de famílias de imagens, digite o primeiro caractere (diferencia maiúsculas de minúsculas) do nome da família de imagens. Por exemplo, se você digitar **U**, será exibida uma lista das famílias de servidores do Ubuntu. Quando você selecionar na lista, o Jenkins usará a versão mais recente da imagem do sistema dessa família ao provisionar sua VM.

    ![lista de famílias de SO][OS family list]

    Se você tiver uma imagem personalizada que deseja usar, insira o nome dessa imagem. Os nomes de imagens personalizadas não são mostrados em uma lista; por isso, você precisará garantir que o nome foi digitado corretamente.

    Para este tutorial, digite **U** para exibir uma lista de imagens do Ubuntu e selecione **Ubuntu Server 14.04 LTS**.

1. Em **Launch method**, selecione **SSH**.

1. Copie o script abaixo e cole no campo **Init script**.

		# Install Java

		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk

		# Install git

		sudo apt-get install -y git

		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

    O **script de inicialização** será executado após a criação da VM. Neste exemplo, o script instala ant, git e Java.

1. Nos campos **Username** e **Password**, insira valores de sua preferência para a conta de administrador que será criada na sua VM.

1. Clique em **Verify Template** para verificar se os parâmetros especificados são válidos.

1. Clique em **Save**.

## Criar um trabalho do Hudson executado em um nó subordinado no Azure

Nesta seção, você criará uma tarefa do Hudson que será executada em um nó subordinado no Azure.

1. No painel do Hudson, clique em **New Job**.

1. Digite um nome para o trabalho que você está criando.

1. Para o tipo de trabalho, selecione **Build a free-style software job**.

1. Clique em **OK**.

1. Na página de configuração de trabalho, selecione **Restrict where this project can be run**.

1. Selecione **Node and label menu** e selecione **linux** (especificamos esse rótulo ao criar o modelo de máquina virtual na seção anterior).

1. Na seção **Build**, clique em **Add build step** e selecione **Execute shell**.

1. Edite o seguinte script, substituindo **(nome da sua conta do GitHub)**, **(nome do projeto)** e **(diretório do projeto)** por valores apropriados e cole o script editado na área de texto exibida.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e {your project directory} ]; then

  			cd {your project directory}

  			git pull origin master

		else

  			git clone https://github.com/{your github account name}/{your project name}.git

		fi

		# change directory to project

		cd $currentDir/{your project directory}

		#Execute build task

		ant

1. Clique em **Save**.

1. No painel do Hudson, localize o trabalho que você acabou de criar e clique no ícone **Schedule a build**.

Em seguida, o Hudson criará um nó subordinado usando o modelo criado na seção anterior e executará o script que você especificou na etapa de compilação dessa tarefa.

## Próximas etapas

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[perfil de assinatura]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

<!---HONumber=AcomDC_0706_2016-->