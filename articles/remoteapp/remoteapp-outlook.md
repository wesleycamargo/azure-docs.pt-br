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
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Usando o Microsoft Outlook no Azure RemoteApp

> [AZURE.IMPORTANT]
> O RemoteApp do Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

O Azure RemoteApp dá suporte ao Microsoft Outlook O365. Saiba mais sobre como o [Office funciona no Azure RemoteApp](remoteapp-officesubscription.md). Há algumas configurações recomendadas para o Outlook quando usado no Azure RemoteApp.

## <a name="cached-mode"></a>Modo cache
O modo cache é uma configuração recomendada para o uso do Outlook no Azure RemoteApp. Quando você configura uma conta do Outlook 2013 para usar o Modo Cache do Exchange, o Outlook 2013 funciona de uma cópia local da caixa de correio do Microsoft Exchange do usuário armazenada em um arquivo OST (arquivo de dados offline) no computador do usuário, junto com o OAB (Catálogo de Endereços Offline). A caixa de correio em cache e o OAB são atualizados periodicamente no serviço O365. Leia mais sobre [as diferenças entre o modo cache e o modo online](https://technet.microsoft.com/library/jj683103.aspx).

O usuário pode selecionar **Modo Cache do Exchange** ou **Modo Online** durante a configuração da conta ou durante a alteração das configurações de conta. Você também pode implantar um modo ou outro usando a OCT (Ferramenta de Personalização do Office) ou a Política de Grupo.  

Leia [instruções passo a passo sobre como habilitar o modo cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Pesquisar
No Azure RemoteApp, há limitações para o uso da pesquisa no Outlook. O Azure RemoteApp usa VMs em pool para acomodar sessões de usuário. A indexação de pesquisa depende da ID da máquina, que é diferente para VMs diferentes. É possível que sempre que um usuário efetuar o logon no Azure RemoteApp, ele seja direcionado a uma nova VM. Isso significa que, se habilitarmos a pesquisa local, o indexador será executado sempre que a ID da máquina for alterada (quando o usuário estiver em uma VM diferente). Dependendo do tamanho do arquivo .OST, o indexador pode demorar muito para concluir e usar os recursos necessários para outros aplicativos. A pesquisa não seria apenas lenta, como talvez não produzisse resultados. Usar um perfil de conta do Modo Online poderia contornar esse problema, mas o desempenho geral seria afetado devido à falta de um cache local (veja o link acima para saber mais sobre a diferença entre o modo em cache e online). Infelizmente, a pesquisa indexada/local não pode ser desabilitada e a pesquisa online não pode ser habilitada por padrão no Outlook 2013.



<!--HONumber=Oct16_HO2-->


