# Kernel Refresh for an Embedded Linux Engineer — Syllabus

#### Note: This course is aimed at Senior/Staff-level preparation.

## Course assumptions
- **Goal:** rebuild a complete, coherent kernel mental model (arch → core → subsystems → debug/production → RT → upstream) so you can reconstruct mechanisms from memory, not definitions.
- **Method:** run four tracks in parallel throughout:
  1. **Mental model** (short, precise notes)
  2. **Code reading** (specific files)
  3. **Labs** (QEMU/ARM64 + your own patches)
  4. **Interview drilling** (answers “cold”, without aids)

## Mandatory artifacts you produce
- `kernel-notes/` — 1 file per module: **mechanism → invariants → failure modes → debug plan**
- `labs/` — reproducible labs, build/boot config, scripts, patches, and per-lab READMEs
- `cheatsheets/` — 10–20 pages: boot, MM, sched, IRQ, driver core, DMA, PM, VFS, net, tracing
- `grilling/` — Q/A: short answers (10–20 lines), then expanded versions

## Standard lab environment (all linkable)
**Kernel docs (primary)**
- [Linux Kernel Documentation](https://docs.kernel.org/)
- [Development tools for the kernel](https://docs.kernel.org/dev-tools/index.html)
- [Debugging advice for Linux Kernel developers](https://docs.kernel.org/process/debugging/index.html)

**Build / toolchain**
- [Linux Kernel Makefiles (kbuild)](https://docs.kernel.org/kbuild/makefiles.html)
- [Building Linux with Clang/LLVM](https://docs.kernel.org/kbuild/llvm.html)
- [Clang documentation](https://clang.llvm.org/docs/index.html)
- [LLD (LLVM linker)](https://lld.llvm.org/)

**Virtualization / bring-up**
- [QEMU Arm system emulator](https://www.qemu.org/docs/master/system/target-arm.html)
- [QEMU `virt` machine (ARM/arm64)](https://qemu-project.gitlab.io/qemu/system/arm/virt.html)
- [Kernel.org arm64 QEMU HOWTO](https://www.kernel.org/pub/linux/kernel/people/will/docs/qemu/qemu-arm64-howto.html)

**Userspace**
- [BusyBox documentation](https://busybox.net/BusyBox.html)

**Tracing / profiling**
- [ftrace](https://docs.kernel.org/trace/ftrace.html)
- [Tracepoints](https://docs.kernel.org/trace/tracepoints.html)
- [perf wiki](https://perfwiki.github.io/main/)
- [`perf(1)` man page](https://man7.org/linux/man-pages/man1/perf.1.html)
- [BPF documentation](https://docs.kernel.org/bpf/)
- [bpftrace docs](https://bpftrace.org/docs/pre-release)
- [bpftool (official repo)](https://github.com/libbpf/bpftool)
- [`bpftool(8)` man page](https://man.archlinux.org/man/bpftool.8.en)

**Debugging / correctness**
- [Lockdep design](https://docs.kernel.org/locking/lockdep-design.html)
- [Memory barriers](https://docs.kernel.org/core-api/wrappers/memory-barriers.html)
- [Atomic types](https://docs.kernel.org/core-api/wrappers/atomic_t.html)
- [printk basics (kernel log buffer)](https://docs.kernel.org/core-api/printk-basics.html)

**Sanitizers**
- [KASAN](https://docs.kernel.org/dev-tools/kasan.html)
- [KCSAN](https://docs.kernel.org/dev-tools/kcsan.html)
- [KFENCE](https://docs.kernel.org/dev-tools/kfence.html)
- [KMSAN](https://docs.kernel.org/dev-tools/kmsan.html)

**Crash capture / post-mortem**
- [Kdump](https://docs.kernel.org/admin-guide/kdump/kdump.html)
- [Crash utility white paper](https://crash-utility.github.io/crash_whitepaper.html)
- [Ramoops (pstore backend)](https://docs.kernel.org/admin-guide/ramoops.html)
- [Netconsole](https://docs.kernel.org/networking/netconsole.html)

**Kernel debugging with GDB**
- [GDB (Sourceware) documentation](https://www.sourceware.org/gdb/documentation/)
- [Kernel debugging via GDB (kernel helpers)](https://docs.kernel.org/6.2/dev-tools/gdb-kernel-debugging.html)

---

# Course structure (16 weeks, Senior/Staff level)

## Week 0 — Setup and baseline
**Model**
- How the kernel is built, booted, and debugged without distribution “magic”.
- [Kernel init overview (`init=` and early userspace)](https://docs.kernel.org/admin-guide/init.html)

**Code**
- `README`, `Documentation/`, `Makefile`, `init/Kconfig`, `kernel/printk/`

**Lab**
- Build kernel + initramfs, boot in QEMU:
  - [QEMU `virt` machine](https://qemu-project.gitlab.io/qemu/system/arm/virt.html)
  - [arm64 QEMU HOWTO](https://www.kernel.org/pub/linux/kernel/people/will/docs/qemu/qemu-arm64-howto.html)
- Add `earlycon` / `console=`:
  - [Kernel command-line parameters](https://docs.kernel.org/admin-guide/kernel-parameters.html)
  - [Linux Serial Console](https://docs.kernel.org/admin-guide/serial-console.html)
- Remote debugging:
  - [Kernel debugging via GDB](https://docs.kernel.org/6.2/dev-tools/gdb-kernel-debugging.html)

**Drill**
- 5-minute flow: reset → bootloader → kernel entry → `start_kernel()` → init.

---

# Phase 1: Boot + Arch bring-up (Weeks 1–2)

## Week 1 — ARM64 boot path and EL transitions
**Model**
- Entry, stack, MMU enable, exceptions, initcalls.

**Code**
- `arch/arm64/kernel/head.S`
- `arch/arm64/mm/*` (intro)
- `init/main.c` (`start_kernel`, `rest_init`)

**Lab**
- Implement boot checkpoints: trace buffer in RAM + dump after boot.
- Identify moments: MMU on, IRQ on, scheduler init.
- Validate early logging path:
  - [printk basics](https://docs.kernel.org/core-api/printk-basics.html)
  - [Ramoops](https://docs.kernel.org/admin-guide/ramoops.html) (optional for persistence)

**Drill**
- Answer interview items 1.1 and 1.3 with correct ordering.

## Week 2 — Device Tree in boot and early init
**Model**
- DT handoff, fixups, early param parsing, initcall levels, deferred probe.
- DT core docs:
  - [Devicetree index](https://docs.kernel.org/devicetree/index.html)
  - [Linux and the Devicetree](https://docs.kernel.org/devicetree/usage-model.html)

**Code**
- `drivers/of/*`
- `init/main.c` (initcall levels)
- `drivers/base/*` (intro)

**Lab**
- Add your own DT node for a “fake device”.
- Write a minimal platform driver matching on `compatible`.

**Drill**
- Debug without printk (JTAG/QEMU GDB stub, RAM checkpoints, earlycon).

---

# Phase 2: Memory Management (Weeks 3–4)

## Week 3 — `struct page`, buddy, zones, migrations
**Model**
- `memblock` → page allocator, zones, watermarks, reclaim.
- Reading map:
  - [Boot-time memory management (memblock)](https://docs.kernel.org/core-api/boot-time-mm.html)
  - [Physical Memory](https://docs.kernel.org/mm/physical_memory.html)
  - [Memory Management docs index](https://docs.kernel.org/mm/index.html)

**Code**
- `mm/page_alloc.c`
- `mm/memblock.c`
- `include/linux/mm_types.h` (`struct page`)

**Lab**
- Collect buddy telemetry: `/proc/buddyinfo` + your own tracepoint.
- Force fragmentation and show where buddy starts failing.

**Drill**
- 2.1–2.4: bus vs phys vs virt, `struct page`, buddy, page fault flow.

## Week 4 — SLUB, vmalloc, CMA, writeback pressure (intro)
**Model**
- [Memory Allocation Guide](https://docs.kernel.org/core-api/memory-allocation.html)
- [SLUB guide](https://docs.kernel.org/6.9/mm/slub.html)
- [vmalloc](https://docs.kernel.org/mm/vmalloc.html)
- CMA visibility/testing:
  - [CMA debugfs interface](https://docs.kernel.org/admin-guide/mm/cma_debugfs.html)

**Code**
- `mm/slub.c`
- `mm/vmalloc.c`
- `mm/cma.c`

**Lab**
- Micro-torture: object allocations, leak hunting, slab growth.
- Enable sanitizers on a test build:
  - [KASAN](https://docs.kernel.org/dev-tools/kasan.html)
  - [KCSAN](https://docs.kernel.org/dev-tools/kcsan.html) (later, concurrency week also)

**Drill**
- 2.5–2.7: kmalloc vs vmalloc, SLUB, CMA: costs + failure modes + debug.

---

# Phase 3: Scheduler + Concurrency (Weeks 5–6)

## Week 5 — Scheduler: history, CFS, RT, deadline
**Model**
- Scheduler map:
  - [Scheduler index](https://docs.kernel.org/scheduler/index.html)
  - [CFS design](https://docs.kernel.org/scheduler/sched-design-CFS.html)
  - [EEVDF scheduler](https://docs.kernel.org/scheduler/sched-eevdf.html)
- Keep the “mental model” at the level: rq → entity → vruntime/lag → pick-next → balance.

**Code**
- `kernel/sched/*` (especially `fair.c`, `rt.c`, `deadline.c`, `core.c`)

**Lab**
- Trace scheduler: wakeups, migrations, latencies:
  - [ftrace](https://docs.kernel.org/trace/ftrace.html)
  - [Tracepoints](https://docs.kernel.org/trace/tracepoints.html)
- Profile CPU hot paths:
  - [perf wiki](https://perfwiki.github.io/main/)
  - [`perf(1)`](https://man7.org/linux/man-pages/man1/perf.1.html)

**Drill**
- 3.1 and 9.1: CFS mechanics + what RT changes.

## Week 6 — Concurrency: locks, atomics, barriers, RCU
**Model**
- Locking / correctness:
  - [Locking index](https://docs.kernel.org/locking/index.html)
  - [Lockdep design](https://docs.kernel.org/locking/lockdep-design.html)
- Concurrency primitives:
  - [Atomic types](https://docs.kernel.org/core-api/wrappers/atomic_t.html)
  - [Memory barriers](https://docs.kernel.org/core-api/wrappers/memory-barriers.html)
- RCU:
  - [RCU Handbook](https://docs.kernel.org/RCU/index.html)
  - [RCU concepts](https://docs.kernel.org/RCU/rcu.html)

**Code**
- `kernel/locking/*`
- `include/linux/rcupdate.h`, `kernel/rcu/*`

**Lab**
- Enable lockdep, trigger a deliberate deadlock in a module, interpret the report.
- Implement a “read-mostly” data structure using RCU.
- Add a targeted race and catch it with:
  - [KCSAN](https://docs.kernel.org/dev-tools/kcsan.html)

**Drill**
- 3.2–3.5: preemption, locks, RCU, “sleep while atomic”.

---

# Phase 4: IRQ, time, bottom halves (Week 7)

## Week 7 — IRQ pipeline on ARM + softirq/workqueues
**Model**
- Generic IRQ layer:
  - [Generic IRQ handling](https://docs.kernel.org/core-api/genericirq.html)
  - [irq_domain mapping](https://docs.kernel.org/core-api/irq/irq-domain.html)
- Workqueues:
  - [Workqueue API](https://docs.kernel.org/core-api/workqueue.html)

**Code**
- `kernel/irq/*`
- `kernel/softirq.c`
- `kernel/workqueue.c`
- `drivers/irqchip/*` (GIC overview)

**Lab**
- Build a “storm simulator” (software-trigger) and show symptoms:
  - `/proc/interrupts`, trace events, ftrace
- Move work from softirq to workqueue and demonstrate the difference (sleep vs no-sleep).

**Drill**
- 4.1–4.4: top/bottom halves, softirq, IRQ storms: diagnosis playbook.

---

# Phase 5: Driver Core + DT/clock/reset/regulator (Weeks 8–9)

## Week 8 — Device model: kobject, sysfs, bus/class, probe
**Model**
- Driver core mindset:
  - [Driver core / infrastructure](https://docs.kernel.org/driver-api/infrastructure.html)

**Code**
- `drivers/base/*`
- `lib/kobject.c`
- `fs/sysfs/*`

**Lab**
- Driver with sysfs attributes, correct lifetime management (`devm_*` vs manual).
- Force a resource init failure and show clean unwind.

**Drill**
- 5.1–5.4: minimal driver, probe/remove, debugging hangs.

## Week 9 — DT in practice: clocks, resets, regulators, pinctrl
**Model**
- DT usage/bindings:
  - [Devicetree index](https://docs.kernel.org/devicetree/index.html)
  - [Linux and the Devicetree](https://docs.kernel.org/devicetree/usage-model.html)

**Code**
- `drivers/clk/*`
- `drivers/reset/*`
- `drivers/regulator/*`
- `drivers/pinctrl/*`
- `drivers/of/*` (bindings mindset)

**Lab**
- Extend the fake device with clock/reset/regulator dependencies and demonstrate deferred probe.
- Inject a DT error (trigger/polarity/address) and diagnose it like real bring-up.

**Drill**
- “DT bug vs driver bug”: distinguish by symptoms + evidence.

---

# Phase 6: DMA + IOMMU (Week 10)

## Week 10 — DMA mapping, cache coherency, IOMMU/SWIOTLB
**Model**
- DMA API:
  - [DMA API (reference)](https://docs.kernel.org/core-api/dma-api.html)
  - [DMA API HOWTO](https://docs.kernel.org/core-api/dma-api-howto.html)
- Bounce buffering:
  - [swiotlb](https://docs.kernel.org/core-api/swiotlb.html)
- IOMMU surface (conceptual + interfaces):
  - [VFIO (IOMMU-protected device access)](https://docs.kernel.org/driver-api/vfio.html)
  - [IOMMUFD (userspace API)](https://docs.kernel.org/userspace-api/iommufd.html)

**Code**
- `kernel/dma/*`, `include/linux/dma-mapping.h`
- `drivers/iommu/*` (overview)

**Lab**
- Driver with a DMA buffer: correct map/unmap + a deliberate cache sync bug and its symptom.
- Demonstrate coherent vs non-coherent assumptions and where they break.

**Drill**
- 5.5–5.6: DMA/IOMMU without mixing address types.

---

# Phase 7: Storage + VFS/FS (Weeks 11–12)

## Week 11 — Block layer and I/O schedulers
**Model**
- Block docs map:
  - [Block index](https://docs.kernel.org/block/index.html)
  - [blk-mq](https://docs.kernel.org/block/blk-mq.html)

**Code**
- `block/*`, `drivers/block/*` (overview)

**Lab**
- Profile I/O: perf + block tracepoints. Pinpoint where performance is lost (queueing vs FS vs device).
- Change scheduler and show latency impact.

**Drill**
- “System loses performance after days”: diagnostic path focused on I/O.

## Week 12 — VFS, page cache, writeback, open()
**Model**
- VFS / lookup:
  - [VFS overview](https://docs.kernel.org/filesystems/vfs.html)
  - [Pathname lookup](https://docs.kernel.org/filesystems/path-lookup.html)

**Code**
- `fs/namei.c`
- `mm/filemap.c`, `mm/page-writeback.c`
- `fs/*` (pick one FS: ext4/ubifs for structural reading)

**Lab**
- Trace `open()`/`read()`/`write()` through page cache and writeback.
- Force writeback congestion and demonstrate throttling.

**Drill**
- 7.1–7.3: VFS/open/page cache + production failure patterns.

---

# Phase 8: Networking (Week 13)

## Week 13 — `sk_buff`, NAPI, netdev (interview scope)
**Model**
- Networking docs:
  - [Networking index](https://docs.kernel.org/networking/index.html)
  - [`struct sk_buff`](https://docs.kernel.org/networking/skbuff.html)
  - [NAPI](https://docs.kernel.org/networking/napi.html)
  - [Netdevices guide (driver oriented)](https://docs.kernel.org/networking/netdevices.html)

**Code**
- `net/core/*`
- `drivers/net/*` (overview)

**Lab**
- Trace NAPI and softirq load; show the effect on scheduling.
- Identify bottleneck: IRQ vs ksoftirqd vs user-space.

**Drill**
- “Random degradation” including networking.

---

# Phase 9: Power Management (Week 14)

## Week 14 — suspend/resume + runtime PM
**Model**
- PM docs:
  - [Power Management index](https://docs.kernel.org/power/index.html)
  - [System suspend code flows](https://docs.kernel.org/admin-guide/pm/suspend-flows.html)
  - [Device PM basics](https://docs.kernel.org/driver-api/pm/devices.html)
  - [Basic PM debugging](https://docs.kernel.org/power/basic-pm-debugging.html)

**Code**
- `drivers/base/power/*`
- `kernel/power/*`

**Lab**
- Dummy driver with `pm_ops`, correct quiesce and restore.
- Simulate “hang on 3rd resume”: state accumulation (deliberate bug) and debug plan.

**Drill**
- 6.1–6.4: resume failure modes + tools.

---

# Phase 10: Debugging/Tracing/Production (Week 15)

## Week 15 — Observability: ftrace/perf/lockdep/kgdb/kdump/crash
**Model**
- Tool map:
  - [Debugging advice index](https://docs.kernel.org/process/debugging/index.html)
  - [ftrace](https://docs.kernel.org/trace/ftrace.html)
  - [Tracepoints](https://docs.kernel.org/trace/tracepoints.html)
  - [Lockdep](https://docs.kernel.org/locking/lockdep-design.html)
  - [Kdump](https://docs.kernel.org/admin-guide/kdump/kdump.html)
  - [Crash utility white paper](https://crash-utility.github.io/crash_whitepaper.html)
  - [Ramoops](https://docs.kernel.org/admin-guide/ramoops.html)
  - [Netconsole](https://docs.kernel.org/networking/netconsole.html)

**Code**
- `kernel/trace/*`
- `kernel/debug/*` (overview)

**Lab**
- ftrace function_graph on a chosen problem.
- perf + kernel stacks.
- kdump/crash: analyze a vmcore (baseline commands: tasks, bt, log, kmem, files).

**Drill**
- 8.1–8.4: production debugging playbooks.

---

# Phase 11: Real-Time (Week 16)

## Week 16 — PREEMPT_RT and deterministic latency
**Model**
- RT internals:
  - [Real-time preemption (PREEMPT_RT)](https://docs.kernel.org/core-api/real-time/)
  - [How realtime kernels differ](https://docs.kernel.org/core-api/real-time/differences.html)
  - [RT-mutex subsystem](https://docs.kernel.org/locking/rt-mutex.html)

**Code**
- `kernel/locking/rtmutex.c` (look-in)
- `kernel/sched/rt.c` (review in context of RT class behavior)

**Lab**
- Latency measurements:
  - [cyclictest (LF realtime wiki)](https://wiki.linuxfoundation.org/realtime/documentation/howto/tools/cyclictest/start)
- Latency source hunting:
  - [timerlat tracer](https://docs.kernel.org/trace/timerlat-tracer.html)

**Drill**
- 9.1–9.3: RT mechanics and what breaks determinism.

---

# Staff-level modules (attach as 1-week sprints after Week 16)
Each sprint = 1 week.

## Sprint A — Boot time reduction
- Use `initcall_debug` and tracing to isolate slow initcalls:
  - [Kernel command-line parameters](https://docs.kernel.org/admin-guide/kernel-parameters.html)
  - [ftrace](https://docs.kernel.org/trace/ftrace.html)

## Sprint B — Regression after kernel update
- `git bisect` + automated test, perf delta, config delta, vendor patchset delta:
  - [perf wiki](https://perfwiki.github.io/main/)

## Sprint C — Vendor divergence vs upstream
- Patch hygiene strategy, upstreaming, stable backports, security risk management:
  - [Submitting patches (kernel docs)](https://docs.kernel.org/process/submitting-patches.html)

## Sprint D — Debug framework for a new SoC
- Multi-layer logging (early/persistent/trace/hw):
  - [printk basics](https://docs.kernel.org/core-api/printk-basics.html)
  - [Ramoops](https://docs.kernel.org/admin-guide/ramoops.html)
  - [Netconsole](https://docs.kernel.org/networking/netconsole.html)
  - [Tracepoints](https://docs.kernel.org/trace/tracepoints.html)
  - [ftrace](https://docs.kernel.org/trace/ftrace.html)

---

# Capstone (2 weeks, after Phase 11)
- Define a DT device with resources (reg/irq/clock/reset/regulator):
  - [Linux and the Devicetree](https://docs.kernel.org/devicetree/usage-model.html)
- Write the driver: init → IRQ → workqueue → DMA buffer (model) → sysfs control:
  - [Generic IRQ handling](https://docs.kernel.org/core-api/genericirq.html)
  - [Workqueue API](https://docs.kernel.org/core-api/workqueue.html)
  - [DMA API HOWTO](https://docs.kernel.org/core-api/dma-api-howto.html)
- Introduce 3 controlled bugs:
  1) sleep in atomic context,
  2) broken IRQ handling (storm),
  3) DMA sync bug.
- Diagnose each bug using a different tool:
  - [lockdep](https://docs.kernel.org/locking/lockdep-design.html)
  - [ftrace](https://docs.kernel.org/trace/ftrace.html)
  - [perf](https://perfwiki.github.io/main/)
  - [ramoops](https://docs.kernel.org/admin-guide/ramoops.html) / [netconsole](https://docs.kernel.org/networking/netconsole.html)
- Produce a patch series: fix + commit message + test plan.

---

## Minimum passing standard (measurable)
- ARM boot flow (v7/v8 differences) explained from memory: 10–15 minutes without hesitation.
- MM: buddy/SLUB/page fault/CMA explained mechanically + 3 common failures and debug approaches.
- Scheduler: O(1)→CFS→RT/Deadline: what changed, why, what it costs.
- IRQ pipeline: top/bottom/softirq/workqueue + storm diagnosis.
- Driver core: probe/remove, device model, DT dependencies, DMA/IOMMU contracts.
- Suspend/resume: ordering, wakeups, “third resume hang” playbook.
- Production: action plans for “panic without logs”, “crash without stack trace”, “degradation after days”.
- Tracing: ftrace/perf/lockdep/kgdb/kdump used practically in labs.
