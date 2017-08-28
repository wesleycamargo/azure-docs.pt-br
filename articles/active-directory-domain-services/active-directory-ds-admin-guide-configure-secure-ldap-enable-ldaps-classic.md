---
title: "Configurar o LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure AD |Microsoft Docs"
description: "Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de começar
Não deixe de concluir a [Tarefa 2 – exportar o certificado de LDAP seguro para um arquivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).

Escolha se deseja usar a experiência de versão prévia do portal do Azure ou o portal clássico do Azure para concluir esta tarefa.
> [!div class="op_single_selector"]
> * **Portal do Azure (Versão prévia)**: [habilitar o LDAP seguro usando o portal do Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Portal clássico do Azure**: [habilitar o LDAP seguro usando o portal do Azure clássico](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>Tarefa 3 – habilitar o LDAP seguro para o domínio gerenciado usando o portal do Azure clássico
Execute as seguintes etapas de configuração para habilitar o LDAP seguro:

1. Navegue até o **[portal clássico do Azure](https://manage.windowsazure.com)**.
2. No painel esquerdo, selecione o nó **Active Directory** .
3. Selecione o diretório do AD do Azure (também conhecido como “locatário”), para o qual você habilitou os Serviços de Domínio do Azure AD.

    ![Selecionar um diretório do AD do Azure](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Clique na guia **Configurar** .

    ![Guia Configurar do diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Role para baixo até a seção chamada **serviços de domínio**. Você verá uma opção chamada **LDAP Seguro (LDAPS)** , como mostra a captura de tela a seguir:

    ![Seção Configuração dos Serviços de Domínio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. Clique no botão **Configurar certificado...** para exibir a caixa de diálogo **Configurar Certificado para LDAP Seguro**.

    ![Configurar Certificado para LDAP Seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. Clique no ícone de pasta após **ARQUIVO PFX COM CERTIFICADO** para especificar o arquivo PFX que contém o certificado que você deseja usar para ter acesso LDAP seguro ao domínio gerenciado. Além disso, insira a senha que você especificou ao exportar o certificado para o arquivo PFX. Em seguida, clique no botão Concluído na parte inferior.

    ![Especificar arquivo PFX e senha de LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. A seção **serviços de domínio** da guia **Configurar** deve ficar esmaecida e permanecerá no estado **Pendente...** por alguns minutos. Durante esse período, a precisão do certificado LDAPS é verificada e o LDAP seguro é configurado para seu domínio gerenciado.

    ![LDAP Seguro - estado pendente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > Demora cerca de 10 a 15 minutos para habilitar o LDAP seguro para seu domínio gerenciado. Se o certificado LDAP seguro fornecido não corresponder aos critérios necessários, LDAP seguro não estará habilitado para o diretório e você verá uma falha. Por exemplo, o nome de domínio está incorreto, o certificado expirou ou expirará em breve.
   >
   >

9. Quando o LDAP seguro for habilitado com êxito para o domínio gerenciado, a mensagem **Pendente...** desaparecerá. Você deverá ver a impressão digital do certificado exibida.

    ![LDAP Seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarefa 4 – habilitar o acesso LDAP seguro pela Internet
**Tarefa opcional** – Ignore esta tarefa de configuração se você não pretende acessar o domínio gerenciado usando LDAPS pela Internet.

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal).

1. Você verá uma opção para **HABILITAR O ACESSO LDAP SEGURO PELA INTERNET** na seção **serviços de domínio** da página **Configurar**. Isso será definido como **NÃO** por padrão, pois o acesso da Internet ao domínio gerenciado por meio de LDAP seguro está desabilitado por padrão.

    ![LDAP Seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. Alterne **HABILITAR ACESSO LDAP SEGURO PELA INTERNET** para **SIM**. Clique no botão **SALVAR** no painel inferior.
    ![LDAP Seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. A seção **serviços de domínio** da guia **Configurar** deve ficar esmaecida e permanecerá no estado **Pendente...** por alguns minutos. Após algum tempo, o acesso à Internet para seu domínio gerenciado por meio de LDAP seguro será habilitado.

    ![LDAP Seguro - estado pendente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > Demora cerca de 10 minutos para habilitar o acesso da Internet por meio de LDAP seguro para seu domínio gerenciado.
   >
   >
4. Quando o acesso LDAP seguro para seu domínio gerenciado pela Internet for habilitado com êxito, a mensagem **Pendente...** desaparecerá. Você deve ver o endereço IP externo que pode ser usado para acessar seu diretório por meio de LDAPS no campo **ENDEREÇO IP EXTERNO PARA ACESSO LDAPS**.

    ![LDAP Seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 5 – configurar o DNS para acessar o domínio gerenciado pela Internet
**Tarefa opcional** – Ignore esta tarefa de configuração se você não pretende acessar o domínio gerenciado usando LDAPS pela Internet.

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 4](#task-4---enable-secure-ldap-access-over-the-internet).

Depois que você habilitar o acesso LDAP seguro pela Internet para seu domínio gerenciado, será necessário atualizar o DNS para que os computadores cliente possam localizar esse domínio gerenciado. No final da tarefa 4, um endereço IP externo é exibido na guia **Configurar**, em **ENDEREÇO IP EXTERNO PARA ACESSO LDAPS**.

Configure seu provedor DNS externo para que o nome DNS do domínio gerenciado (por exemplo, 'ldaps.contoso100.com') aponte para esse endereço IP externo. Em nosso exemplo, precisaremos criar a entrada DNS a seguir:

    ldaps.contoso100.com  -> 52.165.38.113

Isso é tudo - agora você está pronto para se conectar ao domínio gerenciado usando LDAP seguro pela Internet.

> [!WARNING]
> Lembre-se de que os computadores cliente devem confiar no emissor do certificado LDAPS para poderem se conectar com êxito ao domínio gerenciado usando LDAPS. Se você estiver usando uma autoridade de certificação corporativa ou uma autoridade de certificação confiável publicamente, não será necessário fazer qualquer coisa, pois os computadores clientes confiarão nesses emissores de certificado. Se você estiver usando um certificado autoassinado, precisará instalar a parte pública do certificado autoassinado no repositório de certificados confiáveis no computador cliente.
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Bloquear o acesso LDAPS ao domínio gerenciado pela Internet
> [!NOTE]
> **Tarefa opcional** – ignore esta tarefa de configuração se você não tiver habilitado o acesso LDAPS ao domínio gerenciado pela Internet.
>
>

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 4](#task-4---enable-secure-ldap-access-over-the-internet).

Expor seu domínio gerenciado para acesso LDAPS pela Internet representa uma ameaça à segurança. O domínio gerenciado é acessível pela Internet na porta usada para LDAP seguro (ou seja, a porta 636). Portanto, você pode optar por restringir o acesso ao domínio gerenciado a endereços IP conhecidos específicos. Para segurança aprimorada, crie um NSG (grupo de segurança de rede) e associe-o à sub-rede na qual você habilitou o Azure AD Domain Services.

A tabela a seguir ilustra um exemplo de NSG que você pode configurar para bloquear o acesso LDAP seguro pela Internet. O NSG contém um conjunto de regras que permitem o acesso LDAPS de entrada pela porta TCP 636 somente de um conjunto especificado de endereços IP. A regra “DenyAll” padrão se aplica a todos os outros tráfegos de entrada da Internet. A regra de NSG para permitir o acesso LDAPS pela Internet de endereços IP especificados tem prioridade mais alta que a regra de NSG DenyAll.

![Exemplo de NSG para acesso LDAPS seguro pela Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mais informações** - [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)
* [Criar um Grupo de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

