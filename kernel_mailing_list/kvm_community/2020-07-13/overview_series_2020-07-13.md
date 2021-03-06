#### linux-next: manual merge of the kvm-arm tree with the kvm tree
##### From: Stephen Rothwell <sfr@canb.auug.org.au>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Stephen Rothwell <sfr@canb.auug.org.au>
X-Patchwork-Id: 11658679
Return-Path: <SRS0=/1XX=AY=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 8E45A13B4
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 13 Jul 2020 04:39:52 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 6A3132068F
	for <patchwork-kvm@patchwork.kernel.org>;
 Mon, 13 Jul 2020 04:39:52 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (2048-bit key) header.d=canb.auug.org.au header.i=@canb.auug.org.au
 header.b="P3rtYUhc"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1727107AbgGMEjm (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Mon, 13 Jul 2020 00:39:42 -0400
Received: from lindbergh.monkeyblade.net ([23.128.96.19]:49798 "EHLO
        lindbergh.monkeyblade.net" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S1725804AbgGMEjm (ORCPT <rfc822;kvm@vger.kernel.org>);
        Mon, 13 Jul 2020 00:39:42 -0400
Received: from ozlabs.org (ozlabs.org [IPv6:2401:3900:2:1::2])
        by lindbergh.monkeyblade.net (Postfix) with ESMTPS id B060AC061794;
        Sun, 12 Jul 2020 21:39:41 -0700 (PDT)
Received: from authenticated.ozlabs.org (localhost [127.0.0.1])
        (using TLSv1.3 with cipher TLS_AES_256_GCM_SHA384 (256/256 bits)
         key-exchange ECDHE (P-256) server-signature RSA-PSS (4096 bits)
 server-digest SHA256)
        (No client certificate requested)
        by mail.ozlabs.org (Postfix) with ESMTPSA id 4B4rXS3ndgz9sDX;
        Mon, 13 Jul 2020 14:39:35 +1000 (AEST)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=canb.auug.org.au;
        s=201702; t=1594615178;
        bh=v/MgjPrqiPe4ZuBU4tfWPEezuWjdEPIyi5/ryEXFJ20=;
        h=Date:From:To:Cc:Subject:From;
        b=P3rtYUhcPvPZOTsyIvefv/IuitUkCrDUyk1g8GlwotOhWW2079csyInalZwUVs7om
         gmGUnTAdAyTazMrmdM0SJvBPXkpFvKucxP7C87o4Uc6zzeIk3zbbh/icfTRtHoibq3
         DIn6xyZbCO/MuZN6DGc5qXSuMn0qVc2pzYeM8R7tMV+xwg8+IbyqD/jURj6E5r70yJ
         qfoLc/yoP/c7S4+KB2oTgDFG6uan1oOHaz5fNIIqJmarI2QakEoqBDLLXH8t13AbpF
         PPt3x1dHZzG4YIIIFqyE04kGLgPB//CdxOwmnF8EZksd1JI3QU1eYjtPbGudRLXC3X
         uSoN4QCd1HZCQ==
Date: Mon, 13 Jul 2020 14:39:35 +1000
From: Stephen Rothwell <sfr@canb.auug.org.au>
To: Christoffer Dall <cdall@cs.columbia.edu>,
        Marc Zyngier <maz@kernel.org>,
        Paolo Bonzini <pbonzini@redhat.com>, KVM <kvm@vger.kernel.org>
Cc: Linux Next Mailing List <linux-next@vger.kernel.org>,
        Linux Kernel Mailing List <linux-kernel@vger.kernel.org>,
        Tianjia Zhang <tianjia.zhang@linux.alibaba.com>,
        James Morse <james.morse@arm.com>,
        Gavin Shan <gshan@redhat.com>
Subject: linux-next: manual merge of the kvm-arm tree with the kvm tree
Message-ID: <20200713143935.799861b9@canb.auug.org.au>
MIME-Version: 1.0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Hi all,

Today's linux-next merge of the kvm-arm tree got conflicts in:

  arch/arm64/include/asm/kvm_coproc.h
  arch/arm64/kvm/handle_exit.c

between commit:

  74cc7e0c35c1 ("KVM: arm64: clean up redundant 'kvm_run' parameters")

from the kvm tree and commits:

  6b33e0d64f85 ("KVM: arm64: Drop the target_table[] indirection")
  750ed5669380 ("KVM: arm64: Remove the target table")
  3a949f4c9354 ("KVM: arm64: Rename HSR to ESR")

from the kvm-arm tree.

I fixed it up (see below) and can carry the fix as necessary. This
is now fixed as far as linux-next is concerned, but any non trivial
conflicts should be mentioned to your upstream maintainer when your tree
is submitted for merging.  You may also want to consider cooperating
with the maintainer of the conflicting tree to minimise any particularly
complex conflicts.

```
