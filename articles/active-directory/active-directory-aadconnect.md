<properties
	pageTitle="Azure AD Connect: Integrando suas identidades locais ao Active Directory do Azure. | Microsoft Azure"
	description="O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory. Isso permite que você forneça uma identidade comum para os aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure."
    keywords="introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é o Azure AD Connect, instalar o active directory"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/19/2016"
	ms.author="andkjell;billmath"/>

# Integração de suas identidades locais com o Active Directory do Azure
O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory. Isso permite que você forneça uma identidade comum para os usuários dos aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure. Este tópico o guiará nas etapas de planejamento, implantação e operação. É uma coleção de links para os tópicos relacionados a essa área.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [AZURE.IMPORTANT] [Azure AD Connect is the best way to connect your on-premises directory with Azure AD and Office 365. This is a great time to upgrade to Azure AD Connect from Windows Azure Active Directory Sync (DirSync) or Azure AD Sync as these tools are now deprecated and will reach end of support on April 13, 2017.](https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-dirsync-deprecated/?WT.mc_id=DirSyncDepACOM)

![O que é o Azure AD Connect](./media/active-directory-aadconnect/arch.png)

## Por que usar o Azure AD Connect
A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos ao fornecer uma identidade comum para acesso aos recursos na nuvem e locais. Os usuários e as organizações podem beneficiar-se do seguinte:

- Os usuários podem usar uma única identidade para acessar aplicativos locais e serviços na nuvem, como o Office 365.

- Ferramenta única para fornecer uma experiência de implantação fácil de sincronização e entrada.

- Fornece os recursos mais recentes para seus cenários. O Azure AD Connect substitui as versões mais antigas das ferramentas de integração de identidade, como DirSync e Azure AD Sync. Para saber mais, confira [Comparação das ferramentas de integração de diretórios de Identidade Híbrida](active-directory-hybrid-identity-design-considerations-tools-comparison.md).


### Como o Azure AD Connect funciona
O Azure Active Directory Connect possui três componentes principais: serviços de sincronização, componente opcional dos Serviços de Federação do Active Directory e componente de monitoramento denominado [Azure AD Connect Health](active-directory-aadconnect-health.md).

<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Sincronização - esse componente é responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.
- AD FS - a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Isto pode ser usado pelas organizações para endereçar as implantações complexas, como o SSO de ingresso no domínio, imposição da política de entrada no AD e cartão inteligente ou MFA de terceiros.
- Monitoramento de Integridade - o Azure AD Connect Health pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade. Para obter informações adicionais, consulte [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## Instalar o Azure AD Connect

Você pode encontrar o download para o Azure AD Connect no [Centro de Download da Microsoft.](http://go.microsoft.com/fwlink/?LinkId=615771).


Solução | Cenário
----- | ----- |
Antes de iniciar: [Hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md) | <li>Etapas a serem concluídas antes de começar a instalar o Azure AD Connect.</li>
[Configurações expressas](active-directory-aadconnect-get-started-express.md) | <li>Se você tiver uma única floresta do AD, então, essa será a opção recomendada para usar.</li> <li>Entrada do usuário com a mesma senha usando a sincronização de senha.</li>
[Configurações personalizadas](active-directory-aadconnect-get-started-custom.md) | <li>Usadas quando você tem várias florestas. Dá suporte a várias [topologias](active-directory-aadconnect-topologies.md) locais.</li> <li>Personalize sua opção de entrada, como o ADFS para federação, ou use um provedor de identidade de terceiros.</li> <li>Personalize os recursos de sincronização, como filtragem e write-back.</li>
[Atualização do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Usado quando você tem um servidor DirSync existente já em execução.</li>
[Atualizar do Azure AD Sync ou do Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md)| <li>Há vários métodos diferentes, dependendo de sua preferência.</li>


[Após a instalação](active-directory-aadconnect-whats-next.md), verifique se ele está funcionando conforme o esperado e atribua licenças aos usuários.

### Próximas etapas para instalar o Azure AD Connect

Tópico |  
--------- | ---------
Baixar o Azure AD Connect | [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
Instalar usando as Configurações expressas | [Instalação expressa do Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Instalar usando Configurações personalizadas | [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Atualizar do DirSync | [Atualizar a partir da ferramenta de sincronização do AD do Azure (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Após a instalação | [Verifique a instalação e atribua licenças ](active-directory-aadconnect-whats-next.md)

### Saiba mais sobre como instalar o Azure AD Connect

Você também deve se preparar para questões [operacionais](active-directory-aadconnectsync-operations.md). Talvez você queira ter um servidor em espera para que possa recorrer a ele facilmente em caso de [desastre](active-directory-aadconnectsync-operations.md#disaster-recovery). Se você pretende fazer alterações de configuração frequentes, deve planejar um servidor no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode).

Tópico |  
--------- | ---------
Topologias com suporte | [Topologias para o Azure AD Connect](active-directory-aadconnect-topologies.md)
Conceitos de design | [Conceitos de design do Azure AD Connect](active-directory-aadconnect-design-concepts.md)
Contas usadas para instalação | [Mais informações sobre permissões e as credenciais de Conexão do AD do Azure](active-directory-aadconnect-accounts-permissions.md)
Planejamento operacional | [Sincronização do Azure AD Connect: considerações e tarefas operacionais](active-directory-aadconnectsync-operations.md)
Opções de entrada de usuário | [Opções de entrada de usuário do Azure AD Connect](active-directory-aadconnect-user-signin.md)

## Configurar recursos de sincronização
O Azure Connect AD vem com vários recursos que você pode ativar opcionalmente ou que estão habilitados por padrão. Em alguns casos, alguns recursos podem exigir uma configuração adicional em certos cenários e topologias.

A [filtragem](active-directory-aadconnectsync-configure-filtering.md) é usada para limitar quais objetos são sincronizados ao Azure AD. Por padrão, todos os usuários, contatos, grupos e computadores Windows 10 são sincronizados. Você pode alterar a filtragem com base em domínios, UOs ou atributos.

A [Sincronização de senhas](active-directory-aadconnectsync-implement-password-synchronization.md) sincroniza o hash de senhas no Active Directory para o Azure AD. O usuário final pode usar a mesma senha no local e na nuvem, mas apenas gerenciá-la em um único lugar. Como ele usa seu Active Directory local como a autoridade, você também poderá usar sua própria política de senha.

O [write-back de senha](active-directory-passwords-getting-started.md) permitirá aos usuários alterar e redefinir suas senhas na nuvem e ter sua política de senha local aplicada.

O [write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md) permitirá que um dispositivo registrado no Azure AD seja gravado de volta no Active Directory local, para que possa ser usado para acesso condicional.

O recurso para [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) é ativado por padrão e protege seu diretório na nuvem contra muitas exclusões ao mesmo tempo. Por padrão, ele permite 500 exclusões por execução. Você pode alterar essa configuração, dependendo do tamanho de sua organização.

A [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) está habilitada por padrão para as instalações de configurações expressas e garante que o Azure AD Connect esteja sempre atualizado com a última versão.

### Próximas etapas para configurar recursos de sincronização

Tópico |  
--------- | --------- |
Configurar a filtragem | [Sincronização do Azure AD Connect: configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md)
Sincronização de senha | [Azure AD Connect Sync: implementar a sincronização de senha](active-directory-aadconnectsync-implement-password-synchronization.md)
Write-back de senha | [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
Write-back de dispositivo | [Habilitando write-back de dispositivo no Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)
Impedir exclusões acidentais | [Sincronização do Azure AD Connect: impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
Atualização automática | [Azure AD Connect: Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md)

## Personalizar a sincronização do Azure AD Connect
A sincronização do Azure AD Connect vem com uma configuração padrão que tem como objetivo funcionar para a maioria dos clientes e topologias. Porém, sempre há situações em que a configuração padrão não funciona e deve ser ajustada. Há suporte para fazer alterações, conforme documentado nesta seção e nos tópicos vinculados.

Se você não tiver trabalhado com uma topologia de sincronização antes, convém começar a entender os conceitos básicos e os termos usados, conforme descrito em [conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md). O Azure AD Connect é a evolução de MIIS2003, ILM2007 e FIM2010. Mesmo que algumas coisas sejam idênticas, também houve muitas mudanças.

A [configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md) pressupõe que pode haver mais de uma floresta na configuração. Nessas topologias, um objeto de usuário pode ser representado como um contato em outra floresta. O usuário também pode ter uma caixa de correio vinculada em outra floresta de recursos. O comportamento da configuração padrão é descrito em [usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

O modelo de configuração em sincronização é chamado de [provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Os fluxos de atributo avançados estão usando [funções](active-directory-aadconnectsync-functions-reference.md) para expressar transformações de atributos. Você pode ver e examinar a configuração inteira usando ferramentas que vêm com o Azure AD Connect. Se você precisar fazer alterações na configuração, siga as [práticas recomendadas](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) para que seja mais fácil adotar as novas versões.

### Próximas etapas para personalizar a sincronização do Azure AD Connect

Tópico |  
--------- | ---------
Todos os artigos sobre a sincronização do Azure AD Connect | [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
Conceitos técnicos | [Sincronização do Azure AD Connect: conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md)
Noções básicas sobre a configuração padrão | [Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md)
Noções básicas sobre usuários e contatos | [Azure AD Connect Sync: noções básicas sobre usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
Provisionamento declarativo | [Azure AD Connect Sync: noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
Alterar a configuração padrão | [Práticas recomendadas para alterar a configuração padrão](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)

## Configurar recursos de federação
O ADFS pode ser configurado para dar suporte a [vários domínios](active-directory-aadconnect-multiple-domains.md). Por exemplo, você pode ter vários domínios superiores que precisem ser usados para federação.

se seu servidor ADFS não tiver sido configurado para atualizar automaticamente os certificados do Azure AD ou se você usar uma solução não ADFS, será notificado quando precisar [atualizar os certificados](active-directory-aadconnect-o365-certs.md).

### Próximas etapas para configurar recursos de federação

Tópico |  
--------- | ---------
Configurar o ADFS com subdomínios | [Suporte a Vários Domínios para Federação com o Azure AD](active-directory-aadconnect-multiple-domains.md)
Gerenciar o farm do AD FS | [Gerenciamento do AD FS e personalização com o Azure AD Connect](active-directory-aadconnect-federation-management.md)
Atualização manual dos certificados de federação | [Renovando certificados de federação para o Office 365 e AD do Azure](active-directory-aadconnect-o365-certs.md)

## Mais informações e referências

Tópico |  
--------- | --------- |
Histórico de versão | [Histórico de versão](active-directory-aadconnect-version-history.md)
Comparar o DirSync, Azure ADSync e o Azure AD Connect | [Comparação de ferramentas de integração de diretório](active-directory-hybrid-identity-design-considerations-tools-comparison.md)
Lista de compatibilidade de soluções não ADFS para o AD do Azure | [Lista de compatibilidade de federação do AD do Azure](active-directory-aadconnect-federation-compatibility.md)
Atributos sincronizados | [Atributos sincronizados](active-directory-aadconnectsync-attributes-synchronized.md)
Monitorando com o Azure AD Connect Health | [Azure AD Connect Health](active-directory-aadconnect-health.md)
Perguntas frequentes | [Perguntas frequentes do Azure AD Connect](active-directory-aadconnect-faq.md)


**Recursos adicionais**


Apresentação Ignite 2015 sobre como expandir seus diretórios locais para a nuvem.

>[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

<!---HONumber=AcomDC_0525_2016-->