---
title: Como usar o plug-in subordinado do Azure com Jenkins Continuous Integration | Microsoft Docs
description: Descreve como usar o plug-in subordinado do Azure com Jenkins Continuous Integration.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 09/20/2016
ms.author: robmcm

---
# Como usar o plug-in subordinado do Azure com Jenkins Continuous Integration
Você pode usar o plug-in subordinado do Azure para Jenkins para provisionar nós subordinados no Azure ao executar compilações distribuídas.

## Instalar o plug-in subordinado do Azure
1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Na página **Gerenciar Jenkins**, clique em **Gerenciar Plug-ins**.
3. Clique na guia **Disponível**.
4. No campo de filtro, acima da lista de plug-ins disponíveis, digite **Azure** para limitar a lista aos plug-ins relevantes.
   
    Se optar por percorrer a lista de plug-ins disponíveis, você encontrará o plug-in subordinado do Azure na seção **Gerenciamento de Cluster e Compilação Distribuída**.
5. Marque a caixa de seleção **Plug-in Subordinado do Azure**.
6. Clique em **Install without restart** ou **Download now and install after restart**.

Agora que o plug-in está instalado, as próximas etapas são configurar o plug-in com seu perfil de assinatura do Azure e criar um modelo que será usado na criação da máquina virtual para o nó subordinado.

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

              Id="<Subscription ID value>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Após obter seu perfil de assinatura, siga estas etapas para configurar o plug-in subordinado do Azure:

1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Clique em **Configure System**.
3. Role a página para encontrar a seção **Cloud**.
4. Clique em **Add new cloud > Microsoft Azure**.
   
    ![seção de nuvem][cloud section]
   
    Isso mostrará os campos em que você precisa inserir os detalhes da sua assinatura.
   
    ![configuração de assinatura][subscription configuration]
5. Copie os valores do certificado de gerenciamento e da ID de assinatura do seu perfil de assinatura e cole-os nos campos apropriados.
   
    Ao copiar o certificado de gerenciamento e a ID de assinatura, não inclua as aspas que contêm os valores.
6. Clique em **Verificar Configuração**.
7. Quando a configuração for verificada e estiver correta, clique em **Save**.

## Configurar um modelo de máquina virtual para o plug-in subordinado do Azure
Um modelo de máquina virtual define os parâmetros que o plug-in usará para criar um nó subordinado no Azure. Nas etapas a seguir, criaremos o modelo para uma máquina virtual do Ubuntu.

1. No painel do Jenkins, clique em **Manage Jenkins**.
2. Clique em **Configure System**.
3. Role a página para encontrar a seção **Cloud**.
4. Na seção **Nuvem**, encontre **Adicionar Modelo de Máquina Virtual do Azure** e clique no botão **Adicionar**.
   
    ![adicionar modelo de vm][add vm template]
   
    Isso mostrará os campos em que você insere detalhes sobre o modelo que está criando.
   
    ![configuração geral em branco][blank general configuration]
5. Na caixa **Nome**, especifique um nome de serviço de nuvem do Azure. Se o nome informado se referir a um serviço de nuvem existente, a máquina virtual será provisionada nesse serviço. Caso contrário, o Azure criará um novo.
6. Na caixa **Descrição**, especifique o texto que descreve o modelo que você está criando. Essas informações servem apenas para seus registros e não são usadas no provisionamento de uma máquina virtual.
7. A caixa **Rótulos** é usada para identificar o modelo que você está criando e, mais à frente, é usado para mencionar o modelo ao criar um trabalho do Jenkins. Para nosso objetivo, digite **linux** nessa caixa.
8. Na lista **Região**, clique na região em que a máquina virtual será criada.
9. Na lista **Tamanho da Máquina Virtual**, clique no tamanho apropriado.
10. Na caixa **Nome da Conta de Armazenamento**, especifique uma conta de armazenamento em que a máquina virtual será criada. Verifique se ela está na mesma região do serviço de nuvem que será usado. Se desejar criar um novo armazenamento, deixe essa caixa em branco.
11. O tempo de retenção especifica o número de minutos antes de o Jenkins excluir um subordinado ocioso. Deixe o valor padrão de 60. Também é possível desligar o subordinado, em vez de excluí-lo quando estiver ocioso. Para isso, marque a caixa de seleção **Somente Desligamento (Não Excluir) Após Tempo de Retenção**.
12. Na lista **Uso**, clique na condição apropriada quando esse nó subordinado será usado. Por enquanto, clique em **Utilizar este nó o máximo possível**.
    
     Neste momento, seu formulário deverá ser semelhante a este:
    
     ![configuração do modelo geral de ponto de verificação][checkpoint general template config]
    
     A próxima etapa é fornecer detalhes sobre a imagem do sistema operacional na qual você deseja que o subordinado seja criado.
13. Na caixa **Família ou ID da Imagem**, você precisa especificar qual imagem do sistema será instalada em sua máquina virtual. Você pode selecionar em uma lista de famílias de imagens ou especificar uma imagem personalizada.
    
     Caso deseje selecionar em uma lista de famílias de imagens, digite o primeiro caractere (diferencia maiúsculas de minúsculas) do nome da família de imagens. Por exemplo, se você digitar **U**, será exibida uma lista das famílias de servidores do Ubuntu. Depois de selecionar uma na lista, o Jenkins usará a versão mais recente da imagem do sistema dessa família ao provisionar sua máquina virtual.
    
     ![exemplo de lista de imagens de SO][OS Image list sample]
    
     Se você tiver uma imagem personalizada que deseja usar, insira o nome dessa imagem. Os nomes de imagens personalizadas não são mostrados em uma lista; por isso, você precisará garantir que o nome foi digitado corretamente.
    
     Para este tutorial, digite **U** para exibir uma lista de imagens do Ubuntu e clique em **Ubuntu Server 14.04 LTS**.
14. Na lista **Método de Inicialização**, clique em **SSH**.
15. Copie o script abaixo e cole-o na caixa **Script de Inicialização**.
    
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
    
     O script de inicialização será executado após a criação da máquina virtual. Neste exemplo, o script instala Java, Git e ant.
16. Nas caixas **Nome de Usuário** e **Senha**, insira valores de sua preferência para a conta de administrador que será criada na sua máquina virtual.
17. Clique em **Verificar Modelo** para verificar se os parâmetros especificados são válidos.
18. Clique em **Salvar**.

## Criar um trabalho do Jenkins executado em um nó subordinado no Azure
Nesta seção, você criará uma tarefa do Jenkins que será executada em um nó subordinado no Azure. Você precisará ter seu próprio projeto no GitHub para continuar.

1. No painel do Jenkins, clique em **New Item**.
2. Digite um nome para a tarefa que você está criando.
3. Para o tipo de projeto, clique em **Projeto Freestyle**.
4. Clique em **OK**.
5. Na página de configuração de tarefa, selecione **Restrict where this project can be run**.
6. Na caixa **Expressão de Rótulo**, digite **linux**. Na seção anterior, criamos um modelo subordinado chamado **linux**, que é o que estamos especificando aqui.
7. Na seção **Build**, clique em **Add build step** e selecione **Execute shell**.
8. Edite o seguinte script, substituindo **(nome da sua conta do GitHub)**, **(nome do projeto)** e **(diretório do projeto)** por valores apropriados e cole o script editado na área de texto exibida.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e (your project directory) ]; then
   
              cd (your project directory)
   
              git pull origin master
   
        else
   
              git clone https://github.com/(your GitHub account name)/(your project name).git
   
        fi
   
        # change directory to project
   
        cd $currentDir/(your project directory)
   
        #Execute build task
   
        ant
9. Clique em **Salvar**.
10. No painel do Jenkins, passe o mouse sobre a tarefa que acabou de criar e clique na seta suspensa para exibir opções de tarefa.
11. Clique em **Compilar agora**.

Em seguida, o Jenkins criará um nó subordinado usando o modelo criado na seção anterior e executará o script que você especificou na etapa de compilação dessa tarefa.

## Próximas etapas
Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[perfil de assinatura]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png

<!---HONumber=AcomDC_0921_2016-->