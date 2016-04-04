<properties
   pageTitle="Visão geral do cache local do Serviço de Aplicativo do Azure"
   description="Este artigo descreve como habilitar, redimensionar e consultar o status do recurso de Cache Local do Serviço de Aplicativo do Azure"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="Uso restrito aos usuários avançados de SEO. Separe os termos com vírgulas. Verifique com seu usuário avançado de SEO antes de alterar o conteúdo deste artigo que contém esses termos."/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Visão geral do cache local do Serviço de Aplicativo do Azure

O conteúdo do aplicativos Web do Azure é armazenado no Armazenamento do Azure e exibido de forma duradoura como um compartilhamento de conteúdo. Esse design tem como objetivo trabalhar com vários aplicativos e tem os seguintes atributos:

* O conteúdo é compartilhado entre várias instâncias de VM do aplicativo Web. 
* O conteúdo é duradouro e pode ser modificado pela execução dos aplicativos Web. 
* Os arquivos de log e arquivos de dados de diagnóstico estão disponíveis na mesma pasta de conteúdo compartilhado. 
* A publicação de um novo conteúdo atualiza diretamente a pasta de conteúdo, e o mesmo pode ser visto imediatamente no site do SCM e no aplicativo Web em execução (normalmente, algumas tecnologias como ASP.NET iniciam uma reinicialização do aplicativo Web em algumas alterações de arquivo para selecionar o conteúdo mais recente). 

Embora muitos aplicativos Web usem um ou todos esses recursos, alguns aplicativos Web querem apenas um repositório de conteúdo somente leitura de alto desempenho a partir do qual possam ser executados com alta disponibilidade. Esses aplicativos podem se beneficiar de uma cópia específica de instância de VM do conteúdo, a partir de agora chamado de "Cache Local". O _Cache Local_ fornece um modo de exibição de função Web de seu conteúdo, e esse conteúdo é um cache de gravação-mas-descarte de seu conteúdo de armazenamento criado de forma assíncrona na inicialização do site. Quando o cache estiver pronto, o site será alternado para executar o conteúdo em cache. Os aplicativos Web em execução no Cache Local aproveitam os seguintes benefícios:

* Eles estão imunes às latências enfrentadas ao acessar o conteúdo no Armazenamento do Azure 
* Eles estão imunes aos servidores que fornecem o compartilhamento de conteúdo e que estão passando por atualizações planejadas ou por tempos de inatividade não planejados, e por quaisquer outras interrupções no Armazenamento do Azure. 
* Uma quantidade menor de reinicializações do aplicativo devido a alterações no compartilhamento do armazenamento. 

## Como o Cache Local muda o comportamento do Serviço de Aplicativo

* O cache local é uma cópia das pastas /site e /siteextensions do aplicativo Web e é criado na instância local da VM na inicialização do aplicativo Web. O tamanho do cache local por aplicativo Web é limitado a 300 MB por padrão, mas pode ser aumentado até 1 GB. 
* O cache local é de gravação e leitura, mas quaisquer modificações serão descartadas quando o aplicativo Web mover as máquinas virtuais ou for reiniciado. O cache local não deve ser usado para aplicativos que persistem dados críticos no repositório de conteúdo. 
* Os aplicativos Web podem continuar a gravar arquivos de log e dados de diagnóstico, como fazem atualmente. Os arquivos de log e dados, porém, são armazenados localmente na VM e depois são copiados periodicamente no repositório do conteúdo compartilhado. A cópia para o repositório de conteúdo compartilhado é a melhor opção, e podem ocorrer perdas de write-backs devido a uma falha repentina de uma instância de VM. 
* Há uma alteração na estrutura da pastas das pastas LogFiles e Data para aplicativos Web que usam o Cache Local. Há agora subpastas nas pastas de armazenamento "LogFiles" e "Data" seguindo o padrão de nomenclatura de "identificador exclusivo" + carimbo de data/hora. Cada uma das subpastas corresponde a uma instância de VM na qual o aplicativo Web está em execução ou em que foi executado.  
* A publicação de alterações no aplicativo Web por meio de qualquer um dos mecanismos de publicação, publicará no repositório de conteúdo compartilhado. Isso ocorre por padrão, pois queremos que o conteúdo publicado seja duradouro. Para atualizar o cache local do aplicativo Web, é necessário reiniciá-lo. Parece uma etapa excessiva? Confira as informações abaixo para tornar o ciclo de vida perfeito.
* D:\\Home apontará para o Cache Local. D:\\Local continuará apontando para o armazenamento temporário específico da VM. 
* A exibição de conteúdo padrão do site do SCM continuará a ser aquela do repositório de conteúdo compartilhado. Para ver qual é a aparência de sua pasta de cache local, navegue até https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache

## Como habilitar o Cache Local no Serviço de Aplicativo do Azure

O Cache Local é configurado usando uma combinação de configurações de aplicativo reservadas. Estas Configurações de Aplicativo podem ser feitas usando os métodos a seguir:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Gerenciador de Recursos do Azure](#Configure-Local-Cache-ARM)

### Como configurar o Cache Local usando o Portal do Azure
<a name="Configure-Local-Cache-Portal"></a>

O Cache Local é habilitado de acordo com cada aplicativo Web usando um AppSetting. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![Configurações de Aplicativo do Portal do Azure: Cache Local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Como configurar o Cache Local usando o Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300" 
    }
}

...
```

## Como alterar o tamanho do Cache Local do Serviço de Aplicativo?

Por padrão, o tamanho do cache local é de **300 MB**. Isso inclui as pastas Site, SiteExtensions que são copiadas do repositório de conteúdo, bem como quaisquer pastas de dados e de logs criadas localmente. Para aumentar esse limite, use o AppSetting `WEBSITE_LOCAL_CACHE_SIZEINMB`. O tamanho pode ser aumentado para até **1 GB** (1.000 MB) por aplicativo Web.

## Práticas recomendadas para usar o Cache Local do Serviço de Aplicativo

Recomendamos que o Cache Local seja usado em conjunto com o recurso [Ambientes de preparo](../app-service-web/web-sites-staged-publishing.md).

* Adicione um Appsetting _temporário_ `WEBSITE_LOCAL_CACHE_OPTION` com o valor `Always` ao **Slot de produção**. Se estiver usando `WEBSITE_LOCAL_CACHE_SIZEINMB`, adicione-o também como uma configuração temporária ao slot de produção. 
* Crie um slot de preparo e publique em seu slot de preparo. Normalmente, o slot de preparo não usa o cache local para habilitar um ciclo de vida contínuo de compilação-implantação-teste para preparo, enquanto recebe os benefícios do Cache Local para o slot de produção. 
*	Teste seu site no Slot de preparo.  
*	Quando estiver pronto, execute uma [operação de permuta](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre seus slots de **Preparo** e de **Produção**.  
*	As configurações temporárias ocorrem por nome e são fixadas a um slot. Então, quando o Slot de preparo é trocado pelo de Produção, ele herda as configurações do Aplicativo do Cache Local. O Slot de produção recém-trocado será executado no Cache Local após alguns minutos e será aquecido como parte do aquecimento de slot após a troca. Então, quando a permuta do slot é concluída, o slot de produção é executado no Cache Local.

## Perguntas frequentes

### Como saber se o Cache Local é aplicável ao meu aplicativo Web? 

Se o seu aplicativo Web precisar de um repositório de conteúdo confiável e de alto desempenho, não usar o repositório de conteúdo para gravar dados críticos em tempo de execução e tiver menos de 1 GB de tamanho total, a resposta será Sim! Para obter o tamanho total das pastas "site" e "site extensions", você pode usar a extensão de site "Uso de Disco de Aplicativos Web do Azure".
 
### Como posso habilitar o Cache Local? 

Veja a seção acima sobre Práticas recomendadas ao usar o Cache Local.
 
### Como saber se meu site passou a usar o cache local? 

Se estiver usando o recurso de Cache Local com os Ambientes de Preparo, a operação de permuta não será concluída até que o Cache Local seja aquecido. Para verificar se seu site está em execução no cache local, verifique a variável de ambiente do processo de trabalho WEBSITE\_LOCALCACHE\_READY. Use as instruções descritas na página [variável de ambiente do processo de trabalho](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) para acessar as variáveis de ambiente do processo de trabalho em várias instâncias.
 
### Acabei de publicar novas alterações, mas meu aplicativo Web parece não tê-las. Por quê? 
Se o seu aplicativo Web usar o Cache Local, será necessário reiniciar o site para obter as últimas alterações. Não quer fazer isso em um site de produção? Veja as opções de slot acima.
 
### Onde estão meus logs? 

Com o cache local, as pastas de logs e de dados são um pouco diferentes. No entanto, a estrutura de suas subpastas permanece a mesmo, exceto pelo fato de estarem aninhadas em uma subpasta com o formato "Identificador exclusivo da VM" + carimbo de data/hora.
 
### Meu Cache Local está habilitado, mas meu aplicativo Web ainda é reiniciado. Por que isso acontece? Pensei que o Cache Local ajudasse com reinicializações frequentes do aplicativo. 

O Cache Local ajuda a evitar reinicializações de aplicativo Web relacionadas ao armazenamento. No entanto, seu aplicativo Web ainda pode sofrer reinicializações durante atualizações de infraestrutura planejadas da VM. A quantidade de reinicializações de aplicativo gerais enfrentadas com o Cache Local habilitado deve ser menor.

<!---HONumber=AcomDC_0323_2016-->