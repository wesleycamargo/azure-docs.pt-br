---
title: "O que há de novo no Azure RemoteApp? | Microsoft Docs"
description: "Saiba mais sobre alterações e aprimoramentos feitos ao Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 40f1ba79-80f1-47bd-bf39-f86c03e2306a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 6720bb6471ef36179042562c751e1a501979f02b
ms.lasthandoff: 03/31/2017


---
# <a name="whats-new-in-azure-remoteapp"></a>O que há de novo no Azure RemoteApp?
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Uma das vantagens do Azure RemoteApp é que estamos sempre trabalhando para melhorá-lo. Sempre que isso for feito, anunciaremos as alterações aqui.

## <a name="future-updates"></a>Atualizações futuras
Oi, você sabia que a equipe do RemoteApp do Azure envia atualizações mensais para o blog do RDS? Você pode ver não apenas o que mudou no RemoteApp do Azure, mas também outras informações sobre como usar o RDS. Confira o [Blog de Serviços de Área de Trabalho Remota](https://blogs.msdn.microsoft.com/rds/)para obter informações. Por exemplo, duas semanas atrás, eles postaram uma entrada sobre [levantar e mudar cargas de trabalho com o Azure RemoteApp e o Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).

## <a name="september-2015"></a>Setembro de 2015
* O Infopath foi adicionado à imagem da galeria e ao modelo do Microsoft Office 365. Para compartilhar o Infopath, atualize suas coleções com a imagem mais recente.
* Atualizações do cliente:
  * O cliente Windows foi atualizado para permitir que os usuários compartilhem comentários, principalmente sobre problemas de conexão.
  * O Cliente iOS foi atualizado para corrigir mensagens de erro e um problema de expiração de credenciais antes da data prevista.
* Estamos trabalhando para testar o suporte do Office 2016. Após a conclusão, procure imagens atualizadas.
* Foi publicado um novo artigo sobre o [as diferenças entre as coleções de nuvem e híbridos](remoteapp-collections.md) – isso o ajudará a escolher o tipo de coleção que funciona melhor para aplicativos – somente em nuvem, nuvem + VNET ou híbrida.
* Deseja compartilhar QuickBooks usando o Azure RemoteApp, mas não tem certeza das etapas? Confira o [novo artigo do Eric](remoteapp-quickbooks.md) que lhe diz exatamente o que fazer.

## <a name="august-2015"></a>Agosto de 2015
Grandes mudanças aconteceram em agosto – eis alguns destaques:

* Agora você pode usar uma VNET do Azure com uma coleção de nuvem! Confira as [instruções de criação de nuvem](remoteapp-create-cloud-deployment.md) das novas etapas.
* Agora é possível adicionar aplicativos ao menu **Iniciar** para o cliente Windows RemoteApp. Os aplicativos aparecerão na lista de aplicativos e você poderá fixá-los no menu **Iniciar** no Windows.
* Uma nova imagem foi adicionada à galeria de VM do Azure – Host da Sessão da Área de Trabalho Remota do Windows Server com o Microsoft Office 365 ProPlus.
* O cliente Mac foi corrigido para que aplicativos com janelas modais parem congelar.
* Foram documentadas orientações de como usar a [assinatura do Office 365 ProPlus](remoteapp-officesubscription.md) com o Azure RemoteApp.
* Foram detalhadas orientações de como [proteger aplicativos e dados](remoteapp-secure.md) na coleção Azure RemoteApp.

## <a name="july-2015"></a>2015 de julho
Julho prepara o terreno para mudanças planejadas em agosto, portanto, não há muito sobre o que discorrer no momento; o que há são principalmente atualizações de documento. Essas são as alterações mais recentes:

* Uma guia **Suporte** foi adicionada ao portal para que você possa acessar mais facilmente recursos de suporte, como fóruns.
* As informações de solução de problemas foram reformuladas para a criação de uma coleção híbrida. Confira as dicas [mais recentes e melhores](remoteapp-hybridtrouble.md) de solução de problemas, por exemplo, como identificar as portas corretas para configurar sua VNET.
* O modo pelo qual os [dados de usuário](remoteapp-upd.md) são criados e salvos no Azure RemoteApp foi documentado.
* A maneira de [bloquear aplicativos](remoteapp-secure.md)foi documentada.
* Os [cmdlets do Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx)foram publicados.
* E, finalmente, começamos uma conversa sobre terminologia com alguns usuários de RemoteApp do Azure. Procure por alterações no modo como fazemos referência às diferentes opções de coleção.

## <a name="june-2015"></a>Junho de 2015
Muitas mudanças! A equipe tem estado muito ocupada em junho:

* A Azure RemoteApp [página de aterrissagem](https://www.remoteapp.windowsazure.com/) foi redesenhada, confira!
* O software foi atualizado em todas as imagens disponíveis como parte de sua assinatura.
* Foram feitas melhorias na coleções híbridas, incluindo o suporte de túnel e verificação do tamanho de sub-rede IP antes de tentar criar a coleção.
* Descobrimos que o curinga * não funciona para webcams. Em vez disso, você precisa especificar a ID da instância ou GUID. Vamos atualizar as informações de redirecionamento para demonstrar isso.
* Foi feito com que você possa adicionar software antivírus personalizado para a imagem, quando criar uma imagem de modelo da galeria do Azure.

Temos mais alterações preparadas para julho, pelo que voltaremos em breve com outra atualização.

## <a name="may-2015"></a>Maio de 2015
Houve várias adições (e meses) desde que criamos este tópico, então essa lista faz um pouco de batota e começa no início de março e vai até maio. Vejas esses novos recursos:

* Automatize tudo - o Azure RemoteApp agora tem [cmdlets no módulo do Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
* [Criar uma imagem do Azure RemoteApp com base em uma máquina virtual do Azure](remoteapp-image-on-azurevm.md). Torna carregar sua imagem personalizada para o Azure muito mais rápido.
* Use uma Rede Virtual do Azure em vez de uma Rede Virtual do RemoteApp para se conectar os recursos da sua rede corporativa ao Azure. Nós atualizamos as [instruções de coleta híbrida](remoteapp-create-hybrid-deployment.md) para orientá-lo na criação de uma Rede Virtual do Azure (é a etapa 1).
* Falando em Redes Virtuais, confira as [novas diretrizes](remoteapp-vnetsizing.md) em torno dos limites e limitações de tamanho de Redes Virtuais.
* E, falando em limites - quais são os [limites e padrões de serviço](../azure-subscription-service-limits.md)?

Quer saber mais sobre o RemoteApp do Azure? A equipe de RemoteApp estava em força no Ignite, há algumas semanas. Veja o vídeo do Eric: [Os Conceitos Básicos de Gerenciamento e Administração do Microsoft Azure RemoteApp](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Precisa ver o Azure RemoteApp no mundo real? Confira o tutorial [Executar qualquer aplicativo em qualquer dispositivo, em qualquer lugar](remoteapp-anyapp.md) - que mostra como compartilhar o acesso com seus usuários, incluindo o compartilhamento de arquivos de banco de dados. Também temos um tutorial sobre [fazendo o Office 365](remoteapp-tutorial-o365anywhere.md) executar da mesma forma em qualquer dispositivo.

Obrigado por estar conosco - o mês que vem traz mais atualizações.

### <a name="help-us-help-you"></a>Ajude-nos a ajudar você
Você sabia que, além de classificar este artigo e fazer comentários, você pode alterar o próprio artigo? Falta alguma coisa? Há algo errado? Escrevi algo que não ficou muito claro? Role para cima e clique em **Editar no GitHub** para fazer alterações - elas serão enviadas para que as examinemos e, assim que elas forem desconectadas, você verá suas alterações e aprimoramentos bem aqui.


