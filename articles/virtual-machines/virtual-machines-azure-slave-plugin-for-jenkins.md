---
title: "Como usar o plug-in subordinado do Azure com a solução de integração contínua Jenkins | Microsoft Docs"
description: "Descreve como usar o plug-in subordinado do Azure com a solução de integração contínua Jenkins."
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Como usar o plug-in subordinado do Azure com a solução de integração contínua Jenkins
O plug-in subordinado do Azure para Jenkins facilita o provisionamento de nós subordinados no Azure ao executar compilações distribuídas e dá suporte à criação de:

* Servidores subordinados do Windows na Nuvem do Azure usando SSH e o JNLP (Protocolo de Inicialização de Rede do Java )
  
  * Para iniciar uma imagem do Windows via SSH, a imagem precisa ser pré-configurada com o SSH.
  * Para obter informações sobre como preparar uma imagem personalizada do Windows, consulte [Como capturar uma máquina virtual do Windows no modelo de implantação do Resource Manager][windows-image-capture].
* Servidores subordinados do Linux na Nuvem do Azure usando o SSH
  
  * Para obter informações sobre como preparar uma imagem personalizada do Linux, consulte [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][linux-image-capture].

### <a name="prerequisites"></a>Pré-requisitos
Antes de realizar as etapas neste artigo, você precisa registrar e autorizar o aplicativo cliente e, em seguida, recuperar a ID do cliente e o Segredo do Cliente que será enviado ao Azure Active Directory durante a autenticação. Para saber mais sobre esses pré-requisitos, confira os artigos a seguir:

* [Integração de aplicativos com o Azure Active Directory][integrate-apps-with-AAD]
* [Registrar um aplicativo cliente][register-client-app]

Além disso, para concluir as etapas na seção [Criar um Trabalho Jenkins que é executado em um Nó Subordinado no Azure](#create-jenkins-project) deste artigo, você precisará ter uma configuração de projeto no GitHub.

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Instalar o plug-in subordinado do Azure
1. No painel do Jenkins, clique em **Manage Jenkins**.
   
    ![Gerenciar o Jenkins][jenkins-dashboard-manage]
2. Na página **Gerenciar Jenkins**, clique em **Gerenciar Plug-ins**.
   
    ![Gerenciar plug-ins][jenkins-manage-plugins]
3. Clique na guia **Disponível** e coloque "Azure" como um filtro e selecione o **Plug-in subordinado do Azure**. 
   
    ![Plug-in Subordinado do Azure][search-plugins]
   
    Se você escolher rolar a lista de plug-ins disponíveis, encontrará o plug-in subordinado do Azure na seção **Gerenciamento de Cluster e Build Distribuído**.
4. Clique em **Instalar sem reiniciar** ou **Baixar agora e instalar após a reiniciar**.
   
    ![Instalar o plug-in][install-plugin]

Agora que o plug-in está instalado, as próximas etapas são configurar o plug-in com seu perfil de assinatura do Azure e criar um modelo que será usado na criação da máquina virtual para o nó subordinado.

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>Configurar o Plug-in subordinado do Azure para usar seu Perfil de assinatura
Um perfil de assinatura, também conhecido como configurações de publicação, é um arquivo XML que contém credenciais seguras e informações adicionais, das quais você precisará para trabalhar com o Azure no ambiente de desenvolvimento. Para configurar o plug-in subordinado do Azure, você precisará do seguinte:

* A ID de sua assinatura
* Um certificado de gerenciamento da sua assinatura

Eles podem ser encontrados no seu [perfil de assinatura], que pode ser semelhante ao seguinte exemplo:

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
   
    ![Gerenciar o Jenkins][jenkins-dashboard-manage]
2. Clique em **Configure System**.
   
    ![Configurar sistema][jenkins-configure-system]
3. Role a página para encontrar a seção **Cloud** .
4. Clique em **Adicionar uma nova nuvem** e, em seguida, selecione **Microsoft Azure**.
   
    ![Adicionar uma nova nuvem][cloud-section]
   
    Isso mostrará os campos em que você precisa inserir os detalhes da sua assinatura.
   
    ![Configuração de assinatura][subscription-configuration]
5. Copie os valores do certificado de gerenciamento e da ID de assinatura do seu perfil de assinatura e cole-os nos campos apropriados.
   
    Ao copiar o certificado de gerenciamento e a ID de assinatura, não inclua as aspas que contêm os valores.
6. Clique em **Verificar a configuração** para verificar se os parâmetros especificados são válidos e, em seguida, clique em **Salvar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Configurar um Modelo de máquina virtual para o Plug-in subordinado do Azure
Nesta seção você adicionará um modelo de máquina virtual que define os parâmetros que o plug-in subordinado do Azure usará para criar um nó subordinado no Azure. Nas etapas a seguir, você criará um modelo para uma máquina virtual do Ubuntu.

1. No painel do Jenkins, clique em **Manage Jenkins**.
   
    ![Gerenciar o Jenkins][jenkins-dashboard-manage]
2. Clique em **Configure System**.
   
    ![Configurar sistema][jenkins-configure-system]
3. Role a página para encontrar a seção **Cloud** .
4. Na seção **Nuvem**, encontre **Adicionar modelo de máquina virtual do Azure** para a nuvem que você adicionou na seção anterior e, em seguida, clique em **Adicionar**.
   
    ![Adicionar Modelo de máquina virtual do Azure][add-vm-template]
   
    O Jenkins exibirá um formulário que contém os campos em que você insere os detalhes sobre o modelo que você está criando.
   
    ![Configuração geral em branco][blank-general-configuration]
5. Insira as informações de **Configuração geral** para o modelo:
   
   1. Na caixa **Nome**, forneça um nome para o novo modelo; é somente para seu uso e não será usado no provisionamento de uma máquina virtual.
   2. Na caixa **Descrição** , especifique o texto que descreve o modelo que você está criando. Essas informações servem apenas para seus registros e não são usadas no provisionamento de uma máquina virtual.
   3. A caixa **Rótulos** é usada para identificar o modelo que você está criando e, mais à frente, é usado para mencionar o modelo ao criar um trabalho do Jenkins. Para nosso objetivo, digite "**linux**" nessa caixa.
   4. Na lista **Região**, clique na região do Azure em que a máquina virtual será criada.
   5. Na lista **Tamanho da máquina virtual**, escolha o tamanho apropriado no menu suspenso.
   6. Na caixa **Nome da Conta de Armazenamento** , especifique uma conta de armazenamento em que a máquina virtual será criada. Se quiser que o Jenkins crie uma conta de armazenamento usando o valor padrão "jenkinsarmst", você pode deixar essa caixa em branco.
   7. O **Tempo de retenção** especifica o número de minutos antes de o Jenkins excluir um servidor subordinado ocioso. Deixe com o valor padrão de 60.
      
      * Também é possível desligar o subordinado, em vez de excluí-lo quando estiver ocioso. Para isso, marque a caixa de seleção **Somente Desligamento (Não Excluir) Após Tempo de Retenção** .
      * Você também pode especificar 0 se desejar que os servidores subordinados ociosos não sejam excluídos automaticamente.
   8. Na lista **Uso**, você pode escolher entre as seguintes opções:
      
      * **Utilize este nó tanto quanto possível** – O Jenkins pode executar qualquer trabalho no servidor subordinado enquanto estiver disponível.
      * **Deixe este nó apenas para trabalhos associados** – O Jenkins só criará um projeto (ou trabalho) neste nó quando o projeto foi vinculado especificamente para esse nó; isso permite que um servidor subordinado seja reservado para determinados tipos de trabalhos.
      
      Para este tutorial, clique em **Utilize este nó tanto quanto possível**.
      
      Neste momento, seu formulário deverá ser semelhante a este:
      
      ![Configuração do modelo geral][general-template-config]
6. Forneça a **Configuração da imagem** para o modelo:
   
   1. Para a família de imagem, você tem duas opções:
      
      * **Imagem de usuário personalizada** – essa opção exige que você forneça a URL para uma imagem personalizada que está na mesma conta de armazenamento em que você pretende criar nós subordinados.
      * **Referência de imagem** – essa opção requer que você especifique o *Publicador*, a *Oferta* e o *Sku* para uma imagem, que pode ser encontrada na [Marketplace de máquinas virtuais do Azure][azure-images].
        
        Para este tutorial, escolha **Referência de imagem** e use os seguintes valores:
      * **Publicador de imagem**: Canonical
      * **Oferta de imagem**: UbuntuServer
      * **Sku de imagem**: 14.04.4-LTS
   2. Para a lista **Método de Inicialização**, você tem duas opções: **SSH** ou **JNLP**. Para fins deste tutorial, escolha **SSH**. No entanto, existem várias advertências que você precisa considerar ao escolher o método de inicialização:
      
      * Servidores subordinados do Linux só podem ser iniciados usando o SSH.
      * Servidores subordinados do Windows podem usar o SSH ou JNLP, mas quando o SSH é usado para servidores subordinados do Windows, a imagem deve ser preparada personalizada com um servidor SSH.
        
        Quando estiver usando JNLP como o método de inicialização, você precisa verificar se todos os seguintes itens estão configurados:
      * O servidor subordinado do Azure precisa ser capaz de alcançar a URL do Jenkins, portanto você precisa configurar os firewalls adequadamente. É possível encontrar a URL do Jenkins clicando em **Gerenciar Jenkins**, em seguida, **Configurar sistema** e procurar a seção **Localização do Jenkins**.
   3. O servidor subordinado do Azure que é iniciado usando JNLP precisa ser capaz de alcançar a porta de TCP Jenkins; por esse motivo, é recomendável que você use uma porta TCP fixa para que possa configurar os firewalls adequadamente. Você pode especificar a porta TCP Jenkins clicando em **Gerenciar Jenkins**, em seguida, **Configurar segurança Global**, verifique as opções para **Habilitar segurança** e use uma porta **Fixa** e especifique a porta a ser usada.
   4. Para o **Script de Inicialização**, você precisa fornecer um script de inicialização que será executado depois que a máquina virtual for criada com as seguintes considerações:
      
      * No mínimo, o script deve instalar o Java.
      * Se você estiver usando JNLP, o script de inicialização deve ser escrito no PowerShell.
      * Se for esperado que o script de inicialização deva demorar muito tempo para executar, por exemplo, para instalar uma grande quantidade de pacotes, é recomendável que se prepare uma imagem personalizada com todo o software necessário pré-instalado em vez de instalá-lo por meio de script de inicialização.
        
        Para esse tutorial, copie o script abaixo e cole-o na caixa **Script de Inicialização**:
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        No exemplo acima, o script de inicialização instala *Java*, *Git* e *ant*.
   5. Nas caixas **Nome de Usuário** e **Senha**, insira valores de sua preferência para a conta de administrador que será criada na sua máquina virtual.
      
      Neste momento, seu formulário deverá ser semelhante a este:
      
      ![Configuração da imagem Jenkins][jenkins-image-configuration]
7. Clique em **Verificar Modelo** para verificar se os parâmetros especificados são válidos e, em seguida, clique em **Salvar**.
   
    ![O Jenkins salva o modelo][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Criar um trabalho do Jenkins que pode ser executado em um nó subordinado no Azure
Nesta seção, você criará uma tarefa do Jenkins que será executada em um nó subordinado no Azure. Para concluir essas etapas, você precisará ter um projeto no GitHub.

1. No painel do Jenkins, clique em **New Item**.
   
    ![Novo Item do painel do Jenkins][jenkins-dashboard-new-item]
2. Insira um nome para a tarefa que você está criando, clique em **Freestyle project** para o tipo de projeto e clique **OK**.
   
    ![Criar novo item do Jenkins][jenkins-create-new-item]
3. Na página de configuração da tarefa, selecione **Restringir onde esse projeto pode ser executado** e digite "**linux**" na caixa **Expressão de rótulo**; esse valor corresponde ao rótulo do modelo subordinado que você criou na seção anterior.
   
    ![Restringir Novo Item do Jenkins][jenkins-new-item-restrict]
4. No seção **Build**, clique em **Adicionar etapa de build** e selecione **Executar shell**.
   
    ![Build de Novo Item do Jenkins][jenkins-new-item-build]
5. Edite o seguinte script, substituindo **(nome da sua conta do GitHub)**, **(nome do projeto)** e **(diretório do projeto)** por valores apropriados e cole o script editado na área de texto exibida.
   
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
   
    Seu formulário deverá se parecer com o exemplo a seguir:
   
    ![Script de novo item do Jenkins][jenkins-new-item-script]
6. Quando você tiver concluído todas as etapas de configuração, clique em **Salvar**.
   
    ![Salvar novo item do Jenkins][jenkins-new-item-save]
7. No painel do Jenkins, clique na seta suspensa ao lado do projeto que você acabou de criar e, em seguida, clique em **Compilar agora**.
   
    ![Compilar agora do Jenkins][jenkins-build-now]

O Jenkins criará um nó subordinado usando o modelo criado na seção anterior e executará o script que você especificou na etapa de build dessa tarefa.

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>Considerações ao trabalhar com Modelos de imagem
As seções a seguir contêm algumas informações úteis para configurar vários modelos.

### <a name="when-you-are-using-ubuntu-image-templates"></a>Quando você estiver usando Modelos de imagem do Ubuntu
* Se o script de inicialização for demorar muito tempo para executar, por exemplo, para instalar uma grande quantidade de pacotes, é recomendável que você prepare uma imagem personalizada do Ubuntu com todo o software necessário pré-instalado em vez de instalá-lo por meio de um script de inicialização.
* No mínimo, o script deve instalar o Java. Conforme mostrado nos exemplos anteriores, você pode usar o script a seguir para instalar o Ant, o Git e o Java.
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Quando você estiver usando Modelos de imagem do Windows e o método de inicialização JNLP
* Se o mestre Jenkins não tiver a segurança configurada:
  
  * Deixe o campo **Script de inicialização** em branco para que o script padrão seja executado no servidor subordinado.
* Se o mestre Jenkins tem a segurança configurada: 
  
  * Copie o script de [Instalação de servidores subordinados do Windows][windows-slaves-setup] e modifique-o com suas credenciais Jenkins.
  * No mínimo, o script precisa ser modificado com um token da API e uma ID de usuário Jenkins. Para recuperar o token da API para um usuário, use as seguintes etapas:
    
    1. No painel do Jenkins, clique em **Pessoas**.
    2. Clique na conta de usuário apropriada.
    3. Clique em **Configurar** no menu à esquerda.
    4. Clique em **Mostrar Token da API...**.

<a name="see-also"></a>

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

Para obter informações adicionais sobre o Plug-in Subordinado do Azure para Jenkins, consulte o projeto [Plug-in Subordinado do Azure] no GitHub.

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[Plug-in Subordinado do Azure]: https://github.com/jenkinsci/azure-slave-plugin/
[perfil de assinatura]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


