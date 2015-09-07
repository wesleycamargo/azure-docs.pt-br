<properties 
   pageTitle="Configurando e usando o Emulador de Armazenamento com o Visual Studio"
	description="Configurando e usando o Emulador de Armazenamento com o Visual Studio"
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee"/>
<tags 
   ms.service="storage"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/24/2015"
	ms.author="kempb"/>

# Configurando e usando o emulador de armazenamento com o Visual Studio

O ambiente de desenvolvimento SDK do Azure inclui o emulador de armazenamento, um utilitário que simula os serviços Blob, Fila e Tabela disponíveis no Azure em seu computador de desenvolvimento local. Se você estiver criando um serviço de nuvem que utiliza os serviços de armazenamento do Azure, ou escrevendo qualquer aplicativo externos que chama os serviços de armazenamento, você pode testar seu código localmente no emulador de armazenamento. As Ferramentas do Azure para o Microsoft Visual Studio integram o gerenciamento do emulador de armazenamento no Visual Studio. As Ferramentas do Azure inicializam o banco de dados do emulador de armazenamento no primeiro uso, iniciam o serviço do emulador de armazenamento quando você executa ou depura seu código do Visual Studio e fornecem acesso somente leitura aos dados de emulador de armazenamento por meio do Gerenciador de Armazenamento do Azure.

Para obter informações detalhadas sobre o emulador de armazenamento incluindo requisitos de sistema e instruções de configuração personalizada, consulte [Usar o Emulador de Armazenamento do Azure para Desenvolvimento e Teste](./storage/storage-use-emulator/).

>[AZURE.NOTE]Existem algumas diferenças de funcionalidade entre a simulação do emulador de armazenamento e os serviços de armazenamento do Azure. Consulte [Diferenças entre o Emulador de Armazenamento e os Serviços de Armazenamento do Azure](./storage/storage-use-emulator) na documentação do SDK do Azure para obter informações sobre as diferenças específicas.

## Configurando uma cadeia de conexão para o emulador de armazenamento

Para acessar o emulador de armazenamento do código dentro de uma função, você deve configurar uma cadeia de conexão que aponta para o emulador de armazenamento e que pode ser alterada posteriormente para apontar para uma conta de armazenamento do Azure. Uma cadeia de conexão é uma configuração que sua função pode ler em tempo de execução para conectar-se a uma conta de armazenamento. Para obter mais informações sobre como criar cadeias de conexão, consulte [Configurar o Aplicativo do Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE]Você pode retornar uma referência à conta do emulador de armazenamento do seu código usando a propriedade **DevelopmentStorageAccount**. Essa abordagem funciona corretamente se você deseja acessar o emulador de armazenamento do seu código, mas se você planeja publicar seu aplicativo no Azure, você precisará criar uma cadeia de conexão para acessar sua conta de armazenamento do Azure e modificar seu código, antes de publicá-lo, para usar essa cadeia de conexão. Se você estiver alternando com frequência entre a conta do emulador de armazenamento e uma conta de armazenamento do Azure, uma cadeia de conexão simplificará esse processo.

## Inicializando e executando o emulador de armazenamento

Você pode especificar que, quando você executa ou depura seu serviço no Visual Studio, o Visual Studio inicia automaticamente o emulador de armazenamento. No Gerenciador de Soluções, abra o menu de atalho para o seu projeto do **Azure** e escolha **Publicar**. Na guia **Desenvolvimento**, na lista **Iniciar o Emulador de Armazenamento do Azure**, escolha **True** (se ainda não estiver definido para esse valor).

Na primeira vez que você executar ou depurar o serviço do Visual Studio, o emulador de armazenamento inicia um processo de inicialização. Esse processo reserva portas locais para o emulador de armazenamento e cria o banco de dados do emulador de armazenamento. Uma vez concluído, esse processo não precisa executar novamente a menos que o banco de dados do emulador de armazenamento seja excluído.

>[AZURE.NOTE]Começando com a versão de junho de 2012 das ferramentas do Azure, o emulador de armazenamento é executado, por padrão, no SQL Express LocalDB. Em versões anteriores das Ferramentas do Azure, o emulador de armazenamento é executado em uma instância padrão do SQL Express 2005 ou 2008, que deve ser instalado antes de você instalar o SDK do Azure. Você também pode executar o emulador de armazenamento em uma instância nomeada do SQL Express ou uma instância nomeada ou padrão do Microsoft SQL Server. Se você precisa configurar o emulador de armazenamento para executar em uma instância diferente da instância padrão, consulte [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](./storage/storage-use-emulator/).

O emulador de armazenamento fornece uma interface do usuário para exibir o status dos serviços de armazenamento local e para iniciá-los, pará-los e redefini-los. Depois que o serviço do emulador de armazenamento tiver sido iniciado, você poderá exibir a interface do usuário ou iniciar ou parar o serviço clicando no ícone da área de notificação para o Emulador do Microsoft Azure na barra de tarefas do Windows.

## Exibindo dados do emulador de armazenamento no Gerenciador de Servidores

O nó de Armazenamento do Azure no Gerenciador de Servidores habilita você a exibir dados e alterar as configurações de dados de blob e de tabela em suas contas de armazenamento, incluindo o emulador de armazenamento. Para obter mais informações, consulte [Procurando e Gerenciando Recursos de Armazenamento com o Gerenciador de Servidores](https://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=August15_HO9-->