#### [PATCH][resend] KVM: fix error handling in svm_hardware_setup
##### From: Li RongQing <lirongqing@baidu.com>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Li RongQing <lirongqing@baidu.com>
X-Patchwork-Id: 11398713
Return-Path: <SRS0=+npl=4L=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 3F60E138D
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 23 Feb 2020 08:13:32 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 29099217F4
	for <patchwork-kvm@patchwork.kernel.org>;
 Sun, 23 Feb 2020 08:13:32 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1726650AbgBWIN0 (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sun, 23 Feb 2020 03:13:26 -0500
Received: from mx59.baidu.com ([61.135.168.59]:25356 "EHLO
        tc-sys-mailedm02.tc.baidu.com" rhost-flags-OK-OK-OK-FAIL)
        by vger.kernel.org with ESMTP id S1726386AbgBWIN0 (ORCPT
        <rfc822;kvm@vger.kernel.org>); Sun, 23 Feb 2020 03:13:26 -0500
Received: from localhost (cp01-cos-dev01.cp01.baidu.com [10.92.119.46])
        by tc-sys-mailedm02.tc.baidu.com (Postfix) with ESMTP id 82DE111C0034;
        Sun, 23 Feb 2020 16:13:12 +0800 (CST)
From: Li RongQing <lirongqing@baidu.com>
To: pbonzini@redhat.com, sean.j.christopherson@intel.com,
        vkuznets@redhat.com, wanpengli@tencent.com, x86@kernel.org,
        kvm@vger.kernel.org, linux-kernel@vger.kernel.org
Subject: [PATCH][resend] KVM: fix error handling in svm_hardware_setup
Date: Sun, 23 Feb 2020 16:13:12 +0800
Message-Id: <1582445592-732-1-git-send-email-lirongqing@baidu.com>
X-Mailer: git-send-email 1.7.1
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

rename svm_hardware_unsetup as svm_hardware_teardown, move
it before svm_hardware_setup, and call it to free all memory
if fail to setup in svm_hardware_setup, otherwise memory will
be leaked

remove __exit attribute for it since it is called in __init
function

Signed-off-by: Li RongQing <lirongqing@baidu.com>
Reviewed-by: Vitaly Kuznetsov <vkuznets@redhat.com>
---
original patch: https://patchwork.kernel.org/patch/10852055/ 


 arch/x86/kvm/svm.c | 41 ++++++++++++++++++++---------------------
 1 file changed, 20 insertions(+), 21 deletions(-)

```
