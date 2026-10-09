# DOScraft hardware: 486DX/25 primary, Everex-class 386 port deferred

Research date: 2026-10-08; hardware priorities updated by user decision 2026-10-09. Status: source-verified development profiles, not boot-tested VMs or measured FPS results. No emulator behavior changes accompany this document. Main compiler target is now 486/x87; the Everex port is deferred until the main demake is satisfactory.

## Repository

- Fork: https://github.com/MeatisOmalley/86Box
- Upstream: https://github.com/86Box/86Box
- Local checkout: `C:\Users\meat\Documents\craft\86craft\86Box`
- Research branch: `codex/1989-hardware-target`, based on upstream master commit `f60098841542e2e4abca4197e7343dc356523716`.
- `origin` fetches/pushes the user's fork; `upstream` fetches the original project. Upstream pushes are disabled locally.
- This is an independent Git repository inside the Townscraft workspace, not a submodule or a commit to Townscraft's master.

Keep the fork's master available for upstream synchronization; keep 86craft-specific work on its own branches. The stable release reference is 86Box v6.0 (`4fef696a4eead1d55a28d6ac0e5bd2864e5454da`); record the exact emulator revision for every future benchmark.

## Recommended starting combination

Current scope: the **486DX/25 main demake** with an **Everex-class 386DX/33 long-term compatibility/port target**. This supersedes the previous 386-primary and immediate dual-support choices. The main executable may use 486-specific instructions and integrated x87; it need not run on a 386. Neither unbenchmarked combination is claimed definitively fastest, nor are the proxy motherboards/ROMs authenticated 1989 retail machines.

| Component | Target / 86Box selection | Reason and limits |
| --- | --- | --- |
| CPU | Primary: Intel i486DX, 25 MHz (`i486dx`, speed `25`). Deferred: Intel i386DX/33. | Full 32-bit CPUs. The main compiler targets `i486`; a future 386 executable must have its own ISA-safe build/backend. No Pentium/SSE instructions in the 486 build. |
| Motherboard | Primary: `[SiS 401] ASUS ISA-486` (`isa486`). Deferred: `[SiS 310] ASUS ISA-386C` (`asus386`). | Supported ISA proxies, **not exact Compaq Deskpro/Everex Step motherboards**. Both selected ROMs identify 05/05/91. No claim of calibrated real-board cache behavior. |
| RAM | 16 MB / 16384 KB on both development profiles | Keep the agreed budget. Emulator profile ceilings do not establish period retail configurations. |
| FPU | Primary: integrated 486DX x87 (`internal`). Deferred historical upgrade: Cyrix FasMath 83D87/33. | 86Box has no separate FasMath model/timing table. The old 386 diagnostic still selects Intel `387`, explicitly not Cyrix. No invented 50% timing adjustment; FasMath work is deferred with the 386 port. |
| Video | `Tseng Labs ET4000AX (ISA)` (`et4000ax`), 1 MB | User-selected original ET4000 ISA target, accepting Q4 1989 batches. Not ET4000/W32, VLB or PCI. The 1 MB development configuration and later emulator BIOS are not a verified reconstruction of a particular first-batch retail board; no measured FPS advantage is claimed. |
| HDD controller | `WD1007V-SE1 (ESDI)` (`esdi_at`) | Documented in 1989 and implemented by 86Box. Use a separate AT floppy controller; the SE1 is HDD-only. |
| HDD | `[ESDI] CDC 94186-383 (Wren V 383M)` (`CDC94186383`) timing model | Large period drive with an implemented timing profile; use actual disk timings, not the RAM Disk profile. The 383 MB label is **unformatted**, not filesystem capacity. |
| Sound | `Sound Blaster v1.0` (`sb`), port `0x220`, IRQ 7, DMA 1; early DSP 1.03 | Original late-1989 card, not SB Pro/16. OPL2 music, 8-bit mono PCM; 86Box's SB1 model also enables CMS sound. |
| Removable media | 3.5-inch 1.44 MB and 5.25-inch 1.2 MB floppies | Installation/recovery media, not the large-world save medium. |
| Input/display | AT keyboard, serial mouse on COM1, VGA multisync monitor | Avoid assuming PS/2 mouse hardware on an ordinary ISA AT board. |

Use the HDD for persistent world regions, player edits and saved friendly mobs. Generate or install terrain onto it, then cache nearby regions in RAM. That is an architectural recommendation, not implemented behavior. A period SCSI CD-ROM can be an optional distribution/music device, but an exact drive/controller pair still needs a date/ROM audit. Do not substitute a later ATAPI drive or make CD access the live-world hot path.

Start rendering tests at 320x200, 256 colors. Compare a tweaked 320x240 VGA mode later; neither VGA memory size nor a higher advertised SVGA resolution guarantees good 3D FPS. The PC video backend still has to be written: the Towns ISO cannot run here unchanged.

## Historical evidence and emulator substitutions

BYTE's Fall 1989 IBM special edition tested the Everex Step 386/33 with a 33 MHz 80387 and a 160 MB Control Data ESDI drive; its specification table gives a 16 MB RAM maximum and 64 KB cache. That establishes the CPU/FPU/memory class, not exact performance of an ASUS substitute. See the [contemporary review, "Megahertz Madness"](https://www.scribd.com/document/702250728/Byte-1989-11-IBM).

[ASUS's own history](https://www.asus.com/microsite/mb/asusmb1st/index.aspx) identifies ISA-386C as its 1989 motherboard. However, the currently available 86Box ROM `machines/asus386/ASUS_ISA-386C_BIOS.bin` has the reset-vector date **05/05/91**. The alternate ASUS 386/33-64K ROM is dated **04/30/90**. Neither ROM is proven 1989 firmware.

The new primary `isa486` profile is a supported ISA-only Socket 1 board accepting an Intel i486DX at 25 MHz. Its `machines/isa486/ISA-486.BIN` ROM identifies **05/05/91** (SHA-256 `3b6e985cb94a6d37963f481a07cd6b1c84a698d6ba41fe4166298532b7c9a2be`). It is a functional development proxy, not a Compaq reconstruction. The Compaq Deskpro 486/25's November 1989 announcement does not prove 1989 availability: a [contemporary report](https://www.computerwoche.de/article/2780242/mini-plaene-compaq-wird-pc-markt-zu-eng.html) gives January 1990. A strict purchasable-in-1989 hardware/firmware claim remains unresolved despite choosing the 486 architecture for development.

The provisional Video 7 choice was superseded on 2026-10-09 by the user's acceptance of Q4 1989 ET4000 ISA batches. The [Tseng manufacturer databook](https://ardent-tool.com/datasheets/Tseng_ET4000_Databook_1990.pdf) describes the original controller. 86Box implements `et4000ax` independently of the later W32/local-bus variants. Its standard `ET4000_V8_06.BIN` identifies **04/15/92 V8.06X** (SHA-256 `db2f8461274922026acc8b2a4fe1b0675d8ce7d6430108b74196527f36ed2c09`); `ET4000.BIN` identifies **04/07/93 V8.01X**. The DOS scaffold pins V8.06X as a firmware proxy. Neither is original 1989 firmware. Use indexed 256-color VGA, not later high-color RAMDAC features; exact first-batch board/RAM provenance remains unresolved. The original Sound Blaster is dated to 1989 by [Creative's history](https://us.creative.com/soundblaster/ourstory/) and its [tenth-anniversary announcement](https://sg.creative.com/corporate/pressroom?id=6199), which places launch at Fall COMDEX 1989.

The [Western Digital installation guide](https://www.scribd.com/document/741751007/WD1007V) is dated 1989. The [CDC Wren V brochure](https://bitsavers.trailing-edge.com/pdf/cdc/discs/brochures/CDC_Wren_V_Brochure_Jan88.pdf) predates the cutoff and lists the 94186-383. Match image geometry to the actual model rather than interpreting its unformatted name as a 383 MB image-size requirement.

These public ROMs were inspected in memory for date strings; they were not installed or committed. A strict all-components-and-firmware-before-1990 claim remains unverified. Do not silently treat the proposed proxy as an exact Everex reconstruction.

## Source verification

At the pinned upstream revision:

- [Machine table](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/machine/machine_table.c): ISA-486 accepts a Socket 1 i486DX/25 and 16 MB RAM; ISA-386C accepts a 386DX/33 and 16 MB RAM. No exact Everex or Compaq Deskpro 486/25 entry was established. Primary selection was also checked against the stable v6.0 source.
- [CPU/FPU table](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/cpu/cpu_table.c): Intel i486DX/25 has `internal` FPU; Intel i386DX/33 offers `none`/`387`, not Cyrix FasMath. `src/cpu/cpu.c` selects Intel 387 timing for the `387` option.
- [Video device](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/video/vid_et4000.c): ISA ET4000AX supports 256/512/1024 KB and selectable BIOS revisions; the same selection is supported by the pinned v6.0 release.
- [Audio device](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/sound/snd_sb.c): original SB1, selectable DSP versions and port/IRQ settings. Preserve the early DSP; do not select the offered DSP 2.00 upgrade for this target.
- [ESDI controller](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/disk/hdc_esdi_at.c) and [drive profiles](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/disk/hdd.c): WD1007V-SE1 and CDC94186383 are implemented.

Required ROM paths are defined in `src/machine/m_at_socket1.c` (primary), `src/machine/m_at_386dx.c` (deferred), `src/video/vid_et4000.c`, and `src/disk/hdc_esdi_at.c`. ROM availability alone does not constitute a successful POST or operating-system test. The default DOScraft build/profile is 486DX/25. The old 386 scaffold is optional and is not a current playable-game release gate.

## Coprocessor implications

The primary 486DX has integrated x87; it is not upgraded by installing a separate 80387 or FasMath. For the deferred Everex target, the user requested the 1989 Cyrix **FasMath 83D87**. Its [manufacturer manual](https://www.arithmazium.org/classroom/lib/Cyrix_FasMath_83D87.pdf) documents 80387 pin/software compatibility and 20/25/33 MHz operation. The current 86Box version does not model this coprocessor separately. Retain the original Intel 387 diagnostic as a labelled proxy rather than misrepresenting Cyrix timing or blocking the main demake on emulator work.

An x87 unit is an arithmetic coprocessor, not a graphics accelerator. Townscraft's current Makefile explicitly uses `-mno-80387` and integer/fixed-point code. The DOScraft main compiler now enables 486/x87, but this does not itself convert the renderer to floating point or establish a speedup.

After a PC port works, compare selected vertex transforms/projection or other suitable routines against fixed-point implementations. Include operand transfers and conversion overhead. Do not replace the entire rasterizer with floating point or promise an FPS improvement without measurements.

## Performance-test guardrails

1. Boot-test the chosen machine, RAM, FPU, video, disk geometry and SB before accepting a runnable profile. No runnable VM, guest OS, ROM pack or PC game build is supplied here.
2. Use the genuine 486 CPU profile for the primary target, with dynamic recompilation disabled. On the deferred 386 profile, use the native 386 interpreter, not the optional 486 interpreter. Keep accurate timing choices and verify the host sustains full emulation speed. See [machine settings](https://86box.readthedocs.io/en/latest/settings/machine.html).
3. Use period HDD timing profiles, not host-speed RAM Disk mode. Record cold/warm region-cache state. See [disk settings](https://86box.readthedocs.io/en/latest/settings/hdd.html).
4. Do not claim exact Everex 64 KB cache behavior. `src/cpu/cpu.c` uses cache-enabled flags and shared cycle counts; this does not establish a calibrated simulation of the Everex board's finite cache hit/miss behavior.
5. Run a repeatable seed, route and camera sequence: walk level, walk looking down, walk looking up, and break/place blocks. Report each segment separately, including frame-time spikes, input-to-visible-edit latency, streaming I/O and audio-on/off costs.
6. Compare period VGA candidates with the actual backend workload before declaring a fastest card. Keep CPU/RAM/disk/interpreter settings fixed. Software rendering is likely CPU-sensitive, but VGA transfer costs must be measured too.

The 486DX/25 with 16 MB is the **primary PC target**, not a retroactive change to Townscraft's existing minimum spec. Everex compatibility is explicitly deferred until the main demake is satisfactory. Future hardware-only A/B tests should use the same 386-compatible executable on both CPUs; compare 486-tuned compiler/algorithm changes separately, rather than conflating hardware and software gains.
