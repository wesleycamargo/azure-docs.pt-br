<properties
	pageTitle="Condição de correspondência do mecanismo de regras CDN (Rede de Distribuição de Conteúdo do Azure) e detalhes do recurso"
	description="Este tópico lista as descrições detalhadas das condições de correspondência e dos recursos disponíveis para o mecanismo de regras da CDN (Rede de Distribuição de Conteúdo do Azure)."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>


# Condição de correspondência do mecanismo de regras CDN e detalhes do recurso

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

Este tópico lista descrições detalhadas dos recursos e das condições de correspondência disponíveis para o [mecanismo de regras](cdn-rules-engine.md) da CDN (Rede de Distribuição de Conteúdo do Azure)

## Condições de correspondência

Uma condição de correspondência identifica tipos específicos de solicitações para os quais um conjunto de recursos será executado.

Por exemplo, ela pode ser usada para filtrar solicitações de conteúdo em um local específico, solicitações geradas de um endereço IP ou país específico ou por informações de cabeçalho.

### Sempre

A condição de correspondência Sempre foi projetada para aplicar um conjunto de recursos padrão a todas as solicitações.

### Dispositivo

A condição de correspondência Dispositivo identifica solicitações feitas de um dispositivo móvel baseadas em suas propriedades.

### Local padrão

Essas condições de correspondência são projetadas para identificar solicitações baseadas na localização do solicitante.

Nome | Finalidade
-----|--------
Número AS | Identifica solicitações originadas de uma rede específica.
País | Identifica solicitações originadas de países especificados.


### Origem

Essas condições de correspondência são projetadas para identificar solicitações que apontem para o armazenamento CDN ou para um servidor de origem do cliente.

Nome | Finalidade
-----|--------
Origem CDN | Identifica solicitações de conteúdo armazenado no armazenamento CDN.
Origem do Cliente | Identifica solicitações de conteúdo armazenado em um servidor de origem do cliente específico.


### Solicitação

Essas condições de correspondência são projetadas para identificar solicitações baseadas em suas propriedades.

Nome | Finalidade
-----|--------
Endereço IP do Cliente | Identifica solicitações originadas de um endereço IP específico.
Parâmetro de Cookie | Verifica o valor especificado nos cookies associados a cada solicitação.
Regex de Parâmetro de Cookie | Verifica a expressão regular especificada nos cookies associados a cada solicitação.
Cname de Borda | Identifica solicitações que apontam para um CNAME de borda específico.
Domínio de Referência | Identifica solicitações referenciadas dos nomes do host especificados.
Literal de Cabeçalho de Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como valores especificados.
Regex do Cabeçalho da Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponde à expressão regular especificada.
Curinga de Cabeçalho de Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponda ao padrão especificado.
Método de Solicitação | Identifica solicitações pelo método HTTP.
Esquema de Solicitação | Identifica solicitações pelo protocolo HTTP.

### URL

Essas condições de correspondência são projetadas para identificar solicitações baseadas em suas URLs.

Nome | Finalidade
-----|--------
Diretório do Caminho da URL | Identifica solicitações pelo caminho relativo.
Extensão do Caminho da URL | Identifica solicitações pela extensão de nome do arquivo.
Nome do Arquivo do Caminho da URL | Identifica solicitações pelo nome do arquivo
Literal do Caminho da URL | Compara a caminho relativo da solicitação com o valor especificado.
Regex do Caminho da URL | Compara o caminho relativo da solicitação com a expressão regular especificada.
Curinga do Caminho da URL | Compara o caminho relativo da solicitação com o padrão especificado.
Literal da Consulta da URL | Compara a sequência da cadeia de caracteres de consulta da solicitação com o valor especificado.
Parâmetro da Consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a um padrão especificado.
Regex da consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a uma expressão regular especificada.
Curinga da consulta da URL | Compara os valores especificados com a cadeia de caracteres de consulta da solicitação.

## Recursos

Um recurso define o tipo de ação que será aplicado ao tipo de solicitação identificado por um conjunto de condições de correspondência.

### Access

Esses recursos são projetados para controlar o acesso ao conteúdo.

> [AZURE.NOTE] A autenticação de token ainda não está disponível, mas terá suporte em uma versão futura.

Nome | Finalidade
-----|--------
Negar Acesso | Determina se todas as solicitações são rejeitadas com uma resposta 403 Proibido.
Autenticação de Token | Determina se a autenticação baseada em Token será aplicada a uma solicitação.
Código de Negação de Autenticação de Token | Determina o tipo de resposta que será retornado a um usuário quando uma solicitação for negada devido a autenticação baseada em token.
Autenticação de Token Ignorar Maiúsculas e Minúsculas da URL | Determina se as comparações de URL feitas por autenticação baseada em token diferenciam maiúsculas de minúsculas.
Parâmetros de Autenticação de Token | Determina se o parâmetro de cadeia de caracteres de consulta da autenticação baseada em token deve ser renomeado.

### Cache

Esses recursos são projetados para personalizar quando e como o conteúdo é armazenado.

Nome | Finalidade
-----|--------
Parâmetros de Largura de Banda | Determina se os parâmetros de limitação de largura de banda (isto é, ec\_rate e ec\_prebuf) estarão ativos.
Limitação de Largura de Banda | Limita a largura de banda para a resposta fornecida por nossos servidores de borda.
Ignorar o Cache | Determina se a solicitação pode aproveitar nossa tecnologia de cache.
Tratamento de Cabeçalho Cache-Control | Controla a geração de cabeçalhos de Controle de Cache pelo servidor de borda quando o recurso Idade Máxima Externa está ativo.
Cadeia de Caracteres da Consulta da Chave de Cache | Determina se a chave de cache incluirá ou excluirá parâmetros de cadeia de caracteres de consulta associados a uma solicitação.
Regravação da Chave de Cache | Regrava a chave de cache associada a uma solicitação.
Concluir o Preenchimento do Cache | Determina o que acontece quando uma solicitação resulta em uma perda no cache parcial em um servidor de borda.
Compactar Tipos de Arquivo | Define os formatos de arquivo que serão compactados no servidor.
Idade Máxima Interna Padrão | Determina o intervalo de idade máxima padrão para a revalidação de cache do servidor de borda para o servidor de origem.
Tratamento do Cabeçalho Expira | Controla a geração de cabeçalhos Expira por um servidor de borda quando o recurso Idade Máxima Externa está ativo.
Idade Máxima Externa | Determina o intervalo de idade máxima para a revalidação de cache do navegador para o servidor de borda.
Forçar Idade Máxima Interna | Determina o intervalo de idade máxima para a revalidação de cache do servidor de borda para o servidor de origem.
Suporte a H.264 (Download Progressivo de HTTP) | Determina os tipos de formatos de arquivo H.264 que podem ser usados para transmitir conteúdo.
Respeitar Solicitação Sem Cache | Determina se solicitações sem cache de um cliente HTTP serão encaminhadas para o servidor de origem.
Ignorar Ausência de Cache de Origem | Determina se nosso CDN irá ignorar determinadas diretivas atendidas por um servidor de origem.
Ignorar Intervalos Insatisfatórios | Determina a resposta que será retornada aos clientes quando uma solicitação gerar um código de status 416 Intervalo Solicitado Insatisfatório.
Máximo de Estado Obsoleto Interno | Controla quanto tempo após o tempo de expiração normal um ativo em cache pode ser atendido por um servidor de borda quando este não puder revalidar o ativo em cache no servidor de origem.
Compartilhamento de Cache Parcial | Determina se uma solicitação pode gerar conteúdo parcialmente em cache.
Pré-validar Conteúdo Armazenado em Cache | Determina se o conteúdo armazenado em cache será qualificado para revalidação antecipada antes que o TTL expire.
Atualizar Arquivos de Cache de Zero Byte. | Determina como a solicitação de cliente HTTP para um ativo de cache de zero byte é tratado por nossos servidores de borda.
Definir Códigos de Status Armazenáveis em Cache | Define o conjunto de códigos de status que pode resultar em conteúdo armazenado em cache.
Distribuição de Conteúdo Obsoleta em Erro | Determina se o conteúdo armazenado em cache expirado será entregue quando ocorre um erro durante a revalidação do cache ou ao recuperar o conteúdo solicitado do servidor de origem do cliente.
Obsoleto Ao Revalidar | Melhora o desempenho permitindo que os nossos servidores de borda atendam clientes obsoletos para o solicitante enquanto ocorre a revalidação.
Comentário | O recurso Comentário permite a adição de detalhes a uma regra.

### Cabeçalhos

Esses recursos são projetados para adicionar, modificar ou excluir os cabeçalhos da solicitação ou da resposta.

Nome | Finalidade
-----|--------
Cabeçalho de Resposta de Idade | Determina se um cabeçalho de resposta de idade será incluído na resposta enviada ao solicitante.
Depurar Cabeçalhos de Resposta do Cache | Determina se uma resposta pode incluir o cabeçalho de resposta X-EC-Debug, que fornece informações sobre a política de cache para o recurso solicitado.
Modificar Cabeçalho de Solicitação do Cliente | Substitui, acrescenta ou exclui um cabeçalho de uma solicitação.
Modificar Cabeçalho de Resposta do Cliente | Substitui, acrescenta ou exclui um cabeçalho de uma resposta.
Definir Cabeçalho Personalizado da IP do Cliente | Permite que o endereço IP do cliente solicitante seja adicionado à solicitação como um cabeçalho de solicitação personalizado.

### Logs

Esses recursos são projetados para personalizar os dados armazenados em arquivos de log brutos.

Nome | Finalidade
-----|--------
Campo de Log Personalizado 1 | Determina o formato e o conteúdo que será atribuído ao campo de log personalizado em um arquivo de log bruto.
Cadeia de Caracteres de Consulta de Log | Determina se uma cadeia de caracteres de consulta será armazenada juntamente com a URL nos logs de acesso.

### Otimizar

Esses recursos determinam se uma solicitação passará as otimizações fornecidas pelo Otimizador de Borda.

Nome | Finalidade
-----|--------
Otimizador de Borda | Determina se o Otimizador de Borda pode ser aplicado a uma solicitação.
Otimizador de Borda – Instanciar Configuração | Cria uma instância ou ativa a configuração do Otimizador de Borda associada a um site.


### Origem

Esses recursos são criados para controlar como a CDN se comunica com um servidor de origem.

Nome | Finalidade
-----|--------
Máximo de Solicitações Keep-Alive | Define o número máximo de solicitações para uma conexão Keep-Alive antes de ser fechado.
Cabeçalhos Especiais de Proxy | Define o conjunto de cabeçalhos de solicitação CDN específicos que será encaminhado de um servidor de borda para um servidor de origem.

### Especialidade

Esses recursos fornecem funcionalidade avançada que deve ser usada somente por usuários avançados.

Nome | Finalidade
-----|--------
Métodos HTTP Armazenáveis em Cache | Determina o conjunto de métodos HTTP adicionais que podem ser armazenados em cache em nossa rede.
Tamanho do Corpo da Solicitação Armazenável em Cache | Define o limite para determinar se uma resposta POST pode ser armazenada em cache.


### URL

Esses recursos permitem que uma solicitação seja redirecionada ou reescrita em uma URL diferente.

Nome | Finalidade
-----|--------
Seguir Redirecionamentos | Determina se as solicitações podem ser redirecionadas para o nome do host definido no cabeçalho Local retornado por um servidor de origem do cliente.
Redirecionamento de URL | Redireciona as solicitações por meio do cabeçalho Local.
Regravação de URL | Regrava a URL da solicitação.

### Firewall do Aplicativo Web

O recurso Firewall do Aplicativo Web determina se uma solicitação será verificada pelo Firewall do Aplicativo Web.

## Consulte também
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)

<!---HONumber=AcomDC_0518_2016-->