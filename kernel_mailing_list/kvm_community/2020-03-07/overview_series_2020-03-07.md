#### [PATCH] x86/kvm: Disable KVM_ASYNC_PF_SEND_ALWAYS
##### From: Andy Lutomirski <luto@kernel.org>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Andy Lutomirski <luto@kernel.org>
X-Patchwork-Id: 11424979
Return-Path: <SRS0=QkEe=4Y=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id B035D14B4
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Mar 2020 02:16:36 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 8BE302067C
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Mar 2020 02:16:35 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
	s=default; t=1583547396;
	bh=ZIV6z1lwkiwcQzPHzg5HwO+jQAtalo0Uf9NvrIMC3qw=;
	h=From:To:Cc:Subject:Date:List-ID:From;
	b=gKjiGvXzLGTu4bBMV2PQ34OIvX4H1qy30LGllO66/yaCLOm51HXycT7qJA6aELhWz
	 lGTaJW8SENoZ240DFrFicPbHu3Q3QVmHH8r14OCc6Q+MBaYWS4+OKEBjBVbsPEPqbw
	 LHX9OPZbAHk/dua+nRXlt+9nwAjeMSeADVlmudsM=
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726359AbgCGCQc (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Fri, 6 Mar 2020 21:16:32 -0500
Received: from mail.kernel.org ([198.145.29.99]:55656 "EHLO mail.kernel.org"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S1726259AbgCGCQc (ORCPT <rfc822;kvm@vger.kernel.org>);
        Fri, 6 Mar 2020 21:16:32 -0500
Received: from localhost (c-67-180-165-146.hsd1.ca.comcast.net
 [67.180.165.146])
        (using TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits))
        (No client certificate requested)
        by mail.kernel.org (Postfix) with ESMTPSA id E32EA2067C;
        Sat,  7 Mar 2020 02:16:31 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
        s=default; t=1583547392;
        bh=ZIV6z1lwkiwcQzPHzg5HwO+jQAtalo0Uf9NvrIMC3qw=;
        h=From:To:Cc:Subject:Date:From;
        b=m7uWRUdrjOTLRGmZ4uhPFL/Scr9zbtshS1wDjuGwg54H8+1QaeZN5+r+Yh/gV+coV
         qCrIlI0dfVXZ/ma9a/eJalmZQhazh+o/Z5MuBinffJQBH/GNBSgYAfqM01Iaekb3gO
         RtIUVeBjktTdEgO99gcevvp20QlHi7Y9pfzYIFjo=
From: Andy Lutomirski <luto@kernel.org>
To: LKML <linux-kernel@vger.kernel.org>, x86@kernel.org,
        kvm list <kvm@vger.kernel.org>,
        Paolo Bonzini <pbonzini@redhat.com>
Cc: Andy Lutomirski <luto@kernel.org>, stable@vger.kernel.org
Subject: [PATCH] x86/kvm: Disable KVM_ASYNC_PF_SEND_ALWAYS
Date: Fri,  6 Mar 2020 18:16:27 -0800
Message-Id: 
 <25d5c6de22cabf6a4ecb44ce077f33aa5f10b4d4.1583547371.git.luto@kernel.org>
X-Mailer: git-send-email 2.24.1
MIME-Version: 1.0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

The ABI is broken and we cannot support it properly.  Turn it off.

If this causes a meaningful performance regression for someone, KVM
can introduce an improved ABI that is supportable.

Cc: stable@vger.kernel.org
Signed-off-by: Andy Lutomirski <luto@kernel.org>
---
 arch/x86/kernel/kvm.c | 11 ++++++++---
 1 file changed, 8 insertions(+), 3 deletions(-)

```
