

#### [PATCH 0/4] use mmgrab
##### From: Julia Lawall <Julia.Lawall@inria.fr>

```c
From patchwork Sun Dec 29 15:42:54 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Julia Lawall <julia.lawall@inria.fr>
X-Patchwork-Id: 11312251
Return-Path: <SRS0=Vudl=2T=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id F37E7139A
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 29 Dec 2019 16:19:53 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id DCCE1208E4
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 29 Dec 2019 16:19:53 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726752AbfL2QTb (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sun, 29 Dec 2019 11:19:31 -0500
Received: from mail3-relais-sop.national.inria.fr ([192.134.164.104]:38967
        "EHLO mail3-relais-sop.national.inria.fr" rhost-flags-OK-OK-OK-OK)
        by vger.kernel.org with ESMTP id S1726455AbfL2QTb (ORCPT
        <rfc822;kvm@vger.kernel.org>); Sun, 29 Dec 2019 11:19:31 -0500
X-IronPort-AV: E=Sophos;i="5.69,372,1571695200";
   d="scan'208";a="334379015"
Received: from palace.rsr.lip6.fr (HELO palace.lip6.fr) ([132.227.105.202])
  by mail3-relais-sop.national.inria.fr with ESMTP/TLS/AES128-SHA256;
 29 Dec 2019 17:19:29 +0100
From: Julia Lawall <Julia.Lawall@inria.fr>
To: kvm@vger.kernel.org
Cc: kernel-janitors@vger.kernel.org, Cornelia Huck <cohuck@redhat.com>,
        linux-kernel@vger.kernel.org, linuxppc-dev@lists.ozlabs.org,
        openrisc@lists.librecores.org
Subject: [PATCH 0/4] use mmgrab
Date: Sun, 29 Dec 2019 16:42:54 +0100
Message-Id: <1577634178-22530-1-git-send-email-Julia.Lawall@inria.fr>
X-Mailer: git-send-email 1.9.1
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Mmgrab was introduced in commit f1f1007644ff ("mm: add new mmgrab()
helper") and most of the kernel was updated to use it. Update a few
remaining files.
---

 arch/openrisc/kernel/smp.c          |    2 +-
 drivers/misc/cxl/context.c          |    2 +-
 drivers/vfio/pci/vfio_pci_nvlink2.c |    2 +-
 drivers/vfio/vfio_iommu_spapr_tce.c |    2 +-
 4 files changed, 4 insertions(+), 4 deletions(-)
```
