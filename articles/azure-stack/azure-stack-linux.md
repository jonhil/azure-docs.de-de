---
title: Hinzufügen von Linux-Images zu Azure Stack
description: Hier erfahren Sie, wie Sie Linux-Images zu Azure Stack hinzufügen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 98a1235532ec4cc225ac6a5117265e145b21034b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245167"
---
# <a name="add-linux-images-to-azure-stack"></a>Hinzufügen von Linux-Images zu Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können virtuelle Linux-Computer in Azure Stack bereitstellen, indem Sie ein Linux-basiertes Image im Azure Stack-Marketplace hinzufügen. Am einfachsten wird ein Linux-Image über die Marketplace-Verwaltung zu Azure Stack hinzugefügt. Diese Images wurden vorbereitet und auf Kompatibilität mit Azure Stack getestet.

## <a name="marketplace-management"></a>Marketplace-Verwaltung

Die Verfahren zum Herunterladen von Linux-Images aus dem Azure Marketplace werden im Artikel [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md) beschrieben. Wählen Sie die Linux-Images aus, die Sie Benutzern in Azure Stack zur Verfügung stellen möchten. 

Beachten Sie, dass diese Images regelmäßig aktualisiert werden. Überprüfen Sie daher immer wieder die Marketplace-Verwaltung, um auf dem neuesten Stand zu bleiben.

## <a name="prepare-your-own-image"></a>Vorbereiten eines eigenen Image

Laden Sie nach Möglichkeit die Images herunter, die über die Marketplace-Verwaltung verfügbar sind, da diese für Azure Stack konzipiert und getestet wurden. 
 
Der Azure Linux-Agent (in der Regel `WALinuxAgent` oder `walinuxagent`) ist erforderlich, aber nicht alle Versionen des Agents können mit Azure Stack verwendet werden. Verwenden Sie mindestens die Version 2.2.18, wenn Sie Ihr eigenes Image erstellen. Beachten Sie, dass [cloud-init](https://cloud-init.io/) derzeit nicht für Azure Stack unterstützt wird.

Sie können Ihr eigenes Linux-Image gemäß den folgenden Anweisungen vorbereiten:

* [CentOS-basierte Verteilungen](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES &amp; openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Hinzufügen Ihres Images zum Marketplace
 
Gehen Sie wie unter [Verfügbarmachen eines VM-Images in Azure Stack](azure-stack-add-vm-image.md) beschrieben vor. Stellen Sie sicher, dass der `OSType`-Parameter auf `Linux` festgelegt ist.

Nachdem Sie das Image zum Marketplace hinzugefügt haben, wird ein Marketplace-Element erstellt, und Benutzer können einen virtuellen Linux-Computer bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Herunterladen von Marketplace-Elementen von Azure in Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Der Azure Stack-Marketplace – Übersicht](azure-stack-marketplace.md)