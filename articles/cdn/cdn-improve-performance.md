<properties 
	pageTitle="CDN - melhorar o desempenho ao compactar arquivos" 
	description="Você pode aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar seus arquivos." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Melhorar o desempenho ao compactar arquivos

Este tópico descreve como aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar os arquivos.

Há duas maneiras de a CDN poder oferecer suporte à compactação:

- Você pode habilitar a compactação no servidor de origem e, nesse caso, a CDN oferecerá suporte à compactação por padrão e entregará arquivos compactados aos clientes. 
- Você pode habilitar a compactação diretamente nos servidores de borda da CDN e, nesse caso, a CDN compactará os arquivos e os servirá para os usuários finais.

## Habilitando a compactação

> [AZURE.NOTE]As camadas CDN Standard e Premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário varia. Para saber mais sobre as diferenças entre as camadas CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).

### Camada padrão

1. Na folha Perfil CDN, clique no ponto de extremidade da CDN que deseja gerenciar.
	
	![Pontos de extremidade da folha Perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

	A folha do ponto de extremidade da CDN se abre.

2. Clique no botão **Configurar**.

	![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-config-btn.png)
	
	A folha Configuração da CDN se abre.
	
3. Habilitar **Compactação**.
	
	![Opções de compactação da CDN](./media/cdn-file-compression/cdn-compress-standard.png)
	
4. Use os tipos padrão ou modifique a lista removendo ou adicionando tipos de arquivo.

5. Após fazer suas alterações, clique no botão **Salvar**.

### Camada premium

1. Na folha do perfil CDN, clique no botão **Gerenciar**.

	![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-manage-btn.png)
	
	O portal de gerenciamento da CDN é aberto.
	
2. Passe o ponteiro do mouse sobre a guia **HTTP grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Compactação**.
	
	As opções de compactação são exibidas.
	
	![Compactação de arquivos](./media/cdn-file-compression/cdn-compress-files.png)
	
3. Depois de modificar a lista de tipos de arquivo, clique no botão **Atualizar**.


## Regras e processo de compactação

1. O solicitante envia uma solicitação de conteúdo.
2. Um servidor de borda verifica se há um cabeçalho **Accept-Encoding**.
	1. Se tiver sido incluído, esse cabeçalho identificará o método de compactação solicitado.
	1. Se estiver ausente, esse tipo de solicitação será servido em um formato não compactado.
3.	O POP de borda mais próximo verifica o status do cache, o método de compactação e se ele ainda tem uma vida útil válida.
	1.	Erro de Cache: se a versão solicitada não for armazenada em cache, a solicitação será encaminhada para a origem.
	2.	Acerto de Cache com o mesmo método de compactação: o servidor de borda entregará o conteúdo compactado para o cliente imediatamente.
	3.	Acertos de cache com métodos de compactação diferentes: o servidor de borda transcodificará o ativo para o método de compactação solicitado. 
	4.	Acertos de cache e não compactados: se a solicitação inicial fez com que o ativo fosse armazenado em cache em um formato não compactado, será executada uma verificação para ver se a solicitação está qualificada para a compactação no servidor de borda (com base nos critérios da seção de definição/requisito acima).
		1.	Se estiver qualificado, o servidor de borda compactará o arquivo e o servirá ao cliente.
		2.	Se não estiver qualificado: o servidor de borda fornecerá o conteúdo não compactado para o cliente imediatamente. 



## Considerações 

1. Para pontos de extremidade de streaming habilitado para a CDN de Serviços de Mídia, a compactação está habilitada por padrão para os seguintes tipos de conteúdo: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Você não pode habilitar/desabilitar a compactação para os tipos mencionados usando o portal do Azure.  
2. Somente uma versão de arquivo (compactado ou não compactado) será armazenada em cache no servidor de borda. Uma solicitação para uma versão diferente resultará na transcodificação do conteúdo no servidor de borda.  

<!---HONumber=AcomDC_1203_2015-->