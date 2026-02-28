[# embedded-linux-kernel-refresh
](https://github.com/xsub/embedded-linux-kernel-refresh/)

# Kernel Refresh for an Embedded Linux Engineer (Senior/Staff) — Syllabus

## Course assumptions
- **Goal:** rebuild a complete, coherent kernel mental model (arch → core → subsystems → debug/production → RT → upstream) so you can reconstruct mechanisms from memory, not definitions.
- **Method:** run four tracks in parallel throughout:
  1. **Mental model** (short, precise notes)
  2. **Code reading** (specific files)
  3. **Labs** (QEMU/ARM64 + your own patches)
  4. **Interview drilling** (answers “cold”, without aids)

## Mandatory artifacts you produce
- `kernel-notes/` — 1 file per module: **mechanism → invariants → failure modes → debug plan**
- `kernel-labs/` — repo with build/boot config, scripts, patches, and README
- `cheatsheets/` — 10–20 pages: boot, MM, sched, IRQ, driver core, DMA, PM, VFS, net, tracing
- `grilling/` — Q/A: short answers (10–20 lines), then expanded versions

## Standard lab environment
- Mainline kernel (git)
- QEMU `virt` on ARM64
- initramfs with BusyBox
- GDB (remote), ftrace, perf, eBPF tracing (bpftrace/bpftool), lockdep, KASAN/KCSAN (test builds), kdump/crash (where it makes sense)
- Cross-compile + optional clang/lld (toolchain discipline)

---

# Course structure (16 weeks, Senior/Staff level)

## Week 0 — Setup and baseline
**Model**
- How the kernel is built, booted, and debugged without distribution “magic”.

**Code**
- `README`, `Documentation/`, `Makefile`, `init/Kconfig`, `kernel/printk/`

**Lab**
- Build kernel + initramfs, boot in QEMU.
- Add `earlycon`, run remote GDB stub (QEMU `-s -S`).
- Create two builds: “release-like” and “debug-like” (toggle lockdep/KASAN).

**Drill**
- 5-minute flow: reset → bootloader → kernel entry → start_kernel → init.

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

**Drill**
- Answer 1.1 and 1.3 from the interview list: correct ordering, no gaps.

## Week 2 — Device Tree in boot and early init
**Model**
- FDT handoff, fixups, early param parsing, initcall levels, deferred probe.

**Code**
- `drivers/of/*`
- `init/main.c` (initcall levels)
- `drivers/base/*` (intro)

**Lab**
- Add your own DT node for a “fake device”.
- Write a minimal platform driver matching on `compatible`.

**Drill**
- 1.2 and 1.4: debugging without printk (specific techniques and limits).

---

# Phase 2: Memory Management (Weeks 3–4)

## Week 3 — `struct page`, buddy, zones, migrations
**Model**
- `memblock` → page allocator, zones, watermarks, reclaim.

**Code**
- `mm/page_alloc.c`
- `mm/memblock.c`
- `include/linux/mm_types.h` (`struct page`)

**Lab**
- Collect buddy telemetry: `/proc/buddyinfo` + your own tracepoint.
- Force fragmentation and show where buddy starts failing.

**Drill**
- 2.1–2.4: bus vs phys vs virt, `struct page`, buddy, page fault flow.

## Week 4 — slab/slub, vmalloc, CMA, writeback pressure (intro)
**Model**
- kmalloc vs vmalloc, SLUB internals, CMA and DMA requiring contiguity.

**Code**
- `mm/slub.c`
- `mm/vmalloc.c`
- `mm/cma.c`

**Lab**
- Micro-torture: object allocations, leak hunting, slab growth.
- Enable KASAN in a test build and catch a deliberate bug (UAF/OOB in a module).

**Drill**
- 2.5–2.7: differences, costs, typical failure modes and debugging.

---

# Phase 3: Scheduler + Concurrency (Weeks 5–6)

## Week 5 — Scheduler: history, CFS, RT, deadline
**Model**
- O(1) → CFS (RB-tree, vruntime), RT classes, deadline, tick, load balance.

**Code**
- `kernel/sched/*` (especially `fair.c`, `rt.c`, `deadline.c`, `core.c`)

**Lab**
- Trace scheduler: wakeups, migrations, latencies.
- Create a `perf` profile with kernel stacks under CPU load and identify hot paths.

**Drill**
- 3.1 and 9.1: CFS mechanics + what RT actually changes.

## Week 6 — Concurrency: locks, atomics, barriers, RCU
**Model**
- Atomic context, preemption, IRQ contexts, lock ordering, RCU read-side vs update-side.

**Code**
- `kernel/locking/*`
- `include/linux/rcupdate.h`, `kernel/rcu/*`

**Lab**
- Enable lockdep, trigger a deliberate deadlock in a module, interpret the report.
- Implement a “read-mostly” data structure using RCU.

**Drill**
- 3.2–3.5: preemption, locks, RCU, “sleep while atomic” — consequences and detection.

---

# Phase 4: IRQ, time, bottom halves (Week 7)

## Week 7 — IRQ pipeline on ARM + softirq/workqueues
**Model**
- Top half vs bottom half, softirq, tasklets, workqueues, timers, interrupt storm.

**Code**
- `kernel/irq/*`
- `kernel/softirq.c`
- `kernel/workqueue.c`
- `drivers/irqchip/*` (GIC overview)

**Lab**
- Build a “storm simulator” (software-trigger) and show symptoms in `/proc/interrupts` + tracing.
- Move work from softirq to workqueue and demonstrate the difference (sleep vs no-sleep).

**Drill**
- 4.1–4.4: no hand-waving; concrete symptoms and tools.

---

# Phase 5: Driver Core + DT/clock/reset/regulator (Weeks 8–9)

## Week 8 — Device model: kobject, sysfs, bus/class, probe
**Model**
- How the kernel materializes devices, how probe/remove works, why deferred probe exists.

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
- Phandles, provider/consumer, resource ordering, “why it works only sometimes”.

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
- 5.3 + “DT bug vs driver bug” — distinguishing by symptoms.

---

# Phase 6: DMA + IOMMU (Week 10)

## Week 10 — DMA mapping, cache coherency, IOMMU/SWIOTLB
**Model**
- `dma_addr_t` is not PA, mapping directions, sync, coherent vs streaming.
- IOMMU: isolation, translation, device limitations.

**Code**
- `kernel/dma/*`, `drivers/iommu/*` (overview), `include/linux/dma-mapping.h`

**Lab**
- Driver with a DMA buffer: correct map/unmap + a deliberate cache sync bug and its symptom.
- Show how the same code behaves differently on coherent vs non-coherent systems.

**Drill**
- 5.5–5.6: DMA/IOMMU without mixing up address types.

---

# Phase 7: Storage + VFS/FS (Weeks 11–12)

## Week 11 — Block layer and I/O schedulers
**Model**
- bio/request, blk-mq, latency vs throughput, schedulers (mq-deadline/bfq/none).

**Code**
- `block/*`, `drivers/block/*` (overview)

**Lab**
- Profile I/O: `perf` + block tracepoints. Pinpoint where performance is lost (queueing vs FS vs device).
- Change scheduler and show latency impact.

**Drill**
- “System loses performance after days”: diagnostic path focused on I/O.

## Week 12 — VFS, page cache, writeback, open()
**Model**
- dentry/inode/file, path lookup, page cache, dirty throttling, fsync semantics.

**Code**
- `fs/namei.c`
- `mm/filemap.c`, `mm/page-writeback.c`
- `fs/*` (pick one FS: ext4/ubifs for structural reading)

**Lab**
- Trace `open()`/`read()`/`write()` through page cache and writeback.
- Force writeback congestion and demonstrate throttling.

**Drill**
- 7.1–7.3: VFS/open/page cache, plus typical production symptoms.

---

# Phase 8: Networking (Week 13)

## Week 13 — `sk_buff`, NAPI, netdev, netfilter (interview scope)
**Model**
- RX/TX paths, softirq, backlog, where latency and drops come from.

**Code**
- `net/core/*`
- `drivers/net/*` (overview)

**Lab**
- Trace NAPI and softirq load; show the effect on scheduling.
- Identify bottleneck: IRQ vs ksoftirqd vs user-space.

**Drill**
- “Random degradation” diagnostics including networking.

---

# Phase 9: Power Management (Week 14)

## Week 14 — suspend/resume + runtime PM
**Model**
- DPM callbacks, ordering, wakeup sources, cpuidle/cpufreq in embedded.

**Code**
- `drivers/base/power/*`
- `kernel/power/*`

**Lab**
- Dummy driver with `pm_ops`, correct quiesce and restore.
- Simulate “hang on 3rd resume”: state accumulation (deliberate bug) and debug plan.

**Drill**
- 6.1–6.4: resume path failure modes and tools.

---

# Phase 10: Debugging/Tracing/Production (Week 15)

## Week 15 — Observability: ftrace/perf/lockdep/kgdb/kdump
**Model**
- Which tool for which problem type and why.

**Code**
- `kernel/trace/*`
- `kernel/debug/*` (overview)

**Lab**
- ftrace function_graph on a chosen problem.
- perf + flamegraph mindset (kernel stacks).
- kdump/crash: core commands to analyze tasks and stacks.

**Drill**
- 8.1–8.4: production debugging playbooks.

---

# Phase 11: Real-Time (Week 16)

## Week 16 — PREEMPT_RT and deterministic latency
**Model**
- Threaded IRQ, rtmutex, priority inheritance, what generates jitter.

**Code**
- `kernel/locking/rtmutex.c` (look-in), RT sched pieces already covered

**Lab**
- Latency measurements (cyclictest mindset), identify jitter sources (irqsoff/preemptoff).
- Compare behavior: vanilla vs RT (QEMU as a model, not an absolute benchmark).

**Drill**
- 9.1–9.3: RT mechanics and what breaks determinism.

---

# Staff-level modules (attach as 1-week sprints after Week 16)
Each sprint = 1 week.

## Sprint A — Boot time reduction
- `initcall_debug`, dependency trimming, async probe, user-space sequencing.

## Sprint B — Regression after kernel update
- `git bisect` + automated test, perf delta, config delta, vendor patchset delta.

## Sprint C — Vendor divergence vs upstream
- Patch hygiene strategy, upstreaming, stable backports, security risk management.

## Sprint D — Debug framework for a new SoC
- Multi-layer logging (early/persistent/trace/hw), reset reason, crash area, ETM/CoreSight plan.

---

# Capstone (2 weeks, after Phase 11)
- Define a DT device with resources (reg/irq/clock/reset/regulator).
- Write the driver: init → IRQ → workqueue → DMA buffer (model) → sysfs control.
- Introduce 3 controlled bugs:
  1) sleep in atomic context,
  2) broken IRQ handling (storm),
  3) DMA sync bug.
- Diagnose each bug using a different tool (lockdep/ftrace/perf/dmesg+ratelimit/pstore).
- Produce a patch series: fix + commit message + test plan.

---

# Minimum passing standard (measurable)
- ARM boot flow (v7/v8 differences) explained from memory: 10–15 minutes without hesitation.
- MM: buddy/SLUB/page fault/CMA explained mechanically + 3 common failures and debug approaches.
- Scheduler: O(1)→CFS→RT/Deadline: what changed, why, what it costs.
- IRQ pipeline: top/bottom/softirq/workqueue + storm diagnosis.
- Driver core: probe/remove, device model, DT dependencies, DMA/IOMMU contracts.
- Suspend/resume: ordering, wakeups, “third resume hang” playbook.
- Production: action plans for “panic without logs”, “crash without stack trace”, “degradation after days”.
- Tracing: ftrace/perf/lockdep/kgdb/kdump used practically in labs.
