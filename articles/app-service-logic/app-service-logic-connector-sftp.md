<properties
	pageTitle="Conector de SFTP"
	description="Introdução ao Conector de SFTP"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sameerch"/>

# Usando o Conector de SFTP em seu aplicativo lógico #

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.

O Conector de SFTP permite mover dados de/para um servidor SFTP. Ele permite baixar ou carregar arquivos ou listar arquivos de/para um servidor SFTP.

## Criando um conector de SFTP para seu aplicativo lógico ##
Para usar o conector de SFTP, primeiro você precisa criar uma instância do aplicativo de API do conector de SFTP. Isso pode ser feito da seguinte maneira:

1.	Abra o Azure Marketplace usando a opção + NOVO na parte superior esquerda do Portal do Azure.
2.	Procure "Web + Mobile" e procure "Conector SFTP".
3.	Configure o Conector de SFTP da seguinte maneira:

	![][1]
	- **Local** - escolha o local geográfico onde gostaria de implantar o conector
	- **Assinatura** - escolha uma assinatura na qual deseje criar esse conector
	- **Grupo de recursos** - selecione ou crie um grupo de recursos em que o conector deve residir 
	- **Plano de hospedagem de Web** - selecione ou crie uma plano de hospedagem na web
	- **Faixa de preços** - escolha uma faixa de preço para o conector 
	- **Nome** - dê um nome para o conector de SFTP
	- **Configurações do pacote** 
	- **Endereço do servidor** - especifique o nome do servidor SFTP ou endereço IP
		-  **Aceite qualquer HostKey de servidor SSH** - determine se qualquer impressão digital da chave de host pública SHH do servidor deve ser aceita. Se definido como false, a chave do host será comparada com a chave especificada na propriedade "Impressão digital da chave de host do servidor SSH" 
		- **HostKey do Servidor SSH** - especifique a impressão digital da chave pública do host para o servidor SSH - *opcional*. 
		- **Pasta Raiz** - especifique um caminho de pasta raiz. Se o espaço estiver em branco o padrão será a raiz. 
		- **Criptografar codificação** - Especifique a codificação de criptografia -*opcional*.
		- **Porta do servidor** -Especifique o número de porta do servidor SFTP
4. Clique em Criar. Será criado um novo Conector de SFTP.

5. Navegue até o aplicativo de API recém-criado via Procurar -> Aplicativos de API -> <Name of the API App just created> e você poderá ver que o componente "Segurança" não está configurado.

	![][2]
6. Clique no componente "Segurança" para configurar a segurança (nome de usuário, senha, chave privada, senha do arquivo PPK) para o conector SFTP. Selecione a guia de autorização "Senha", "Privatekey" ou "MultiFactor" em Segurança e forneça as propriedades necessárias.

	![][3]
	![][4]
	![][5]
6. Depois que a configuração de segurança for salva, você poderá criar um aplicativo lógico no mesmo grupo de recursos para usar o conector SFTP.

## Usando o Conector de SFTP em seu Aplicativo Lógico ##
Depois de criar seu aplicativo de API, você pode usar o conector de SFTP como gatilho/ação para seu aplicativo lógico. Para fazer isso, você precisa:

1.	Crie um novo aplicativo lógico e escolha o mesmo grupo de recursos que tem o conector de SFTP.

	![][6]
2.	Abrir "Gatilhos e Ações" para abrir o Designer de Aplicativos Lógicos e configurar seu fluxo.

	![][7]
3.	O conector de SFTP será exibido na seção "Aplicativos de API neste grupo de recursos" na galeria, no lado direito.

	![][8]
4.	Você pode soltar o aplicativo de API do Conector de SFTP no editor clicando em "Conector de SFTP".


6.	Agora você pode usar o conector de SFTP no fluxo. Você pode usar o arquivo recuperado do gatilho de SFTP ("TriggerOnFileAvailable") em outras ações no fluxo.

	**Observação:** o gatilho de SFTP "TriggerOnFileAvailable" excluirá o arquivo recuperado após o processamento do arquivo.

8.	Configure as propriedades de entrada de gatilho de SFTP da seguinte maneira:

	- **Caminho da Pasta** - especifique o caminho da pasta da qual os arquivos precisam ser recuperados.
	- **O tipo de arquivo: texto ou binário** - selecione o tipo do arquivo.
	- **Máscara de arquivo** - especifique a máscara de arquivo a ser aplicada para a recuperação de arquivos. ' *' recupera todos os arquivos na pasta especificada.
	- **Excluir Máscara de Arquivo** - especifique a máscara de arquivo a ser aplicada para a exclusão de arquivos. Se a propriedade "Máscara de Arquivo" também for definida, a Máscara de Exclusão de Arquivo será aplicada primeiro.


	![][9]
	![][10]

7.	De maneira semelhante, você pode usar as ações de SFTP no fluxo. Você pode usar a ação "Carregar Arquivo" para carregar um arquivo no servidor SFTP. Configure as propriedades de entrada para a ação "Carregar Arquivo" da seguinte maneira:

	- **Conteúdo** - Especifica o conteúdo do arquivo a ser carregado
	- **Codificação de Transferência de Conteúdo** - especifique nenhuma ou Base64
	- **Caminho do Arquivo** - especifique o caminho do arquivo a ser carregado
	- **Substituir** - especifique "true" para substituir o arquivo se ele já existir
	- **Anexar Se Existir ** - especifique "true" ou "false". Quando definido como "true", os dados serão anexados ao arquivo, se ele existir. Quando definido como "false", o arquivo será substituído, se ele existir
	- **Pasta Temporária** - se fornecida, o adaptador carregará o arquivo para o “Caminho da Pasta Temporária” e, quando o carregamento for concluído, o arquivo será movido para o “Caminho da Pasta”. O “Caminho da Pasta Temporária” deve estar no mesmo disco físico que o “Caminho da Pasta” para garantir que a operação de movimentação seja atômica. A pasta temporária pode ser usada apenas quando a propriedade Anexar se Existir está desabilitada.

	![][11]
	![][12]





<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!----HONumber=August15_HO6-->