---
title: 'Azure AD Connect: Topologias com suporte | Microsoft Docs'
description: Este tópico detalha as topologias com e sem suporte para o Azure AD Connect
services: active-directory
documentationcenter: ''
author: AndKjell
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 06/27/2016
ms.author: billmath

---
# <a name="topologies-for-azure-ad-connect"></a>Topologias para o Azure AD Connect
O objetivo deste tópico é descrever diferentes topologias locais e do Azure AD com o Azure AD Connect Sync como a solução de integração principal. Ele descreve as configurações com e sem suporte.

Legenda de imagens no documento:

| Descrição | ícone |
| --- | --- |
| Floresta do Active Directory local |![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Active Directory com importação filtrada |![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Servidor do Azure AD Connect Sync |![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| “Modo de preparo” do servidor do Azure AD Connect Sync |![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync com FIM2010 ou MIM2016 |![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Servidor do Azure AD Connect Sync detalhado |![Sincronizar](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Diretório do AD do Azure |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Cenário sem suporte |![Sem suporte](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest,-single-azure-ad-directory"></a>Uma única floresta, um único diretório do AD do Azure
![Diretório único de floresta única](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma única floresta local, com um ou vários domínios e um único diretório do Azure AD (também conhecido como locatário). Para a autenticação do Azure AD, é usada a sincronização de senha. A instalação expressa do Azure AD Connect dá suporte apenas a esta topologia.

### <a name="single-forest,-multiple-sync-servers-to-one-azure-ad-directory"></a>Uma única floresta, vários servidores de sincronização para um diretório do AD do Azure
![Floresta única filtrada sem suporte](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não há suporte para vários servidores do Azure AD Connect sync conectados ao mesmo diretório do Azure AD, exceto para um [servidor de preparo](#staging-server). Não haverá suporte para tal mesmo se estiverem configurados para sincronizar um conjunto mutuamente exclusivo de objetos. Talvez você tenha considerado esta opção caso não possa acessar todos os domínios na floresta de um único servidor ou para distribuir a carga entre vários servidores.

## <a name="multiple-forests,-single-azure-ad-directory"></a>Várias florestas, um único diretório do AD do Azure
![Diretório único com várias florestas](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local. Exemplos típicos são designs com florestas de conta-recurso resultantes após uma fusão ou aquisição.

Quando você tem várias florestas, todas elas devem ser acessíveis pelo único servidor do Azure AD Connect Sync. Você não precisa adicionar o servidor a um domínio. O servidor pode ser colocado em uma rede DMZ, se necessário, para alcançar todas as florestas.

O assistente de instalação do Azure AD Connect oferece várias opções para consolidar os usuários representados em várias florestas. O objetivo é que um usuário seja representado somente uma vez no Azure AD. Há algumas topologias comuns que você pode configurar o caminho de instalação personalizada do assistente de instalação. Selecione a opção correspondente que representa sua topologia na página **Identificando exclusivamente seus usuários**. A consolidação está configurada somente para os usuários. Grupos duplicados não são consolidados com a configuração padrão.

As topologias comuns são discutidas na próxima seção: [Separar topologias](#multiple-forests-separate-topologies), [Malha completa](#multiple-forests-full-mesh-with-optional-galsync) e [Recurso da Conta](#multiple-forests-account-resource-forest).

A configuração padrão do Azure AD Connect sync supõe:

1. Os usuários têm apenas uma conta habilitada e a floresta em que essa conta se encontra é usada para autenticar o usuário. Essa suposição serve para a sincronização de senha e para a Federação. UserPrincipalName e sourceAnchor/immutableID vêm desta floresta.
2. Os usuários têm apenas uma caixa de correio.
3. A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (Lista de Endereços Global) do Exchange. Se não houver nenhuma caixa de correio no usuário, qualquer floresta pode ser usada para contribuir com esses valores de atributos.
4. Se você tiver uma caixa de correio vinculada, então também há outra conta em outra floresta usada para fazer logon.

Se seu ambiente não corresponder a essas suposições, acontecerá o seguinte:

* Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização escolherá uma e ignorará as outras.
* Uma caixa de correio vinculada sem nenhuma outra conta ativa não é exportada para o Azure AD. A conta de usuário não é representada como um membro de nenhum grupo. Uma caixa de correio vinculada em DirSync seria representada como uma caixa de correio normal, por isso esta alteração é um comportamento intencionalmente diferente para dar melhor suporte a cenários de várias florestas.

Mais detalhes podem ser encontrados em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests,-multiple-sync-servers-to-one-azure-ad-directory"></a>Várias florestas, vários servidores de sincronização para um diretório do AD do Azure
![Sincronização múltipla de várias florestas sem suporte](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Não há suporte para a conexão de mais de um servidor do Azure AD Connect Sync a um único diretório do Azure AD. A exceção é o uso de um [servidor de preparo](#staging-server).

### <a name="multiple-forests-–-separate-topologies"></a>Várias florestas – topologias separadas
**Os usuários são representados apenas uma vez em todos os diretórios**

![Usuários de várias florestas uma vez](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Topologias separadas de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Nesse ambiente, todas as florestas locais são tratados como entidades separadas e nenhum usuário estaria presente em nenhuma outra floresta.
Cada floresta tem sua própria organização do Exchange e não há nenhuma GALSync entre as florestas. Esta topologia pode ser a situação após uma fusão/aquisição ou em uma organização em que cada unidade de negócios opera isolada das outras. Essas florestas serão da mesma empresa no Azure AD e aparecerão com uma GAL unificada.
Nesta figura, cada objeto em cada floresta será representado uma vez no metaverso e agregado no diretório de destino do Azure AD.

### <a name="multiple-forests-–-match-users"></a>Várias florestas – usuários correspondentes
**As identidades de usuário existem em vários diretórios**

Grupos de distribuição e de segurança comuns a todos esses cenários podem conter uma mescla de usuários, contatos e FSPs (entidades de segurança externas).

FSPs são usados no ADDS para representar os membros de outras florestas em um grupo de segurança. Todas as FSPs serão resolvidas para o objeto real no Azure AD.

### <a name="multiple-forests-–-full-mesh-with-optional-galsync"></a>Várias florestas – malha completa com GALSync opcional
**As identidades de usuário existem em vários diretórios. Correspondência usando: atributo de email**

![Email de usuários de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Malha completa de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que usuários e recursos sejam localizados em qualquer floresta; normalmente, haveriam seria relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, poderá haver uma solução GALSync local opcional. Cada usuário deve ser representado como um contato em todas as outras florestas. A GALSync normalmente é implementada usando o Forefront Identity Manager 2010 ou o Microsoft Identity Manager 2016. O Azure AD Connect não pode ser usado para GALSync local.

Nesse cenário, os objetos de identidade normalmente são ingressados usando o atributo de email. Um usuário com uma caixa de correio em uma floresta é ingressado aos contatos em outras florestas.

### <a name="multiple-forests-–-account-resource-forest"></a>Várias florestas – Floresta de recursos de conta
**As identidades de usuário existem em vários diretórios. Correspondência usando os atributos ObjectSID e msExchMasterAccountSID**

![ObjectSID de usuários de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![AccountResource de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Em uma topologia de floresta de conta-recurso, você tem uma ou mais florestas de conta com contas de usuário ativas. Você também terá uma ou mais florestas de recursos com contas desabilitadas.

Nesse cenário, uma (ou mais) **floresta de recursos** confia em todas as **florestas de conta**. Essa floresta de recurso normalmente tem um esquema do AD estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, bem como outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nesta floresta e a caixa de correio está vinculada à floresta da conta.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 têm certas restrições a topologias com suporte. Se você planeja usar qualquer um deles, leia o tópico de topologias com suporte para a carga de trabalho.

| Carga de trabalho |
| --- | --- |
| Exchange Online |
| Skype for Business |

## <a name="staging-server"></a>servidor de preparo
![servidor de preparo](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

O Azure AD Connect dá suporte à instalação em um segundo servidor no **Modo de preparo**. Um servidor nesse modo lê dados de todos os diretórios conectados, mas não grava nada. Ele está usando o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados de identidade. Em um desastre em que o servidor primário falha, você pode fazer failover para o servidor de preparo. Você pode fazer isso no Assistente do Azure AD Connect. Esse segundo servidor pode estar preferivelmente localizado em um datacenter diferente, pois nenhuma infraestrutura é compartilhada com o servidor primário. É necessário copiar manualmente qualquer alteração de configuração feita no servidor principal para o segundo servidor.

Um servidor de preparo também pode ser usado para testar uma nova configuração personalizada e o efeito sobre seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, transforme o servidor de preparo no servidor ativo e defina o antigo servidor ativo para modo de preparo.

Esse método também pode ser usado para substituir o servidor de sincronização ativo. Prepare o novo servidor e configure-o no modo de preparo. Verifique se ele está em boas condições, desabilite o modo de preparo (tornando-o ativo) e desligue o servidor ativo no momento.

É possível ter mais de um servidor de preparo quando você quiser ter vários backups em datacenters diferentes.

## <a name="multiple-azure-ad-directories"></a>Vários diretórios do AD do Azure
A Microsoft recomenda ter um único diretório no Azure AD para uma organização.
Antes de você planeja usar vários diretórios do Azure AD, consulte esses tópicos que abrangem cenários comuns que permitirão a você usar um único diretório.

| Tópico |
| --- | --- |
| Delegação usando unidades administrativas |

![Diretório múltiplo de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Há uma relação de 1:1 entre um servidor do Azure AD Connect Sync e um diretório do AD do Azure. Para cada diretório do Azure AD, você precisará de uma instalação de servidor do Azure AD Connect Sync. As instâncias de diretório do Azure AD são isolada por padrão e os usuários em um diretório não poderá ver os usuários em outro. Se essa separação for desejada, essa será uma configuração com suporte, porém, em caso contrário, você deverá usar o modelo de diretório único do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-directory"></a>Cada objeto uma única vez em um diretório do AD do Azure
![Floresta única filtrada](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nessa topologia, um servidor do Azure AD Connect sync está conectado a cada diretório do Azure AD. Os servidores do Azure AD Connect sync devem ser configurados para filtragem para que cada um deles tenha um conjunto de objetos mutuamente exclusivos para operação. Você pode, por exemplo, definir o escopo de cada servidor para um determinado domínio ou UO. Um domínio DNS só pode ser registrado em um único diretório do Azure AD. Os UPNs dos usuários no AD local devem usar também os namespaces separados. Por exemplo, na figura acima, três sufixos de UPN separado são registrados no AD local: contoso.com, fabrikam.com e wingtiptoys.com. Os usuários em cada domínio do AD local usam um namespace diferente.

Não há nenhum GALsync entre as instâncias de diretório do Azure AD. O catálogo de endereços no Exchange Online e do Skype for Business apenas mostra usuários no mesmo diretório.

Essa topologia tem as seguintes restrições aos cenários que, caso contrário, têm suporte:

* Apenas um dos diretórios do AD do Azure pode habilitar o Exchange híbrido com o Active Directory local.
* Os dispositivos Windows 10 só podem ser associados a um diretório do AD do Azure.

O requisito para um conjunto de objetos mutuamente exclusivo também se aplica ao write-back. Alguns recursos de write-back não tem suporte com esta topologia, pois esses recursos supõem a existência de uma única configuração local:

* Write-back de grupo com configuração padrão
* Write-back de dispositivo

### <a name="each-object-multiple-times-in-an-azure-ad-directory"></a>Cada objeto várias vezes em um diretório do AD do Azure
![Diretório múltiplo de floresta única sem suporte](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Conectores múltiplos de floresta única sem suporte](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

* Ele não tem suporte para sincronização do mesmo usuário para vários diretórios do AD do Azure.
* Ele não dá suporte para alterar uma configuração para fazer os usuários de um Azure AD aparecerem como contatos em outro diretório do Azure AD.
* Também não há suporte para modificar o Azure AD Connect Sync para se conectar a vários diretórios do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync com o uso de write-back
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Diretórios do AD do Azure são isolados por padrão.

* Não há suporte para alterações da configuração do Azure AD Connect Sync para ler dados de outro diretório do Azure AD.
* Não há suporte para a exportação de usuários como contatos para outro AD local usando o Azure AD Connect Sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync com o servidor de sincronização local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Há suporte para o uso do FIM2010/MIM2016 local para usuários GALsync entre duas organizações do Exchange. Os usuários em uma organização aparecerão como usuários/contatos externos em outras organizações. Esses ADs locais diferentes poderão então ser sincronizados para seus próprios diretórios do AD do Azure.

## <a name="next-steps"></a>Próximas etapas
Para saber como instalar o Azure AD Connect para esses cenários, veja [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!--HONumber=Oct16_HO2-->


