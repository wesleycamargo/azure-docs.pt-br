<properties 
	pageTitle="Instalação personalizada do Azure AD Connect" 
	description="Este documento fornece detalhes sobre as opções de instalação personalizada para o Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Instalação personalizada do Azure AD Connect


A documentação a seguir fornece informações sobre como usar a opção de instalação personalizada para o Azure AD Connect. Você poderá usar essa opção se tiver opções de configuração adicionais ou precisar de recursos opcionais que não são abrangidos pela instalação expressa.

Para obter informações sobre uma instalação expressa, consulte [Instalação expressa](active-directory-aadconnect-get-started/#express-installation-of-azure-ad-connect). Para obter informações sobre como atualizar do DirSync para o Azure AD Connect, consulte [Atualizando o DirSync para o Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)



## Instalar componentes necessários

Quando você instala os serviços de sincronização, pode deixar a seção de configuração opcional desmarcada e o Azure AD Connect configurará tudo automaticamente. Isso inclui a configuração de uma instância do SQL Server 2012 Express, a criação de grupos apropriados e atribuição de permissões a eles. Para alterar os padrões, use a tabela a seguir para entender as opções de configuração opcionais disponíveis.

<center>![Componentes necessários](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png) </center>

Configuração opcional | Descrição 
------------- | ------------- |
Nome do SQL Server |Permite que você especifique o nome do SQL Server e o nome da instância. Escolha essa opção se já tiver o servidor de banco de dados do AD que você quer usar.
Conta de serviço |Por padrão, o Azure AD Connect criará uma conta de serviço local para uso dos serviços de sincronização. O problema é que a senha é gerada automaticamente e desconhecida da pessoa que está instalando o Azure AD Connect. Na maioria dos cenários, isso é bom, mas se você quiser fazer algumas configurações avançadas, como escopo das unidades organizacionais sincronizadas, crie uma conta e escolha sua própria senha. Porém, se você usar um servidor SQL remoto, precisará de um serviço da conta no domínio e precisará conhecer a senha. Nesses casos, insira a conta de serviço a ser usada. |
Permissões | Por padrão o Azure Connect AD criará quatro grupos locais para o servidor quando os serviços de sincronização estiverem instalados. Esses grupos são: grupo de administradores, grupo de operadores, grupo de navegação e grupo de redefinição de senha. Se quiser especificar seus próprios grupos, você pode fazê-lo aqui.


## Entrada do usuário
Depois de instalar os componentes necessários, você será solicitado a especificar o método de logon único que os usuários usarão. A tabela a seguir fornece uma breve descrição das opções disponíveis.

<center>![Entrada de usuário](./media/active-directory-aadconnect-get-started-custom/usersignin.png) </center>


Opção de logon único | Descrição 
------------- | ------------- |
Sincronização de senha |Os usuários poderão entrar nos serviços de nuvem da Microsoft, como Office 365, Dynamics CRM e Windows InTune, usando a mesma senha que usam ao fazer logon em sua rede local. A senha dos usuários é sincronizada no Azure por meio de um hash de senha, e a autenticação ocorre na nuvem.
Federação com o AD FS|Os usuários poderão entrar nos serviços de nuvem da Microsoft, como Office 365, Dynamics CRM e Windows InTune, usando a mesma senha que usam ao fazer logon em sua rede local. Os usuários são redirecionados para sua instância do local ad fs para entrar, e autenticação é feita no local.
Não configurar| Nenhum recurso será instalado e configurado. Escolha essa opção se você já tiver um servidor de federação de terceiros ou outra solução existente em vigor.



## Conectar-se ao AD do Azure
Na tela Conectar ao AD do Azure, digite uma senha e uma conta de administrador global. Verifique se que essa conta não tem a autenticação multifator habilitada. Isso fará com que ele falhe na autenticação. Lembre-se de que essa conta só é usada para criar uma conta de serviço no AD do Azure e não é usada após a conclusão do assistente.

<center>![Entrada de usuário](./media/active-directory-aadconnect-get-started-custom/connectaad.png) </center>

### Conectar seus diretórios
Para se conectar a seu Serviço de Domínio do Active Directory, o Azure AD Connect precisa das credenciais de uma conta com permissões suficientes. Essa conta pode ser uma conta de usuário regular, pois ele precisa apenas de permissões de leitura padrão. No entanto, dependendo do cenário, talvez você precise de permissões adicionais. Para obter mais informações, consulte [Resumo da conta do Azure AD Connect](active-directory-addconnect-account-summary)

<center>![Entrada de usuário](./media/active-directory-aadconnect-get-started-custom/connectdir.png) </center>

### Identificando seus usuários com exclusividade

A correspondência entre os recursos de florestas permite que você defina como os usuários de suas florestas do AD DS são representados no AD do Azure. Um usuário também pode ser representado somente uma vez em todas as florestas ou ter uma combinação de contas habilitadas e desabilitadas.

<center>![Entrada de usuário](./media/active-directory-aadconnect-get-started-custom/unique.png) </center>

Configuração | Descrição 
------------- | ------------- |
Meus usuários são representados apenas uma vez em todas as florestas| Todos os usuários são criados como objetos individuais no AD do Azure.<br> Os objetos não são associados ao metaverso.
Atributo de email | Essa opção associa usuários e contatos quando o atributo de email tem o mesmo valor em florestas diferentes. É recomendável usar essa opção quando seus contatos foram criados com GALSync.
ObjectSID e msExchangeMasterAccountSID|Essa opção associa um usuário habilitado em uma floresta de conta com um usuário desabilitado em uma floresta de recursos do Exchange. Isso também é conhecido como caixa de correio vinculada no Exchange.
sAMAccountName e MailNickName|Essa opção realiza a associação em atributos em que se espera que a ID de logon do usuário possa ser encontrada.
Meu próprio atributo|Essa opção permite que você selecione seu próprio atributo. **Limitação:** escolha um atributo que já exista no metaverso. Se você escolher um atributo personalizado, o assistente não poderá ser concluído.

- **Âncora de origem** - o atributo sourceAnchor é um atributo imutável durante o tempo de vida de um objeto de usuário. É a chave primária de vinculação de usuário local com o usuário no AD do Azure. Como o atributo não pode ser alterado, você deve planejar um bom atributo para usar. Um bom candidato é objectGUID. Esse atributo não será alterado, a menos que a conta de usuário seja movida entre florestas/domínios. Em um ambiente de várias floresta em que você move contas entre florestas, outro atributo deve ser usado, como um atributo com employeeID. Os atributos a serem evitados são aqueles que seriam alterados se uma pessoa casasse ou mudasse de cargo. Você não pode usar atributos com um caractere @. Portanto, email e userPrincipalName não podem ser usados. O atributo também diferencia maiúsculas de minúsculas. Pportanto, se você mover um objeto entre florestas, preserve as maiúsculas/minúsculas. Para atributos binários, o valor é codificado na base 64, mas para outros tipos de atributo, ele permanecerá no estado não codificado. Em cenários de Federação e algumas interfaces do AD do Azure, esse atributo também é conhecido como immutableID.

- **UserPrincipalName** - o atributo userPrincipalName é o atributo que os usuários usarão ao fazer logon no AD do Azure e no Office 365. Os domínios usados, também conhecidos como sufixo UPN, devem ser verificados no AD do Azure antes que os usuários sejam sincronizados. É altamente recomendável manter o atributo padrão userPrincipalName. Se esse atributo não for roteável e não puder ser verificado, será possível selecionar outro atributo, como email, como o atributo contendo a ID de logon. Aviso: usar uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte https://technet.microsoft.com/pt-br/library/dn659436.aspx.





### Filtragem de sincronização com base em grupos
O recurso de filtragem em grupos permite executar um piloto pequeno em que somente um pequeno subconjunto de objetos deve ser criado no AD do Azure e no Office 365. Para usar esse recurso, crie um grupo no Active Directory e adicione os usuários e grupos que devem ser sincronizados com o AD do Azure como membros diretos. Posteriormente, você pode adicionar e remover usuários nesse grupo para manter a lista de objetos que devem estar presentes no AD do Azure. Para usar esse recurso, no caminho personalizado, você verá esta página:

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/filter2.png) </center>

### Recursos opcionais

Essa tela permite que você selecione os recursos opcionais para seus cenários específicos. Abaixo está uma breve explicação de cada um dos recursos individuais.

<center>![Instalação expressa](./media/active-directory-aadconnect-get-started-custom/of.png)</center>


Recursos opcionais | Descrição
-------------------    | ------------- | 
Implantação híbrida do Exchange |O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Azure, sincronizando um conjunto específico de atributos do AD do Azure de volta ao diretório local.
Aplicativo AD do Azure e filtragem de atributos|Ao habilitar o aplicativo AD do Azure e filtragem de atributo, o conjunto de atributos sincronizados pode ser adaptado para um conjunto específico em uma página subsequente do assistente. Isso abre duas páginas de configuração adicionais no assistente.  
Write-back de senha|Ao habilitar o write-back de senha, as alterações de senha que se originam com o AD do Azure serão gravadas no diretório local.
Write-back de usuário|Ao habilitar o write-back de usuário, os usuários criados no AD do Azure serão gravados de volta no diretório local. Isso abre uma página de configuração adicional no assistente.  
Sincronização de atributo de extensão de diretório|Ao habilitar a sincronização de atributo de extensão de diretório, os atributos especificados serão sincronizados com o AD do Azure. Isso abre uma página de configuração adicional no assistente.  

Para opções de configuração adicionais, como alterar a configuração padrão, usando o Editor de regras de sincronização e Provisionamento declarativo, consulte [Gerenciar o Azure AD Connect](active-directory-aadconnect-whats-next.md)




Adicione o nome do grupo que contém os usuários e grupos. Somente os membros desse grupo serão sincronizados no AD do Azure.

## Sincronização de atributo de extensão de diretório
Com as extensões de diretório, você pode estender o esquema no AD do Azure com atributos personalizados adicionados por sua organização ou outros atributos no Active Directory. Para usar esse recurso, selecione "Sincronização de atributo de extensão do diretório" na página "Recursos opcionais". Isso lhe fornecerá essa página, em que você pode selecionar os atributos adicionais.

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/extension2.png) </center>

Há suporte apenas para atributos de valor único, e o valor não pode ter mais de 250 caracteres. O metaverso e o esquema do AD do Azure serão estendidos com os atributos selecionados. No AD do Azure, é adicionado um novo aplicativo com os atributos.

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/extension3.png) </center>

Estes atributos agora estarão disponíveis por meio do Gráfico:

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/extension4.png) </center>

## Write-back de usuário (visualização)
O write-back do usuário permite obter um usuário criado no AD do Azure (por meio do portal, gráfico, PowerShell ou qualquer outro método) e gravar o usuário de volta para o ADDS local. Para habilitar o recurso, selecione "Write-back de usuário" na página de recursos opcionais. Você agora verá o local em que deseja que esses usuários sejam criados. A configuração padrão criará todos os usuários em um único local no AD DS.

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/writeback2.png) </center>
Os usuários serão criados com uma senha aleatória. Portanto, você precisará redefinir a senha no AD DS para o usuário poder realmente fazer logon.

>[AZURE.NOTE]A sincronização de senha e o write-back de senha não são compatíveis com esse recurso de visualização.

## Write-back de grupo (visualização)
A opção para write-back do grupo nos recursos opcionais permitirá que você faça write-back de "Grupos no Office 365" para uma floresta com o Exchange instalado. Esse é um novo tipo de grupo que é sempre controlado na nuvem. Você pode encontrar isso em outlook.office365.com ou em myapps.microsoft.com, conforme mostrado aqui:


<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/office365.png) </center>


<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/myapps.png) </center>

Esse grupo será representado como um grupo de distribuição no AD DS local. Seu servidor Exchange local deve estar no Exchange 2013 com atualização cumulativa 8 (lançado em março de 2015) para reconhecer esse novo tipo de grupo.

**Observação**

- Atualmente, o atributo de catálogo de endereços não é populado. A maneira mais fácil é localizar a propriedade do catálogo de endereços de outro grupo em sua organização e populá-lo fora do mecanismo de sincronização. **A maneira mais fácil de fazer isso é usar o destinatário de atualização do cmdlet do PowerShell.**
- Somente as florestas com o esquema do Exchange são alvos válidos para grupos. Se nenhum Exchange for detectado, não será possível habilitar o write-back de grupo.
- O recurso de write-back de grupo não lida atualmente com grupos de segurança ou grupos de distribuição.

Mais informações podem ser encontradas [aqui](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/).

## Write-back de dispositivo (visualização)
O recurso de write-back de dispositivo permitirá que você coloque um dispositivo registrado na nuvem, por exemplo, no Intune, e o coloque no AD DS para acesso condicional. Para habilitar o recurso, o AD DS deve estar preparado. Se você instalar o AD FS e o DRS (serviço de registro de dispositivos), o DRS fornecerá cmdlets do PowerShell para preparar o AD para write-back de dispositivo. Se não tiver o DRS instalado, você poderá executar C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncAdPrep.psm1 como um administrador corporativo.



## Modo de preparo
Com o modo de preparo, o processo para configurar um novo servidor de sincronização em paralelo com um servidor existente é possível. Há suporte apenas para ter um servidor de sincronização conectado a um diretório na nuvem. Porém, se quisermos mover de outro servidor, por exemplo, um que executa o DirSync, poderemos habilitar o Azure Connect AD no modo de preparo. Quando habilitado, o mecanismo de sincronização importará e sincronizará dados da maneira normal, mas não exportará nada para o AD do Azure e desativará a sincronização e o write-back de senha.

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/stagingmode.png) </center>

No modo de preparo, é possível fazer as alterações necessárias no mecanismo de sincronização e examinar o que está prestes a ser exportado. Quando a configuração parecer adequada, execute novamente o assistente de instalação e desabilite o modo de preparo. Isso habilitará os dados a serem exportados para o AD do Azure. Desabilite o outro servidor ao mesmo tempo, para que somente um servidor esteja exportando ativamente.

### Impedindo exclusões acidentais
Ao se instalar o Azure AD Connect, o recurso para impedir exclusões acidentais será habilitado por padrão e configurado para não permitir uma exportação com mais de 500 exclusões. O valor 500 é um valor padrão e pode ser alterado. Com esse recurso habilitado, se houver um número excessivo de exclusões, a exportação não continuará, e você receberá um email como este:

<center>![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/email.png) </center>

Se isso for inesperado, investigue e tome ações corretivas.

Para desabilitar temporariamente essa proteção e permitir que essas exclusões ocorram, execute: Disable-ADSyncExportDeletionThreshold

Para habilitar novamente a proteção ou alterar a configuração de limite padrão, execute: Enable-ADSyncExportDeletionThreshold


## Configurando a federação com o AD FS
Configurar o AD FS com o Azure AD Connect é simples, com apenas alguns cliques. É necessário o seguinte antes da instalação.

- Um servidor Windows Server 2012 R2 para o servidor de federação com gerenciamento remoto habilitado
- Um servidor Windows Server 2012 R2 para o servidor de proxy de aplicativo Web com gerenciamento remoto habilitado
- Um certificado SSL para o nome do serviço de federação que você pretende usar (por exemplo, adfs.contoso.com)

### Criar um novo farm do AD FS ou usar um farm do AD FS existente
Você pode usar um farm do AD FS existente ou pode optar por criar um novo farm do AD FS. Se optar por criar um novo, você precisará fornecer o certificado SSL. Se o certificado SSL estiver protegido por senha, você será solicitado a fornecer a senha.

<center>![Farm do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png) </center>
**Observação:** se optar por usar um farm existente do AD FS, você ignorará algumas páginas e será levado diretamente para uma tela de configuração de uma relação de confiança entre o AD FS e o AD Azure.

### Especificar os servidores do AD FS

Aqui, você digitará os servidores específicos em que deseja instalar o AD FS. Você pode adicionar um ou mais servidores com base em sua necessidades de planejamento de capacidade. Esses servidores devem ser associados a um domínio do Active Directory antes da realização dessa configuração. É recomendável instalar um único servidor do AD FS para implantações de teste e piloto e implantar servidores adicionais abrindo o Azure AD Connect e implantando o AD FS para que servidores adicionais atendam às suas necessidades de dimensionamento.


> [AZURE.NOTE]Verifique se todos os servidores foram associados a um domínio do AD antes de realizar essa configuração.

<center>![Servidores do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png) </center>
### Especificar os servidores proxy de aplicativo Web
Aqui você digitará os servidores específicos que deseja definir como os servidores proxy de aplicativo Web. O servidor proxy de aplicativo Web é implantado em sua rede de DMZ (voltada para a extranet) e dá suporte a solicitações de autenticação da extranet. Você pode adicionar um ou mais servidores com base em sua necessidades de planejamento de capacidade. É recomendável instalar um único servidor proxy de aplicativo Web para implantações de teste e piloto e implantar servidores adicionais abrindo o Azure AD Connect e implantando io Proxy de Aplicativo Web em servidores adicionais. Normalmente, é recomendável ter um número equivalente de servidores proxy para satisfazer à autenticação da intranet.

> [AZURE.NOTE]<li>Se a conta que você está usando para instalar o Azure AD Connect não for administrador local nos servidores do AD FS, você será solicitado a fornecer credenciais para uma conta que tenha permissões suficientes.</li><li>Verifique se há conectividade HTTP/HTTPS entre o servidor do Azure AD Connect e o servidor proxy de aplicativo Web antes de configurar essa etapa.</li><li> Além disso, verifique se há conectividade HTTP/HTTPS entre o servidor de aplicativos Web e o servidor do AD FS para permitir solicitações de autenticação por meio de fluxo.</li>


<center>![Aplicativo Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png) </center>

Você deverá inserir as credenciais para que o servidor de aplicativos Web possa estabelecer uma conexão segura com o servidor do AD FS. Essas credenciais precisam ser um administrador local no servidor do AD FS.

<center>![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png) </center>
### Especifique a conta de serviço para o serviço AD FS
O serviço AD FS requer uma conta de serviço de domínio para autenticar usuários e informações de usuário de pesquisa no Active Directory. Ele pode dar suporte a dois tipos de contas de serviço:

- **Contas de serviço gerenciado por grupo** - esse é um tipo de conta de serviço introduzido no Serviço de Domínio do Active Directory com o Windows Server 2012. Esse tipo de conta fornece serviços como o AD FS para usar uma única conta sem a necessidade de atualizar a senha da conta regularmente. Use essa opção se você já tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencerão.
- **Conta de usuário de domínio** - esse tipo de conta exigirá que você forneça uma senha e atualize a senha regularmente quando a senha for alterada. Use essa opção somente quando você não tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencem.

O Azure AD Connect criará automaticamente a conta de serviço gerenciado por grupo se você estiver conectado como um administrador de domínio.
 
<center>![Conta de serviço do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png) </center>

### Selecione o domínio do AD do Azure que você deseja federar
Essa configuração é usada para configurar a relação de federação entre o AD FS e o AD do Azure. Ela configura o AD FS para emitir tokens de segurança para o AD do Azure e configura o AD do Azure para confiar em tokens dessa instância específica do AD FS. Essa página só permitirá configurar um único domínio na primeira experiência. Você pode configurar domínios adicionais a qualquer momento abrindo o Azure AD Connect novamente e executando esta tarefa.

 
<center>![Domínio do AD do Azure](./media/active-directory-aadconnect-get-started-custom/adfs6.png) </center>
### Executar tarefas adicionais para concluir a configuração da federação
As tarefas adicionais a seguir precisarão ser concluídas para concluir a configuração da federação.

- Configure registros DNS para o nome de serviço de federação do AD FS (por exemplo, adfs.contoso.com) para a intranet (seu servidor DNS interno) e a extranet (DNS público por meio do seu registrador de domínio). Para o registro DNS da intranet, use registros A, não registros CNAME. Isso é necessário para que autenticação do Windows funcione corretamente em seu computador associado ao domínio.
- Se você estiver implantando mais de um servidor AD FS ou servidor proxy de aplicativo Web, verifique se configurou seu balanceador de carga e se os registros DNS do nome do serviço de federação do AD FS (por exemplo, adfs.contoso.com) apontam para o balanceador de carga.
- Para que a autenticação integrada do Windows funcione com aplicativos de navegador usando o Internet Explorer em sua intranet, verifique se o nome do serviço de federação do AD FS (por exemplo, adfs.contoso.com) foi adicionado à zona de intranet no Internet Explorer. Isso pode ser controlado por meio da política de grupo e implantado a todos os computadores associados ao domínio. 

### Verificar a configuração de federação

O Azure AD Connect verificará as configurações de DNS para você quando você clicar no botão Verificar.

<center>![Concluir](./media/active-directory-aadconnect-get-started-custom/adfs7.png) </center>
Além disso, execute as seguintes etapas de verificação:

- Valide o logon do navegador de um computador associado ao domínio do Internet Explorer da intranet: conecte-se a https://myapps.microsoft.com e verifique o logon com sua conta conectada.
- Valide o logon do navegador de qualquer dispositivo da extranet: em um computador doméstico ou um dispositivo móvel, conecte-se a https://myapps.microsoft.com e forneça sua ID de logon e suas credenciais de senha.
- Valide o logon do cliente avançado: conecte-se a https://testconnectivity.microsoft.com, escolha a guia 'Office 365' e escolha o 'Teste de Logon Único do Office 365'.

### Configuração opcional em seu serviço do AD FS
Você pode personalizar a imagem do logotipo e a ilustração para as páginas de logon do AD FS fazendo logon no AD FS e usando PSH para fazer essa configuração.
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}






**Recursos adicionais**

* [Usar sua infraestrutura de identidade local na nuvem](active-directory-aadconnect.md)
* [Como o Azure AD Connect funciona](active-directory-aadconnect-how-it-works.md)
* [O que vem a seguir com o Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Saiba mais](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->