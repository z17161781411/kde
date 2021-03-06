

#### [RFC] [PATCH v2 0/5] Intel Virtual PMU Optimization
##### From: Like Xu <like.xu@linux.intel.com>

```c

As a toolbox treasure to developers, the Performance Monitoring Unit is
designed to monitor micro architectural events which helps in analyzing
how an application or operating systems are performing on the processors.

Today in KVM, version 2 Architectural PMU on Intel and AMD hosts is
implemented and works. With the joint efforts of the community, it would be
an inspiring journey to enable all available PMU features for guest users
as complete/smooth/accurate as possible.

=== Brief description ===

This proposal for Intel vPMU is still committed to optimize the basic
functionality by reducing the PMU virtualization overhead and not a blind
pass-through of the PMU. The proposal applies to existing models, in short,
is "host perf would hand over control to kvm after counter allocation".

The pmc_reprogram_counter is a heavyweight and high frequency operation
which goes through the host perf software stack to create a perf event for
counter assignment, this could take millions of nanoseconds. The current
vPMU always does reprogram_counter when the guest changes the eventsel,
fixctrl, and global_ctrl msrs. This brings too much overhead to the usage
of perf inside the guest, especially the guest PMI handling and context
switching of guest threads with perf in use.

We optimize the current vPMU to work in this manner:

(1) rely on the existing host perf (perf_event_create_kernel_counter)
    to allocate counters for in-use vPMC and always try to reuse events;
(2) vPMU captures guest accesses to the eventsel and fixctrl msr directly
    to the hardware msr that the corresponding host event is scheduled on
    and avoid pollution from host is also needed in its partial runtime;
(3) save and restore the counter state during vCPU scheduling in hooks;
(4) apply a lazy approach to release the vPMC's perf event. That is, if
    the vPMC isn't used in a fixed sched slice, its event will be released.

In the use of vPMC, the vPMU always focus on the assigned resources and
guest perf would significantly benefit from direct access to hardware and
may not care about runtime state of perf_event created by host and always
try not to pay for their maintenance. However to avoid events entering into
any unexpected state, calling pmc_read_counter in appropriate is necessary.

=== vPMU Overhead Comparison ===

For the guest perf usage like "perf stat -e branches,cpu-cycles,\
L1-icache-load-misses,branch-load-misses,branch-loads,\
dTLB-load-misses ./ftest", here are some performance numbers which show the
improvement with this optimization (in nanoseconds) [1]:

(1) Basic operatios latency on legacy Intel vPMU

kvm_pmu_rdpmc                               200
pmc_stop_counter: gp                        30,000
pmc_stop_counter: fixed                		2,000,000
perf_event_create_kernel_counter: gp        30,000,000 <== (mark as 3.1)
perf_event_create_kernel_counter: fixed     25,000

(2) Comparison of max guest behavior latency
                        legacy          v2
enable global_ctrl 		57,000,000		17,000,000 <== (3.2)
disable global_ctrl		2,000,000 		21,000
r/w fixed_ctrl   		21,000          1,100
r/w eventsel     		36,000          17,000
rdpmcl             		35,000   		18,000
x86_pmu.handle_irq 		3,500,000 		8,800 <== (3.3)

(3) For 3.2, the v2 value is just a maximum value for reprogram and
would be quickly weakened to neglect by reusing perf_events. In general,
we can say this optimization is ~400 times (3.3) faster than the original
for Intel vPMU due to a large number reduction of calls to
perf_event_create_kernel_counter (3.1).

(4) Comparison of guest behavior call time
                        legacy                  v2
enable global_ctrl      74,000                  3,000 <== (6.1)
rd/wr fixed_ctrl        11,000                  1,400
rd/wr eventsel          7,000,000               7,600
rdpmcl            		130,000                 10,000
x86_pmu.handle_irq		11                      14

(5) Comparison of perf-attached thread guest context_switch latency
                            legacy          v2
context_switch, sched_in 	350,000,000     4,000,000
context_switch, sched_out	55,000,000      200,000

(6) From 6.1 and table 5, We can see a substantial reduction in the
runtime of a perf attached guest thread and the vPMU is no longer stuck.

=== vPMU Precision Comparison ===

We don't want to lose any precision after optimization and for perf usage
like "perf record -e cpu-cycles --all-user ./ftest"here is the comparison
of the profiling results with and without this optimization [1]:

(1) Test in Guest without optimization:

[ perf record: Woken up 2 times to write data ]
[ perf record: Captured and wrote 0.437 MB perf.data (5198 samples) ]

  36.95%  ftest    ftest          [.] qux
  15.68%  ftest    ftest          [.] foo
  15.45%  ftest    ftest          [.] bar
  12.32%  ftest    ftest          [.] main
   9.56%  ftest    libc-2.27.so   [.] __random
   8.87%  ftest    libc-2.27.so   [.] __random_r
   1.17%  ftest    ftest          [.] random@plt
   0.00%  ftest    ld-2.27.so     [.] _start

(2) Test in Guest with this optimization:

[ perf record: Woken up 4 times to write data ]
[ perf record: Captured and wrote 0.861 MB perf.data (22550 samples) ]

  36.64%  ftest    ftest             [.] qux
  14.35%  ftest    ftest             [.] foo
  14.07%  ftest    ftest             [.] bar
  12.60%  ftest    ftest             [.] main
  11.73%  ftest    libc-2.27.so      [.] __random
   9.18%  ftest    libc-2.27.so      [.] __random_r
   1.42%  ftest    ftest             [.] random@plt
   0.00%  ftest    ld-2.27.so        [.] do_lookup_x
   0.00%  ftest    ld-2.27.so        [.] _dl_new_object
   0.00%  ftest    ld-2.27.so        [.] _dl_sysdep_start
   0.00%  ftest    ld-2.27.so        [.] _start

(3) Test in Host:

[ perf record: Woken up 4 times to write data ]
[ perf record: Captured and wrote 0.789 MB perf.data (20652 samples) ]

  37.87%  ftest    ftest          [.] qux
  15.78%  ftest    ftest          [.] foo
  13.18%  ftest    ftest          [.] main
  12.14%  ftest    ftest          [.] bar
   9.85%  ftest    libc-2.17.so   [.] __random_r
   9.59%  ftest    libc-2.17.so   [.] __random
   1.59%  ftest    ftest          [.] random@plt
   0.00%  ftest    ld-2.17.so     [.] _dl_cache_libcmp
   0.00%  ftest    ld-2.17.so     [.] _dl_start
   0.00%  ftest    ld-2.17.so     [.] _start

=== NEXT ===

This proposal is trying to respected necessary functionality from the host
perf driver and bypasses the host perf subsystem software stack in most
execution paths with no loss of precision compared to the legacy one.
If this proposal is acceptable, here are something we could do for next:

(1) If host perf wants to perceive all the events for scheduling, some
    event hooks could be implemented to update host perf_event with the
    proper counts/runtimes/state.
(2) Loose the scheduling restrictions on pinned,
    but still keeps eyes on special specific requests
(3) This series currently covers the basic perf counter virtualization.
    Other features, such as pebs, bts, lbr will come after this series.

May be there is something wrong in the whole series and please help me
reach the other side of the performance improvement with your comments.

[1] Tested on Linux 5.0.0 on Intel(R) Xeon(R) CPU E5-2699 v4 @ 2.20GHz,
   and added "nowatchdog" to host booting parameter. The values comes from 
	sched_clock() using tsc as guest clocksource.

=== Changelog ===

v1: Wei Wang (8): https://lkml.org/lkml/2018/11/1/937
  perf/x86: add support to mask counters from host
  perf/x86/intel: add pmi callback support
  KVM/x86/vPMU: optimize intel vPMU
  KVM/x86/vPMU: support msr switch on vmx transitions
  KVM/x86/vPMU: intel_pmu_read_pmc
  KVM/x86/vPMU: remove some unused functions
  KVM/x86/vPMU: save/restore guest perf counters on vCPU switching
  KVM/x86/vPMU: return the counters to host if guest is torn down

v2: Like Xu (5):
  perf/x86: avoid host changing counter state for kvm_intel events holder
  KVM/x86/vPMU: add pmc operations for vmx and count to track release
  KVM/x86/vPMU: add Intel vPMC enable/disable and save/restore support
  KVM/x86/vPMU: add vCPU scheduling support for hw-assigned vPMC
  KVM/x86/vPMU: not do reprogram_counter for Intel hw-assigned vPMC

 arch/x86/events/core.c          |  37 ++++-
 arch/x86/events/intel/core.c    |   5 +-
 arch/x86/events/perf_event.h    |  13 +-
 arch/x86/include/asm/kvm_host.h |   2 +
 arch/x86/kvm/pmu.c              |  34 +++++
 arch/x86/kvm/pmu.h              |  22 +++
 arch/x86/kvm/vmx/pmu_intel.c    | 329 +++++++++++++++++++++++++++++++++++++---
 arch/x86/kvm/x86.c              |   6 +
 8 files changed, 421 insertions(+), 27 deletions(-)
```
