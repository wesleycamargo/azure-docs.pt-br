---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279424"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| [Máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) por serviço de nuvem<sup>1</sup> |50 |50 |
| Pontos de extremidade de entrada por serviço de nuvem<sup>2</sup> |150 |150 |

<sup>1</sup>Máquinas virtuais criadas no Gerenciamento de Serviços (e não no Gerenciador de Recursos) são armazenadas automaticamente em um serviço de nuvem. Você pode adicionar mais máquinas virtuais a esse serviço de nuvem para disponibilidade e balanceamento de carga. 

<sup>2</sup>Pontos de extremidade de entrada permitem comunicações com uma máquina virtual de fora do serviço de nuvem da máquina virtual. As máquinas virtuais no mesmo serviço de nuvem ou rede virtual podem se comunicar automaticamente umas com as outras. Confira: [Como instalar pontos de extremidade em uma máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
