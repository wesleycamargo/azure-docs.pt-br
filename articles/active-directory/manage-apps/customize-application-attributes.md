---
title: Personalizar mapeamentos de atributo do Azure AD | Microsoft Docs
description: Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1926849c8ec63b4240d951e46b1341f31f7c5bd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170339"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory
O AD do Microsoft Azure dá suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, Google Apps e outros. Se você tiver provisionamento de usuário para um aplicativo SaaS de terceiros habilitado, o portal do Azure controla seus valores de atributo na forma de mapeamento de atributos.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário do Azure AD e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos além de Usuários, como Grupos. <br> 
 Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Isso significa que você pode alterar ou excluir mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.
 
## <a name="editing-user-attribute-mappings"></a>Editar mapeamentos de atributos de usuário

No portal do Azure AD, você pode acessar esse recurso clicando em uma configuração de **Mapeamentos** em **Provisionamento** na seção **Gerenciar** de um **Aplicativo Enterprise**.


![Salesforce](./media/customize-application-attributes/21.png) 

Clicar em uma configuração de **Mapeamentos** abre a tela **Mapeamento de Atributos** relacionada. Há mapeamentos de atributo que são exigidos por um aplicativo SaaS para funcionar corretamente. Para os atributos necessários, o recurso **Excluir** não está disponível.


![Salesforce](./media/customize-application-attributes/22.png)

No exemplo acima, você pode ver que o atributo **Username** de um objeto gerenciado no Salesforce é preenchido com o valor **userPrincipalName** do Objeto do Azure Active Directory vinculado.

Você pode personalizar os **Mapeamentos de Atributo** existentes clicando em um mapeamento. Isso abre a tela **Editar Atributo**.

![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo
Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro. Há quatro tipos diferentes de mapeamento com suporte:

* **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
* **Constante** – o atributo de destino é preenchido com uma cadeia de caracteres especificada por você.
* **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script. 
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](functions-for-customizing-application-data.md).
* **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se o atributo de destino estiver vazio, ele será preenchido com o valor padrão que você especificar.

Além desses quatro tipos básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor **padrão** opcional. A atribuição do valor padrão assegura que um atributo de destino seja preenchido com um valor, se não houver um valor no AD do Azure nem no objeto de destino. A configuração mais comum é deixar isso em branco.


### <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você já foi apresentado à propriedade de tipo de mapeamento de atributo.
Além dessa propriedade, mapeamentos de atributo também dão suporte aos seguintes atributos:

- **Atributo de origem** – o atributo de usuário do sistema de origem (exemplo: Azure Active Directory).
- **Atributo de destino** – o atributo de usuário no sistema de destino (exemplo: ServiceNow).
- **Combinar objetos utilizando esse atributo** – se esse mapeamento deve ou não ser utilizado para identificar com exclusividade usuários entre os sistemas de origem e destino. Normalmente, isso é definido no atributo userPrincipalName ou email no Azure AD, que costuma ser mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.
- **Aplicar esse mapeamento**
    - **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário
    - **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário


## <a name="editing-group-attribute-mappings"></a>Editar mapeamentos de atributos de grupo

Um número selecionado de aplicativos, como ServiceNow, Box e Google Apps, dão suporte à capacidade de provisionar objetos de Grupo, além de objetos de Usuário. Objetos de Grupo podem conter propriedades do grupo, como nomes de exibição e aliases de email, além dos membros do grupo.

![ServiceNow](./media/customize-application-attributes/24.png)

O provisionamento de grupo pode ser opcionalmente ativado ou desabilitado, selecionando o mapeamento de grupo em **Mapeamentos** e configurando a opção desejada como **Habilitada** na tela do **Mapeamento de Atributos**.

Os atributos provisionados como parte dos objetos de Grupo podem ser personalizados da mesma maneira como os objetos de Usuário, como descrito anteriormente. 

>[!TIP]
>O provisionamento dos objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](assign-user-or-group-access-portal.md) para um aplicativo. É possível atribuir um grupo a um aplicativo, mas somente provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completos não é necessário para usar grupos em atribuições.


## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário que têm suporte para um determinado aplicativo são pré-configurados. A maioria das APIs de gerenciamento de usuários do aplicativo não oferecem suporte para descoberta de esquema, portanto o serviço de provisionamento do Azure AD não é capaz de gerar de forma dinâmica a lista de atributos com suporte por meio de chamadas para o aplicativo. 

No entanto, alguns aplicativos oferecem suporte a atributos personalizados. Para que o serviço de provisionamento do Azure seja capaz de ler e gravar aos atributos personalizados, suas definições devem ser inseridas no portal do Azure usando a caixa de seleção **Mostrar opções avançadas** na parte inferior da tela **Mapeamento de Atributo**.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

* Salesforce
* ServiceNow
* Workday
* Active Directory do Azure ([atributos do Azure AD Graph API padrão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e há suporte para extensões de diretório personalizado)
* Aplicativos que dão suporte ao [SCIM 2.0](https://tools.ietf.org/html/rfc7643), onde os atributos definidos no [esquema principal](https://tools.ietf.org/html/rfc7643) precisam ser adicionados

>[!NOTE]
>Editar a lista de atributos com suporte é recomendado apenas para os administradores que personalizaram o esquema de seus aplicativos e sistemas e que tenham conhecimento de antemão de como seus atributos personalizados foram definidos. Às vezes, isso requer familiaridade com as APIs e as ferramentas dos desenvolvedores fornecidas por um aplicativo ou sistema. 

![Editor](./media/customize-application-attributes/25.png) 

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

* **Nome** - O nome do sistema do atributo, conforme definido no esquema do objeto de destino. 
* **Tipo** - O tipo de dados que o atributo armazena, conforme definido no esquema do objeto de destino. Isso pode ser um dos seguintes:
   * *Binário* - O atributo contém dados binários.
   * *Booliano* - O atributo contém um valor Verdadeiro ou Falso.
   * *DateTime* - O atributo contém uma cadeia de caracteres de data.
   * *Número inteiro* - O atributo contém um número inteiro.
   * *Referência* - O atributo contém uma ID que faz referência a um valor armazenado em outra tabela no aplicativo de destino.
   * *Cadeia de caracteres* - O atributo contém uma cadeia de caracteres de texto. 
* **Chave primária?** - Se o atributo é ou não definido como um campo de chave primária no esquema do objeto de destino.
* **Obrigatório?** - Se o atributo deve ou não ser preenchido no aplicativo ou sistema de destino.
* **Vários valores?** - Se o atributo dá suporte ou não para vários valores.
* **Diferenciar maiúsculas e minúsculas?** - Se os valores dos atributos são ou não avaliados com diferenciação de maiúsculas e minúsculas.
* **Expressão de API** - Não usar, a menos que instruído a fazer isso pela documentação de um conector de provisionamento específico (como o Workday).
* **Referência de Atributo de Objeto** - Se este é um atributo de tipo de Referência, então esse menu permite que você selecione a tabela e o atributo no aplicativo de destino que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Departamento" cujo valor armazenado faz referência a um objeto em uma tabela separada de "Departamentos", selecione "Departments.Name". Observe que as tabelas de referência e os campos primários de ID suportados para um determinado aplicativo são pré-configurados e atualmente não podem ser editados usando o portal do Azure, mas podem ser editados usando a [API do Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Para adicionar um novo atributo, role até o final da lista de atributos com suporte, preencha os campos acima usando as entradas fornecidas e selecione **Adicionar Atributo**. Selecione **Salvar** quando terminar de adicionar atributos. Em seguida, será necessário recarregar a guia **Provisionamento** para que os novos atributos fiquem disponíveis no editor de mapeamento de atributo.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurando os atributos padrão e mapeamentos de atributos

Caso seja necessário recomeçar e redefinir os mapeamentos existentes de volta para seu estado padrão, você pode selecionar a caixa de seleção **Restaurar os mapeamentos padrão** e salvar a configuração. Isso define todos os mapeamentos como se o aplicativo tivesse acabado de ser adicionado ao seu locatário do Azure AD da galeria de aplicativos. 

A seleção dessa opção efetivamente forçará uma ressincronização de todos os usuários enquanto o serviço de provisionamento estiver em execução. 

>[!IMPORTANT]
>É altamente recomendável que o **Status de provisionamento** seja definido como **Desativado** antes de chamar essa opção.


## <a name="what-you-should-know"></a>O que você deve saber

* O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização. 

* A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados. 

* É uma prática recomendada manter o número de alterações consecutivas aos seus mapeamentos de atributos no mínimo.


## <a name="next-steps"></a>Próximas etapas

* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)


