<properties
    pageTitle="Uso do Outlook no Azure RemoteApp | Microsoft Azure" 
    description="Saiba como configurar e usar o Outlook no Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/18/2016"
    ms.author="elizapo" />

# Usando o Microsoft Outlook no Azure RemoteApp

O Azure RemoteApp dá suporte ao Microsoft Outlook O365. Saiba mais sobre como o [Office funciona no Azure RemoteApp](remoteapp-officesubscription.md). Há algumas configurações recomendadas para o Outlook quando usado no Azure RemoteApp.

## Modo cache
O modo cache é uma configuração recomendada para o uso do Outlook no Azure RemoteApp. Quando você configura uma conta do Outlook 2013 para usar o Modo Cache do Exchange, o Outlook 2013 funciona de uma cópia local da caixa de correio do Microsoft Exchange do usuário armazenada em um arquivo OST (arquivo de dados offline) no computador do usuário, junto com o OAB (Catálogo de Endereços Offline). A caixa de correio em cache e o OAB são atualizados periodicamente no serviço O365. Leia mais sobre [as diferenças entre o modo cache e o modo online](https://technet.microsoft.com/library/jj683103.aspx).

O usuário pode selecionar **Modo Cache do Exchange** ou **Modo Online** durante a configuração da conta ou durante a alteração das configurações de conta. Você também pode implantar um modo ou outro usando a OCT (Ferramenta de Personalização do Office) ou a Política de Grupo.

Leia [instruções passo a passo sobre como habilitar o modo cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## Pesquisar
No Azure RemoteApp, há limitações para o uso da pesquisa no Outlook. O Azure RemoteApp usa VMs em pool para acomodar sessões de usuário. A indexação de pesquisa depende da ID da máquina, que é diferente para VMs diferentes. É possível que sempre que um usuário efetuar o logon no Azure RemoteApp, ele seja direcionado a uma nova VM. Isso significa que, se habilitarmos a pesquisa local, o indexador será executado sempre que a ID da máquina for alterada (quando o usuário estiver em uma VM diferente). Dependendo do tamanho do arquivo .OST, o indexador pode demorar muito para concluir e usar os recursos necessários para outros aplicativos. A pesquisa não seria apenas lenta, como talvez não produzisse resultados. Uma maneira de solucionar isso seria habilitar a pesquisa online por padrão. Infelizmente, a pesquisa indexada/local não pode ser desabilitada e a pesquisa online não pode ser habilitada por padrão no Outlook 2013.

O Outlook 2016 tem uma solução para resolver isso, fornecendo uma nova experiência de pesquisa online para caixas de correio hospedadas no Exchange 2016 (ou hospedadas no Office 365). Essa solução usa os resultados de pesquisa de servidor no cache local (OST). O Outlook pode voltar a usar o indexador de pesquisa em alguns cenários, mas a maioria das pesquisas usaria o modo online. A recomendação do Azure RemoteApp é usar o Outlook 2016 se a pesquisa de emails for um cenário muito importante.

<!---HONumber=AcomDC_0525_2016-->