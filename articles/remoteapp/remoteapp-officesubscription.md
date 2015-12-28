
<properties 
    pageTitle="Como usar sua assinatura do Office 365 com o Azure RemoteApp | Microsoft Azure"
	description="Saiba como usar sua assinatura do Office 365 no Azure RemoteApp para compartilhar aplicativos do Office."
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/14/2015" 
    ms.author="elizapo" />



# Como usar sua assinatura do Office 365 com o Azure RemoteApp

Você sabia que você pode usar sua assinatura do Office 365 existente no Azure RemoteApp para compartilhar aplicativos do Office da nuvem? Continue lendo para obter informações sobre as opções do Office 365 com o Azure RemoteApp, incluindo links para artigos sobre o Office 365 que ajudarão você a aproveitar ao máximo sua assinatura.

## Como usar contas do Office 365 para o Azure RemoteApp?
Veja o novo artigo de Peter para todas as informações: [Como usar o Azure RemoteApp com contas de usuário do Office 365](remoteapp-o365user.md)

## Posso usar minha assinatura do Office 365 para executar aplicativos do Office no Azure RemoteApp?

Sim! Na verdade, usar sua assinatura do Office 365 é a única maneira de levar seus aplicativos do Office para o Azure RemoteApp.

(Observação: se a sua implantação do Azure RemoteApp for entregue por um parceiro de hospedagem, ele poderá conseguir fornecer licenças do Office com base em um [Contrato de Licença do Provedor de Serviço](http://www.microsoft.com/pt-BR/Licensing/licensing-programs/spla-program.aspx))


A boa notícia sobre a sua assinatura do Office 365 é que ela permite que você use a mesma licença de usuário em várias plataformas e ambientes diferentes, incluindo a nuvem do Azure. Quando você usa os aplicativos do Office no Azure RemoteApp, você não precisa comprar licenças adicionais ou configurar suas licenças existentes de uma maneira especial. Tudo o que você precisa é de uma assinatura do Office 365 que inclua o [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

O Office 365 ProPlus permite a [ativação de computador compartilhado](https://technet.microsoft.com/library/Dn782860.aspx) - esse recurso habilita a ativação temporária baseada no usuário para o Office em ambientes virtuais e de nuvem como o Azure RemoteApp (e os Serviços de Área de Trabalho Remota).

Quais planos do Office 365 incluem o Office 365 ProPlus? Confira a tabela de [Disponibilidade de serviço em cada plano](https://technet.microsoft.com/library/office-365-plan-options.aspx). Vale lembrar que nem todos os planos incluem o Office 365 ProPlus (por exemplo, o plano Office 365 Business). Se o seu plano não incluí-lo, considere a atualização para um plano que o inclua (por exemplo, Office 365 Education E3).

## OK. Como as minhas licenças do Office 365 ProPlus são usadas com o Azure RemoteApp?

Cada licença de usuário para o Office 365 ProPlus permite que um único usuário ative os aplicativos do Office em até cinco computadores, além de tablets e telefones. Cada ativação é registrada com o usuário até que ele desative o Office no dispositivo. (Os usuários podem gerenciar seus dispositivos no [portal do Office 365](https://portal.office365.com/).)

Com o Azure RemoteApp, um único usuário pode fazer logon em vários computadores no mesmo dia sem perceber. Isso ocorre porque o serviço gerencia e dimensiona automaticamente os recursos na nuvem, enquanto o usuário vê apenas os aplicativos e programas que você compartilhou. Para este cenário, o Office 365 ProPlus oferece um modo de ativação do computador compartilhado – isso significa que o usuário não precisa realizar nenhum gerenciamento de licenças para acessar esses recursos e que os computadores individuais não contam para o limite de ativação de cinco computadores.

Contanto que você (o administrador) atribua licenças do Office 365 ProPlus aos seus usuários, eles podem usar o Office em seus dispositivos pessoais, bem como por meio de sua coleção do Azure RemoteApp.

## Quais aplicativos do Office posso usar com o Office 365 e o Azure RemoteApp?

Você pode usar sua assinatura do Office 365 ProPlus para compartilhar o Office 2013 e o Office 2016 (após seu lançamento). O Azure RemoteApp não dá suporte às versões anteriores do Office.

## E quanto ao Visio Pro ou o Project Pro?

A imagem do Office 365 ProPlus incluída em sua assinatura do RemoteApp inclui tanto o Visio Pro como o Project Pro. Mas você não pode usar sua assinatura do Office 365 ProPlus para ativar esses programas – cada um deles tem sua própria licença. Você pode ativá-los no [portal do Office 365](https://portal.office365.com/).

Você não precisa licenciar esses programas se não desejar usá-los. Ative apenas os programas que deseja usar – e ignore os outros. Você ainda poderá vê-los na imagem, mas não poderá usá-los.

## Como posso começar a usar o Office 365 e o Azure RemoteApp?

Agora que você conhece os detalhes do licenciamento do Office 365, vamos mostrar como você pode usá-lo no Azure RemoteApp – é muito fácil:

Ao criar sua coleção do Azure RemoteApp, use a imagem do **Office 365 ProPlus (Assinatura necessária)**.

![Imagem do Azure RemoteApp com o Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Esta imagem contém a versão mais recente do Windows Server e do Office 365 ProPlus. Depois de configurar sua coleção (incluindo aplicativos de publicação), seus usuários apenas fazem logon no Azure RemoteApp (usando seu cliente do RemoteApp) e fornecem suas credenciais do Office 365 para todos os aplicativos do Office. As licenças serão entregues automaticamente sem que haja a necessidade de configuração ou gerenciamento.

## Posso criar uma imagem personalizada com o Office 365 ProPlus?

Você pode criar uma imagem personalizada para a sua coleção que contém o Office 365 ProPlus. Há duas opções: use a imagem da galeria do Azure que fornecemos ou crie sua própria imagem personalizada e instale o Office 365 ProPlus nela.

### Usar a imagem da galeria do Azure

A maneira mais fácil de implantar o Office 365 ProPlus em uma coleção é [começar com uma das imagens da galeria do Azure](remoteapp-image-on-azurevm.md) incluídas em sua assinatura do Azure RemoteApp. Lembre-se de escolher a imagem do **Host da Sessão da Área de Trabalho Remota do Windows Server com o Office 365 ProPlus pré-instalado**. Depois, instale outros aplicativos que deseja na imagem – e você está pronto para começar a usá-los.

### Usar uma imagem personalizada

Sempre é possível criar uma imagem personalizada – você pode criar uma [VM do Azure](remoteapp-image-on-azurevm.md) ou [criar a imagem localmente](remoteapp-create-custom-image.md) e carregá-la no Azure. Em ambos os casos, lembre-se de instalar o Office 365 ProPlus usando o nó de ativação de computador compartilhado. Use a [Ferramenta de Implantação do Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) e siga as [instruções](https://technet.microsoft.com/library/Dn782858.aspx) para a instalação.

### Desabilitar atualizações automáticas para o Office 365 ProPlus na imagem personalizada – IMPORTANTE

Sua imagem personalizada é usada pelo Azure RemoteApp como um modelo para adicionar recursos adicionais conforme a demanda de seu usuários aumenta. Para evitar atrasos e problemas de conexão, desabilite as atualizações automáticas para o Office na imagem. Se você não fizer isso, cada recurso criado com esse modelo será atualizado automaticamente quando iniciado. Em vez disso, use o processo padrão do Azure RemoteApp para atualizar sua imagem personalizada. Dessa forma, você atualiza os aplicativos do Office uma vez na imagem do modelo e permite que o Azure RemoteApp se encarregue de obter as atualizações para seus usuários.

Para desabilitar as atualizações automáticas, adicione o seguinte ao arquivo de configuração da Ferramenta de implantação do Office:

		<Updates Enabled="FALSE" />

Agora, o arquivo de configuração deve conter estas linhas:
	
		<Display Level="NONE" AcceptEULA="TRUE" />
		<Property Name="SharedComputerLicensing" Value="1" />
		<Updates Enabled="FALSE" />

## Como é possível atualizar uma imagem com o Office 365 ProPlus?

Há muitas razões para atualizar a imagem em sua coleção. Veja abaixo algumas delas:

- Obter as atualizações mais recentes do Windows 
- Obter as atualizações mais recentes do aplicativo Office 365 ProPlus
- Atualizar seu aplicativo personalizado
- Alterar outras definições de configuração da própria imagem

Para obter as etapas completas para atualizar sua coleção para usar a imagem atualizada, acesse [aqui](remoteapp-update.md). Mas para obter informações sobre como atualizar a imagem e o Office 365 ProPlus, confira as informações a seguir.

Você tem duas opções para atualizar sua imagem: substituir a imagem por uma totalmente nova ou atualizar manualmente a imagem existente.

### Substituir a imagem pela imagem mais recente da galeria do Azure e adicionar personalizações
Com essa opção, você permite que a Microsoft se encarregue das atualizações do Windows Server e do Office 365 ProPlus. Em vez de atualizar a imagem existente, você criará uma imagem totalmente nova com base na imagem mais recente da galeria. Depois, repita as etapas anteriores para personalizar a imagem – instalar aplicativos personalizados, modificar a configuração da imagem, etc.

As imagens da galeria são atualizadas regularmente, por isso você pode ficar tranquilo e saber que os seus aplicativos do Windows Server e do Office 365 ProPlus estão atualizados. Claro que a desvantagem é que você precisará aplicar suas personalizações sempre que tiver uma nova imagem. Você pode criar scripts para automatizar a configuração de suas personalizações.

### Atualizar manualmente a imagem existente

Com essa opção, você pode usar as ferramentas padrão do Windows para aplicar atualizações à imagem. Para o Office 365 ProPlus, use a Ferramenta de implantação do Office para baixar e instalar as atualizações ou as versões mais recentes do Office 365 ProPlus.

> [AZURE.IMPORTANT]Lembre-se: desabilite as atualizações automáticas do Office 365 ProPlus.

Precisa de mais informações sobre como usar a Ferramenta de implantação do Office para atualizações?

- [Implantar Clique para Executar para os produtos do Office 365 usando a Ferramenta de implantação do Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Implantando e atualizando o Office 365 ProPlus usando a Ferramenta de Implantação do Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vídeo)
- [Definir configurações de atualização para o Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)

<!---HONumber=AcomDC_1217_2015-->