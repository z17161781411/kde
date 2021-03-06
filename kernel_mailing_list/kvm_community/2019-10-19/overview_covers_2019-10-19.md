

#### [PATCH v2 0/5] arm64: KVM: Add workaround for errata 1319367 and
##### From: Marc Zyngier <maz@kernel.org>

```c
From patchwork Sat Oct 19 09:55:16 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Marc Zyngier <maz@kernel.org>
X-Patchwork-Id: 11200055
Return-Path: <SRS0=mHfo=YM=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 67F9D112B
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat, 19 Oct 2019 09:55:48 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 460C7222BD
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat, 19 Oct 2019 09:55:48 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
	s=default; t=1571478948;
	bh=N9Z0vUut0qPCNXvusFnCmmykyf3NqqvywC9U3wgaJ8A=;
	h=From:To:Cc:Subject:Date:List-ID:From;
	b=HemEEhtaH8QPaouHkLLKk988U2QIZrE5JHQnH3COMBi0fmw/173y4+eJ+EGTPndgc
	 nx9Ad7yI9aj/OyW3ad7QShmSqL0Ed6qTjpCxKPw7rdLzfclzryq5NnrYzwWT4SDi4x
	 fzaSLHF3uZzjg6AqwZfAC7ZnlB8dJeaWJlzB9vs0=
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1725777AbfJSJz2 (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sat, 19 Oct 2019 05:55:28 -0400
Received: from mail.kernel.org ([198.145.29.99]:53412 "EHLO mail.kernel.org"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S1725283AbfJSJz1 (ORCPT <rfc822;kvm@vger.kernel.org>);
        Sat, 19 Oct 2019 05:55:27 -0400
Received: from big-swifty.lan (78.163-31-62.static.virginmediabusiness.co.uk
 [62.31.163.78])
        (using TLSv1.2 with cipher ECDHE-RSA-AES128-GCM-SHA256 (128/128 bits))
        (No client certificate requested)
        by mail.kernel.org (Postfix) with ESMTPSA id E2AD2222BD;
        Sat, 19 Oct 2019 09:55:24 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
        s=default; t=1571478927;
        bh=N9Z0vUut0qPCNXvusFnCmmykyf3NqqvywC9U3wgaJ8A=;
        h=From:To:Cc:Subject:Date:From;
        b=iwRQbDJLsLS02h0MbcugXlJQkxWnxa+MGT0Kw2YwxaVrq6WI/99bX9SeBccY9+F63
         gM0+5fS5rfMhmh6ovbTkM6ZowJrola1sXlMN1n9ORzu8AJI+FJNp2K0LPv4PSb+puR
         IQSOeby8p0kdiBV3pTVtGYUzsVm6mw2sxJJ48iaM=
From: Marc Zyngier <maz@kernel.org>
To: linux-arm-kernel@lists.infradead.org, kvmarm@lists.cs.columbia.edu,
        kvm@vger.kernel.org
Cc: Mark Rutland <mark.rutland@arm.com>,
        Suzuki K Poulose <suzuki.poulose@arm.com>,
        Catalin Marinas <catalin.marinas@arm.com>,
        James Morse <james.morse@arm.com>,
        Will Deacon <will@kernel.org>,
        Julien Thierry <julien.thierry.kdev@gmail.com>
Subject: [PATCH v2 0/5] arm64: KVM: Add workaround for errata 1319367 and
 1319537
Date: Sat, 19 Oct 2019 10:55:16 +0100
Message-Id: <20191019095521.31722-1-maz@kernel.org>
X-Mailer: git-send-email 2.20.1
MIME-Version: 1.0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Similarily to erratum 1165522 that affects Cortex-A76, our good old
friends A57 and A72 respectively suffer from errata 1319537 and
1319367, potentially resulting in TLB corruption if the CPU speculates
an AT instruction while switching guests.

The fix is slightly more involved since we don't have VHE to help us
here, but the idea is the same: When switching a guest in, we must
prevent any speculated AT from being able to parse the page tables
until S2 is up and running. Only at this stage can we allow AT to take
place.

For this, we always restore the guest sysregs first, except for its
SCTLR and TCR registers, which must be set with SCTLR.M=1 and
TCR.EPD{0,1} = {1, 1}, effectively disabling the PTW and TLB
allocation. Once S2 is setup, we restore the guest's SCTLR and
TCR. Similar things must be done on TLB invalidation... Fun.

This has been tested on an AMD Seattle box.

* From v1 [1]:
  - Reworked patch 4 to close the speculation window on the host
  - Fixed comments
  - Collected ABs/RBs

[1] https://lore.kernel.org/kvmarm/20190925111941.88103-1-maz@kernel.org/

Marc Zyngier (5):
  arm64: Add ARM64_WORKAROUND_1319367 for all A57 and A72 versions
  arm64: KVM: Reorder system register restoration and stage-2 activation
  arm64: KVM: Disable EL1 PTW when invalidating S2 TLBs
  arm64: KVM: Prevent speculative S1 PTW when restoring vcpu context
  arm64: Enable and document ARM errata 1319367 and 1319537

 Documentation/arm64/silicon-errata.rst |  4 +++
 arch/arm64/Kconfig                     | 10 ++++++
 arch/arm64/include/asm/cpucaps.h       |  3 +-
 arch/arm64/kernel/cpu_errata.c         | 13 +++++--
 arch/arm64/kvm/hyp/switch.c            | 48 ++++++++++++++++++++++----
 arch/arm64/kvm/hyp/sysreg-sr.c         | 35 +++++++++++++++++--
 arch/arm64/kvm/hyp/tlb.c               | 23 ++++++++++++
 7 files changed, 124 insertions(+), 12 deletions(-)
```
