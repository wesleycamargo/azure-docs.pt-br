<properties
	pageTitle="Preview dos Serviços de Domínio do Azure Active Directory: Guia de Administração | Microsoft Azure"
	description="Ingresse uma máquina virtual do Red Hat Enterprise Linux nos Serviços de Domínio do Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/20/2016"
	ms.author="maheshu"/>

# Ingressar uma máquina virtual do Red Hat Enterprise Linux 7 em um domínio gerenciado
Este artigo mostra como ingressar em uma máquina virtual do RHEL (Red Hat Enterprise Linux) 7 em um domínio gerenciado dos Serviços de Domínio do Azure AD.

## Provisionar uma máquina virtual do Red Hat Enterprise Linux
Execute as seguintes etapas para provisionar uma máquina virtual do RHEL 7 usando o portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).

    ![Painel do portal do Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Clique em **Novo** no painel à esquerda e digite **Red Hat** na barra de pesquisa, como mostra a captura de tela abaixo. Você deve observar as entradas para Red Hat Enterprise Linux nos resultados da pesquisa. Clique em **Red Hat Enterprise Linux 7.2**.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Os resultados da pesquisa no painel **Tudo** deve listar a imagem do Red Hat Enterprise Linux 7.2. Clique em **Red Hat Enterprise Linux 7.2** para exibir mais informações sobre a imagem da máquina virtual.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. No painel **Red Hat Enterprise Linux 7.2**, você deve ver mais informações sobre a imagem da máquina virtual. Na lista suspensa **Selecionar um modelo de implantação**, selecione **Clássico**. Em seguida, clique no botão **Criar**.

    ![Exibir detalhes da imagem](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. No painel **Criar VM**, insira o **Nome do Host** para a nova máquina virtual. Também especifique um nome de usuário administrador local no campo **Nome de usuário** e uma **Senha**. Você também pode optar por usar uma chave SSH para autenticar o usuário administrador local. Selecione também um **Tipo de Preço** para a máquina virtual.

    ![Criar VM - detalhes básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Clique em **Configuração Opcional**. Isso deve abrir um painel chamado **Configuração opcional**. No painel **Configuração opcional**, clique em **Rede** como mostrado na captura de tela abaixo.

    ![Criar VM - configurar rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Isso deve exibir um painel chamado **Rede**. No painel **Rede**, clique em **Rede Virtual** para selecionar a rede virtual à qual a VM do Linux deve ser implantada. Isso deve abrir o painel **Rede Virtual**. No painel **Rede Virtual**, escolha a opção **Usar rede virtual existente**. Em seguida, selecione a rede virtual na qual os Serviços de Domínio do Azure AD estão disponíveis. Neste exemplo, vamos escolher a rede virtual 'MyPreviewVNet'.

    ![Criar VM - selecionar rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. No painel **Configuração opcional**, clique no botão **OK**.

    ![Criar VM - rede virtual selecionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Agora você está pronto para criar a máquina virtual. No painel **Criar VM**, clique no botão **Criar**.

    ![Criar VM - pronta](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. A implantação da nova máquina virtual com base na imagem do RHEL 7.2 deve começar.

  ![Criar VM - implantação iniciada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Depois de alguns minutos, a máquina virtual deve estar implantada com êxito e pronta para uso.

  ![Criar VM - implantada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## Conectar-se remotamente à máquina virtual do Linux recém-provisionada
A máquina virtual do RHEL 7.2 foi provisionada no Azure. A próxima tarefa é se conectar remotamente à máquina virtual.

**Conectar-se à máquina virtual do RHEL 7.2** siga as instruções no artigo [Como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/virtual-machines-linux-classic-log-on.md).

O restante das etapas a seguir pressupõe que você está usando o PuTTY como o cliente SSH para se conectar à máquina virtual do RHEL. Para obter mais informações, consulte a [PuTTY Download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (Página de download do PuTTY).

1. Abra o programa PuTTY.

2. Insira o **Nome do Host** da máquina virtual do RHEL recém-criada. Neste exemplo, nossa máquina virtual tem o nome de host 'contoso-rhel.cloudapp.net'. Se você não tiver certeza do nome do host da VM, consulte o painel da VM no portal do Azure.

    ![Conectar no PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Faça logon na máquina virtual usando as credenciais de administrador local que você especificou quando a máquina virtual foi criada. Neste exemplo, estávamos usando a conta de administrador local 'mahesh'.

    ![Logon no PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## Instalar os pacotes necessários na máquina virtual do Linux
Depois de se conectar à máquina virtual, a próxima tarefa é instalar os pacotes necessários para o ingresso no domínio na máquina virtual. Execute as seguintes etapas:

1. **Instalar o realmd:** o pacote realmd é usado para o ingresso no domínio. No terminal do PuTTY, digite o comando a seguir.

    sudo yum install realmd

    ![Instalar o realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Depois de alguns minutos, o pacote realmd deve ser instalado na máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Instalar o sssd:** O pacote realmd depende do sssd para executar as operações de ingresso no domínio. No terminal do PuTTY, digite o comando a seguir.

    sudo yum install sssd

	![Instalar o sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Depois de alguns minutos, o pacote sssd deve ser instalado na máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Instalar o kerberos:** no terminal do PuTTY, digite o comando a seguir.

    sudo yum install krb5-workstation krb5-libs

	![Instalar o kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

	Depois de alguns minutos, o pacote realmd deve ser instalado na máquina virtual.

	![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## Ingressar a máquina virtual do Linux no domínio gerenciado
Agora que os pacotes necessários são instalados na máquina virtual do Linux, a próxima tarefa é ingressar a máquina virtual no domínio gerenciado.

1. Descubra o domínio gerenciado dos Serviços de Domínio do AAD. No terminal do PuTTY, digite o comando a seguir.

    sudo realm discover CONTOSO100.COM

	![Realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

	Se **realm discover** não conseguir localizar seu domínio gerenciado, certifique-se de que o domínio esteja acessível da máquina virtual (tente executar o ping). Além disso, verifique se a máquina virtual, de fato, foi implantada na mesma rede virtual na qual o domínio gerenciado está disponível.

2. Inicialize o kerberos. No terminal do PuTTY, digite o comando a seguir. Certifique-se de especificar um usuário que pertence ao grupo 'Administradores do DC do AAD’. Apenas esses usuários podem ingressar computadores ao domínio gerenciado.

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Certifique-se de especificar o nome do domínio em letras maiúsculas, caso contrário, o kinit falhará.

3. Ingresse a máquina no domínio. No terminal do PuTTY, digite o comando a seguir. Certifique-se especificar o mesmo usuário que aquele especificado na etapa acima ('kinit').

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

	![Ingresso no realm](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Você deverá receber uma mensagem ("Computador registrado com êxito no realm") quando a máquina for ingressada com êxito no domínio gerenciado.


## Verificar o ingresso no domínio
Você pode verificar rapidamente se a máquina ingressou com êxito no domínio gerenciado. Isso pode ser feito conectando-se à VM do RHEL recém-ingressada no domínio usando o ssh e uma conta de usuário do domínio e, em seguida, verificando para ver se a conta de usuário está sendo resolvida corretamente.

1. No terminal do PuTTY, digite o seguinte comando para se conectar à máquina virtual do RHEL recém-ingressada no domínio usando SSH. Use uma conta de domínio que pertença à conta gerenciada (por exemplo. 'bob@CONTOSO100.COM' neste caso.)

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. No terminal do PuTTY, digite o seguinte comando para ver se o diretório base do usuário foi inicializado corretamente.

	pwd

3. No terminal do PuTTY, digite o seguinte comando para ver se os membros do grupo do usuário estão sendo resolvidos corretamente.

    ID

Abaixo está uma saída de exemplo desses comandos.

![Verificar o ingresso no domínio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## Solucionando problemas de ingresso no domínio
Consulte o artigo [Troubleshooting domain join](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) (Solucionando problemas de ingresso no domínio).


## Mais informações
- [Como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/virtual-machines-linux-classic-log-on.md).
- [Installing Kerberos](https://access.redhat.com/documentation/pt-BR/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html) (Instalando o Kerberos)
- [Red Hat Enterprise Linux 7 - Windows Integration Guide](https://access.redhat.com/documentation/pt-BR/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html) (Red Hat Enterprise Linux 7: Guia de integração do Windows)

<!---HONumber=AcomDC_0622_2016-->