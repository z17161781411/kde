

#### [PATCH v4 0/4] Retrieving zPCI specific info with VFIO
##### From: Matthew Rosato <mjrosato@linux.ibm.com>

```c
From patchwork Sat Sep  7 00:13:47 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Matthew Rosato <mjrosato@linux.ibm.com>
X-Patchwork-Id: 11136165
Return-Path: <SRS0=kw8a=XC=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 51F1B112B
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Sep 2019 00:14:30 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id 3F61121835
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Sep 2019 00:14:30 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S2393379AbfIGAOG (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Fri, 6 Sep 2019 20:14:06 -0400
Received: from mx0b-001b2d01.pphosted.com ([148.163.158.5]:6366 "EHLO
        mx0a-001b2d01.pphosted.com" rhost-flags-OK-OK-OK-FAIL)
        by vger.kernel.org with ESMTP id S1733029AbfIGAOF (ORCPT
        <rfc822;kvm@vger.kernel.org>); Fri, 6 Sep 2019 20:14:05 -0400
Received: from pps.filterd (m0098419.ppops.net [127.0.0.1])
        by mx0b-001b2d01.pphosted.com (8.16.0.27/8.16.0.27) with SMTP id
 x8707cvU014844;
        Fri, 6 Sep 2019 20:13:57 -0400
Received: from pps.reinject (localhost [127.0.0.1])
        by mx0b-001b2d01.pphosted.com with ESMTP id 2uv1jrrden-1
        (version=TLSv1.2 cipher=ECDHE-RSA-AES256-GCM-SHA384 bits=256
 verify=NOT);
        Fri, 06 Sep 2019 20:13:57 -0400
Received: from m0098419.ppops.net (m0098419.ppops.net [127.0.0.1])
        by pps.reinject (8.16.0.27/8.16.0.27) with SMTP id x87092bj017643;
        Fri, 6 Sep 2019 20:13:56 -0400
Received: from ppma04dal.us.ibm.com (7a.29.35a9.ip4.static.sl-reverse.com
 [169.53.41.122])
        by mx0b-001b2d01.pphosted.com with ESMTP id 2uv1jrrdee-1
        (version=TLSv1.2 cipher=ECDHE-RSA-AES256-GCM-SHA384 bits=256
 verify=NOT);
        Fri, 06 Sep 2019 20:13:56 -0400
Received: from pps.filterd (ppma04dal.us.ibm.com [127.0.0.1])
        by ppma04dal.us.ibm.com (8.16.0.27/8.16.0.27) with SMTP id
 x8709OHn006177;
        Sat, 7 Sep 2019 00:13:56 GMT
Received: from b01cxnp23033.gho.pok.ibm.com (b01cxnp23033.gho.pok.ibm.com
 [9.57.198.28])
        by ppma04dal.us.ibm.com with ESMTP id 2uqgh7t8ya-1
        (version=TLSv1.2 cipher=ECDHE-RSA-AES256-GCM-SHA384 bits=256
 verify=NOT);
        Sat, 07 Sep 2019 00:13:56 +0000
Received: from b01ledav002.gho.pok.ibm.com (b01ledav002.gho.pok.ibm.com
 [9.57.199.107])
        by b01cxnp23033.gho.pok.ibm.com (8.14.9/8.14.9/NCO v10.0) with ESMTP
 id x870Drx652887816
        (version=TLSv1/SSLv3 cipher=DHE-RSA-AES256-GCM-SHA384 bits=256
 verify=OK);
        Sat, 7 Sep 2019 00:13:53 GMT
Received: from b01ledav002.gho.pok.ibm.com (unknown [127.0.0.1])
        by IMSVA (Postfix) with ESMTP id 427A8124053;
        Sat,  7 Sep 2019 00:13:53 +0000 (GMT)
Received: from b01ledav002.gho.pok.ibm.com (unknown [127.0.0.1])
        by IMSVA (Postfix) with ESMTP id B21D5124052;
        Sat,  7 Sep 2019 00:13:52 +0000 (GMT)
Received: from oc4221205838.ibm.com (unknown [9.85.134.207])
        by b01ledav002.gho.pok.ibm.com (Postfix) with ESMTP;
        Sat,  7 Sep 2019 00:13:52 +0000 (GMT)
From: Matthew Rosato <mjrosato@linux.ibm.com>
To: sebott@linux.ibm.com
Cc: gerald.schaefer@de.ibm.com, pasic@linux.ibm.com,
        borntraeger@de.ibm.com, walling@linux.ibm.com,
        linux-s390@vger.kernel.org, iommu@lists.linux-foundation.org,
        joro@8bytes.org, linux-kernel@vger.kernel.org,
        alex.williamson@redhat.com, kvm@vger.kernel.org,
        heiko.carstens@de.ibm.com, robin.murphy@arm.com, gor@linux.ibm.com,
        cohuck@redhat.com, pmorel@linux.ibm.com
Subject: [PATCH v4 0/4] Retrieving zPCI specific info with VFIO
Date: Fri,  6 Sep 2019 20:13:47 -0400
Message-Id: <1567815231-17940-1-git-send-email-mjrosato@linux.ibm.com>
X-Mailer: git-send-email 1.8.3.1
X-TM-AS-GCONF: 00
X-Proofpoint-Virus-Version: vendor=fsecure engine=2.50.10434:,,
 definitions=2019-09-06_11:,,
 signatures=0
X-Proofpoint-Spam-Details: rule=outbound_notspam policy=outbound score=0
 priorityscore=1501
 malwarescore=0 suspectscore=1 phishscore=0 bulkscore=0 spamscore=0
 clxscore=1011 lowpriorityscore=0 mlxscore=0 impostorscore=0
 mlxlogscore=999 adultscore=0 classifier=spam adjust=0 reason=mlx
 scancount=1 engine=8.0.1-1906280000 definitions=main-1909070000
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

Note: These patches by Pierre got lost in the ether a few months back
as he has been unavailable to carry them forward.  I've made changes
based upon comments received on his last version.

We define a new configuration entry for VFIO/PCI, VFIO_PCI_ZDEV
to configure access to a zPCI region dedicated for retrieving
zPCI features.

When the VFIO_PCI_ZDEV feature is configured we initialize
a new device region, VFIO_REGION_SUBTYPE_ZDEV_CLP, to hold
the information from the ZPCI device the userland needs to
give to a guest driving the zPCI function.


Note that in the current state we do not use the CLP instructions
to access the firmware but get the information directly from
the zdev device.

-This means that the patch 1, "s390: pci: Exporting access to CLP PCI
function and PCI group" is not used and can be let out of this series
without denying the good working of the other patches.
- But we will need this later, eventually in the next iteration
  to retrieve values not being saved inside the zdev structure.
  like maxstbl and the PCI supported version

To share the code with arch/s390/pci/pci_clp.c the original functions
in pci_clp.c to query PCI functions and PCI functions group are
modified so that they can be exported.

A new function clp_query_pci() replaces clp_query_pci_fn() and
the previous calls to clp_query_pci_fn() and clp_query_pci_fngrp()
are replaced with calls to zdev_query_pci_fn() and zdev_query_pci_fngrp()
using a zdev pointer as argument.

Changes since v3:
- New patch: define maxstbl
- Remove CLP_UTIL_STR_LEN references from uapi header
- Fix broken ifdef CONFIG_VFIO_PCI_ZDEV
- Change Kconfig option from tristate to bool
- Remove VFIO_REGION_TYPE_IBM_ZDEV, move VFIO_REGION_SUBTYPE_ZDEV_CLP to a 1014 subtype
- reject iswrite in .rw callback
- Remove rw restriction on identical buffer sizes
- Allow arbitrary sized read

Pierre Morel (4):
  s390: pci: Exporting access to CLP PCI function and PCI group
  s390: pci: Define the maxstbl CLP response entry
  vfio: zpci: defining the VFIO headers
  vfio: pci: Using a device region to retrieve zPCI information

 arch/s390/include/asm/pci.h         |  3 ++
 arch/s390/include/asm/pci_clp.h     |  2 +-
 arch/s390/pci/pci_clp.c             | 71 ++++++++++++++++---------------
 drivers/vfio/pci/Kconfig            |  7 +++
 drivers/vfio/pci/Makefile           |  1 +
 drivers/vfio/pci/vfio_pci.c         |  9 ++++
 drivers/vfio/pci/vfio_pci_private.h | 10 +++++
 drivers/vfio/pci/vfio_pci_zdev.c    | 85 +++++++++++++++++++++++++++++++++++++
 include/uapi/linux/vfio.h           |  1 +
 include/uapi/linux/vfio_zdev.h      | 35 +++++++++++++++
 10 files changed, 189 insertions(+), 35 deletions(-)
 create mode 100644 drivers/vfio/pci/vfio_pci_zdev.c
 create mode 100644 include/uapi/linux/vfio_zdev.h
```
#### [PATCH v9 0/8] stg mail -e --version=v9 \
##### From: Alexander Duyck <alexander.duyck@gmail.com>

```c
From patchwork Sat Sep  7 17:25:03 2019
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Alexander Duyck <alexander.duyck@gmail.com>
X-Patchwork-Id: 11136573
Return-Path: <SRS0=kw8a=XC=vger.kernel.org=kvm-owner@kernel.org>
Received: from mail.kernel.org (pdx-korg-mail-1.web.codeaurora.org
 [172.30.200.123])
	by pdx-korg-patchwork-2.web.codeaurora.org (Postfix) with ESMTP id 11FA413BD
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Sep 2019 17:25:23 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [209.132.180.67])
	by mail.kernel.org (Postfix) with ESMTP id D2D86208C3
	for <patchwork-kvm@patchwork.kernel.org>;
 Sat,  7 Sep 2019 17:25:22 +0000 (UTC)
Authentication-Results: mail.kernel.org;
	dkim=pass (2048-bit key) header.d=gmail.com header.i=@gmail.com
 header.b="YtTqzaCi"
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S2392762AbfIGRZJ (ORCPT
        <rfc822;patchwork-kvm@patchwork.kernel.org>);
        Sat, 7 Sep 2019 13:25:09 -0400
Received: from mail-ot1-f66.google.com ([209.85.210.66]:46665 "EHLO
        mail-ot1-f66.google.com" rhost-flags-OK-OK-OK-OK) by vger.kernel.org
        with ESMTP id S2392548AbfIGRZJ (ORCPT <rfc822;kvm@vger.kernel.org>);
        Sat, 7 Sep 2019 13:25:09 -0400
Received: by mail-ot1-f66.google.com with SMTP id g19so8692382otg.13;
        Sat, 07 Sep 2019 10:25:07 -0700 (PDT)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=gmail.com; s=20161025;
        h=subject:from:to:cc:date:message-id:user-agent:mime-version
         :content-transfer-encoding;
        bh=nmJ4QApqKumTefv4gXq6pnqSc2Kc/ikAYZqp8DGK3N4=;
        b=YtTqzaCiUHQIVnbRjPBszW3/10Mn/AqzL88XURJ5PO8BnbHE3EkUoHSA7/nMPk5/OH
         a3uJ6tsC6s7uLk0zHFxJcA3pbEhWnmU3EV6s2l1Kv3a4yFZ5jDdqeyylD8LTKOSSfers
         on4+Xuj15/t5AGN9HrxcVWCConMGOwBmyvn8DDzS38yySzBl9afM2fQjnCzEBaGnswgD
         Lncq8Z/jApgGJmvGK9gzFhn/vsJuAhe+KsFSWQLzEdJ5A2JK7jU6JdW+HED8m5Kwsrta
         dt1lwghS3cFwYEewdRoOKMLYj5oFB00uXvOXGGS3Vg/vN4bWVHyCx60k3Jfv+1CEBQJs
         EHWg==
X-Google-DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=1e100.net; s=20161025;
        h=x-gm-message-state:subject:from:to:cc:date:message-id:user-agent
         :mime-version:content-transfer-encoding;
        bh=nmJ4QApqKumTefv4gXq6pnqSc2Kc/ikAYZqp8DGK3N4=;
        b=AWNamMsmuOv86nF847F7qyYPL26I7YUTi0w4lkdRiFeLKSum7gKzr2SjuOIDlnpsv6
         +asiqHUPZR7H/INkHHRFIZsUph0/zB5YFy6Wloeg19bvt9hSQW5H8Uz4NhC8LAPJOaFz
         8HLS6TBm/QF6m2ncVkdt/9WslkJijOBKZMrCc2VPzIKtzMgg6W1Q3Tze6fEr4O2qPjFv
         +K9ty39/0+GS29qzM3KlIXDG+9EcTCznxkoVgmaZP/PRJ7WifvAG0cfYy6wFJGyjlkfb
         GoBjHOAALdzf1ykD4eQD7EPjQDIDKD8Z8Vm/5iy8l9hBmn8Jwh5gCm065NtmPwxxv1Fz
         g98Q==
X-Gm-Message-State: APjAAAWw6wx3n5+WtZ0AYCoOGLAldCzCvcIed3s0sOCzEYU414c8aNTA
        7j72NAXWCjI4yIoLsvhdcsg=
X-Google-Smtp-Source: 
 APXvYqxK7vsL9wNIVZcn9ZNwkVVeV3ZePDP9KJt3S74KpNdPwb3bMyMqpcrRsx3uH7Gz+vbk7nSDkg==
X-Received: by 2002:a9d:62c4:: with SMTP id z4mr11739662otk.305.1567877107345;
        Sat, 07 Sep 2019 10:25:07 -0700 (PDT)
Received: from localhost.localdomain ([2001:470:b:9c3:9e5c:8eff:fe4f:f2d0])
        by smtp.gmail.com with ESMTPSA id
 i10sm3180368otp.80.2019.09.07.10.25.04
        (version=TLS1_2 cipher=ECDHE-RSA-AES128-GCM-SHA256 bits=128/128);
        Sat, 07 Sep 2019 10:25:06 -0700 (PDT)
Subject: [PATCH v9 0/8] stg mail -e --version=v9 \
From: Alexander Duyck <alexander.duyck@gmail.com>
To: virtio-dev@lists.oasis-open.org, kvm@vger.kernel.org,
        mst@redhat.com, catalin.marinas@arm.com, david@redhat.com,
        dave.hansen@intel.com, linux-kernel@vger.kernel.org,
        willy@infradead.org, mhocko@kernel.org, linux-mm@kvack.org,
        akpm@linux-foundation.org, will@kernel.org,
        linux-arm-kernel@lists.infradead.org, osalvador@suse.de
Cc: yang.zhang.wz@gmail.com, pagupta@redhat.com,
        konrad.wilk@oracle.com, nitesh@redhat.com, riel@surriel.com,
        lcapitulino@redhat.com, wei.w.wang@intel.com, aarcange@redhat.com,
        ying.huang@intel.com, pbonzini@redhat.com,
        dan.j.williams@intel.com, fengguang.wu@intel.com,
        alexander.h.duyck@linux.intel.com, kirill.shutemov@linux.intel.com
Date: Sat, 07 Sep 2019 10:25:03 -0700
Message-ID: <20190907172225.10910.34302.stgit@localhost.localdomain>
User-Agent: StGit/0.17.1-dirty
MIME-Version: 1.0
Content-Type: text/plain; charset="utf-8"
Sender: kvm-owner@vger.kernel.org
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

--to=kvm@vger.kernel.org \
--to=linux-kernel@vger.kernel.org \
--to=linux-mm@kvack.org \
--to=virtio-dev@lists.oasis-open.org \
--to=linux-arm-kernel@lists.infradead.org \
--to=mst@redhat.com \
--to=david@redhat.com \
--to=dave.hansen@intel.com \
--to=akpm@linux-foundation.org \
--cc=nitesh@redhat.com \
--cc=dan.j.williams@intel.com \
--cc=pbonzini@redhat.com \
--cc=lcapitulino@redhat.com \
--cc=pagupta@redhat.com \
--cc=wei.w.wang@intel.com \
--cc=yang.zhang.wz@gmail.com \
--cc=riel@surriel.com \
--cc=konrad.wilk@oracle.com \
--cc=aarcange@redhat.com \
--to=willy@infradead.org \
--to=mhocko@kernel.org \
--to=osalvador@suse.de \
--to=catalin.marinas@arm.com \
--to=will@kernel.org \
--cc=kirill.shutemov@linux.intel.com  \
--cc=fengguang.wu@intel.com \
--cc=ying.huang@intel.com \
--cc=alexander.h.duyck@linux.intel.com \
01-mm-add-per-cpu-logic-to-page..08-virtio-balloon-add-support-for

mm / virtio: Provide support for unused page reporting

This series provides an asynchronous means of reporting to a hypervisor
that a guest page is no longer in use and can have the data associated
with it dropped. To do this I have implemented functionality that allows
for what I am referring to as unused page reporting

The functionality for this is fairly simple. When enabled it will allocate
statistics to track the number of reported pages in a given free area.
When the number of free pages exceeds this value plus a high water value,
currently 32, it will begin performing page reporting which consists of
pulling pages off of free list and placing them into a scatter list. The
scatterlist is then given to the page reporting device and it will perform
the required action to make the pages "reported", in the case of
virtio-balloon this results in the pages being madvised as MADV_DONTNEED
and as such they are forced out of the guest. After this they are placed
back on the free list, and an additional bit is added if they are not
merged indicating that they are a reported buddy page instead of a
standard buddy page. The cycle then repeats with additional non-reported
pages being pulled until the free areas all consist of reported pages.

I am leaving a number of things hard-coded such as limiting the lowest
order processed to PAGEBLOCK_ORDER, and have left it up to the guest to
determine what the limit is on how many pages it wants to allocate to
process the hints. The upper limit for this is based on the size of the
queue used to store the scattergather list.

My primary testing has just been to verify the memory is being freed after
allocation by running memhog 40g on a 40g guest and watching the total
free memory via /proc/meminfo on the host. With this I have verified most
of the memory is freed after each iteration. As far as performance I have
been mainly focusing on the will-it-scale/page_fault1 test running with
16 vcpus. I have modified it to use Transparent Huge Pages. With this I
see almost no difference, -0.08%, with the patches applied and the feature
disabled. I see a regression of -0.86% with the feature enabled, but the
madvise disabled in the hypervisor due to a device being assigned. With
the feature fully enabled I see a regression of -3.27% versus the baseline
without these patches applied. In my testing I found that most of the
overhead was due to the page zeroing that comes as a result of the pages
having to be faulted back into the guest.

One side effect of these patches is that the guest becomes much more
resilient in terms of NUMA locality. With the pages being freed and then
reallocated when used it allows for the pages to be much closer to the
active thread, and as a result there can be situations where this patch
set will out-perform the stock kernel when the guest memory is not local
to the guest vCPUs. To avoid that in my testing I set the affinity of all
the vCPUs and QEMU instance to the same node.

I have not included the QEMU patches with this set as they haven't really
changed in the last several revisions. If needed they can be located with
the v8 patchset.

Changes from the RFC:
https://lore.kernel.org/lkml/20190530215223.13974.22445.stgit@localhost.localdomain/
Moved aeration requested flag out of aerator and into zone->flags.
Moved boundary out of free_area and into local variables for aeration.
Moved aeration cycle out of interrupt and into workqueue.
Left nr_free as total pages instead of splitting it between raw and aerated.
Combined size and physical address values in virtio ring into one 64b value.

Changes from v1:
https://lore.kernel.org/lkml/20190619222922.1231.27432.stgit@localhost.localdomain/
Dropped "waste page treatment" in favor of "page hinting"
Renamed files and functions from "aeration" to "page_hinting"
Moved from page->lru list to scatterlist
Replaced wait on refcnt in shutdown with RCU and cancel_delayed_work_sync
Virtio now uses scatterlist directly instead of intermediate array
Moved stats out of free_area, now in separate area and pointed to from zone
Merged patch 5 into patch 4 to improve review-ability
Updated various code comments throughout

Changes from v2:
https://lore.kernel.org/lkml/20190724165158.6685.87228.stgit@localhost.localdomain/
Dropped "page hinting" in favor of "page reporting"
Renamed files from "hinting" to "reporting"
Replaced "Hinted" page type with "Reported" page flag
Added support for page poisoning while hinting is active
Add QEMU patch that implements PAGE_POISON feature

Changes from v3:
https://lore.kernel.org/lkml/20190801222158.22190.96964.stgit@localhost.localdomain/
Added mutex lock around page reporting startup and shutdown
Fixed reference to "page aeration" in patch 2
Split page reporting function bit out into separate QEMU patch
Limited capacity of scatterlist to vq size - 1 instead of vq size
Added exception handling for case of virtio descriptor allocation failure

Changes from v4:
https://lore.kernel.org/lkml/20190807224037.6891.53512.stgit@localhost.localdomain/
Replaced spin_(un)lock with spin_(un)lock_irq in page_reporting_cycle()
Dropped if/continue for ternary operator in page_reporting_process()
Added checks for isolate and cma types to for_each_reporting_migratetype_order
Added virtio-dev, Michal Hocko, and Oscar Salvador to to:/cc:
Rebased on latest linux-next and QEMU git trees

Changes from v5:
https://lore.kernel.org/lkml/20190812213158.22097.30576.stgit@localhost.localdomain/
Replaced spin_(un)lock with spin_(un)lock_irq in page_reporting_startup()
Updated shuffle code to use "shuffle_pick_tail" and updated patch description
Dropped storage of order and migratettype while page is being reported
Used get_pfnblock_migratetype to determine migratetype of page
Renamed put_reported_page to free_reported_page, added order as argument
Dropped check for CMA type as I believe we should be reporting those
Added code to allow moving of reported pages into and out of isolation
Defined page reporting order as minimum of Huge Page size vs MAX_ORDER - 1
Cleaned up use of static branch usage for page_reporting_notify_enabled

Changes from v6:
https://lore.kernel.org/lkml/20190821145806.20926.22448.stgit@localhost.localdomain/
Rebased on linux-next for 20190903
Added jump label to __page_reporting_request so we release RCU read lock
Removed "- 1" from capacity limit based on virtio ring
Added code to verify capacity is non-zero or return error on startup

Changes from v7:
https://lore.kernel.org/lkml/20190904150920.13848.32271.stgit@localhost.localdomain/
Updated poison fixes to clear flag if "nosanity" is enabled in kernel config
Split shuffle per-cpu optimization into separate patch
Moved check for !phdev->capacity into reporting patch where it belongs
Added Reviewed-by tags received for v7

Changes from v8:
https://lore.kernel.org/lkml/20190906145213.32552.30160.stgit@localhost.localdomain/
Added patch that moves HPAGE_SIZE definition for ARM64 to match other archs
Switch back to using pageblock_order instead of HUGETLB_ORDER and MAX_ORDER - 1
Boundary allocation now dynamic to support HUGETLB_PAGE_SIZE_VARIABLE option
Made use of existing code/functions to reduce size of move_to_boundary function
Dropped unused zone pointer from add_to/del_from_boundary functions
Added additional possible mm and arm64 people as reviewers to Cc
Added Reviewed-by tags received for v8
Fixed missing parameter in kerneldoc
---

Alexander Duyck (8):
      mm: Add per-cpu logic to page shuffling
      mm: Adjust shuffle code to allow for future coalescing
      mm: Move set/get_pcppage_migratetype to mmzone.h
      mm: Use zone and order instead of free area in free_list manipulators
      arm64: Move hugetlb related definitions out of pgtable.h to page-defs.h
      mm: Introduce Reported pages
      virtio-balloon: Pull page poisoning config out of free page hinting
      virtio-balloon: Add support for providing unused page reports to host


 arch/arm64/include/asm/page-def.h   |    9 +
 arch/arm64/include/asm/pgtable.h    |    9 -
 drivers/virtio/Kconfig              |    1 
 drivers/virtio/virtio_balloon.c     |   87 ++++++++-
 include/linux/mmzone.h              |  124 ++++++++----
 include/linux/page-flags.h          |   11 +
 include/linux/page_reporting.h      |  178 +++++++++++++++++
 include/uapi/linux/virtio_balloon.h |    1 
 mm/Kconfig                          |    5 
 mm/Makefile                         |    1 
 mm/internal.h                       |   18 ++
 mm/memory_hotplug.c                 |    1 
 mm/page_alloc.c                     |  217 +++++++++++++++------
 mm/page_reporting.c                 |  358 +++++++++++++++++++++++++++++++++++
 mm/shuffle.c                        |   40 ++--
 mm/shuffle.h                        |   12 +
 16 files changed, 931 insertions(+), 141 deletions(-)
 create mode 100644 include/linux/page_reporting.h
 create mode 100644 mm/page_reporting.c

--
```
