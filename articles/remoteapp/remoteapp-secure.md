
<properties
    pageTitle="Proteger aplicativos e recursos no RemoteApp do Azure | Microsoft Azure"
    description="Saiba como bloquear aplicativos e recursos no Azure RemoteApp"
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
    ms.date="06/13/2016"
    ms.author="elizapo" />



# Proteger aplicativos e recursos no Azure RemoteApp

O Azure RemoteApp fornece aos usuários acesso aos aplicativos do Windows gerenciados centralmente, o que permite a você controlar o que os usuários podem e não podem fazer. Isso é especialmente útil quando o usuário está se conectando de um dispositivo não gerenciado (como o Macbook pessoal) e você deseja controlar o acesso ou a experiência do usuário.

Por exemplo, se estiver usando o Active Directory para autenticação do usuário e desejar impedir que os usuários copiem dados de um aplicativo, você poderá configurar uma Política de Grupo de Área de Trabalho Remota para impedir que eles copiem dados.

Outro exemplo é se você quiser bloquear o acesso à Internet para um aplicativo específico de sua coleção. Você pode criar uma regra do Firewall do Windows que bloqueie o acesso quando cria a imagem da sua coleção.

## Opções de implementação

  Veja a seguir as principais opções de implementação, que podem ser usadas individualmente ou em conjunto, conforme a necessidade:

1.	Se sua coleção do RemoteApp fizer parte de um domínio, você poderá impor qualquer [Política de Grupo](https://technet.microsoft.com/library/cc725828.aspx) (exceto as políticas de tempo limite Ocioso e Desconectar, descritas [aqui](../azure-subscription-service-limits.md)).
2.	Como alternativa à Política de Grupo (se a coleção não fizer parte de um domínio ou você não tiver os privilégios corretos no AD), você pode configurar [Políticas Locais](https://technet.microsoft.com/library/cc775702.aspx) na sua imagem de modelo. Observe que essas políticas de grupos superam as políticas locais quando há um conflito.
3.	Algumas configurações de sistema operacional/aplicativo não podem ser definidas pela política, mas podem ser por meio da chave de Registro usando a [ferramenta RegEdit](./remoteapp-hybridtrouble.md) durante a configuração da imagem de modelo.
4.	Você pode usar o [Firewall do Windows](http://windows.microsoft.com/pt-BR/windows-8/Windows-Firewall-from-start-to-finish) para controlar o acesso bidirecional à rede do computador em que o aplicativo está em execução. Apenas certifique-se de não bloquear as URLs e portas definidas aqui.
5.	Você pode usar o [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) para controlar quais aplicativos e arquivos os usuários podem executar. Por exemplo, usuários experientes podem descobrir como executar aplicativos que você não publicou, mas que estão disponíveis na imagem usada para criar sua coleção — o AppLocker pode impedir isso.

## Informações detalhadas

- As políticas RDS a seguir, provavelmente, são as mais úteis:
	- [Redirecionamento de dispositivo e recurso](https://technet.microsoft.com/library/ee791794.aspx)
	- [Redirecionamento de impressora](https://technet.microsoft.com/library/ee791784.aspx)
	- [Perfis](https://technet.microsoft.com/library/ee791865.aspx).
- Observe que a configuração de redirecionamentos pelo módulo PowerShell do RemoteApp (conforme visto [aqui](./remoteapp-redirection.md)) conta com o computador cliente para impor a política, de modo que se a segurança for o objetivo principal, convém impor a política usando a política local da imagem de modelo ou a política de grupo.
- [Políticas do Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Políticas do Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (incluindo [como personalizar a barra de ferramentas do Office](https://technet.microsoft.com/library/cc179143.aspx)).

<!---HONumber=AcomDC_0622_2016-->