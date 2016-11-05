---
title: Enviar trabalhos para um cluster do Pacote HPC no Azure | Microsoft Docs
description: Saiba como configurar um computador local para enviar trabalhos para um cluster de Pacote HPC no Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 07/15/2016
ms.author: danlep

---
# Enviar trabalhos HPC de um computador local para um cluster de Pacote HPC implantado no Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configure um computador cliente local executando o Windows para executar ferramentas de envio de trabalho do Pacote HPC que se comunicam via HTTPS com um cluster do Pacote HPC no Azure. Isso fornece uma maneira simples e flexível para uma variedade de usuários de enviar trabalhos para um cluster de Pacote HPC baseado em nuvem sem a necessidade de se conectar diretamente à VM do nó principal ou acessar uma assinatura do Azure para executar as ferramentas de envio de trabalho.

![Enviar um trabalho para um cluster no Azure][jobsubmit]

## Pré-requisitos
* **Nó de cabeçalho do HPC Pack implantado em uma VM do Azure** – Recomendamos o uso de ferramentas automatizadas como um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) ou um [script do Azure PowerShell](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implantar o nó de cabeçalho e o cluster. Você precisará do nome DNS do nó principal e as credenciais de um administrador de cluster para concluir as etapas neste artigo.
* **Computador cliente**: você precisará de um computador Windows Client ou Windows Server que possa executar utilitários de cliente do Pacote HPC (consulte os [requisitos do sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se você quiser usar a API REST ou o portal da Web do Pacote HPC para enviar trabalhos, poderá usar qualquer computador cliente de sua escolha.
* **Mídia de instalação do Pacote HPC** - Para instalar os utilitários de cliente do Pacote HPC, o pacote de instalação gratuita para a versão mais recente do Pacote HPC (Pacote HPC 2012 R2) está disponível no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Confira se você baixou a mesma versão do Pacote HPC que está instalada na VM do nó principal.

## Etapa 1: instalar e configurar os componentes Web no nó principal
Para habilitar uma interface REST para enviar trabalhos ao cluster por HTTPS, instale e configure os componentes Web Pacote HPC no nó principal do Pacote HPC, se eles ainda não estiverem configurados. Você primeiro deve instalar os componentes Web executando o arquivo de instalação HpcWebcomponents.msi. Em seguida, configure os componentes executando o script do HPC PowerShell **Set-HPCWebComponents.ps1**.

Para obter procedimentos detalhados, consulte [Instalar os componentes Web do Microsoft Pacote HPC](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Alguns modelos de início rápido do Azure para o HPC Pack instalam e configuram os componentes Web automaticamente. Se você usar o [script de implantação do Pacote HPC IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster, pode, opcionalmente, instalar e configurar os componentes Web como parte da implantação.
> 
> 

**Para instalar os componentes Web**

1. Conecte-se à VM do nó principal usando as credenciais de um administrador de cluster.
2. Na pasta de instalação do Pacote HPC, execute HpcWebcomponents.msi no nó principal.
3. Siga as etapas no Assistente para instalar os componentes Web

**Para configurar os componentes Web**

1. No nó principal, inicie o PowerShell HPC como administrador.
2. Para alterar o diretório para o local do script de configuração, digite o seguinte comando:
   
    ```
    cd $env:CCP_HOME\bin
    ```
3. Para configurar a interface REST e iniciar o serviço Web HPC, digite o seguinte comando:
   
    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Quando for solicitado que você selecione um certificado, escolha o certificado que corresponde ao nome DNS público do nó principal. Por exemplo, se você implantar a VM do nó de cabeçalho usando o modelo de implantação clássica, o nome do certificado terá a forma CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net. Se você usar um modelo de implantação do Gerenciador de Recursos, o nome do certificado terá a forma CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.com.
   
   > [!NOTE]
   > Você precisa selecionar esse certificado para enviar trabalhos posteriormente para o nó principal de um computador local. Não selecione nem configure um certificado que corresponda ao nome do computador do nó principal no domínio do Active Directory (por exemplo, CN=*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Para configurar o portal da Web para envio de trabalho, digite o seguinte comando:
   
    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Depois que o script for concluído, pare e reinicie o serviço do Agendador de trabalho do HPC digitando o seguinte:
   
    ```
    net stop hpcscheduler
    net start hpcscheduler
    ```

## Etapa 2: instalar os utilitários de cliente do Pacote HPC em um computador local
Se você desejar instalar os utilitários do Pacote HPC, baixe os arquivos de instalação do Pacote HPC (instalação completa) pelo [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024) para o computador cliente. Quando você começar a instalação, escolha a opção de instalação dos utilitários cliente do Pacote HPC.

Para usar as ferramentas clientes Pacote HPC para enviar trabalhos para a VM do nó principal, você também precisará exportar um certificado do nó principal e instalá-lo no computador cliente. Você precisará do certificado no. Formato .CER.

**Para exportar o certificado do nó principal**

1. No nó principal, adicione o snap-in de Certificados ao Console de Gerenciamento Microsoft para a conta do computador local. Para obter as etapas para adicionar o snap-in, consulte [Adicionar o Snap-in de Certificados a um MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. Na árvore de console, expanda **Certificados – Computador Local** > **Pessoal** e clique em **Certificados**.
3. Localize o certificado que você configurou para os componentes Web do Pacote HPC na [Etapa 1: Instalar e configurar os componentes Web no nó principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por exemplo, CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net).
4. Clique no certificado, clique em **Todas as Tarefas** e clique em **Exportar**.
5. No Assistente de Exportação de Certificados, clique em **Avançar** e escolha a opção **Não, não exporte a chave particular**.
6. Siga as etapas restantes do Assistente para exportar o certificado no formato DER binário X.509 codificado (.CER).

**Para importar o certificado no computador cliente**

1. Copie o certificado exportado do nó principal para uma pasta no computador cliente.
2. No computador cliente, execute o certmgr.msc.
3. No Gerenciador de Certificados, expanda **Certificados – Usuário atual** > **Autoridades de Certificação Confiáveis**, clique com o botão direito do mouse em **Certificados**, clique em **Todas as Tarefas** e em **Importar**.
4. No Assistente de Importação de Certificados, clique em **Avançar** e siga as etapas para importar o certificado exportado do nó principal para o repositório de Autoridades de Certificação Confiáveis.

> [!TIP]
> Você pode ver um aviso de segurança, já que a autoridade de certificação no nó principal não será reconhecida pelo computador cliente. Para fins de teste, você pode ignorar esse aviso e concluir a importação de certificados.
> 
> 

## Etapa 3: executar trabalhos de teste no cluster
Para verificar a configuração, tente executar trabalhos no cluster no Azure usando o computador local. Por exemplo, você pode usar ferramentas de GUI do Pacote HPC ou comandos de linha de comando para enviar trabalhos para o cluster. Você também pode usar um portal baseado na Web para enviar trabalhos.

**Para executar comandos de envio de trabalho no computador cliente**

1. Em um computador cliente em que os utilitários de cliente do Pacote HPC estão instalados, inicie um Prompt de comando.
2. Digite um comando de exemplo. Por exemplo, para listar todos os trabalhos no cluster, digite um comando semelhante a uma das opções a seguir, dependendo do nome DNS completo do nó principal:
   
    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
   
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Use o nome DNS completo do nó principal, não o endereço IP, na URL do Agendador. Se você especificar o endereço IP, verá um erro semelhante a "O certificado do servidor precisa ter uma cadeia de confiança válida ou ser colocado no repositório raiz confiável".
   > 
   > 
3. Quando solicitado, digite o nome de usuário (com a forma &lt;NomedeDomínio&gt;&lt;NomedeUsuário&gt;) e a senha do administrador do cluster HPC ou de outro usuário do cluster configurado. Você pode optar por armazenar as credenciais localmente para mais operações de trabalho.
   
    Uma lista de trabalhos é exibida.

**Para usar o gerenciador de trabalhos de HPC no computador cliente**

1. Se você não armazenou anteriormente credenciais de domínio para um usuário de cluster no computador cliente ao enviar o trabalho, pode adicionar as credenciais no Gerenciador de Credenciais.
   
    a. No painel de controle no computador cliente, inicie o Gerenciador de Credenciais.
   
    b. Clique nas **Credenciais do Windows** e clique em **Adicionar uma credencial genérica**.
   
    c. Especifique o endereço de Internet (por exemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler) e forneça o nome de usuário (com a forma <NomedeDomínio><NomedeUsuário>) e a senha do administrador do cluster HPC ou de outro usuário do cluster que você configurou.
2. No computador cliente, inicie o gerenciador de trabalho do HPC.
3. Na caixa de diálogo **Selecionar Nó Principal**, digite a URL para o nó principal no Azure (por exemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com).
   
    O gerenciador de trabalhos do HPC abre e mostra uma lista de trabalhos no nó principal.

**Para usar o portal da Web no nó de cabeçalho**

1. Inicie um navegador da Web no computador cliente e digite o seguinte, dependendo do nome DNS completo do nó principal:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
   
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Na caixa de diálogo de segurança, digite as credenciais de domínio do administrador de cluster HPC. (Você também pode adicionar outros usuários do cluster em funções diferentes. Consulte [Gerenciando Usuários de Cluster](https://technet.microsoft.com/library/ff919335.aspx).)
   
    O portal da Web abre no modo de exibição de lista de trabalho.
3. Para enviar um trabalho de exemplo que retorna a cadeia de caracteres "Olá, Mundo" do cluster, clique em **Novo trabalho** no painel de navegação esquerdo.
4. Na página **Novo Trabalho**, em **Das páginas de envio**, clique em **HelloWorld**. A página de envio de trabalho é exibida.
5. Clique em **Enviar**. Se solicitado, forneça as credenciais de domínio do administrador de cluster HPC. O trabalho é enviado e a ID de trabalho aparece na página **Meus Trabalhos**.
6. Para exibir os resultados do trabalho enviado, clique na ID do trabalho e em **Exibir Tarefas** para exibir a saída do comando (em **Saída**).

## Próximas etapas
* Você também pode enviar trabalhos para o cluster do Azure com a [API REST do Pacote HPC](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Se você quiser enviar trabalhos do cluster de um cliente Linux, consulte o exemplo do Python no [SDK do Pacote HPC 2012 R2 e código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

<!---HONumber=AcomDC_0720_2016-->