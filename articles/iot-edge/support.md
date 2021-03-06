---
title: Suporte da plataforma do Azure IoT Edge | Microsoft Docs
description: Plataformas com suporte do Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2163179d3353626e12495381fc64792db56ec78e
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286355"
---
# <a name="azure-iot-edge-support"></a>Suporte do Azure IoT Edge
Há várias maneiras de buscar suporte para o produto Azure IoT Edge.

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Sistemas operacionais
O Azure IoT Edge executa na maioria dos sistemas operacionais que podem executar contêineres, no entanto, todos estes igualmente não têm suporte. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.

### <a name="tier-1"></a>Camada 1
Sistemas de camada 1 podem ser considerados como oficialmente com suporte. Isso significa que a Microsoft:
* tem esses sistemas operacionais em testes automatizados
* fornece pacotes de instalação aos sistemas

Disponível para o público geral
| Sistema operacional | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Não  | SIM|
| Ubuntu Server 16.04 | SIM | Não  |
| Ubuntu Server 18.04 | SIM | Não  |

Visualização pública
| Sistema operacional | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core (atualização de abril de 2018) | SIM | Não  |
| Windows 10 IoT Enterprise (atualização de abril de 2018) | SIM | Não  |
| Windows 10 Server 1803 | SIM | Não  |

### <a name="tier-2"></a>Camada 2
Os sistemas da Camada 2 podem ser considerados compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Isso significa que:
* A Microsoft fez testes ad-hoc nas plataformas ou conhece um parceiro executando o Azure IoT Edge com êxito na plataforma
* Pacotes de instalação para outras plataformas podem funcionar nessas plataformas

| Sistema operacional | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | SIM | SIM |
| Debian 8 | SIM | SIM |
| Debian 9 | SIM | SIM |
| RHEL 7.5 | SIM | SIM |
| Ubuntu 18.04 | SIM | SIM |
| Ubuntu 16.04 | SIM | SIM |
| Wind River 8 | SIM | Não  |
| Yocto | SIM | Não  |

## <a name="container-engines"></a>Mecanismos de contêiner
O Azure IoT Edge precisa de um mecanismo de contêiner para iniciar módulos, independentemente do sistema operacional no qual está em execução. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Ele é baseado no projeto de software livre Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Também são baseados no projeto de software livre Moby e compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte ao esforço para sistemas que usam esses mecanismos de contêiner, no entanto, a Microsoft não tem capacidade de enviar correções para problemas inerentes a esses mecanismos. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
