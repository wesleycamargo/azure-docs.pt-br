<properties 
    pageTitle="Que tipo de coleção é necessária para o Azure RemoteApp? | Microsoft Azure" 
    description="Saiba mais sobre os tipos de coleções disponíveis com o Azure RemoteApp." 
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
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Que tipo de coleção é necessária para o Azure RemoteApp?

O Azure RemoteApp permite que você compartilhe aplicativos e recursos com usuários em qualquer dispositivo. Fazemos isso criando coleções para manter os aplicativos e os recursos e, em seguida, e você compartilha essas coleções com os usuários. Há duas opções diferentes de coleção, com opções de rede e de autenticação – qual é a ideal para você?

Vamos examinar as diferentes considerações e opções que você precisa fazer para aproveitar ao máximo sua coleção do Azure RemoteApp.


## Resumo rápido das diferenças entre os tipos de coleção

| | Nuvem | Híbrido |
|-----------|-------|--------|
|Usa uma VNET existente| Sim| Sim|
|Requer contas conectadas ao AD (DirSync)| Não| Sim|
|Requer o ingresso no domínio| Não| Sim|
|Requer um controlador de domínio acessível à VNET| Não| Sim|

## Coleções na nuvem
- Rápido para criar – a coleção é rapidamente provisionada, o que significa que seus aplicativos chegam aos usuários mais rapidamente.
- Traga seus próprios aplicativos ou compartilhe os nossos. Você pode usar uma imagem personalizada (criada por meio de uma VM do Azure) ou uma das imagens incluídas com a sua assinatura.
- Você não precisa configurar uma conexão entre sua coleção e seu domínio local.
- Mas, opcionalmente, você pode usar sua própria VNET do Azure para fornecer acesso no ambiente local para o compartilhamento de dados ou para usar a autenticação não Windows em recursos como o SQL Server (usando a autenticação de banco de dados).


OK, como posso criar uma?

- Somente nuvem? Crie com a opção **Criação Rápida** no portal.
- Nuvem + VNET? Crie usando a opção **Criar com VNET**, mas NÃO escolha ingressar em um domínio.

## Coleções híbridas
- Fornece acesso completo à rede local + VNET do Azure.
- Inclui o acesso ao ingresso no domínio para aplicativos e dados. Os aplicativos remotos podem fazer a autenticação em seu Active Directory local – em seguida, eles poderão acessar os recursos em seu domínio.
- Habilitar o monitoramento e gerenciamento avançados com soluções existentes do System Center e Políticas de Grupo do Windows (por meio de uma imagem personalizada criada no Windows Server 2012 R2)
- Suporte à [Rota Expressa](http://azure.microsoft.com/services/expressroute/) para conectar sua VNET do Azure à sua VNET local.

Crie usando a opção **Criar com VNET** e ESCOLHA ingressar em um domínio.

## Opções de autenticação
O Azure RemoteApp dá suporte a contas da Microsoft e a contas do Active Directory do Azure, mas nem todas as coleções dão suporte a todos os métodos.

| Tipo de conta | | Nuvem | Nuvem + VNET | Híbrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|-------|
| Conta da Microsoft | | Sim | Sim | Não |
| Active Directory do Azure (Azure AD) | | | | |
| | Somente AD do Azure | Sim | Sim | Não |
| | AD Connect com sincronização de senha | Sim | Sim | Sim |
| | AD Connect sem sincronização de senha | Sim | Sim | Não |
| | AD Connect com o AD FS | Sim | Sim | Sim |
| | Provedores de identidade terceiros com suporte do Azure (por exemplo, Ping) | Sim | Sim | Sim |
| Multi-Factor Authentication | | Sim | Sim | Sim |



### Nuvem e Nuvem + VNET 
Com coleções de nuvem, você pode usar contas da Microsoft, contas do AD do Azure ou uma combinação aos duas. Use as contas que funcionam melhor para seus usuários.

Não há nenhum requisito específico para o uso das contas da Microsoft.

Se desejar usar contas do AD do Azure , você precisa se certificar de que seu locatário do AD do Azure corresponde ao associado à sua assinatura. Quando você criou sua assinatura do Azure RemoteApp, o locatário do AD do Azure que você estava usando foi automaticamente associado à sua assinatura. Qualquer usuário do AD do Azure ao qual você forneça permissão precisa ser esse mesmo locatário. Se necessário, é possível [alterar o locatário do AD do Azure](remoteapp-changetenant.md) associado à sua assinatura.
 
### Híbrido (ou nuvem + AD do Azure + AD)

Usar o AD do Azure + Active Directory local é um pré-requisito para uma coleção híbrida. Você precisa usar o AD Connect para integrar os dois diretórios. Mas quando se trata da maneira como você configura o AD Connect, você tem algumas opções.

Há dois cenários do AD Connect – usar a sincronização de senha ou a federação do AD. Confira as [informações do AD Connect](active-directory-aadconnect.md) para descobrir qual delas funciona melhor para você.

Você também pode usar o AD do Azure + AD com uma coleção na nuvem. Lembre-se de seguir as mesmas etapas de configuração.

Confira os [requisitos do AD do Azure + Active Directory para o Azure RemoteApp](remoteapp-ad.md) para obter as etapas necessárias para configurar o AD do Azure e o Active Directory.

## Crie sua coleção!
OK, acho que desvendamos tudo agora. Há apenas uma coisa que resta fazer: criar sua primeira coleção do Azure RemoteApp.

[Criar uma coleção na nuvem](remoteapp-create-cloud-deployment.md) ou [criar uma coleção híbrida](remoteapp-create-hybrid-deployment.md) – basta começar a criar.

<!---HONumber=AcomDC_0114_2016-->