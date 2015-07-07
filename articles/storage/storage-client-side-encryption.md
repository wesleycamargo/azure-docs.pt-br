<properties 
	pageTitle="Criptografia do lado do cliente para o Armazenamento do Microsoft Azure | Microsoft Azure" 
	description="A visualização da Biblioteca de cliente do Armazenamento do Azure para .NET oferece suporte à criptografia do lado do cliente e à integração com o Cofre da Chave do Azure. A criptografia do lado do cliente oferece o máximo de segurança para seus aplicativos do Armazenamento do Azure, uma vez que suas chaves de acesso nunca ficam disponíveis para o serviço. A criptografia do lado do cliente está disponível para blobs, filas e tabelas." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Criptografia do lado do cliente para o Armazenamento do Microsoft Azure (visualização)

## Visão geral

Bem-vindo à [visualização da nova biblioteca de cliente do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Essa biblioteca de visualização contém novos recursos para ajudar os desenvolvedores a criptografar dados nos aplicativos de cliente antes de carregar para o Armazenamento do Azure e descriptografar os dados durante o download. A biblioteca de visualização também oferece suporte à integração com o [Cofre da Chave](http://azure.microsoft.com/services/key-vault/) do Azure para gerenciamento de chaves de contas de armazenamento.

## Por que usar a criptografia do lado do cliente?

A criptografia do lado do cliente oferece uma vantagem significativa em relação à criptografia do lado do servidor: você controla totalmente as suas chaves de acesso da conta. A criptografia do lado do cliente oferece o máximo de segurança para seus aplicativos, uma vez que o Armazenamento do Azure nunca vê as suas chaves e nunca poderá descriptografar os dados. A biblioteca de visualização está totalmente disponível em [GitHub](https://github.com/Azure/azure-storage-net/tree/preview), de modo que você pode ver como a biblioteca criptografa os dados para garantir que ela atenda aos seus padrões.

## Por que estamos fornecendo uma biblioteca com suporte à criptografia do lado do cliente?

Embora qualquer desenvolvedor possa criptografar seus dados no lado do cliente antes de carregá-los, isso exige experiência em criptografia. Também exige design para desempenho e segurança. Diferentes desenvolvedores teriam que projetar sua própria solução de criptografia e, como cada solução seria diferente, nenhuma delas funcionaria em conjunto.

A biblioteca de visualização é projetada para:

- Implementar as práticas recomendadas de segurança para você.
- Maximizar o desempenho.
- Oferecer facilidade de uso para cenários comuns.
- Dar suporte à interoperabilidade entre linguagens. Os dados criptografados usando a biblioteca de cliente .NET no futuro poderão ser descriptografados pelas bibliotecas de cliente para nossas outras linguagens compatíveis, incluindo C++, Java e Node.js (e vice-versa).

## O que está disponível agora?

A biblioteca de visualização atualmente oferece suporte à criptografia de blobs, tabelas e filas usando a técnica de envelope. A criptografia e descriptografia com chaves assimétricas são computacionalmente dispendiosas. Portanto, na técnica de envelope, os dados em si não estão criptografados diretamente com essas chaves mas, em vez disso, são criptografados usando uma chave aleatória de criptografia simétrica de conteúdo. Em seguida, essa chave de criptografia de conteúdo é criptografada usando uma chave pública. O suporte do Cofre da Chave do Azure pode ajudá-lo a gerenciar as chaves com eficiência.

É simples usar a criptografia do lado do cliente. Você pode especificar as opções de solicitação com a política de criptografia adequada (Blob, Fila ou Tabela) e passá-las para as APIs de upload/download de dados. A biblioteca de cliente criptografa automaticamente os dados no cliente durante o carregamento para o Armazenamento do Azure e descriptografa os dados quando eles são recuperados. Você pode encontrar mais detalhes e exemplos de código na postagem de blog [Introdução à criptografia do lado do cliente para o Armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx).

Alguns detalhes adicionais sobre a criptografia do lado do cliente:

- **Segurança**: os dados criptografados não são legíveis mesmo que as chaves das contas de armazenamento dos clientes sejam comprometidas.
- **Criptografia com sobrecarga fixa**: os dados criptografados terão um tamanho previsível com base no tamanho original.
- **Criptografia independente**: cada blob, entidade de tabela ou mensagem da fila armazena todas as informações de criptografia no próprio objeto ou em seus metadados. O único valor externo necessário é a sua chave de criptografia.
- **Rotação de chaves**: os usuários podem alternar as chaves eles mesmos e podem ser usadas várias chaves durante o processo de rotação de chaves.
- **Caminho de atualização limpo**: algoritmos de criptografia adicionais e versões do protocolo terão suporte no futuro sem necessidade de alterações significativas no seu código.
- **Suporte à criptografia de blobs**:
	- **Carga total de blobs**: você pode criptografar e carregar arquivos como documentos, fotos e vídeos em sua totalidade.
	- **Download de blobs completo ou com base em intervalos**: você pode baixar e descriptografar um blob em sua totalidade ou em intervalos.


>[AZURE.IMPORTANT]Lembre-se dos seguintes pontos importantes ao usar a biblioteca de visualização:
>
>- Não use a biblioteca de visualização de dados de produção. No futuro, as alterações na biblioteca afetarão os esquemas usados. A descriptografia de dados que foram criptografados com a biblioteca de visualização não é garantida em versões futuras.  
>- Durante a leitura ou gravação de um blob criptografado, use comandos de carregamento de blob completo e comandos de download de blob completo/em intervalos. Evite gravar em um blob criptografado usando operações de protocolo tais como Put Block, Put Block List, Write Pages ou Clear Pages; caso contrário, você pode corromper o blob criptografado e torná-lo ilegível.
>- Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
>- Se você definir metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isso também é verdadeiro para instantâneos - evite especificar metadados ao criar um instantâneo de um blob criptografado.

## Consulte também

- [Introdução à criptografia do lado do cliente para o Armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx)  
- [Biblioteca de cliente do Armazenamento do Azure para o pacote NuGet do .NET (visualização)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview)  
- [Biblioteca de cliente do Armazenamento do Azure para o código-fonte do .NET (visualização)](https://github.com/Azure/azure-storage-net/tree/preview)
 

<!---HONumber=62-->