---
title: Cobertura de geocodificação nos mapas do Azure | Microsoft Docs
description: Saiba mais sobre a cobertura de geocodificação nos Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b8caa5cd7476489a92ce97570794a1d397f32630
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230899"
---
# <a name="azure-maps-geocoding-coverage"></a>Cobertura da geocodificação dos Mapas do Azure

Quando você procura um local com o Mapas do Azure, o serviço de pesquisa usa os termos de pesquisa e retorna as coordenadas de latitude e longitude, um processo chamado geocodificação. No entanto, Mapas não têm o mesmo nível de informações e precisão para todas as regiões. Use este artigo para determinar que tipo de locais você pode procurar de forma confiável em cada região. 

A capacidade de geocode em um país/região é dependente de cobertura de dados de estrada e a precisão da codificação geográfica do serviço de geocodificação. As seguintes categorizações são usadas especificar o nível de suporte de geocodificação em cada país/região.
* **Pontos de endereço** - dados de endereços podem ser resolvidos para uma coordenada de latitude/longitude dentro do pacote de endereço (limite de propriedade). Às vezes chamado de “Telhado” preciso. Esse é o nível mais alto de precisão para endereços disponíveis. 
* **Números residenciais** - endereços são interpolados para uma coordenada de latitude/longitude na rua.
* **Nível da rua** - endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado.
* **Nível de cidade** - nomes de locais de cidade que são suportados.

## <a name="americas"></a>Américas

| País/Região                                       | Pontos de endereço | Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguila                                            |                 |                |              |      ✓     |          ✓         |
| Antártica                                          |                 |                |              |      ✓     |          ✓         |
| Antígua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Santo Eustáquio e Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canadá                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Cayman                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colômbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Equador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Malvinas                                    |                 |                |              |      ✓     |          ✓         |
| Guiana Francesa                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Granada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| México                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicarágua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panamá                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguai                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| São Bartolomeu                                    |                 |                |       ✓      |      ✓     |          ✓         |
| São Cristóvão e Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Lúcia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| São Pedro e Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| São Vicente e Granadinas                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Geórgia do Sul e Sandwich do Sul        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Insulares dos Estados Unidos                |                 |                |              |      ✓     |          ✓         |
| Estados Unidos da América                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguai                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Virgens Britânicas                            |                 |                |              |      ✓     |          ✓         |
| Ilhas Virgens dos Estados Unidos                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Pacífico Asiático

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americana                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Butão                                              |                 |                |              |      ✓     |          ✓         |
| Território Britânico do Oceano Índico                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Camboja                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Ilha Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ilhas Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Comores                                             |                 |                |              |      ✓     |          ✓         |
| Ilhas Cook                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Polinésia Francesa                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| Região Administrativa Especial de Hong Kong                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Índia                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japão                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Coreia do Sul                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| RAE de Macau                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malásia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésia                                          |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nova Caledônia                                       |                 |                |              |      ✓     |          ✓         |
| Nova Zelândia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Ilha Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Coreia do Norte                                         |                 |                |              |      ✓     |          ✓         |
| Ilhas Marianas do Norte                            |                 |                |       ✓      |      ✓     |          ✓         |
| Paquistão                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua-Nova Guiné                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Paracel                                     |                 |                |              |      ✓     |                    |
| Filipinas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Senkaku                                     |        ✓        |                |              |      ✓     |          ✓         |
| Cingapura                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Salomão                                     |                 |                |              |      ✓     |          ✓         |
| Ilhas Curilas do Sul                                     |        ✓        |                |              |      ✓     |          ✓         |
| Ilhas Spratly                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tailândia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turcos e Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnã                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albânia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armênia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Áustria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaijão                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Bélgica                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bósnia e Herzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Belarus                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croácia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chipre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| República Tcheca                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dinamarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estônia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Faroe                                       |                 |                |              |      ✓     |          ✓         |
| Finlândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| França                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Geórgia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Alemanha                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grécia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groelândia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hungria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilha de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itália                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Cazaquistão                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Quirguistão                                          |                 |                |              |      ✓     |          ✓         |
| Letônia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituânia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedônia do Norte                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mônaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Países Baixos                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noruega                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polônia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Açores e Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Romênia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federação Russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sérvia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslováquia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovênia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espanha                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suécia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suíça                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadjiquistão                                          |                 |                |              |      ✓     |          ✓         |
| Turquia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turcomenistão                                        |                 |                |              |      ✓     |          ✓         |
| Ucrânia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Reino Unido                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbequistão                                          |                 |                |              |      ✓     |          ✓         |
| Cidade do Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Oriente Médio e África

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afeganistão                                         |                 |                |              |      ✓     |          ✓         |
| Algéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ilha Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camarões                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| República Centro-Africana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chade                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| República Democrática do Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egito                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| República da Guiné Equatorial                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritreia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Franceses do Sul|                        |                |              |      ✓     |          ✓         |
| Gabão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gâmbia                                              |                 |                |              |      ✓     |          ✓         |
| Gana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irã                                                |                 |                |              |      ✓     |          ✓         |
| Iraque                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordânia                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Quênia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Líbano                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesoto                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malaui                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivas                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Marshall                                    |                 |                |              |      ✓     |          ✓         |
| Mauritânia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurício                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marrocos                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Moçambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Níger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omã                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunião                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arábia Saudita                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seicheles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Serra Leoa                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somália                                             |                 |                |              |      ✓     |          ✓         |
| África do Sul                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudão do Sul                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazilândia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Síria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzânia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunísia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirados Árabes Unidos                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cisjordânia                                           |                 |                |              |      ✓     |          ✓         |
| Iêmen                                               |                 |                |              |      ✓     |          ✓         |
| Zâmbia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbábue                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a geocodificação de Mapas do Azure, consulte as páginas de referência [Pesquisa](https://docs.microsoft.com/rest/api/maps/search).

Saiba mais sobre o [áreas de cobertura para serviço de tráfego do Mapas](traffic-coverage.md). 

