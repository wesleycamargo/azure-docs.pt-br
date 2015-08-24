
<properties 
    pageTitle="O que há de novo no Azure RemoteApp?"
    description="Saiba mais sobre alterações e aprimoramentos feitos ao Azure RemoteApp" 
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
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# O que há de novo no RemoteApp?

Uma das vantagens do RemoteApp é que estamos sempre trabalhando para melhorá-lo. Sempre que isso for feito, anunciaremos as alterações aqui.

## 2015 de julho

Julho prepara o terreno para mudanças planejadas em agosto, portanto, não há muito sobre o que discorrer no momento; o que há são principalmente atualizações de documento. Essas são as alterações mais recentes:

- Uma guia **suporte** foi adicionada ao portal para que você possa acessar mais facilmente os recursos de suporte, como os fóruns.
- As informações de solução de problemas foram reformuladas para a criação de uma coleção híbrida. Confira as dicas [mais recentes e melhores](remoteapp-hybridtrouble.md) de solução de problemas, por exemplo, como identificar as portas corretas para configurar sua VNET.
- O modo como os [dados de usuário](remoteapp-upd.md) são criados e salvos no RemoteApp do Azure foi documentado.
- A maneira de [bloquear aplicativos](remoteapp-secure.md) foi documentada.
- Os [cmdlets do RemoteApp do Azure](https://msdn.microsoft.com/library/mt428031.aspx) foram publicados.
- E, finalmente, começamos uma conversa sobre terminologia com alguns usuários de RemoteApp do Azure. Procure por alterações no modo como fazemos referência às diferentes opções de coleção.

## Junho de 2015

Muitas mudanças! A equipe tem estado muito ocupada em junho:

- A Azure RemoteApp [página de aterrissagem](https://www.remoteapp.windowsazure.com/) foi redesenhada, confira! 
- O software foi atualizado em todas as imagens disponíveis como parte de sua assinatura.
- Foram feitas melhorias na coleções híbridas, incluindo o suporte de túnel e verificação do tamanho de sub-rede IP antes de tentar criar a coleção.
- Descobrimos que o curinga * não funciona para webcams. Em vez disso, você precisa especificar a ID da instância ou GUID. Vamos atualizar as informações de redirecionamento para demonstrar isso.
- Foi feito com que você possa adicionar software antivírus personalizado para a imagem, quando criar uma imagem de modelo da galeria do Azure.

Temos mais alterações preparadas para julho, pelo que voltaremos em breve com outra atualização.

## Maio de 2015

Houve várias adições (e meses) desde que criamos este tópico, então essa lista faz um pouco de batota e começa no início de março e vai até maio. Vejas esses novos recursos:

- Automatize tudo - o Azure RemoteApp agora tem [cmdlets no módulo do Azure PowerShell](remoteapp-tutorial-arawithpowershell.md). 
- [Criar uma imagem do Azure RemoteApp com base em uma máquina virtual do Azure](remoteapp-image-on-azurevm.md). Torna carregar sua imagem personalizada para o Azure muito mais rápido.
- Use uma Rede Virtual do Azure em vez de uma Rede Virtual do RemoteApp para se conectar os recursos da sua rede corporativa ao Azure. Nós atualizamos as [instruções de coleta híbrida](remoteapp-create-hybrid-deployment.md) para orientá-lo na criação de uma Rede Virtual do Azure (é a etapa 1).
- Falando em Redes Virtuais, confira as [novas diretrizes](remoteapp-vnetsizing.md) em torno dos limites e limitações de tamanho de Redes Virtuais.
- E, falando em limites - quais são os [limites e padrões de serviço](remoteapp-servicelimits.md)?

Quer saber mais sobre o RemoteApp do Azure? A equipe de RemoteApp estava em força no Ignite, há algumas semanas. Veja o vídeo do Eric: [Os Conceitos Básicos de Gerenciamento e Administração do Microsoft Azure RemoteApp](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Precisa ver o Azure RemoteApp no mundo real? Confira o tutorial [Executar qualquer aplicativo em qualquer dispositivo, em qualquer lugar](remoteapp-anyapp.md) - que mostra como compartilhar o acesso com seus usuários, incluindo o compartilhamento de arquivos de banco de dados. Também temos um tutorial sobre [fazendo o Office 365](remoteapp-tutorial-o365anywhere.md) executar da mesma forma em qualquer dispositivo.

Obrigado por estar conosco - o mês que vem traz mais atualizações.

<!---HONumber=August15_HO7-->