
<properties 
    pageTitle="Requisitos do AD do Azure + Active Directory para o Azure RemoteApp | Microsoft Azure" 
    description="Saiba como configurar o Active Directory para trabalhar com o Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/28/2015" 
    ms.author="elizapo" />



# Requisitos do AD do Azure + Active Directory para o Azure RemoteApp



Para a coleção híbrida do Azure RemoteApp ou para uma coleção na nuvem que você deseja federar usando o AD Connect, você precisa fazer o seguinte:

### Conecte o AD do Azure e o Active Directory

Se desejar conectar seu locatário do AD do Azure e seus ambientes locais do Active Directory, use o AD Connect. Levará apenas [quatro cliques](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) para conectar os dois diretórios.

Observação - a sincronização de diretórios é necessária para coleções híbridas.

### Verifique se o seu “@domain.com” é correspondente
Antes de começar, lembre-se de que o UPN da floresta local corresponde ao sufixo do seu domínio do AD do Azure.

Depois de configurar o sufixo de domínio UPN no AD do Azure, todos os usuários que fizerem logon no Azure RemoteApp farão logon como “usuário @<the suffix you set up>”. Certifique-se de que os usuários também podem fazer logon com o mesmo user@suffix no domínio local. Em alguns casos, é possível configurar um nome de domínio no AD do Azure, ao mesmo tempo que você especifica um sufixo de domínio diferente para o usuário local. Nesse caso, seus usuários não poderão se conectar a nenhum computador ou recurso ingressado no domínio por meio do Azure RemoteApp.

Por exemplo, se você configurar o sufixo de domínio UPN no AAD como contoso.com, mas alguns usuários locais/no AD estiverem configurados para fazer logon em @contoso.uk, esses usuários não poderão fazer logon corretamente na coleção ARA. Os UPNs dos usuários no AAD e no AD devem ser iguais para que o logon seja possível.

### Criar objetos para o Azure RemoteApp
Você também precisará criar os seguintes objetos locais do Active Directory:

- Uma conta de serviço para fornecer acesso aos recursos do domínio para programas RemoteApp unindo os pontos de extremidade RDSH ao domínio local.
- Uma OU (unidade organizacional) para conter objetos de computador do RemoteApp. Uso da OU é recomendável (mas não obrigatório) para isolar as contas e as políticas que você usará com o RemoteApp.

Você precisa desses dois objetos ao criar sua coleção do RemoteApp, portanto lembre-se de realizar essas etapas primeiro.

<!---HONumber=Oct15_HO3-->