<properties 
	pageTitle="Configurações Personalizadas para Ambientes de Serviço de Aplicativo" 
	description="Definições de configuração personalizadas para Ambientes de Serviço de Aplicativo" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# Definições de Configuração Personalizadas para Ambientes de Serviço de Aplicativo

## Visão geral ##
Como os Ambientes de Serviço de Aplicativo são isolados em um único cliente, há certas definições de configuração que podem ser aplicadas exclusivamente a um Ambiente de Serviço de Aplicativo. Este artigo documenta as várias personalizações específicas do Ambiente de Serviço de Aplicativo que estão disponíveis.

As personalizações do Ambiente de Serviço de Aplicativo são armazenadas usando uma matriz no novo atributo "clusterSettings" encontrado no dicionário de “Propriedades” da entidade ARM *hostingEnvironments*.

Um trecho do modelo ARM abreviado (abaixo) mostra o atributo "clusterSettings":


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

O atributo "clusterSettings" pode ser incluído em um modelo do ARM para atualizar o Ambiente de Serviço de Aplicativo.

Como alternativa, o valor do atributo pode ser atualizado usando o [Gerenciador de Recursos do Azure](https://resources.azure.com). No Gerenciador de Recursos do Azure, navegue até o nó para Ambiente de Serviço de Aplicativo (assinaturas resourceGroups --> provedores Micrososft.Web --> hostingEnvironments) e clique no Ambiente de Serviço de Aplicativo específico a ser atualizado.

Na janela do navegador à direita, clique em "Leitura/gravação" na barra de ferramentas superior para permitir a edição interativa no Gerenciador de Recursos. Clique no botão azul "Editar" para tornar o modelo ARM editável. Role até a parte inferior da janela do navegador à direita. O atributo "clusterSettings" estará na parte inferior, na qual você poderá inserir ou atualizar seu valor.

Digite (ou copie e cole) a matriz de valores de configuração desejada no atributo "clusterSettings". Em seguida, clique no botão verde "PUT" localizado na parte superior da janela do navegador à direita para confirmar a alteração no Ambiente de Serviço de Aplicativo.

Independentemente da abordagem usada ara atualizar o Ambiente de Serviço de Aplicativo, depois que a alteração for enviada, levará aproximadamente 30 minutos, multiplicados pelo número de front-ends no Ambiente de Serviço de Aplicativo para que a alteração entre em vigor. Por exemplo, se um Ambiente de Serviço de Aplicativo tiver quatro front-ends, levará aproximadamente duas horas para que a atualização de configuração seja concluída. Embora a alteração de configuração esteja sendo revertida, outras operações de dimensionamento ou operações de alteração de configuração não serão possíveis no Ambiente de Serviço de Aplicativo.

## Desabilitando o TLS 1.0 ##
Uma dúvida recorrente dos clientes, principalmente daqueles lidando com auditorias de conformidade de PCI, é a capacidade de desabilitar explicitamente o TLS 1.0 para seus aplicativos.

O TLS 1.0 pode ser desabilitado com a seguinte entrada de *clusterSettings*:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## Introdução
O site de modelo de ARM de Início Rápido do Azure inclui um modelo com a definição básica sobre a [criação de um Ambiente de Serviço de Aplicativo](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->