<properties linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Adicionar uma Máquina Virtual à Rede Virtual" pageTitle="Adicionar uma Máquina Virtual à Rede Virtual - Azure" metaKeywords="" description="Um tutorial que ensina a criar uma conta de armazenamento e uma VM (Máquina Virtual) que você adiciona a uma Rede Virtual do Azure." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Adicionar uma Máquina Virtual a uma Rede Virtual" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnet3">Adicionar uma máquina virtual a uma rede virtual</h1>

<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

Este tutorial explica as etapas para criar uma conta de armazenamento do Azure e uma máquina virtual que você adiciona a uma [rede virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx).

Este tutorial pressupõe que você não tem experiência anterior com o Azure.

<div class="dev-callout"> 
<b>Importante</b>

<p>Se você planejar criar uma VM para instalar uma nova floresta do Active Directory, siga as instruções em <a href="../active-directory-forest/">Instalar uma nova floresta do Active Directory no Azure</a>.</p>
</div>


## Objetivos ##

Neste tutorial, você irá aprender:

-  <a href="#CreateStorageAcct">Como criar uma conta de armazenamento</a>

-  <a href="#CreateVM">Como criar uma Máquina Virtual e implantá-la em uma Rede Virtual</a>

## Pré-requisitos ##

- Concluir um dos seguintes tutoriais: 

	-  [Criar uma Rede Virtual no Azure](/pt-br/manage/services/networking/create-a-virtual-network/)

		-OU- 
	-  [Criar uma rede virtual para conectividade entre instalações](/pt-br/manage/services/networking/cross-premises-connectivity/)

- Conta do Windows Live com pelo menos uma assinatura válida e ativa.	

- Nomes dos seguintes procedimentos de [Criar uma Rede Virtual no Azure](/pt-br/manage/services/networking/create-a-virtual-network/) ou [Criar uma rede virtual para conectividade entre instalações](/pt-br/manage/services/networking/cross-premises-connectivity/)

	-	Grupo de afinidade atribuído a sua rede virtual.

	-	Nome de sua rede virtual.

	-   Nomes de suas sub-redes.

## <a name="CreateStorageAcct">Criar conta de armazenamento</a> ##

1.	Depois de criar sua rede virtual no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/), no canto inferior à esquerda da tela, clique em **Nova**.

	![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.	No painel de navegação, clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e, em seguida, em **CRIAÇÃO RÁPIDA**.

	![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.	Insira as informações a seguir e clique na marca de seleção na parte inferior direita da tela.

-  **URL:** digite *yourstorage*.

-  **GRUPO DE REGIÃO/AFINIDADE:** na lista suspensa, selecione **YourAffinityGroup**

-  **HABILITAR REPLICAÇÃO GEOGRÁFICA:** deixe essa caixa marcada.
 
	![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.	Na página **Armazenamento**, a coluna **STATUS** exibirá **Online** quando o processo for concluído.
 
	![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)


## <a name="CreateVM">Como criar uma Máquina Virtual e implantá-la em uma Rede Virtual</a> ##
**Para criar uma máquina virtual e implantá-la em uma rede virtual:**

1.	Depois que você criou sua conta de armazenamento, no canto inferior esquerdo da tela, clique em **Nova**.

	![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)


2.	No painel de navegação, clique em **COMPUTAÇÃO**, **MÁQUINA VIRTUAL** e, em seguida, clique em **DA GALERIA**.
 
	![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)


3.	Na tela **Seleção do Sistema Operacional da VM**, selecione **Windows Server 2008 R2 SP1, outubro de 2012** (ou a versão mais recente disponível) e clique na seta de avanço.
 
	![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)


4.	Na tela **Configuração de máquina Virtual**, digite as informações a seguir e, em seguida, clique na seta de avanço. 
	<!-- DEVEMOS INFORMAR AOS USUÁRIOS PARA ANOTAR O NOME DO USUÁRIO E A SENHA?? -->

	**Dica:** anote o nome do usuário e a senha porque essas são as credenciais que você usará para fazer logon em sua nova máquina virtual.

-  **NOME DA MÁQUINA VIRTUAL:** digite *YourVMachine*

-  **NOME DO NOVO USUÁRIO:** somente leitura.

-  **NOVA SENHA:** digite uma senha forte.

-  **CONFIRMAR SENHA:** digite a senha novamente.

-  **TAMANHO:** selecione **Pequeno**.
 
	![VMConfig](. / media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.	Na tela **Modo da máquina Virtual**, digite as informações a seguir e, em seguida, clique na seta de avanço.

-  **Máquina Virtual Autônoma:** mantenha essa opção selecionada.

-  **NOME DNS:** digite *yourcloudapplication*.

-  **CONTA DE ARMAZENAMENTO:** selecione **yourstorage**.

-  **GRUPO/REDE VIRTUAL DE REGIÃO/AFINIDADE:** na lista suspensa, selecione **YourVirtualNetwork**
 
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.	Na tela **Opções de máquina Virtual**, digite as informações a seguir e, em seguida, clique no botão de marca de seleção. Sua máquina virtual será criada agora. Pode levar até 10 minutos para que a nova máquina seja criada.
	<!-- CONFIRMAR QUANTO TEMPO PODE LEVAR EM MÉDIA PARA A VM SER CRIADA -->

-  **CONJUNTO DE DISPONIBILIDADE:** Selecione **nenhum**.

-  **SUB-REDES DE REDE VIRTUAL:** selecione **FrontEndSubnet**.
	
	<div class="dev-callout"> 
	<b>OBSERVAÇÃO</b> 

	<p>Você deve selecionar pelo menos uma sub-rede e NÃO selecionar a sub-rede do gateway.</p>
	</div> 
 
	![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

7.	Quando sua máquina virtual for criada, na tela de máquinas virtuais, o **STATUS** será **Executando**.
 
	![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)


8.	No painel de navegação, clique em **TODOS OS ITENS**. Todos os objetos que você criou serão exibidos com seu status atual.
 
	![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## Próximas etapas ##
para instalar um controlador de domínio adicional para um domínio do Active Directory local na VM que você acabou de criar, consulte [Instalar uma réplica do Controlador de Domínio do Active Directory Rede Virtual do Azure (a rede pode estar em inglês)](/pt-br/manage/services/networking/replica-domain-controller/)

## Consulte também

-  [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Configurar uma rede Virtual usando arquivos de configuração de rede](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156097.aspx)

<!-- LINKS -->

[wa_com]: http://manage.windowsazure.com/
[Tut2_VN]: ..Tutorial2_CreateVNetCrossPrem 
[Tut1_VN]: ..Tutorial1_CreateVirtualNetwork
























