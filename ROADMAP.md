# ROADMAP.md

## Progress tracking
Rule: each week produces 3 commits minimum:
1) notes (`kernel-notes/`), 2) lab (`labs/`), 3) grilling (`grilling/`).

Status legend:
- [ ] not started
- [~] in progress
- [x] done

---

## Week 0 — Setup and baseline
- [ ] `docs/environment.md` (tooling, versions, build/run commands)
- [ ] `labs/00-setup/` (build + initramfs + QEMU run + GDB)
- [ ] `kernel-notes/00-baseline.md`
- [ ] `grilling/week-00.md`
- [ ] `cheatsheets/environment.md`

## Week 1 — ARM64 boot path and EL transitions
- [ ] `kernel-notes/01-boot-arm64.md`
- [ ] `labs/01-boot-checkpoints/` (RAM trace buffer + dump)
- [ ] `grilling/week-01.md`
- [ ] `cheatsheets/boot.md`

## Week 2 — Device Tree in boot and early init
- [ ] `kernel-notes/02-devicetree-early.md`
- [ ] `labs/02-fake-device-dt/` (DT node + platform driver)
- [ ] `grilling/week-02.md`
- [ ] `cheatsheets/devicetree.md`

## Week 3 — `struct page`, buddy, zones, migrations
- [ ] `kernel-notes/03-mm-buddy.md`
- [ ] `labs/03-buddy-telemetry/` (buddyinfo + tracepoint)
- [ ] `grilling/week-03.md`
- [ ] `cheatsheets/mm.md`

## Week 4 — SLUB, vmalloc, CMA (intro)
- [ ] `kernel-notes/04-mm-slub-vmalloc-cma.md`
- [ ] `labs/04-kasan-slub-torture/` (KASAN + deliberate bug)
- [ ] `grilling/week-04.md`

## Week 5 — Scheduler: history, CFS, RT, deadline
- [ ] `kernel-notes/05-sched-overview.md`
- [ ] `labs/05-sched-tracing/` (wakeups/migrations + perf)
- [ ] `grilling/week-05.md`
- [ ] `cheatsheets/sched.md`

## Week 6 — Concurrency: locks, barriers, RCU
- [ ] `kernel-notes/06-concurrency-locks-rcu.md`
- [ ] `labs/06-lockdep-rcu/` (deadlock demo + RCU example)
- [ ] `grilling/week-06.md`
- [ ] `cheatsheets/locking-rcu.md`

## Week 7 — IRQ + softirq/workqueues
- [ ] `kernel-notes/07-irq-softirq-wq.md`
- [ ] `labs/07-irq-storm/` (storm simulator + tracing)
- [ ] `grilling/week-07.md`
- [ ] `cheatsheets/irq.md`

## Week 8 — Device model: kobject/sysfs/probe
- [ ] `kernel-notes/08-driver-core.md`
- [ ] `labs/08-sysfs-driver/` (sysfs attrs + unwind)
- [ ] `grilling/week-08.md`
- [ ] `cheatsheets/driver-core.md`

## Week 9 — DT: clocks/resets/regulators/pinctrl
- [ ] `kernel-notes/09-dt-resources.md`
- [ ] `labs/09-deferred-probe/` (resource deps + DT error case)
- [ ] `grilling/week-09.md`

## Week 10 — DMA + IOMMU
- [ ] `kernel-notes/10-dma-iommu.md`
- [ ] `labs/10-dma-mapping/` (map/unmap + cache sync bug)
- [ ] `grilling/week-10.md`
- [ ] `cheatsheets/dma.md`

## Week 11 — Block layer + I/O schedulers
- [ ] `kernel-notes/11-block-blkmq.md`
- [ ] `labs/11-io-profiling/` (perf + block tracepoints)
- [ ] `grilling/week-11.md`
- [ ] `cheatsheets/storage.md`

## Week 12 — VFS + page cache + writeback
- [ ] `kernel-notes/12-vfs-pagecache.md`
- [ ] `labs/12-vfs-tracing/` (open/read/write path + writeback congestion)
- [ ] `grilling/week-12.md`
- [ ] `cheatsheets/vfs.md`

## Week 13 — Networking (interview scope)
- [ ] `kernel-notes/13-net-skb-napi.md`
- [ ] `labs/13-net-tracing/` (NAPI/softirq load)
- [ ] `grilling/week-13.md`
- [ ] `cheatsheets/net.md`

## Week 14 — Power management: suspend/resume + runtime PM
- [ ] `kernel-notes/14-pm-suspend.md`
- [ ] `labs/14-suspend-resume/` (pm_ops + “3rd resume hang” bug)
- [ ] `grilling/week-14.md`
- [ ] `cheatsheets/pm.md`

## Week 15 — Debugging/tracing for production
- [ ] `kernel-notes/15-tracing-toolbox.md`
- [ ] `labs/15-observability/` (ftrace + perf + crash basics)
- [ ] `grilling/week-15.md`
- [ ] `cheatsheets/tracing.md`

## Week 16 — Real-Time Linux (PREEMPT_RT)
- [ ] `kernel-notes/16-rt-preempt.md`
- [ ] `labs/16-rt-latency/` (irqsoff/preemptoff + jitter)
- [ ] `grilling/week-16.md`
- [ ] `cheatsheets/rt.md`

---

## Staff-level sprints (optional)
### Sprint A — Boot time reduction
- [ ] `kernel-notes/sprint-a-boot-time.md`
- [ ] `labs/sprint-a-boot-time/`
- [ ] `grilling/sprint-a.md`

### Sprint B — Regression after kernel update
- [ ] `kernel-notes/sprint-b-regression.md`
- [ ] `labs/sprint-b-bisect/`
- [ ] `grilling/sprint-b.md`

### Sprint C — Vendor divergence vs upstream
- [ ] `kernel-notes/sprint-c-divergence.md`
- [ ] `grilling/sprint-c.md`

### Sprint D — Debug framework for a new SoC
- [ ] `kernel-notes/sprint-d-debug-framework.md`
- [ ] `grilling/sprint-d.md`

---

## Capstone (2 weeks)
- [ ] `labs/capstone/` (DT device + driver + 3 bugs)
- [ ] `kernel-notes/capstone-debug-playbook.md`
- [ ] `grilling/capstone.md`
- [ ] `cheatsheets/capstone.md`
