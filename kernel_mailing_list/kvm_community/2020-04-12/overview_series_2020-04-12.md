#### [PATCH v2] vdpa: make vhost, virtio depend on menu
##### From: "Michael S. Tsirkin" <mst@redhat.com>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: "Michael S. Tsirkin" <mst@redhat.com>
X-Patchwork-Id: 11484527
Return-Path: <SRS0=AyL5=54=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id AD75D92C
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 12 Apr 2020 12:51:25 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 8849020656
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 12 Apr 2020 12:51:25 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (1024-bit key) header.d=redhat.com header.i=@redhat.com
 header.b="boqULPck"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1727005AbgDLMvB (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sun, 12 Apr 2020 08:51:01 -0400
Received: from us-smtp-delivery-1.mimecast.com ([205.139.110.120]:31573 "EHLO
        us-smtp-1.mimecast.com" rhost-flags-OK-OK-OK-FAIL) by vger.kernel.org
        with ESMTP id S1726043AbgDLMvB (ORCPT <rfc822;kvm@vger.kernel.org>);
        Sun, 12 Apr 2020 08:51:01 -0400
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed; d=redhat.com;
        s=mimecast20190719; t=1586695860;
        h=from:from:reply-to:subject:subject:date:date:message-id:message-id:
         to:to:cc:cc:mime-version:mime-version:content-type:content-type;
        bh=J+P6WFvAHjtNNYxSujhikQ/SLnkXm6oAjgZY+UpbQI8=;
        b=boqULPck+2ZVamI2iBA3qBi3H36o8AJE3Tv1e7Gm+bYamCF0VSvHa2UpHnaiCGxLS1lo0/
        3OZibIUI2yAjCyUqy0TkiL+QAVGQ7zIwv+DTpgT3o8kldkYj5gHgiY99c3v2bWvTN0eSUL
        BF+KudVeERpXvvcVtuDmaU7wTBE/MUQ=
Received: from mail-wr1-f71.google.com (mail-wr1-f71.google.com
 [209.85.221.71]) (Using TLS) by relay.mimecast.com with ESMTP id
 us-mta-12-SiryCZSNOkefZPMtMjnwDg-1; Sun, 12 Apr 2020 08:50:56 -0400
X-MC-Unique: SiryCZSNOkefZPMtMjnwDg-1
Received: by mail-wr1-f71.google.com with SMTP id m5so1811194wru.15
        for <kvm@vger.kernel.org>; Sun, 12 Apr 2020 05:50:56 -0700 (PDT)
X-Google-DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=1e100.net; s=20161025;
        h=x-gm-message-state:date:from:to:cc:subject:message-id:mime-version
         :content-disposition;
        bh=J+P6WFvAHjtNNYxSujhikQ/SLnkXm6oAjgZY+UpbQI8=;
        b=FFkr9mfWIGm/hpE2JcgFhfzKhaCGMdwGVrjJhs16b8FOR49frQy/f2kckXicglcr3/
         4/4U1756SdStZ+Xcijq791RUabZvHz+Jub/9vBi8KcS6LYmM3Y8U/ipRONiw9xrZEDo6
         YfBF0M+MC3LeWHSQxDCwvgmLuyWPdb9TxmT56UjSzE6G68781S6yozjvOGihPwCS2Tbd
         qKofgkSE5BFV3JpTvnWJonlGAYTBJxzSujxF0W0mKSpc1HX1wl1p7KKig/tfQw68pNaa
         YBWmibTl/U4kP2b3dSMobAjbOepguM+K6/42uTKeKZ+PdFyo6INe7F481vILz7zD3gUo
         YMSg==
X-Gm-Message-State: AGi0PubgA4r+hZXI5zzYRkwWI5eadaECEAWgv61qzll/JOqwwLWwTxCs
        N5ZEtNSX/s0Fh2mh8JHLxve41nYKW2+ZtAN/U1MqNmR8E6yhAhX6i2moy6mqH/7JU8E0UFKq5Bw
        X8JjpF1eYZ8c+
X-Received: by 2002:adf:ed01:: with SMTP id a1mr14380004wro.18.1586695855375;
        Sun, 12 Apr 2020 05:50:55 -0700 (PDT)
X-Google-Smtp-Source: 
 APiQypIUhlFj+KOcSj2QR37FIgHBt0IwiNyC4XwCoIuZzbiBL5BykeS8EPIx8I2HyZxUPmIjnjTv0Q==
X-Received: by 2002:adf:ed01:: with SMTP id a1mr14379984wro.18.1586695855098;
        Sun, 12 Apr 2020 05:50:55 -0700 (PDT)
Received: from redhat.com (bzq-79-183-51-3.red.bezeqint.net. [79.183.51.3])
        by smtp.gmail.com with ESMTPSA id
 b11sm10866184wrq.26.2020.04.12.05.50.53
        (version=TLS1_3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256);
        Sun, 12 Apr 2020 05:50:54 -0700 (PDT)
Date: Sun, 12 Apr 2020 08:50:52 -0400
From: "Michael S. Tsirkin" <mst@redhat.com>
To: linux-kernel@vger.kernel.org
Cc: Jason Wang <jasowang@redhat.com>,
        virtualization@lists.linux-foundation.org, kvm@vger.kernel.org,
        netdev@vger.kernel.org
Subject: [PATCH v2] vdpa: make vhost, virtio depend on menu
Message-ID: <20200412125018.74964-1-mst@redhat.com>
MIME-Version: 1.0
Content-Disposition: inline
X-Mailer: git-send-email 2.24.1.751.gd10ce2899c
X-Mutt-Fcc: =sent
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

If user did not configure any vdpa drivers, neither vhost
nor virtio vdpa are going to be useful. So there's no point
in prompting for these and selecting vdpa core automatically.
Simplify configuration by making virtio and vhost vdpa
drivers depend on vdpa menu entry. Once done, we no longer
need a separate menu entry, so also get rid of this.
While at it, fix up the IFC entry: VDPA->vDPA for consistency
with other places.

Signed-off-by: Michael S. Tsirkin <mst@redhat.com>
---

changes from v1:
	fix up virtio vdpa Kconfig

 drivers/vdpa/Kconfig   | 16 +++++-----------
 drivers/vhost/Kconfig  |  2 +-
 drivers/virtio/Kconfig |  2 +-
 3 files changed, 7 insertions(+), 13 deletions(-)

```
