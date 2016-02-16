<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: Introdução | Microsoft Azure"
	description="Introdução aos Serviços de Domínio do Active Directory do Azure"
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
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Serviços de Domínio do AD do Azure *(Visualização)* - Introdução

## Etapa 3: Habilitar os Serviços de Domínio do AD do Azure
Nesta etapa, você pode habilitar os Serviços de Domínio do AD do Azure para seu diretório. Execute as seguintes etapas de configuração para habilitar os Serviços de Domínio do AD do Azure para seu diretório.

1. Navegue no **portal de gerenciamento do Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. No painel esquerdo, selecione o nó **Active Directory**.
3. Selecione o locatário do AD do Azure (diretório) para o qual você deseja habilitar os Serviços de Domínio do AD do Azure.

    ![Selecionar um diretório do AD do Azure](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **Configurar**.

    ![Guia Configurar do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Role para baixo até uma seção chamada **serviços de domínio**.

    ![Seção Configuração dos Serviços de Domínio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Alterne a opção chamada **Habilitar serviços de domínio para este diretório** para **SIM**. Você verá que algumas opções adicionais de configuração para serviços de domínio do AD do Azure são exibidas na página.

    ![Habilitar Serviços de Domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Quando você habilitar os Serviços de Domínio do AD do Azure para seu locatário, o AD do Azure irá gerar e armazenar os hashes de credencial de NTML e Kerberos necessários para autenticar usuários.

7. Especifique o **nome de domínio DNS dos serviços de domínio**.
   - O nome de domínio padrão do diretório (ou seja, terminando com sufixo de domínio **.onmicrosoft.com**) será selecionado por padrão.
   - A lista contém todos os domínios configurados para o diretório do AD do Azure, incluindo domínios verificados e não verificados que você configura na guia "Domínios".
   - Além disso, você também pode adicionar um nome de domínio personalizado a essa lista, basta digitá-lo.

     > [AZURE.WARNING] Certifique-se de que o prefixo de domínio do nome do domínio especificado por você (por exemplo, "contoso" no nome de domínio “contoso.local”) seja inferior a 15 caracteres. Você não pode criar um domínio dos Serviços de Domínio do AD do Azure com um prefixo de domínio maior do que 15 caracteres.

8. A próxima etapa é selecionar uma rede virtual na qual você gostaria que os Serviços de Domínio do AD do Azure estivesse disponível. Selecione a rede virtual que você acabou de criar na lista suspensa chamada **Conectar serviços de domínio a essa rede virtual**.
   - Certifique-se de que a rede virtual especificada pertença a uma região do Azure com suporte dos Serviços de Domínio do AD do Azure.
   - Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do AD do Azure estão disponíveis.

9. Quando você tiver terminado de selecionar as opções acima, clique em **Salvar** no painel de tarefas na parte inferior da página para habilitar os Serviços de Domínio do AD do Azure.
10. A página exibirá um estado "Pendente...", enquanto os Serviços de Domínio do AD do Azure estiver sendo habilitado para seu diretório.

    ![Habilitar Serviços de Domínio - estado pendente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Os Serviços de Domínio do AD do Azure fornecem alta disponibilidade para seu domínio gerenciado. Quando você habilitar os serviços de domínio do AD do Azure para seu domínio, verá os endereços IP em que os Serviços de Domínio estão disponíveis na rede virtual aparecerem um por um. O segundo endereço IP será exibido em breve, assim que o serviço permitir a alta disponibilidade para seu domínio. Quando a alta disponibilidade estiver configurada e ativa para seu domínio, você deverá ver dois endereços IP na seção **serviços de domínio** da guia **Configurar**.

11. Após cerca de 20 a 30 minutos, você verá o primeiro endereço IP em que os Serviços de Domínio estão disponíveis em sua rede virtual no campo **endereço IP** na página **Configurar**.

    ![Serviços de Domínio habilitados - primeiro IP provisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. Quando a alta disponibilidade estiver operacional para seu domínio, você verá dois endereços IP sendo exibidos na página. Esses são os endereços IP em que os Serviços de Domínio do AD do Azure estarão disponíveis em sua rede virtual selecionada. Anote esses endereços IP para que você possa atualizar as configurações de DNS para sua rede virtual. Essa etapa permite às máquinas virtuais na rede virtual se conectarem ao domínio para operações como o ingresso no domínio.

    ![Serviços de Domínio habilitados - os dois IPs provisionados](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Dependendo do tamanho do seu diretório do AD do Azure (número de usuários, grupos etc.), levará algum tempo para que o conteúdo do diretório esteja disponível nos Serviços de Domínio do AD do Azure. Esse processo de sincronização ocorre em segundo plano. Para diretórios maiores com dezenas de milhares de objetos, pode levar um ou dois dias para todos os usuários membros do grupo e credenciais serem sincronizados e disponibilizados nos Serviços de Domínio do AD do Azure.


---
[**Próxima etapa - Atualizar as configurações de DNS para a rede virtual do Azure.**](active-directory-ds-getting-started-dns.md)

<!---HONumber=AcomDC_0211_2016-->