<properties
    pageTitle="Como usar o plug-in subordinado do Azure com Jenkins Continuous Integration"
    description="Descreve como usar o plug-in subordinado do Azure com Jenkins Continuous Integration."
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

#Como usar o plug-in subordinado do Azure com Jenkins Continuous Integration

O plug-in subordinado do Azure para Jenkins permite provisionar nós subordinados no Azure ao executar compilações distribuídas.

## Instalar o plug-in subordinado do Azure
1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Na página **Manage Jenkins**, clique em **Manage Plug-ins**.
3. Clique na guia **Disponível**.
4. No campo de filtro, acima da lista de plug-ins disponíveis, digite **Azure** para limitar a lista de plug-ins relevantes.

	Se optar por percorrer a lista de plug-ins disponíveis, você encontrará o plug-in subordinado do Azure na seção **Cluster Management and Distributed Build**.
	 
5. Marque a caixa de seleção **Azure Slave Plugin**.
6. Clique em **Install without restart** ou **Download now and install after restart**.

Agora que o plug-in está instalado, as próximas etapas são a configuração do plug-in com seu perfil de assinatura do Azure e criação de um modelo que será usado na criação da VM para o nó subordinado.


## Configurar o plug-in subordinado do Azure com seu perfil de assinatura

Um perfil de assinatura, também conhecido como configurações de publicação, é um arquivo XML que contém credenciais seguras e informações adicionais, das quais você precisará para trabalhar com o Azure no ambiente de desenvolvimento. Para configurar o plug-in subordinado do Azure, você precisa do seguinte:

* sua ID de assinatura
* um certificado de gerenciamento da sua assinatura

Eles podem ser encontrados em seu perfil de assinatura. Caso não tenha uma cópia do seu perfil de assinatura, você pode baixá-lo [aqui](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0). Veja a seguir um exemplo de perfil de assinatura.

	<?xml version="1.0" encoding="utf-8"?>

		<PublishData>

  		<PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

    	<Subscription

      		ServiceManagementUrl="https://management.core.windows.net"

      		Id="<Subscription ID value>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

Após obter seu perfil de assinatura, siga estas etapas para configurar o plug-in subordinado do Azure.

1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Clique em **Configure System**.
3. Role a página para encontrar a seção **Cloud**.
4. Clique em **Add new cloud > Microsoft Azure**.

	![seção de nuvem](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	Isso mostrará os campos em que você precisa inserir os detalhes da sua assinatura.

	![configuração de assinatura](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. Copie os valores do certificado de gerenciamento e da ID de assinatura do seu perfil de assinatura e cole-os nos campos apropriados.

	Ao copiar o certificado de gerenciamento e a ID de assinatura, **não** inclua as aspas que contêm os valores.
	
6. Clique em **Verify configuration**.
7. Quando a configuração for verificada e estiver correta, clique em **Save**.

## Configurar um modelo de máquina virtual para o plug-in subordinado do Azure

Um modelo de máquina virtual define os parâmetros que o plug-in usará para criar um nó subordinado no Azure. Nas etapas a seguir, criaremos o modelo para uma VM do Ubuntu.

1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Clique em **Configure System**.
3. Role a página para encontrar a seção **Cloud**.

4. Na seção Cloud, encontre Add Azure Virtual Machine Template e clique no botão Add.

	![adicionar modelo de vm](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	Isso mostrará os campos em que você insere detalhes sobre o modelo que está criando.

	![configuração geral em branco](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. Especifique um nome de serviço de nuvem do Azure no campo **Name**. Se o nome informado se referir a um serviço de nuvem existente, a VM será provisionada nesse serviço. Caso contrário, o Azure criará um novo.

6. No campo **Description**, especifique o texto que descreve o modelo que você está criando. Essas informações são apenas para fins de documentação e não são usadas no provisionamento de uma VM.
7. O campo **Labels** é usado para identificar o modelo que você está criando e, mais à frente, é usado para referenciar o modelo ao criar um trabalho do Jenkins. Para nosso objetivo, digite **linux** nesse campo. 
8. Selecione uma região onde a VM será criada.
9. Selecione o tamanho adequado da VM.
10. Selecione uma conta de armazenamento onde a VM será criada. Verifique se ela está na mesma região do serviço de nuvem que será usado. Caso deseje criar um novo armazenamento, deixe esse campo em branco.
11. O tempo de retenção especifica o número de minutos antes de o Jenkins excluir um subordinado ocioso. Deixe o valor padrão de 60. Também é possível desligar o subordinado, em vez de excluí-lo quando estiver ocioso. Para isso, marque a caixa de seleção **Shutdown Only (Do Not Delete) After Retention Time**.
12. Em **Usage**, selecione a condição apropriada quando esse nó subordinado será usado. Por enquanto, selecione **Utilize this node as much as possible**.

	Neste momento, seu formulário será semelhante a este:

	![configuração do modelo geral de ponto de verificação](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	A próxima etapa é fornecer detalhes sobre a imagem do sistema operacional na qual deseja que o subordinado seja criado.
 
13. Em **Image Family or Id**, você precisa especificar qual imagem do sistema será instalada em sua VM. Você pode selecionar em uma lista de famílias de imagens ou especificar uma imagem personalizada.

	Caso deseje selecionar em uma lista de famílias de imagens, digite o primeiro caractere (diferencia maiúsculas de minúsculas) do nome da família de imagens. Por exemplo, se você digitar **U**, será exibida uma lista das famílias de servidores do Ubuntu. Quando você selecionar na lista, o Jenkins usará a versão mais recente da imagem do sistema dessa família ao provisionar sua VM.

	![exemplo de lista de imagens de SO](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)
	
	Se você tiver uma imagem personalizada que deseja usar, insira o nome dessa imagem. Os nomes de imagens personalizadas não são mostrados em uma lista; por isso, você precisará garantir que o nome foi digitado corretamente.

	Para este tutorial, digite **U** para exibir uma lista de imagens do Ubuntu e selecione **Ubuntu Server 14.04 LTS**.
 
14. Em **Launch method**, selecione **SSH**.
15. Copie o script abaixo e cole no campo **Init script**.

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
	
16. Nos campos **Username** e **Password**, insira valores de sua preferência para a conta de administrador que será criada na sua VM.
17. Clique em **Verify Template** para verificar se os parâmetros especificados são válidos.
18. Clique em **Save**.


## Criar um trabalho do Jenkins executado em um nó subordinado no Azure

Nesta seção, você criará uma tarefa do Jenkins que será executada em um nó subordinado no Azure. Você precisará ter seu próprio projeto no github para continuar.

1. No painel do Jenkins, clique em **New Item**. 
2. Digite um nome para a tarefa que você está criando.
3. Para o tipo de projeto, selecione **Freestyle project**.
4. Clique em **OK**.
5. Na página de configuração de tarefa, selecione **Restrict where this project can be run**.
6. No campo **Label Expression**, digite **linux**. Na seção anterior, criamos um modelo subordinado chamado **linux**, que é o que estamos especificando aqui.
7. Na seção **Build**, clique em **Add build step** e selecione **Execute shell**.
8. Edite o seguinte script, substituindo **(nome da sua conta do github)**, **(nome do projeto)** e **(diretório do projeto)** por valores adequados e cole o script editado na área de texto exibida.

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your github account name)/(your project name).git

		fi
		
		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant
		
9. Clique em **Save**.
10. No painel do Jenkins, passe o mouse sobre a tarefa que acabou de criar e clique na seta para baixo para exibir opções de tarefa.
11. Clique em **Build now**.

Em seguida, o Jenkins criará um nó subordinado usando o modelo criado na seção anterior e executará o script que você especificou na etapa de compilação dessa tarefa.






  

  

<!---HONumber=July15_HO4-->