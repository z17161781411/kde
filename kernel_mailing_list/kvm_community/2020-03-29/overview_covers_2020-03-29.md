

#### [PATCH 0/6] vhost: Reset batched descriptors on SET_VRING_BASE call
##### From: =?utf-8?q?Eugenio_P=C3=A9rez?= <eperezma@redhat.com>

```c
From patchwork Sun Mar 29 11:33:53 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 8bit
X-Patchwork-Submitter: Eugenio Perez Martin <eperezma@redhat.com>
X-Patchwork-Id: 11463983
Return-Path: <SRS0=zb6b=5O=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id DF78C92C
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 29 Mar 2020 11:34:17 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id B5A3920774
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 29 Mar 2020 11:34:17 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="He/no2RA"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1728096AbgC2LeQ (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sun, 29 Mar 2020 07:34:16 -0400
Received: from us-smtp-delivery-74.mimecast.com ([63.128.21.74]:40798 "EHLO
        us-smtp-delivery-74.mimecast.com" rhost-flags-OK-OK-OK-OK)
        by vger.kernel.org with ESMTP id S1727869AbgC2LeQ (ORCPT
        <rfc822;kvm@vger.kernel.org>); Sun, 29 Mar 2020 07:34:16 -0400
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1585481655;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type:
         content-transfer-encoding:content-transfer-encoding;
        bh=ViPGPqwEgspRtJgmZ4rE2OqFoX1oHWclqobpFUgHsnM=;
        b=He/no2RAPkfymhV+3KbcSW24BNsO8sYmihMdWTVOIwLmI7MHQowgaFCJeI492JWjDGaJgY
        E/IB8www1NV0Gac9ERd3sZwBosPtBsTXwlsbdtv1fsSl2KiKIQs6vnvmV4WcO3i3Zy4HpG
        XnKKEplZo7LfetEPxAeUtNAFwn3AJIw=
Received: from mimecast-mx01.redhat.com (mimecast-mx01.redhat.com
 [209.132.183.4]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-224-gzzDmRD2MRCIB1hML660mw-1; Sun, 29 Mar 2020 07:34:11 -0400
X-MC-Unique: gzzDmRD2MRCIB1hML660mw-1
Received: from smtp.corp.redhat.com (int-mx06.intmail.prod.int.phx2.redhat.com
 [10.5.11.16])
        (using TLSv1.2 with cipher AECDH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by mimecast-mx01.redhat.com (Postfix) with ESMTPS id EA33118B5F69;
        Sun, 29 Mar 2020 11:34:09 +0000 (UTC)
Received: from eperezma.remote.csb (ovpn-112-95.ams2.redhat.com
 [10.36.112.95])
        by smtp.corp.redhat.com (Postfix) with ESMTP id 393DB5C1BE;
        Sun, 29 Mar 2020 11:34:03 +0000 (UTC)
From: =?utf-8?q?Eugenio_P=C3=A9rez?= <eperezma@redhat.com>
To: "Michael S. Tsirkin" <mst@redhat.com>
Cc: "virtualization@lists.linux-foundation.org"
  <virtualization@lists.linux-foundation.org>,
 Halil Pasic <pasic@linux.ibm.com>,
 =?utf-8?q?Eugenio_P=C3=A9rez?= <eperezma@redhat.com>,
 Stephen Rothwell <sfr@canb.auug.org.au>,
 Linux Next Mailing List <linux-next@vger.kernel.org>,
 kvm list <kvm@vger.kernel.org>, Cornelia Huck <cohuck@redhat.com>,
 Christian Borntraeger <borntraeger@de.ibm.com>,
 "linux-kernel@vger.kernel.org" <linux-kernel@vger.kernel.org>
Subject: [PATCH 0/6] vhost: Reset batched descriptors on SET_VRING_BASE call
Date: Sun, 29 Mar 2020 13:33:53 +0200
Message-Id: <20200329113359.30960-1-eperezma@redhat.com>
MIME-Version: 1.0
X-Scanned-By: MIMEDefang 2.79 on 10.5.11.16
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Vhost did not reset properly the batched descriptors on SET_VRING_BASE event. Because of that, is possible to return an invalid descriptor to the guest.

This series ammend this, and creates a test to assert correct behavior. To do that, they need to expose a new function in virtio_ring, virtqueue_reset_free_head. Not sure if this can be avoided.

Also, change from https://lkml.org/lkml/2020/3/27/108 is not included, that avoids to update a variable in a loop where it can be updated once.

This is meant to be applied on top of eccb852f1fe6bede630e2e4f1a121a81e34354ab in git.kernel.org/pub/scm/linux/kernel/git/mst/vhost.git, and some commits should be squashed with that series.

Eugenio Pérez (6):
  tools/virtio: Add --batch option
  tools/virtio: Add --batch=random option
  tools/virtio: Add --reset=random
  tools/virtio: Make --reset reset ring idx
  vhost: Delete virtqueue batch_descs member
  fixup! vhost: batching fetches

 drivers/vhost/test.c         |  57 ++++++++++++++++
 drivers/vhost/test.h         |   1 +
 drivers/vhost/vhost.c        |  12 +++-
 drivers/vhost/vhost.h        |   1 -
 drivers/virtio/virtio_ring.c |  18 +++++
 include/linux/virtio.h       |   2 +
 tools/virtio/linux/virtio.h  |   2 +
 tools/virtio/virtio_test.c   | 123 +++++++++++++++++++++++++++++++----
 8 files changed, 201 insertions(+), 15 deletions(-)
```
