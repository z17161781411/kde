#### [PATCH stable 4.9] arm64: entry: Place an SB sequence following an ERET instruction
##### From: Florian Fainelli <f.fainelli@gmail.com>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Florian Fainelli <f.fainelli@gmail.com>
X-Patchwork-Id: 11601245
Return-Path: <SRS0=BPzk=7Z=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 2D52D912
	for <patchwork-kvm@patchwork.kernel.org>;
 Fri, 12 Jun 2020 04:42:51 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 14C2D20838
	for <patchwork-kvm@patchwork.kernel.org>;
 Fri, 12 Jun 2020 04:42:51 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (2048-bit key) header.d=gmail.com header.i=@gmail.com
 header.b="p2J2k390"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726029AbgFLEme (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Fri, 12 Jun 2020 00:42:34 -0400
Received: from lindbergh.monkeyblade.net ([23.128.96.19]:50166 "EHLO
        lindbergh.monkeyblade.net" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S1725763AbgFLEme (ORCPT <rfc822;kvm@vger.kernel.org>);
        Fri, 12 Jun 2020 00:42:34 -0400
Received: from mail-pl1-x641.google.com (mail-pl1-x641.google.com
 [IPv6:2607:f8b0:4864:20::641])
        by lindbergh.monkeyblade.net (Postfix) with ESMTPS id 2B728C03E96F;
        Thu, 11 Jun 2020 21:42:34 -0700 (PDT)
Received: by mail-pl1-x641.google.com with SMTP id g12so3242332pll.10;
        Thu, 11 Jun 2020 21:42:34 -0700 (PDT)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=gmail.com; s=20161025;
        h=from:to:cc:subject:date:message-id;
        bh=tnkNvIy4NyhOcfhzdkEEgf5Q0aBBdpOiKM99Ch2kRHo=;
        b=p2J2k390abBAM4NCbA9yAEM/LrDNElIECJSQNv3Nr8adiQVAS5SzbFY95WgXXxsQIh
         WJkHweRSLgzRw+GhG7AdtIsCCWhBM8XJSKuStAPyg5GzWrP6UfZokkvvSDuCia7LKDe8
         71ujb6m40rMGRbwrRU+cN48oUNWf6r0I2VKCdk3XFkhd7SI1mBjeH2MYM00dZWC5qOCP
         FHXiJVbAQiG/K5Obfn+8x9PhykJbXAlXcTajBOJKJlM1Wemqs6OzfESWAa5E+VfI9uFY
         0/YzlC1zdTftffN4s1iJyIMA8CW79XvwgF/CZ2Um4s/Uxf3elaXGjlbcz4Tn9Hlj3jV0
         d2tg==
X-Google-DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=1e100.net; s=20161025;
        h=x-gm-message-state:from:to:cc:subject:date:message-id;
        bh=tnkNvIy4NyhOcfhzdkEEgf5Q0aBBdpOiKM99Ch2kRHo=;
        b=jnbxLZSaU7SwEo9AWESk+sdWpRd0acuQbNxVGlvA178Yr3FThVn9nxmrSnfij3jgYh
         AbDNn0ylgjdUqtmpRWt3ncPcSIU8t5D/y/fZ2E2em3n82W7QIK5DE+ZgwsXvsTzjbf3q
         WTjPi+44VD5V1UhCsBJUdD2SsfdNJQ/Tj/iCAstzTsCbNw5pEZS0rJMQ5aQ9XawhEVbM
         Wm44JjZi4/SOq6RotGy4vZR2CTYHJ3YxpvnqHo92ijzOIvkRA/ZQr12CjDc56u8qLN21
         bzjToWMY8SN2aLQ6+QRSBx3j6Ee2pWygpbaLPBtTEI4uNxocvoB6Bug2rjD2LnGJQyPM
         Y2ug==
X-Gm-Message-State: AOAM530n+c/lTU6DkdV0kw91aOr8y6QHbomFPES85YR/QxpC6TsRrqhu
        LWugph0OAvPpHEYWa1g7HRo=
X-Google-Smtp-Source: 
 ABdhPJwVfVwWuvBqsmyJQUwzIkKhTVDfAOSa0G75EXUmTNsmKXeQmrkCijvdn4svfC9O7GaAMKn6pA==
X-Received: by 2002:a17:902:c30c:: with SMTP id
 k12mr10389411plx.130.1591936953486;
        Thu, 11 Jun 2020 21:42:33 -0700 (PDT)
Received: from localhost.localdomain ([192.19.223.252])
        by smtp.gmail.com with ESMTPSA id
 s9sm4084239pgo.22.2020.06.11.21.42.31
        (version=TLS1_3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256);
        Thu, 11 Jun 2020 21:42:32 -0700 (PDT)
From: Florian Fainelli <f.fainelli@gmail.com>
To: linux-arm-kernel@lists.infradead.org
Cc: stable@vger.kernel.org, will@kernel.org,
 Will Deacon <will.deacon@arm.com>, Florian Fainelli <f.fainelli@gmail.com>,
 Catalin Marinas <catalin.marinas@arm.com>,
 Paolo Bonzini <pbonzini@redhat.com>,
 =?utf-8?b?UmFkaW0gS3LEjW3DocWZ?= <rkrcmar@redhat.com>,
 Christoffer Dall <christoffer.dall@linaro.org>,
 Marc Zyngier <marc.zyngier@arm.com>,
 linux-kernel@vger.kernel.org (open list),
 kvm@vger.kernel.org (open list:KERNEL VIRTUAL MACHINE (KVM)),
 kvmarm@lists.cs.columbia.edu (open list:KERNEL VIRTUAL MACHINE FOR ARM64
 (KVM/arm64))
Subject: [PATCH stable 4.9] arm64: entry: Place an SB sequence following an
 ERET instruction
Date: Thu, 11 Jun 2020 21:42:18 -0700
Message-Id: <20200612044219.31606-1-f.fainelli@gmail.com>
X-Mailer: git-send-email 2.17.1
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

From: Will Deacon <will.deacon@arm.com>

commit 679db70801da9fda91d26caf13bf5b5ccc74e8e8 upstream

Some CPUs can speculate past an ERET instruction and potentially perform
speculative accesses to memory before processing the exception return.
Since the register state is often controlled by a lower privilege level
at the point of an ERET, this could potentially be used as part of a
side-channel attack.

This patch emits an SB sequence after each ERET so that speculation is
held up on exception return.

Signed-off-by: Will Deacon <will.deacon@arm.com>
[florian: Adjust hyp-entry.S to account for the label]
Signed-off-by: Florian Fainelli <f.fainelli@gmail.com>
---
Will,

Can you confirm that for 4.9 these are the only places that require
patching? Thank you!

 arch/arm64/kernel/entry.S      | 2 ++
 arch/arm64/kvm/hyp/entry.S     | 1 +
 arch/arm64/kvm/hyp/hyp-entry.S | 4 ++++
 3 files changed, 7 insertions(+)

```
