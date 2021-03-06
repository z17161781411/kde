#### linux-next: manual merge of the kvm tree with Linus' tree
##### From: Stephen Rothwell <sfr@canb.auug.org.au>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Stephen Rothwell <sfr@canb.auug.org.au>
X-Patchwork-Id: 11469791
Return-Path: <SRS0=vumV=5S=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 8674292C
	for <patchwork-kvm@patchwork.kernel.org>;
 Thu,  2 Apr 2020 02:36:48 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 5037F2077D
	for <patchwork-kvm@patchwork.kernel.org>;
 Thu,  2 Apr 2020 02:36:48 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (2048-bit key) header.d=canb.auug.org.au header.i=@canb.auug.org.au
 header.b="qP5YLDMi"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1733308AbgDBCgp (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Wed, 1 Apr 2020 22:36:45 -0400
Received: from ozlabs.org ([203.11.71.1]:42495 "EHLO ozlabs.org"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S1727135AbgDBCgp (ORCPT <rfc822;kvm@vger.kernel.org>);
        Wed, 1 Apr 2020 22:36:45 -0400
Received: from authenticated.ozlabs.org (localhost [127.0.0.1])
        (using TLSv1.3 with cipher TLS_AES_256_GCM_SHA384 (256/256 bits)
         key-exchange ECDHE (P-256) server-signature RSA-PSS (4096 bits)
 server-digest SHA256)
        (No client certificate requested)
        by mail.ozlabs.org (Postfix) with ESMTPSA id 48t6dh5T7Wz9sQt;
        Thu,  2 Apr 2020 13:36:40 +1100 (AEDT)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=canb.auug.org.au;
        s=201702; t=1585795001;
        bh=gvH+b1yhEEXi4hltTGU/dEz55S7XWG8/Z8nwvw29J6k=;
        h=Date:From:To:Cc:Subject:From;
        b=qP5YLDMi5e9RXEU/t6f9G9TVheuDFnomV9xxTQQcBowK32uTfPJu8lcLRqd2KKdkX
         sFju3AKcIE8dsbf8FhrloqwDy/4HV7y73ngTmlJLH0Eut2D7ZKCyf7ptt0opm4Nzqs
         6wYcJQ8eW8kVySShQjpr3bw2C50c4jfKd3D83rvyhXjrU5OYHyGK4nlE8/1QOs3HNy
         M8eBLH3mh1T6dMs6dXe4Andd8m3n5ejr5rM9Aaex50nAZn4zed320InZbShS+Qqp5P
         pi/MidFcupZUhmjTlLYLMlzEepmEjG9adkrEHdMy7OkqOYOzIRCqygm9EZHYniyLeH
         OAJL9LepOtepA==
Date: Thu, 2 Apr 2020 13:36:37 +1100
From: Stephen Rothwell <sfr@canb.auug.org.au>
To: Paolo Bonzini <pbonzini@redhat.com>, KVM <kvm@vger.kernel.org>
Cc: Linux Next Mailing List <linux-next@vger.kernel.org>,
        Linux Kernel Mailing List <linux-kernel@vger.kernel.org>,
        Haiwei Li <lihaiwei.kernel@gmail.com>,
        Tom Lendacky <thomas.lendacky@amd.com>,
        Joerg Roedel <jroedel@suse.de>
Subject: linux-next: manual merge of the kvm tree with Linus' tree
Message-ID: <20200402133637.296e70a9@canb.auug.org.au>
MIME-Version: 1.0
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Hi all,

Today's linux-next merge of the kvm tree got a conflict in:

  arch/x86/kvm/svm/svm.c

between commits:

  aaca21007ba1 ("KVM: SVM: Fix the svm vmexit code for WRMSR")
  2da1ed62d55c ("KVM: SVM: document KVM_MEM_ENCRYPT_OP, let userspace detect if SEV is available")
  2e2409afe5f0 ("KVM: SVM: Issue WBINVD after deactivating an SEV guest")

from Linus' tree and commits:

  83a2c705f002 ("kVM SVM: Move SVM related files to own sub-directory")
  41f08f0506c0 ("KVM: SVM: Move SEV code to separate file")

(at least)

from the kvm tree.

Its a bit of a pain this code movement appearing during the merge
window.  Is it really intended for v5.7?

I fixed it up (see below) and can carry the fix as necessary. This
is now fixed as far as linux-next is concerned, but any non trivial
conflicts should be mentioned to your upstream maintainer when your tree
is submitted for merging.  You may also want to consider cooperating
with the maintainer of the conflicting tree to minimise any particularly
complex conflicts.

```
