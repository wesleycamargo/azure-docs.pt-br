<properties 
	pageTitle="Configurar uma máquina virtual do Azure para ciência de dados" 
	description="Configure uma Máquina Virtual do Azure para uso em um ambiente de ciência de dados baseado em nuvem com o IPython Server." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Configurar uma máquina virtual do Azure para ciência de dados

Este tópico mostra como provisionar e configurar uma máquina virtual do Azure que pode ser usada como parte de um ambiente de ciência de dados baseado em nuvem.  A máquina virtual do Windows está configurada com ferramentas de suporte como o IPython Notebook, o Azure Storage Explorer e o AzCopy, bem como outros utilitários que são úteis para projetos de ciência de dados.  O Azure Storage Explorer e o AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados no armazenamento de blob do Azure em seu computador local ou baixá-lo em seu computador local por meio do armazenamento de blob.

## <a name="create-vm"></a>Etapa 1:  Criar uma máquina virtual do Azure com finalidade geral

Se você já tiver uma máquina virtual do Azure e apenas deseja configurar um servidor do IPython Notebook nela, é possível ignorar esta etapa e prosseguir para a [Etapa 2:  Adicionar um ponto de extremidade aos IPython Notebooks para uma máquina virtual existente](#add-endpoint). 
 
Antes de iniciar o processo de criação de uma máquina virtual no Azure, você precisa determinar o tamanho da máquina que é necessária para processar os dados para o seu projeto.  Máquinas menores têm menos memória e menos núcleos de CPU que máquinas maiores, mas elas também são mais baratas.  Para obter uma lista dos tipos de máquinas e seus preços, consulte a página [Preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) 

1. Faça logon em https://manage.windowsazure.com e clique em **Novo** no canto inferior esquerdo.  Uma janela será exibida.  Selecione **COMPUTAÇÃO** -> **MÁQUINA VIRTUAL** -> **DA GALERIA**.

	![Create workspace][24]

2. Escolha uma das seguintes imagens: 

	* Windows Server 2012 R2 Datacenter 
	* Experiência do Windows Server Essentials (Windows Server 2012 R2) 

	Em seguida, clique na seta apontando para a direita na parte inferior direita para ir para a próxima página de configuração.
	
	![Create workspace][25]

3. Digite um nome para a máquina virtual que você deseja criar, selecione o tamanho da máquina com base no tamanho dos dados que a máquina processará e a potência que você deseja que a máquina tenha (tamanho da memória e número de núcleos de computação), digite um nome de usuário e senha para a máquina.  Em seguida, clique na seta para a direita para ir para a próxima página de configuração.

	![Create workspace][26]

4. Selecione a **REGIÃO/GRUPO DE AFINIDADE/REDE VIRTUAL** que contém a **CONTA DE ARMAZENAMENTO** que você planeja usar para essa máquina virtual e, em seguida, selecione essa conta de armazenamento.  Adicione um ponto de extremidade na parte inferior no campo **PONTOS DE EXTREMIDADE** digitando o nome do ponto de extremidade ("IPython" aqui).  Você pode escolher qualquer cadeia de caracteres como o **NOME** do ponto de extremidade e qualquer inteiro entre 0 e 65536 que esteja **disponível** como a **PORTA PÚBLICA**.  A **PORTA PRIVADA** deve ser **9999**.  Os usuários devem **evitar** usar portas públicas que já tenham sido atribuídas a serviços de Internet.  [As Portas para serviços de Internet](http://www.chebucto.ns.ca/~rakerman/port-table.html) fornecem uma lista de portas que foram atribuídas e devem ser evitadas. 

	![Create workspace][27]

	>[AZURE.NOTE] Se o ponto de extremidade tiver sido adicionado nesta etapa, a[Etapa 2:  Adicionar um ponto de extremidade para IPython Notebooks para uma máquina virtual existente](#add-endpoint) pode ser ignorada.

5. Clique na marca de seleção para iniciar o processo de provisionamento da máquina virtual. 

	![Create workspace][28]


Pode levar de 15 a 25 minutos para concluir o processo de provisionamento da máquina virtual.  Depois que a máquina virtual tiver sido criada, o status dessa máquina deverá aparecer como **Executando**.

![Create workspace][29]
	
## <a name="add-endpoint"></a>Etapa 2:  Adicionar um ponto de extremidade aos IPython Notebooks para uma máquina virtual existente

Se você criou a máquina virtual seguindo as instruções na Etapa 1, o ponto de extremidade para o IPython Notebook já foi adicionado e essa etapa pode ser ignorada. 

Se a máquina virtual já existir e você precisar adicionar um ponto de extremidade ao IPython Notebook que você instalará na Etapa 3 abaixo, primeiro faça logon no portal de gerenciamento do Azure, selecione a máquina virtual e adicione o ponto de extremidade ao servidor do IPython Notebook.  A figura a seguir contém uma captura de tela do portal após o ponto de extremidade do IPython Notebook ter sido adicionado à máquina virtual do Windows. 

![Create workspace][17]

## <a name="run-commands"></a>Etapa 3:  Instalar o IPython Notebook e outras ferramentas de suporte

Depois que a máquina virtual é criada, use o protocolo RDP para fazer logon na máquina virtual do Windows.  Para obter instruções, consulte [Como fazer logon a uma máquina virtual que executa o Windows Server](../virtual-machines-log-on-windows-server.md).  Abra o **Prompt de comando** (**não a janela de comando do Powershell**) como Administrador e execute o comando a seguir.
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Quando a instalação for concluída, o servidor do IPython Notebook será iniciado automaticamente no diretório  *C:\Users\&#60;user name>\Documents\IPython Notebooks*.

Quando solicitado, digite uma senha para o IPython Notebook e a senha de administrador do computador.  Isso permite que o IPython Notebook seja executado como um serviço no computador. 

## <a name="access"></a>Etapa 4:  Acesse o IPython Notebooks usando um navegador da Web
Para acessar o servidor do IPython Notebook, abra um navegador da Web e insira  *https://&#60;virtual machine DNS name>:&#60;public port number>* na caixa de texto da URL.  Aqui, o *&#60;public port number>* deve ser o número da porta especificado quando o ponto de extremidade do IPython Notebook foi adicionado.  Se você escolher *443* como o número de porta pública, o IPython Notebook pode ser acessado sem especificar explicitamente o número da porta na caixa de texto da URL.  Caso contrário, o **&#60;número da porta pública>* é necessário. 

O *&#60;nome DNS da máquina virtual>* pode ser encontrado no portal de gerenciamento do Azure.  Depois de fazer logon no portal de gerenciamento, clique em **MÁQUINAS VIRTUAIS**, selecione a máquina que você criou e, em seguida, selecione **PAINEL**, o nome DNS será mostrado da seguinte maneira:

![Create workspace][19]

Você encontrará um aviso informando que _Há um problema com o certificado_ de segurança deste site (Internet Explorer) ou a _Sua conexão não é privada_ (Chrome), conforme mostrado nas figuras a seguir.  Clique em **Continuar neste site (não recomendado)** (Internet Explorer) ou **Avançado** e **Continuar em &#60;*Nome DNS*> (perigoso)** (Chrome) para continuar.  Em seguida, insira a senha que você especificou anteriormente para acessar o IPython Notebooks.

Internet Explorer:
![Create workspace][20]

Chrome:
![Create workspace][21]

Depois de fazer logon no IPython Notebook, um diretório  *DataScienceSamples* aparecerá no navegador.  Esse diretório contém o IPython Notebooks de amostra que é compartilhado pela Microsoft para ajudar os usuários a realizar tarefas de ciência de dados.  Esses IPython Notebooks de amostra são verificados no[**Repositório Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) para as máquinas virtuais durante o processo de configuração do servidor do IPython Notebook.  A Microsoft mantém e atualiza esse repositório com frequência.  Os usuários podem visitar o repositório Github para obter a amostra do IPython Notebooks atualizada mais recentemente. 
![Create workspace][18]

## <a name="upload"></a>Etapa 5:  Carregar um IPython Notebook existente de um computador local para o servidor do IPython Notebook

Os IPython Notebooks fornecem uma maneira fácil para que os usuários carreguem um IPython Notebook existente em suas máquinas locais para o servidor do IPython Notebook nas máquinas virtuais.  Depois que os usuários fizerem logon no IPython Notebook em um navegador da Web, clique no **diretório** no qual o IPython Notebook será carregado.  Em seguida, selecione um arquivo. ipynb do IPython Notebook para carregá-lo do computador local no **Gerenciador de Arquivos** e arraste-o e solte-o no diretório IPython Notebook no navegador da Web.  Clique no botão **Carregar** para carregar o arquivo. ipynb para o servidor do IPython Notebook.  Então, outros usuários podem começar a usá-lo em seus navegadores da web.

![Create workspace][22]

![Create workspace][23]


## <a name="shutdown"></a>Desligar e desalocar a máquina virtual quando ela não estiver em uso

As máquinas virtuais do Azure são cobradas como **pague somente pelo que usa**.  Para garantir que você não esteja sendo cobrado quando não estiver usando sua máquina virtual, ela deverá estar no estado **Parado (desalocado)** quando não estiver em uso.

> [AZURE.NOTE] Se você desligar a máquina virtual de dentro da VM (usando as opções de energia do Windows), a VM será interrompida, mas permanecerá alocada.  Para garantir que você não continue a ser cobrado, sempre pare as máquinas virtuais no[Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).  Você também pode interromper a VM por meio do Powershell chamando **ShutdownRoleOperation** com "PostShutdownAction" igual a "StoppedDeallocated".

Desligar e desalocar a máquina virtual:

1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) (a página pode estar em inglês) usando sua conta.  

2. Selecione **MÁQUINAS VIRTUAIS** na barra de navegação à esquerda.

3. Na lista de máquinas virtuais, clique no nome da máquina virtual e acesse a página **PAINEL**.

4. Na parte inferior da página, clique em **DESLIGAR**. 

![VM Shutdown][15]

A máquina virtual será desalocada, mas não excluída.  Você pode reiniciar a máquina virtual a qualquer momento no Portal de Gerenciamento do Azure.

## Sua VM do Azure está pronta para uso: o que vem a seguir?

Agora, a sua máquina virtual está pronta para ser usada em seus exercícios de ciência de dados.  A máquina virtual também está pronta para uso como um servidor do IPython Notebook para a exploração e o processamento de dados e outras tarefas em conjunto com o Aprendizado de Máquina do Azure e o Processo de Ciência de Dados de Nuvem. 

As próximas etapas do processo de ciência de dados são mapeadas no [Guia de aprendizagem:  O processamento avançado de dados no Azure](machine-learning-data-science-advanced-data-processing.md) e pode incluir etapas que movem dados para o HDInsight, o processe-as e demonstre-as na preparação de aprendizado com os dados no Aprendizado de Máquina do Azure.

* Para obter informações sobre como mover dados para HDInsight por meio do armazenamento de blob do Azure, consulte [Criar e carregar dados em tabelas de Hive do armazenamento de blob do Azure](machine-learning-data-science-hive-tables.md).
* Para obter informações sobre o processamento de dados no HDInsight com consultas de Hive, consulte [Enviar consultas Hive para os clusters do HDInsight Hadoop no processo de ciência de dados de nuvem](machine-learning-data-science-process-hive-tables.md).
* Para obter informações sobre os dados de amostragem no HDInsight, consulte [Dados de amostra nas tabelas do Azure HDInsight Hive](machine-learning-data-science-sample-data-hive.md).



[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png




<!--HONumber=49--> 