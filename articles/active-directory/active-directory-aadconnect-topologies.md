<properties
   pageTitle="Topologias do Azure AD Connect | Microsoft Azure"
   description="Este tópico detalha as topologias com e sem suporte para o Azure AD Connect"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="andkjell"/>

# Topologias para o Azure AD Connect

O objetivo deste tópico é descrever diferentes topologias locais e do AD do Azure com o Azure AD Connect Sync como a solução de integração principal. Ele descreve as configurações com e sem suporte.

Legenda de imagens no documento:

| Descrição | ícone |
|-----|-----|
| Floresta do Active Directory local | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory com importação filtrada | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Servidor do Azure AD Connect Sync | ![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| “Modo de preparo” do servidor do Azure AD Connect Sync | ![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync com FIM2010 ou MIM2016 | ![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Servidor do Azure AD Connect Sync detalhado |![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Diretório do AD do Azure |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| Cenário sem suporte | ![Sem suporte](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## Uma única floresta, um único diretório do AD do Azure
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma única floresta local, com um ou vários domínios e um único diretório AD do Azure (também conhecido como Locatário). A autenticação do AD do Azure é feita com a sincronização de senha. Esta é a topologia com suporte pela instalação expressa do Azure AD Connect.

### Uma única floresta, vários servidores de sincronização para um diretório do AD do Azure
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não há suporte para a conexão de vários servidores do Azure AD Connect Sync ao mesmo diretório do AD do Azure, mesmo que eles forem configurados para sincronizar conjuntos de objetos mutuamente exclusivos (com exceção de um [servidor de preparo](#staging-server)). Isso poderia ser tentado porque um domínio em uma floresta não é acessível de um local de rede comum ou em uma tentativa de distribuir a carga de sincronização entre vários servidores.

## Várias florestas, um único diretório do AD do Azure
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes que incluem várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local implantada. Os exemplos típicos são designs com florestas de conta-recurso, florestas relacionadas a fusões e aquisições ou florestas usadas para terceirizar dados.

Quando você tem várias florestas, todas elas devem ser acessíveis pelo único servidor do Azure AD Connect Sync. O servidor não precisa ser associado a um domínio e pode ser colocado em uma rede de perímetro se precisar ser capaz de acessar todas as florestas.

O assistente do Azure AD Connect oferece várias opções de como consolidar os usuários até que o mesmo usuário seja representado várias vezes em diferentes florestas, o usuário será representado apenas uma vez no AD do Azure. Algumas topologias comuns são descritas abaixo. Configure qual topologia você tem usando o caminho de instalação personalizada do assistente de instalação e selecione a opção correspondente na página "Identificar os usuários com exclusividade". A consolidação está ocorrendo somente para os usuários. Se os grupos são duplicados, eles não são consolidados com a configuração padrão.

As topologias comuns são discutidas na próxima seção: [Separar topologias](#multiple-forests-separate-topologies), [Malha completa](#multiple-forests-full-mesh-with-optional-galsync) e [Conta-recurso](#multiple-forests-account-resource-forest).

Sobre a configuração padrão entregue pelo Azure AD Connect Sync, são feitas as seguintes suposições: 1. Os usuários têm apenas uma conta habilitada e a floresta em que essa conta se encontra é usada para autenticar o usuário. Isso se aplica a para ambas as sincronizações de senha e de federação; userPrincipalName e sourceAnchor/immutableID virão desta floresta. 2. Os usuários têm apenas uma caixa de correio. 3. A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (Lista de Endereços Global) do Exchange. Se não houver nenhuma caixa de correio no usuário, qualquer floresta pode ser usada para contribuir com esses valores de atributos. 4. Se você tiver uma caixa de correio vinculada, então também há outra conta em outra floresta usada para fazer logon.

Se seu ambiente não corresponder a essas suposições, acontecerá o seguinte:-Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização escolherá uma e ignorará as outras. -Se você tiver caixas vinculadas porém nenhuma outra conta, essas contas não serão exportadas para o AD do Azure e o usuário não será um membro de nenhum grupo. Em DirSync, uma caixa de correio vinculada seria representada como uma caixa de correio normal, por isso este é um comportamento intencionalmente diferente para dar melhor suporte a cenários de várias florestas.

### Várias florestas, vários servidores de sincronização para um diretório do AD do Azure
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Não há suporte para a conexão de mais de um servidor do Azure AD Connect Sync a um único diretório do AD do Azure (com exceção de um [servidor de preparo](#staging-server)).

### Várias florestas – topologias separadas
"Os usuários são representados apenas uma vez em todos os diretórios"

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Nesse ambiente, todas as florestas locais são tratados como entidades separadas e nenhum usuário estaria presente em nenhuma outra floresta. Cada floresta tem sua própria organização do Exchange e não há nenhuma GALSync entre as florestas. Esta pode ser a situação após uma fusão/aquisição ou em uma organização onde cada unidade de negócios opera isoladas das outras. No AD do Azure, essas florestas serão da mesma empresa e aparecerão com uma GAL unificada. Nesta figura, cada objeto em cada floresta será representado uma vez no metaverso e agregado no diretório de destino do AD do Azure.

### Várias florestas – usuários correspondentes
O comum a todos os cenários de várias florestas em que você selecionar uma das opções em "As identidades de usuário existem em vários diretórios" é que os grupos de distribuição e de segurança podem ser encontrados em cada floresta, podendo conter uma mistura de usuários, contatos e FSPs (Entidades de Segurança Externas).

FSPs são usados no ADDS para representar os membros de outras florestas em um grupo de segurança. O mecanismo de sincronização resolverá o FSP para o usuário real e representará o grupo de segurança no AD do Azure com todos os FSPs resolvidos para o objeto real.

### Várias florestas – malha completa com GALSync opcional
"As identidades de usuário existem em vários diretórios. Correspondência usando: atributo de email"

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que usuários e recursos sejam localizados em qualquer floresta; normalmente, haveriam seria relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, poderia haver uma solução GALSync local opcional que represente um usuário em uma floresta como um contato em todas as outras florestas. A GALSync normalmente é implementada usando o Forefront Identity Manager 2010 ou o Microsoft Identity Manager 2016. O Azure AD Connect não pode ser usado para GALSync local.

Nesse cenário, os objetos de identidade normalmente são ingressados usando o atributo de email. Como resultado, um usuário com uma caixa de correio em uma floresta é unido aos contatos em outras florestas.

### Várias florestas – Floresta de recursos de conta
"As identidades de usuário existem em vários diretórios. Correspondência usando os atributos ObjectSID e msExchMasterAccountSID"

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Em uma topologia de floresta de conta-recurso, você tem uma ou mais florestas de conta com contas de usuário ativas.

Este cenário inclui uma floresta que confia em todas as florestas de conta. Essa floresta normalmente tem um esquema do AD estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, bem como outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nesta floresta e a caixa de correio está vinculada à floresta da conta.

## Office 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 têm certas restrições a topologias com suporte. Se você planeja usar qualquer um desses, consulte as páginas de topologias com suporte de cada carga de trabalho.

| Carga de trabalho | |
| --------- | --------- |
| Exchange Online |	Se houver mais de uma organização do Exchange local (ou seja, o Exchange for implantado em mais de uma floresta), você deve usar o Exchange 2013 SP1 ou posterior. Os detalhes podem ser encontrados aqui: [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/pt-BR/library/jj873754.aspx) |
| Skype for Business | Ao usar várias florestas locais, somente a topologia de floresta conta-recurso terá suporte. Detalhes para as topologias com suporte podem ser encontrados aqui: [Requisitos ambientais do Skype for Business Server 2015](https://technet.microsoft.com/pt-BR/library/dn933910.aspx) |

## Servidor de preparo
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

O Azure AD Connect dá suporte à instalação em um segundo servidor no "Modo de preparo”. Um servidor nesse modo apenas lê dados de todos os diretórios conectados e, portanto, terá uma cópia atualizada dos dados de identidade. Em caso de desastre de falha do servidor primário, é fácil realizar o failover manualmente para o segundo servidor usando o assistente do Azure AD Connect. Esse segundo servidor pode estar preferivelmente localizado em um datacenter diferente, pois nenhuma infraestrutura é compartilhada com o servidor primário. Qualquer alteração de configuração feita no servidor principal deve ser copiada para o segundo servidor por você.

Um servidor de preparo também pode ser usado para testar uma nova configuração personalizada e o efeito sobre seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, transforme o servidor de preparo no servidor ativo e defina o antigo servidor ativo para modo de preparo.

Esse método também pode ser usado se você quiser substituir o servidor de sincronização e deseja preparar um novo antes de desligar o servidor ativo no momento.

É possível ter mais de um servidor de preparo se você quiser ter vários backups em datacenters diferentes.

## Vários diretórios do AD do Azure
A Microsoft recomenda ter um único diretório no AD do Azure para uma organização. Antes de você planeja usar vários diretórios do AD do Azure, consulte esses tópicos que abrangem cenários comuns que permitirão a você usar um único diretório.

| Tópico | |
| --------- | --------- |
| Delegação usando unidades administrativas | [Gerenciamento de unidades administrativas no AD do Azure ](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Há uma relação de 1:1 entre um servidor do Azure AD Connect Sync e um diretório do AD do Azure. Para cada diretório do AD do Azure, você precisará de uma instalação de servidor do Azure AD Connect Sync. As instâncias de diretório do AD do Azure são isolada por padrão e os usuários em um diretório não poderá ver os usuários em outro. Se isso for o desejado, essa é uma configuração com suporte, porém em caso contrário, você deve usar os modelos de diretório único do Azure AD descritos acima.

### Cada objeto uma única vez em um diretório do AD do Azure
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nessa topologia, um servidor do AAD Connect Sync está conectado a cada diretório do AD do Azure. Os servidores do Azure AD Connect Sync devem ser configurados para a filtragem para que cada um deles tenha um conjunto de objetos mutuamente exclusivo para a operação, por exemplo, de definição do escopo de cada servidor para um determinado domínio ou OU. Um domínio DNS só pode ser registrado em um único diretório AD do Azure para que o UPNs dos usuários no AD local também precise usar namespaces separados. Por exemplo, na figura acima, três sufixos de UPN separado são registrados no AD local: contoso.com, fabrikam.com e wingtiptoys.com. Os usuários em cada domínio do AD local usam um namespace diferente.

Nessa topologia não há nenhum “GALsync” entre as instâncias de diretório do AD do Azure, por isso o catálogo de endereços no Exchange Online e Skype for Business mostrará apenas os usuários no mesmo diretório.

Com esta topologia, apenas um dos diretórios do AD do Azure pode habilitar Exchange híbridos com o Active Directory local.

O requisito para um conjunto de objetos mutuamente exclusivo também se aplica ao write-back. Isso faz com que alguns recursos de write-back não tenham suporte com esta topologia, pois eles supõem a existência de uma única configuração local. Isso inclui: - Write-back de grupo com configuração padrão - Write-back de dispositivo

### Cada objeto várias vezes em um diretório do AD do Azure
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Ele não tem suporte para sincronização do mesmo usuário para vários diretórios do AD do Azure. Ele também não dá suporte para alterar uma configuração para fazer os usuários de um AD do Azure aparecerem como contatos em outro diretório do AD do Azure. Também não há suporte para modificar o Azure AD Connect Sync para se conectar a vários diretórios do AD do Azure.

### GALsync com o uso de write-back
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Diretórios do AD do Azure são isolados por padrão. Não há suporte para alterações da configuração do Azure AD Connect Sync para ler dados de outro diretório do AD do Azure como uma tentativa de criar uma GAL comum e unificada entre os diretórios. Também não há suporte para a exportação de usuários como contatos para outro AD local usando o Azure AD Connect Sync.

### GALsync com o servidor de sincronização local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Há suporte para o uso do FIM2010/MIM2016 local para usuários GALsync entre duas organizações do Exchange. Os usuários em uma organização aparecerão como usuários/contatos externos em outras organizações. Esses ADs locais diferentes poderão então ser sincronizados para seus próprios diretórios do AD do Azure.

## Próximas etapas
Para saber como instalar o Azure AD Connect para esses cenários, veja [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Para saber mais sobre a configuração do Azure AD Connect Sync, veja [Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md).

<!---HONumber=Sept15_HO4-->