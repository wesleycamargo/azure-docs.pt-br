# Guia do colaborador de documentação técnica do Azure

Você descobriu o repositório do GitHub que hospeda a origem da documentação técnica publicada no Centro de Documentos do Azure, em [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Esse repositório também contém orientações para ajudar você a contribuir para a nossa documentação técnica. Para obter uma lista de artigos do guia do colaborador, confira [o índice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Contribuir para a documentação do Azure

Obrigado por seu interesse na documentação do Azure.

* [Maneiras de contribuir](#ways-to-contribute)
* [Sobre as suas contribuições ao conteúdo do Azure](#about-your-contributions-to-azure-content)
* [Organização do repositório](#repository-organization)
* [Usar o GitHub, o Git e este repositório](#use-github-git-and-this-repository)
* [Como usar a redução para formatar seu tópico](#how-to-use-markdown-to-format-your-topic)
* [Mais recursos](#more-resources)
* [Índice de todos os artigos do guia do colaborador](./contributor-guide/contributor-guide-index.md) (abre uma nova página)

## Maneiras de contribuir

Você pode contribuir com facilidade para artigos técnicos na interface do usuário do GitHub. Localize o artigo neste repositório ou visite o artigo em [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e clique no link no artigo para a fonte do GitHub do artigo.

##Sobre as suas contribuições ao conteúdo do Azure

###Correções secundárias

As correções secundárias ou esclarecimentos enviados para a documentação e os exemplos de código neste repositório são cobertos pelos [Termos de uso do site do Azure (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###Envios maiores

Se você enviar uma solicitação de pull com alterações significativas ou novas para a documentação e para os exemplos de código, enviaremos um comentário para o GitHub solicitando o envio de um Contrato de Licença de Contribuição (CLA) online caso você esteja em um destes grupos:

* Membros do grupo Microsoft Open Technologies.
* Colaboradores que não trabalham na Microsoft.

Precisamos que você preencha o formulário online antes de podermos aceitar sua solicitação de pull.

Todos os detalhes estão disponíveis em [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla).

## Organização do repositório

O conteúdo do repositório azure-content segue a organização da documentação em [Azure.Microsoft.com](http://azure.microsoft.com). Esse repositório contém duas pastas raiz:

### \articles

A pasta *\articles* contém os artigos de documentação formatados como arquivos de redução com uma extensão *.md*.

Os artigos no diretório raiz são publicados no Azure.Microsoft.com no caminho *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*.

* **Nomes de arquivo do artigo:** confira [nossas diretrizes de nomenclatura de arquivo](./contributor-guide/file-names-and-locations.md).

Os artigos nas próprias pastas de serviço são publicados no Azure.Microsoft.com no caminho 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Subpastas de mídia:** a pasta *\articles* contém a pasta *\media* dos arquivos de mídia do artigo de diretório raiz, onde há subpastas com as imagens de cada artigo. As pastas de serviço contêm uma pasta de mídia separada para os artigos em cada pasta de serviço. As pastas de imagem do artigo têm nomes idênticos ao do arquivo do artigo, menos a extensão de arquivo *.md*.

### \includes

Você pode criar seções de conteúdo reutilizável a ser incluído em um ou mais artigos. Confira [Extensões personalizadas usadas em nosso conteúdo técnico](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates

Essa pasta contém nosso modelo de redução padrão com a formatação de redução básica de que você precisa para um artigo.

### \contributor-guide

Essa pasta contém artigos que fazem parte do nosso guia do colaborador.

## Usar o GitHub, o Git e este repositório

Para saber mais sobre como contribuir, como usar a IU do GitHub para contribuir com pequenas alterações e como bifurcar e clonar o repositório para contribuições mais significativas, confira [Instalar e configurar as ferramentas de criação no GitHub](./contributor-guide/tools-and-setup.md).

Se você instalar o GitBash e optar por trabalhar localmente, as etapas para criar uma nova ramificação de trabalho local, para fazer alterações e para enviar as alterações de volta à ramificação principal serão listadas em [Comandos do Git para a criação de um novo artigo ou para a atualização de um artigo existente](./contributor-guide/git-commands-for-master.md)

### Ramificações

É recomendável que você crie ramificações de trabalho locais destinadas a um escopo de alteração específico. Cada ramificação deve ser limitada a um único conceito/artigo para simplificar o fluxo de trabalho e reduzir a possibilidade de conflitos de mesclagem. Os seguintes esforços são do escopo apropriado para uma nova ramificação:

* Um novo artigo (e as imagens associadas)
* Edições de ortografia e gramática em um artigo.
* Aplicação de uma única alteração de formatação em um grande conjunto de artigos (por exemplo, um novo rodapé de direitos autorais).

## Como usar a redução para formatar seu tópico

Todos os artigos desse repositório usam a redução do GitHub. Veja uma lista de recursos.

- [Noções básicas de redução](https://help.github.com/articles/markdown-basics/)

- [Folha de referência de reduções imprimível](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Para obter nossa lista de editores de redução, confira o [tópico sobre ferramentas e instalação](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadados do artigo

Os metadados do artigo habilitam determinadas funcionalidades do site azure.microsoft.com, como a atribuição de autor, a atribuição de colaborador, as trilhas, as descrições do artigo e as otimizações de SEO, bem como os relatórios usados pela Microsoft para a avaliação do desempenho do conteúdo. Portanto, os metadados são importantes! [Veja as diretrizes para garantir que seus metadados estejam corretos](./contributor-guide/article-metadata.md).

## Mais recursos

Confira o [índice do nosso guia do colaborador](./contributor-guide/contributor-guide-index.md) para obter todos os tópicos de orientação.

<!---HONumber=AcomDC_0307_2016-->