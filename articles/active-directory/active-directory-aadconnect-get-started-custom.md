<properties
	pageTitle="Instalação personalizada do Azure AD Connect | Microsoft Azure"
	description="Este documento fornece detalhes sobre as opções de instalação personalizada para o Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="billmath;andkjell"/>

# Instalação personalizada do Azure AD Connect


A documentação a seguir fornece informações sobre como usar a opção de instalação personalizada para o Azure AD Connect. Você poderá usar essa opção se tiver opções de configuração adicionais ou precisar de recursos opcionais que não são abrangidos pela instalação expressa.

## Documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados. Os três primeiros tópicos em negrito são necessários antes de iniciar a instalação.

| Tópico | |
| --------- | --------- |
| **Baixar o Azure AD Connect** | [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware e pré-requisitos** | [Azure AD Connect: hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md) |
| **Contas usadas para instalação** | [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |
| Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Após a instalação | [Verifique a instalação e atribua licenças](active-directory-aadconnect-whats-next.md) |




## Instalar componentes necessários

Quando você instala os serviços de sincronização, pode deixar a seção de configuração opcional desmarcada e o Azure AD Connect configurará tudo automaticamente. Isso inclui a configuração de uma instância do SQL Server 2012 Express, a criação de grupos apropriados e atribuição de permissões a eles. Para alterar os padrões, use a tabela a seguir para entender as opções de configuração opcionais disponíveis.

![Componentes necessários](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)


| Configuração opcional | Descrição |
| ------------- | ------------- |
| Usar um SQL Server existente | Permite que você especifique o nome do SQL Server e o nome da instância. Escolha essa opção se já tiver um servidor de banco de dados que você quer usar. Se o SQL Server não tem a navegação habilitada e você deve especificar um número de porta, insira na caixa de diálogo **Nome da Instância** o nome de instância, seguido por uma vírgula e pelo número da porta. |
| Usar uma conta de serviço existente | Por padrão, o Azure AD Connect criará uma conta de serviço local para uso dos serviços de sincronização. A senha é gerada automaticamente e desconhecida para a pessoa que está instalando o Azure AD Connect. Se você usa um SQL Server remoto, precisa de uma conta de serviço no domínio e precisa saber a senha. Nesses casos, insira a conta de serviço a ser usada. Verifique se o usuário que está executando a instalação é um SA no SQL para que um logon para a conta de serviço possa ser criado. Veja [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation) |
| Especificar grupos de sincronização personalizados | Por padrão o Azure Connect AD criará quatro grupos locais para o servidor quando os serviços de sincronização estiverem instalados. Esses grupos são: grupo de administradores, grupo de operadores, grupo de navegação e grupo de redefinição de senha. Se quiser especificar seus próprios grupos, você pode fazê-lo aqui. Os grupos devem ser locais no servidor e não podem ser localizados no domínio. |


## Entrada do usuário
Depois de instalar os componentes necessários, você será solicitado a especificar o método de logon único que os usuários usarão. A tabela a seguir fornece uma breve descrição das opções disponíveis. Para obter uma descrição completa dos métodos de entrada, consulte [Entrada do usuário](active-directory-aadconnect-user-signin.md).

![Entrada do usuário](./media/active-directory-aadconnect-get-started-custom/usersignin.png)



Opção de logon único | Descrição
------------- | ------------- |
Sincronização de senha |Os usuários poderão entrar nos serviços de nuvem da Microsoft, como Office 365, Dynamics CRM e Windows InTune, usando a mesma senha que usam ao fazer logon em sua rede local. A senha do usuário é sincronizada no Azure por meio de um hash de senha, e a autenticação ocorre na nuvem. Veja [Sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md) para obter mais informações.
Federação com o AD FS|Os usuários poderão entrar nos serviços de nuvem da Microsoft, como Office 365, Dynamics CRM e Windows InTune, usando a mesma senha que usam ao fazer logon em sua rede local. Os usuários são redirecionados para sua instância do local ad fs para entrar, e autenticação é feita no local.
Não configurar| Nenhum recurso será instalado e configurado. Escolha essa opção se você já tiver um servidor de federação de terceiros ou outra solução existente em vigor.



## Conectar-se ao AD do Azure
Na tela Conectar ao AD do Azure, digite uma senha e uma conta de administrador global. Verifique se que essa conta não tem a autenticação multifator habilitada. Isso fará com que ele falhe na autenticação. Lembre-se de que essa conta só é usada para criar uma conta de serviço no AD do Azure e não é usada após a conclusão do assistente.

![Entrada do usuário](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Se você receber um erro e tiver problemas de conectividade, confira [Solucionar problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).

## Páginas na seção Sincronização

### Conectar seus diretórios
Para se conectar a seu Serviço de Domínio do Active Directory, o Azure AD Connect precisa das credenciais de uma conta com permissões suficientes. Essa conta pode ser uma conta de usuário regular, pois ele precisa apenas de permissões de leitura padrão. No entanto, dependendo do cenário, talvez você precise de permissões adicionais. Para obter mais informações, veja [Contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Entrada do usuário](./media/active-directory-aadconnect-get-started-custom/connectdir.png)


### Identificando seus usuários com exclusividade

A correspondência entre os recursos de florestas permite que você defina como os usuários de suas florestas do AD DS são representados no AD do Azure. Um usuário também pode ser representado somente uma vez em todas as florestas ou ter uma combinação de contas habilitadas e desabilitadas.

![Entrada do usuário](./media/active-directory-aadconnect-get-started-custom/unique.png)


Configuração | Descrição
------------- | ------------- |
[Meus usuários são representados apenas uma vez em todas as florestas](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Todos os usuários são criados como objetos individuais no AD do Azure.<br> Os objetos não são associados ao metaverso.
[Atributo de email](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Essa opção associa usuários e contatos quando o atributo de email tem o mesmo valor em florestas diferentes. É recomendável usar essa opção quando seus contatos foram criados com GALSync.
[ObjectSID e msExchangeMasterAccountSID](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest)|Essa opção associa um usuário habilitado em uma floresta de conta com um usuário desabilitado em uma floresta de recursos do Exchange. Isso também é conhecido como caixa de correio vinculada no Exchange. Essa opção também pode ser usada se você usar somente o Lync e o Exchange não estiver presente na floresta de recursos.
sAMAccountName e MailNickName|Essa opção realiza a associação em atributos em que se espera que a ID de logon do usuário possa ser encontrada.
Meu próprio atributo|Essa opção permite que você selecione seu próprio atributo. **Limitação:** escolha um atributo que já exista no metaverso. Se você escolher um atributo personalizado, o assistente não poderá ser concluído.

- **Âncora de origem** - o atributo sourceAnchor é um atributo imutável durante o tempo de vida de um objeto de usuário. É a chave primária de vinculação de usuário local com o usuário no AD do Azure. Como o atributo não pode ser alterado, você deve planejar um bom atributo para usar. Um bom candidato é objectGUID. Esse atributo não será alterado, a menos que a conta de usuário seja movida entre florestas/domínios. Em um ambiente de várias floresta em que você move contas entre florestas, outro atributo deve ser usado, como um atributo com employeeID. Os atributos a serem evitados são aqueles que seriam alterados se uma pessoa casasse ou mudasse de cargo. Você não pode usar atributos com um caractere @. Portanto, email e userPrincipalName não podem ser usados. O atributo também diferencia maiúsculas de minúsculas. Pportanto, se você mover um objeto entre florestas, preserve as maiúsculas/minúsculas. Para atributos binários, o valor é codificado na base 64, mas para outros tipos de atributo, ele permanecerá no estado não codificado. Em cenários de Federação e algumas interfaces do AD do Azure, esse atributo também é conhecido como immutableID. Mais informações sobre a âncora de origem podem ser encontradas nos [conceitos de design](active-directory-aadconnect-design-concepts.md#sourceAnchor).

- **UserPrincipalName** - o atributo userPrincipalName é o atributo que os usuários usarão ao fazer logon no AD do Azure e no Office 365. Os domínios usados, também conhecidos como sufixo UPN, devem ser verificados no AD do Azure antes que os usuários sejam sincronizados. É altamente recomendável manter o atributo padrão userPrincipalName. Se esse atributo não for roteável e não puder ser verificado, será possível selecionar outro atributo, como email, como o atributo contendo a ID de logon. Isso é conhecido como **ID Alternativa**. O valor do atributo da ID Alternativa deve seguir o padrão RFC822. Uma ID alternativa pode ser usada com SSO de senha e SSO de federação como solução de logon.

>[AZURE.WARNING]Usar uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, veja [Configurando ID de logon alternativa](https://technet.microsoft.com/library/dn659436.aspx).



### Filtragem de sincronização com base em grupos
O recurso de filtragem em grupos permite executar um piloto pequeno em que somente um pequeno subconjunto de objetos deve ser criado no AD do Azure e no Office 365. Para usar esse recurso, crie um grupo no Active Directory e adicione os usuários e grupos que devem ser sincronizados com o AD do Azure como membros diretos. Posteriormente, você pode adicionar e remover usuários nesse grupo para manter a lista de objetos que devem estar presentes no AD do Azure. Todos os objetos que você deseja sincronizar devem ser membros diretos do grupo. Isso incluirá os usuários, grupos, contatos e computadores/dispositivos. Associação de grupo aninhado não será resolvida; um membro do grupo incluirá apenas o grupo em si e não a seus membros.

Para usar esse recurso, no caminho personalizado, você verá esta página: ![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]Esse recurso destina-se somente a oferecer suporte a uma implantação piloto e não deve ser usado em uma implantação de produção completa.

Em uma implantação de produção completa, será ser difícil manter um único grupo com todos os objetos a sincronizar. Em vez disso, você deve usar um dos métodos em [Configurar filtragem](active-directory-aadconnectsync-configure-filtering.md).

### Recursos opcionais

Essa tela permite que você selecione os recursos opcionais para seus cenários específicos. Abaixo estão breves explicações de cada um dos recursos individuais.

![Recursos opcionais](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [AZURE.WARNING]Se você tiver o DirSync ou a Sincronização do AD do Azure ativa, não ative os recursos de write-back no Azure AD Connect

Recursos opcionais | Descrição
-------------------    | ------------- |
Implantação híbrida do Exchange |O recurso Implantação Híbrida do Exchange permite a coexistência de caixas de correio do Exchange localmente e no Azure, sincronizando um conjunto específico de [atributos](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) do AD do Azure de volta ao diretório local.
Aplicativo AD do Azure e filtragem de atributos|Ao habilitar o aplicativo AD do Azure e filtragem de atributo, o conjunto de atributos sincronizados pode ser adaptado para um conjunto específico em uma página subsequente do assistente. Isso abre duas páginas de configuração adicionais no assistente.  
Sincronização de senha | Você pode habilitar essa opção se selecionou a federação como a solução de logon. Em seguida, a sincronização de senha pode ser usada como uma opção de backup. Para obter mais informações, veja [Sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md).
Write-back de senha|Ao habilitar o write-back de senha, as alterações de senha que se originam com o AD do Azure serão gravadas no diretório local. Para obter mais informações, veja [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md).
Write-back de grupo |Se você usar o recurso **Grupos no Office 365**, você poderá ter esses grupos no Active Directory local como um grupo de distribuição. Essa opção só estará disponível se você tiver o Exchange presente no seu Active Directory local. Para saber mais, veja [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback).
Write-back de dispositivo | Permite o write-back de objetos de dispositivo no AD do Azure para seu Active Directory local para cenários de acesso condicional. Para obter mais informações, veja [Habilitando o write-back do dispositivo no Azure AD Connect](active-directory-aadconnect-get-started-custom-device-writeback.md)
Sincronização de atributo de extensão de diretório|Ao habilitar a sincronização de atributo de extensão de diretório, os atributos adicionais especificados serão sincronizados com o AD do Azure. Para obter mais informações, veja [Extensões de diretório](active-directory-aadconnect-feature-preview.md#directory-extensions).

### Aplicativo AD do Azure e filtragem de atributos
Se você deseja limitar quais atributos sincronizar com o AD do Azure, inicie selecionando quais serviços usará. Se você configurar essa página, qualquer novo serviço deve ser selecionado explicitamente executando novamente o assistente de instalação.

![Recursos opcionais](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

De acordo com os serviços selecionados na etapa anterior, essa página mostrará todos os atributos que serão sincronizados. Esta lista é uma combinação de todos os tipos de objeto que estão sendo sincronizado. Se houver alguns atributos específicos que não devem ser sincronizados, você pode desmarcá-los. Na figura abaixo, o atributo homePhone foi desmarcado e não será sincronizado com o AD do Azure.

![Recursos opcionais](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

### Sincronização de atributo de extensão do diretório (visualização)
Com as extensões de diretório, você pode estender o esquema no AD do Azure com atributos personalizados adicionados por sua organização ou outros atributos no Active Directory. Para usar esse recurso, selecione "Sincronização de atributo de extensão do diretório" na página "Recursos opcionais". Isso lhe fornecerá essa página, em que você pode selecionar os atributos adicionais.

![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Para obter mais informações, veja [Extensões de diretório](active-directory-aadconnect-feature-preview.md#directory-extensions).

## Configurando a federação com o AD FS
Configurar o AD FS com o Azure AD Connect é simples, com apenas alguns cliques. É necessário o seguinte antes da instalação.

- Um servidor Windows Server 2012 R2 para o servidor de federação com gerenciamento remoto habilitado
- Um servidor Windows Server 2012 R2 para o servidor de proxy de aplicativo Web com gerenciamento remoto habilitado
- Um certificado SSL para o nome do serviço de federação que você pretende usar (por exemplo, adfs.contoso.com)

### Criar um novo farm do AD FS ou usar um farm do AD FS existente
Você pode usar um farm do AD FS existente ou pode optar por criar um novo farm do AD FS. Se optar por criar um novo, você precisará fornecer o certificado SSL. Se o certificado SSL estiver protegido por senha, você será solicitado a fornecer a senha.

![Farm do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**Observação:** se optar por usar um farm existente do AD FS, você ignorará algumas páginas e será levado diretamente para uma tela de configuração de uma relação de confiança entre o AD FS e o AD Azure.

### Especificar os servidores do AD FS

Aqui, você digitará os servidores específicos em que deseja instalar o AD FS. Você pode adicionar um ou mais servidores com base em sua necessidades de planejamento de capacidade. Esses servidores devem ser associados a um domínio do Active Directory antes da realização dessa configuração. É recomendável instalar um único servidor do AD FS para implantações de teste e piloto e implantar servidores adicionais abrindo o Azure AD Connect e implantando o AD FS para que servidores adicionais atendam às suas necessidades de dimensionamento.


> [AZURE.NOTE]Verifique se todos os servidores foram associados a um domínio do AD antes de realizar essa configuração.

![Servidores do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)



### Especificar os servidores proxy de aplicativo Web
Aqui você digitará os servidores específicos que deseja definir como os servidores proxy de aplicativo Web. O servidor proxy de aplicativo Web é implantado em sua rede de DMZ (voltada para a extranet) e dá suporte a solicitações de autenticação da extranet. Você pode adicionar um ou mais servidores com base em sua necessidades de planejamento de capacidade. É recomendável instalar um único servidor proxy de aplicativo Web para implantações de teste e piloto e implantar servidores adicionais abrindo o Azure AD Connect e implantando io Proxy de Aplicativo Web em servidores adicionais. Normalmente, é recomendável ter um número equivalente de servidores proxy para satisfazer à autenticação da intranet.


> [AZURE.NOTE]- Se a conta que você está usando para instalar o Azure AD Connect não for um administrador local nos servidores do AD FS, serão solicitadas credenciais de uma conta que tenha permissões suficientes. - Certifique-se de que há conectividade HTTP/HTTPS entre o servidor do Azure AD Connect e o servidor do Proxy de Aplicativo Web antes de configurar essa etapa. - Além disso, certifique-se de que há conectividade HTTP/HTTPS entre o servidor de aplicativos Web e o servidor do AD FS para permitir a passagem de solicitações de autenticação.


![Aplicativo Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)


Você deverá inserir as credenciais para que o servidor de aplicativos Web possa estabelecer uma conexão segura com o servidor do AD FS. Essas credenciais precisam ser um administrador local no servidor do AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)


### Especifique a conta de serviço para o serviço AD FS
O serviço AD FS requer uma conta de serviço de domínio para autenticar usuários e informações de usuário de pesquisa no Active Directory. Ele pode dar suporte a dois tipos de contas de serviço:

- **Contas de serviço gerenciado por grupo** - esse é um tipo de conta de serviço introduzido no Serviço de Domínio do Active Directory com o Windows Server 2012. Esse tipo de conta fornece serviços como o AD FS para usar uma única conta sem a necessidade de atualizar a senha da conta regularmente. Use essa opção se você já tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencerão.
- **Conta de usuário de domínio** - esse tipo de conta exigirá que você forneça uma senha e atualize a senha regularmente quando a senha for alterada. Use essa opção somente quando você não tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencem.

O Azure AD Connect criará automaticamente a conta de serviço gerenciado por grupo se você estiver conectado como um administrador de domínio.

![Conta de serviço do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### Selecione o domínio do AD do Azure que você deseja federar
Essa configuração é usada para configurar a relação de federação entre o AD FS e o AD do Azure. Ela configura o AD FS para emitir tokens de segurança para o AD do Azure e configura o AD do Azure para confiar em tokens dessa instância específica do AD FS. Essa página só permitirá configurar um único domínio na primeira experiência. Você pode configurar domínios adicionais a qualquer momento abrindo o Azure AD Connect novamente e executando esta tarefa.


![Domínio do AD do Azure](./media/active-directory-aadconnect-get-started-custom/adfs6.png)


### Executar tarefas adicionais para concluir a configuração da federação
As tarefas adicionais a seguir precisarão ser concluídas para concluir a configuração da federação.

- Configure registros DNS para o nome de serviço de federação do AD FS (por exemplo, adfs.contoso.com) para a intranet (seu servidor DNS interno) e a extranet (DNS público por meio do seu registrador de domínio). Para o registro DNS da intranet, use registros A, não registros CNAME. Isso é necessário para que autenticação do Windows funcione corretamente em seu computador associado ao domínio.
- Se você estiver implantando mais de um servidor AD FS ou servidor proxy de aplicativo Web, verifique se configurou seu balanceador de carga e se os registros DNS do nome do serviço de federação do AD FS (por exemplo, adfs.contoso.com) apontam para o balanceador de carga.
- Para que a autenticação integrada do Windows funcione com aplicativos de navegador usando o Internet Explorer em sua intranet, verifique se o nome do serviço de federação do AD FS (por exemplo, adfs.contoso.com) foi adicionado à zona de intranet no Internet Explorer. Isso pode ser controlado por meio da política de grupo e implantado a todos os computadores associados ao domínio.


### Configuração opcional em seu serviço do AD FS
Você pode personalizar a imagem do logotipo e a ilustração para as páginas de logon do AD FS fazendo logon no AD FS e usando PSH para fazer essa configuração.

	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

## Configurar e verificar páginas
Nesta página, a configuração realmente ocorrerá.

### Modo de preparo
Com o modo de preparo, o processo para configurar um novo servidor de sincronização em paralelo com um servidor existente é possível. Há suporte apenas para ter um servidor de sincronização conectado a um diretório na nuvem. Porém, se quisermos mover de outro servidor, por exemplo, um que executa o DirSync, poderemos habilitar o Azure AD Connect no modo de preparo. Quando habilitado, o mecanismo de sincronização importará e sincronizará dados da maneira normal, mas não exportará nada para o AD do Azure e desativará a sincronização e o write-back de senha.

![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)


No modo de preparo, é possível fazer as alterações necessárias no mecanismo de sincronização e examinar o que está prestes a ser exportado. Quando a configuração parecer adequada, execute novamente o assistente de instalação e desabilite o modo de preparo. Isso habilitará os dados a serem exportados para o AD do Azure. Desabilite o outro servidor ao mesmo tempo, para que somente um servidor esteja exportando ativamente.

 Para obter mais informações, veja [Modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode).

### Verificar a configuração de federação

O Azure AD Connect verificará as configurações de DNS para você quando você clicar no botão Verificar.

![Concluído](./media/active-directory-aadconnect-get-started-custom/adfs7.png)


Além disso, execute as seguintes etapas de verificação:

- Valide o logon do navegador de um computador associado ao domínio do Internet Explorer da intranet: conecte-se a https://myapps.microsoft.com e verifique o logon com sua conta conectada.
- Valide o logon do navegador de qualquer dispositivo da extranet: em um computador doméstico ou um dispositivo móvel, conecte-se a https://myapps.microsoft.com e forneça sua ID de logon e suas credenciais de senha.
- Valide o logon do cliente avançado: conecte-se a https://testconnectivity.microsoft.com, escolha a guia 'Office 365' e escolha o 'Teste de Logon Único do Office 365'.


## Próximas etapas
Após a instalação, saia e entre novamente no Windows antes de usar o Gerenciador de Serviços de Sincronização ou o Editor de Regra de Sincronização.

Agora que você tem o Azure AD Connect instalado, é possível [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0121_2016-->