---
title: "Configurar o LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure AD |Microsoft Docs"
description: "Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 44be67cd5c59a57cafd244ce0a49a6fadf44bdda
ms.openlocfilehash: 8abde97ecb906384cd6e62811fcbb833bdc8be51
ms.lasthandoff: 01/25/2017


---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD
Este artigo mostra como você pode habilitar o protocolo LDAPS para seu domínio gerenciado dos Serviços de Domínio do Azure AD. O LDAP Seguro também é conhecido como “LDAP sobre protocolo SSL/TLS”.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](active-directory-ds-getting-started.md).
4. Um **certificado a ser usado para habilitar o LDAP seguro**.

   * **Recomendado** - Obtenha um certificado da uma autoridade de certificação pública confiável. Essa opção de configuração é a mais segura.
   * Como alternativa, você também pode optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) como mostrado neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Obtenha um certificado válido que esteja de acordo com as diretrizes a seguir, antes de habilitar o LDAP seguro. Você encontrará falhas se tentar habilitar o LDAP seguro para seu domínio gerenciado com um certificado inválido/incorreto.

1. **Emissor confiável** : o certificado deve ser emitido por uma autoridade confiável para os computadores que tenham de se conectar ao domínio usando LDAP seguro. Essa autoridade pode ser uma autoridade de certificação pública de confiança nesses computadores.
2. **Tempo de vida** : o certificado deve ser válido por, pelo menos, os próximos três a seis meses. O acesso LDAP seguro para seu domínio gerenciado é interrompido quando o certificado expira.
3. **Nome da entidade** : o nome da entidade do certificado deve ser um caractere curinga para seu domínio gerenciado. Por exemplo, se o domínio se chamar “contoso100.com”, o nome da entidade do certificado deverá ser “*.contoso100.com”. Defina o nome DNS (nome alternativo da entidade) como esse nome curinga.
4. **Uso de chave** : o certificado deve ser configurado para estas utilizações - assinaturas digitais e codificação de chave.
5. **Finalidade do certificado** : o certificado deve ser válido para autenticação de servidor SSL.

> [!NOTE]
> **Autoridades de Certificação Corporativas:** os Serviços de Domínio do Azure AD não oferecem suporte no momento usando certificados LDAP seguros emitidos pela autoridade de certificação corporativa de sua organização. Essa restrição ocorre porque o serviço não confia em sua autoridade de certificação corporativa como uma autoridade de certificação raiz. Esperamos adicionar suporte para autoridades de certificação corporativas em breve. Se você precisar mesmo usar certificados emitidos por sua autoridade de certificação corporativa, [entre em contato conosco](active-directory-ds-contact-us.md) para obter assistência.
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 – obter um certificado para LDAP seguro
A primeira tarefa envolve a obtenção de um certificado que será usado para acesso LDAP seguro ao domínio gerenciado. Você tem duas opções:

* Obtenha um certificado de uma autoridade de certificação. A autoridade pode ser uma autoridade de certificação pública.
* Crie um certificado autoassinado.

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção A (recomendada) - obter um certificado LDAP seguro de uma autoridade de certificação
Se sua organização obtiver seus certificados de uma autoridade de certificação pública, será necessário obter o certificado LDAP seguro da autoridade de certificação pública.

Ao solicitar um certificado, siga os requisitos descritos em [Requisito para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [!NOTE]
> Computadores cliente que precisam se conectar ao domínio gerenciado usando o LDAP seguro devem confiar no emissor do certificado LDAP seguro.
>
>

### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para LDAP seguro
Se você não pretende usar um certificado de uma autoridade de certificação pública, crie um certificado autoassinado para LDAP seguro.

**Criar um certificado autoassinado usando o PowerShell**

No computador Windows, abra uma nova janela do PowerShell como **Administrador** e digite os comandos a seguir para criar um novo certificado autoassinado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

No exemplo anterior, substitua "contoso100.com" pelo nome de domínio DNS do seu domínio gerenciado dos Azure AD Domain Services.

![Selecionar um diretório do AD do Azure](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado recém-criado é colocado no repositório de certificados do computador local.

## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2 - exportar o certificado LDAP seguro para um arquivo .PFX
Antes de iniciar esta tarefa, verifique se você obteve o certificado LDAP seguro de sua autoridade de certificação pública ou se criou um certificado autoassinado.

Execute as etapas a seguir para exportar o certificado LDAPS para um arquivo .PFX.

1. Pressione o botão **Iniciar** e digite **R**. Na caixa de diálogo **Executar**, digite **mmc** e clique em **OK**.

    ![Iniciar o console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. No prompt **Controle de Conta de Usuário**, clique em **SIM** para iniciar o MMC (Console de Gerenciamento Microsoft) como administrador.
3. No menu **Arquivo**, clique em **Adicionar/Remover Snap-in...**.

    ![Adicionar snap-in ao console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Na caixa de diálogo **Adicionar ou Remover Snap-ins**, escolha o snap-in **Certificados** e clique no botão **Adicionar >**.

    ![Adicionar snap-in de certificados ao console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. No assistente do **Snap-in de certificados**, escolha **Conta de computador** e clique em **Avançar**.

    ![Adicionar snap-in de certificados à conta do computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na página **Selecionar Computador**, escolha **Computador local: (o computador no qual este console está sendo executado)** e clique em **Concluir**.

    ![Adicionar snap-in de certificados - escolher computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Na caixa de diálogo **Adicionar ou Remover Snap-ins**, clique em **OK** para adicionar o snap-in de certificados ao MMC.

    ![Adicionar snap-in de certificados ao MMC - concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Na janela do MMC, clique para expandir **Raiz do Console**. Você deve ver o snap-in de Certificados carregado. Clique em **Certificados (Computador Local)** para expandir. Clique para expandir o nó **Pessoal**, seguido pelo nó **Certificados**.

    ![Abrir repositório de certificados pessoais](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Você deve ver o certificado autoassinado que acabamos de criar. É possível examinar as propriedades do certificado para garantir que a impressão digital corresponda àquela relatada nas janelas do PowerShell quando você criou o certificado.
10. Escolha o certificado autoassinado e **clique com o botão direito do mouse**. No menu de clique com o botão direito do mouse, escolha **Todas as Tarefas** e **Exportar...**.

    ![Exportar o certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. No **Assistente para Exportação de Certificados**, clique em **Avançar**.

    ![Assistente de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na página **Exportar Chave Privada**, escolha **Sim, exporte a chave privada** e clique em **Avançar**.

    ![Chave privada de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Você DEVE exportar a chave privada junto com o certificado. Se você fornecer um PFX que não contém a chave privada do certificado, a habilitação do LDAP seguro para seu domínio gerenciado falhará.
    >
    >
13. Na página **Exportar Formato de Arquivo**, escolha **Personal Information Exchange – PKCS #12 (.PFX)** como o formato de arquivo para o certificado exportado.

    ![Formato de arquivo de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Somente o formato de arquivo .PFX tem suporte. Não exporte o certificado para o formato de arquivo .CER.
    >
    >
14. Na página **Segurança**, escolha a opção **Senha** e digite uma senha para proteger o arquivo .PFX. Guarde essa senha, pois ela será necessária na próxima tarefa. Clique em **Avançar** para continuar.

    ![Senha para exportação de certificado ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Anote essa senha. Você precisa dela ao habilitar o LDAP seguro para este domínio gerenciado na [Tarefa 3 – Habilitar o LDAP seguro para o domínio gerenciado](#task-3---enable-secure-ldap-for-the-managed-domain)
    >
    >
15. Na página **Arquivo a ser Exportado** , especifique o nome do arquivo e o local para onde você deseja exportar o certificado.

    ![Caminho para exportação de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na página seguinte, clique em **Concluir** para exportar o certificado para um arquivo PFX. Você verá o diálogo de confirmação quando o certificado tiver sido exportado.

    ![Exportar certificado concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tarefa 3 – Habilitar o LDAP seguro para o domínio gerenciado
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
7. Clique no ícone de pasta abaixo de **ARQUIVO PFX COM CERTIFICADO** para especificar o arquivo PFX que contém o certificado que você deseja usar para acesso LDAP seguro para o domínio gerenciado. Além disso, insira a senha que você especificou ao exportar o certificado para o arquivo PFX. Em seguida, clique no botão Concluído na parte inferior.

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

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarefa 4 - habilitar o acesso LDAP seguro pela Internet
**Tarefa opcional** – Ignore esta tarefa de configuração se você não pretende acessar o domínio gerenciado usando LDAPS pela Internet.

Antes de iniciar esta tarefa, verifique se você concluiu as etapas descritas na [Tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain).

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

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 5 - configurar o DNS para acessar o domínio gerenciado a partir da Internet
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

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)

