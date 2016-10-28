<properties
	pageTitle="Visão geral do portal do Microsoft Azure"
	description="Saiba como usar o portal do Microsoft Azure."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/16/2015"
	ms.author="dwrede"/>

# Visão geral do portal do Microsoft Azure

O portal do Microsoft Azure é um local central no qual você pode provisionar e gerenciar os recursos do Azure. Este tutorial mostrará o portal e como usar alguns de seus principais recursos:
- Um **marketplace abrangente** que permite que você procure milhares de itens da Microsoft e de outros fornecedores que podem ser adquiridos e/ou provisionados.
- Uma **experiência de procura unificada e escalonável** que facilita a localização de recursos importantes para você e a execução de várias operações de gerenciamento.
- **Páginas de gerenciamento consistente** (ou folhas) que permitem que você gerencie uma ampla variedade de serviços do Azure por meio de uma maneira consistente de expor configurações, ações, informações de cobrança, monitoramento de integridade e uso de dados, entre outros.
- Uma **experiência pessoal** que permite que você crie uma tela de início personalizada que mostra as informações que quer ver sempre que entrar. Você também pode personalizar qualquer uma das folhas gerenciamento que contêm blocos.

 ![Orientação da interface do usuário do Portal do Azure][UIOrientation]

## Antes de começar

Você precisará de uma assinatura válida do Azure para realizar este tutorial. Se não tiver uma, [inscreva-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) hoje mesmo. Quando tiver uma assinatura, você pode acessar o portal em [https://portal.azure.com].

## Como criar um recurso

O Azure tem um marketplace com milhares de itens que você pode criar em um único lugar. Digamos que você deseja criar uma nova VM (máquina virtual) do Windows Server 2012. A hub +NOVO é o ponto de entrada para um conjunto estruturado de categorias em destaque do marketplace. Cada categoria tem um pequeno conjunto de itens em destaque juntamente com um link para o marketplace completo que mostra todas as categorias e a pesquisa. Para criar essa nova VM do Windows Server 2012, execute as seguintes ações:

1.	O Windows Server 2012 está em destaque, portanto você pode selecioná-lo na categoria Computação.  
2.	Preencha algumas informações básicas em um formulário.
3.	Clique em 'Criar' e sua VM começará a provisionar imediatamente.

O hub de notificações vai alertá-lo quando o recurso tiver sido criado e uma folha de gerenciamento será aberta (você sempre pode procurar recursos posteriormente).

![Categorias do portal][PortalCategories]


## Como localizar os recursos

Você sempre pode fixar os recursos acessados com frequência na sua tela inicial, mas talvez seja necessário procurar algo que não acessa com frequência. O hub de procura mostrado abaixo é a maneira de chegar a todos os seus recursos. Você pode filtrar por assinatura, escolher/redimensionar colunas e navegar até as folhas de gerenciamento clicando em itens individuais.

![Hub de procura][BrowseHub]

## Como gerenciar e delegar acesso a um recurso

A partir dessa folha, você pode se conectar à máquina virtual usando a área de trabalho remota, monitorar as principais métricas de desempenho, controlar o acesso a essa VM usando o acesso baseado em função (RBAC), configurar a VM e executar outras tarefas de gerenciamento importantes. A delegação de acesso com base em função é essencial ao gerenciamento em grande escala. Clique em [aqui](./active-directory/role-based-access-control-configure.md) para saber mais sobre isso. Para delegar acesso a um recurso, execute as seguintes ações:

1.	Procure o recurso.
2.	Clique em 'Todas as configurações' na seção Elementos Essenciais.
3.	Clique em 'Usuários' na lista de configurações.
4.	Clique em ‘Adicionar’ na barra de comandos.
5.	Selecione um usuário e uma função.

![Gerenciando um recurso][ManageResource]

## Como personalizar uma folha de recurso

O Azure pré-configura as folhas para seus recursos, mas os blocos nessas folhas são controlados por você. Você pode facilmente entrar no modo de personalização para adicionar, remover, redimensionar ou reorganizar os blocos. Para personalizar uma folha, execute as seguintes ações:

1.	Procure o recurso.
2.	Clique no ‘…’ na parte superior da folha que deseja personalizar.
3.	Clique em ‘Adicionar componentes’.
4.	Comece a arrastar e soltar componentes.  

![Personalizando folhas][CustomizeBlades]

## Como obter ajuda

Se você tiver um problema, estamos à sua disposição. O portal tem uma página de ajuda e suporte que pode indicar a direção certa. Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), você também pode criar tíquetes de suporte diretamente no portal. Depois de criar um tíquete de suporte, você pode gerenciar o ciclo de vida do tíquete de dentro do portal. Você pode acessar a página de ajuda e suporte navegando até Navegar -> Ajuda + suporte.

![Ajuda e suporte][HelpSupport]

## Resumo

Vamos analisar o que você aprendeu neste tutorial:
- Você aprendeu a inscrever-se, a obter uma assinatura e a navegar até o portal
- Você obteve orientação sobre a interface de usuário do portal e aprendeu a criar e a procurar recursos
- Você aprendeu a criar um recurso e a procurar recursos
- Você aprendeu sobre as folhas de estrutura ou de gerenciamento e como pode gerenciar consistentemente diferentes tipos de recursos
- Você aprendeu a personalizar o portal para trazer as informações importantes para a frente e para o centro
- Você aprendeu a controlar o acesso a recursos usando o acesso baseado em função (RBAC)
- Você aprendeu a obter ajuda e suporte

O portal do Microsoft Azure simplifica radicalmente a criação e o gerenciamento de seus aplicativos na nuvem. Dê uma olhada no [blog de gerenciamento de](https://azure.microsoft.com/blog/topics/management/) para se manter atualizado, uma vez que estamos constantemente [ouvindo comentários](https://feedback.azure.com/forums/223579-azure-preview-portal/) e fazendo melhorias. O [ScottGu’s blog (Blog do ScottGu)](http://weblogs.asp.net/scottgu) é outro ótimo lugar para procurar todas as atualizações do Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

<!---HONumber=AcomDC_0413_2016-->