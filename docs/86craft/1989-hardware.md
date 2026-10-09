# 86craft: late-1989 386DX hardware target

Research date: 2026-10-08. Status: source-verified recommendation, not a boot-tested VM or a measured FPS result. No game port or emulator behavior changes accompany this document.

## Repository

- Fork: https://github.com/MeatisOmalley/86Box
- Upstream: https://github.com/86Box/86Box
- Local checkout: `C:\Users\meat\Documents\craft\86craft\86Box`
- Research branch: `codex/1989-hardware-target`, based on upstream master commit `f60098841542e2e4abca4197e7343dc356523716`.
- `origin` fetches/pushes the user's fork; `upstream` fetches the original project. Upstream pushes are disabled locally.
- This is an independent Git repository inside the Townscraft workspace, not a submodule or a commit to Townscraft's master.

Keep the fork's master available for upstream synchronization; keep 86craft-specific work on its own branches. The stable release reference is 86Box v6.0 (`4fef696a4eead1d55a28d6ac0e5bd2864e5454da`); record the exact emulator revision for every future benchmark.

## Recommended starting combination

Scope: the Everex-class 386DX/33 discussed for this project, using hardware families available by the end of 1989. This is not a claim that a 386 was the absolute fastest computer sold in 1989, or that this unbenchmarked combination is definitively fastest.

| Component | Target / 86Box selection | Reason and limits |
| --- | --- | --- |
| CPU | Intel i386DX, 33 MHz (`i386dx`, speed `33`) | Full 32-bit CPU and memory bus; do not substitute a 386SX or later AMD 40 MHz part. |
| Motherboard | `[SiS 310] ASUS ISA-386C` (`asus386`) | Supported ISA 386 board family that ASUS dates to 1989. A functional proxy, **not an emulated Everex Step motherboard**. |
| RAM | 16 MB / 16384 KB | Defensible high-end Everex-class capacity. Ignore the emulator profile's larger 64 MB ceiling; it does not establish 1989 availability. |
| FPU | Intel 80387, select `387` | Separate floating-point coprocessor, nominally matching the 33 MHz CPU. Supported by the CPU profile. |
| Video | `Video 7 VGA 1024i (HT208)` (`v7_vga_1024i`), 512 KB | Period 16-bit ISA VGA/SVGA family; use its maximum documented memory option. Provisional choice, not a measured winner over other period VGA cards. |
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

A [Video Seven advertisement in BYTE, December 1989](https://www.worldradiohistory.com/Archive-Byte/80s/Byte-1989-12.pdf) documents VGA 1024i and its 256/512 KB options. The supported v2.19 ROM contains **01/18/90**. Later HP/OEM BIOS revisions are not evidence of period correctness. The original Sound Blaster is dated to 1989 by [Creative's history](https://us.creative.com/soundblaster/ourstory/) and its [tenth-anniversary announcement](https://sg.creative.com/corporate/pressroom?id=6199), which places launch at Fall COMDEX 1989.

The [Western Digital installation guide](https://www.scribd.com/document/741751007/WD1007V) is dated 1989. The [CDC Wren V brochure](https://bitsavers.trailing-edge.com/pdf/cdc/discs/brochures/CDC_Wren_V_Brochure_Jan88.pdf) predates the cutoff and lists the 94186-383. Match image geometry to the actual model rather than interpreting its unformatted name as a 383 MB image-size requirement.

These public ROMs were inspected in memory for date strings; they were not installed or committed. A strict all-components-and-firmware-before-1990 claim remains unverified. Do not silently treat the proposed proxy as an exact Everex reconstruction.

## Source verification

At the pinned upstream revision:

- [Machine table](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/machine/machine_table.c): ISA-386C accepts a 386DX at 33 MHz and 16 MB RAM; no Everex machine entry was found.
- [CPU/FPU table](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/cpu/cpu_table.c): Intel i386DX 33 MHz references the FPU list containing `387`.
- [Video device](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/video/vid_ht216.c): VGA 1024i supports 256/512 KB and the selectable BIOS revisions.
- [Audio device](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/sound/snd_sb.c): original SB1, selectable DSP versions and port/IRQ settings. Preserve the early DSP; do not select the offered DSP 2.00 upgrade for this target.
- [ESDI controller](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/disk/hdc_esdi_at.c) and [drive profiles](https://github.com/86Box/86Box/blob/f60098841542e2e4abca4197e7343dc356523716/src/disk/hdd.c): WD1007V-SE1 and CDC94186383 are implemented.

Required ROM paths are defined in `src/machine/m_at_386dx.c`, `src/video/vid_ht216.c`, and `src/disk/hdc_esdi_at.c`. ROM availability alone does not constitute a successful POST or operating-system test.

## Coprocessor implications

Yes, this class of machine supported an Intel 80387. It is an x87 arithmetic coprocessor, not a graphics accelerator. Townscraft's current Makefile explicitly uses `-mno-80387` and integer/fixed-point code, so installing an FPU does not automatically make the current renderer faster.

After a PC port works, compare selected vertex transforms/projection or other suitable routines against fixed-point implementations. Include operand transfers and conversion overhead. Do not replace the entire rasterizer with floating point or promise an FPS improvement without measurements.

## Performance-test guardrails

1. Boot-test the chosen machine, RAM, FPU, video, disk geometry and SB before accepting a runnable profile. No runnable VM, guest OS, ROM pack or PC game build is supplied here.
2. Use the native 386 interpreter; disable "Use the 486 interpreter for 286 and 386 processors." Keep the default accurate timing choices and verify the host sustains full emulation speed. See [machine settings](https://86box.readthedocs.io/en/latest/settings/machine.html).
3. Use period HDD timing profiles, not host-speed RAM Disk mode. Record cold/warm region-cache state. See [disk settings](https://86box.readthedocs.io/en/latest/settings/hdd.html).
4. Do not claim exact Everex 64 KB cache behavior. `src/cpu/cpu.c` uses cache-enabled flags and shared cycle counts; this does not establish a calibrated simulation of the Everex board's finite cache hit/miss behavior.
5. Run a repeatable seed, route and camera sequence: walk level, walk looking down, walk looking up, and break/place blocks. Report each segment separately, including frame-time spikes, input-to-visible-edit latency, streaming I/O and audio-on/off costs.
6. Compare period VGA candidates with the actual backend workload before declaring a fastest card. Keep CPU/RAM/disk/interpreter settings fixed. Software rendering is likely CPU-sensitive, but VGA transfer costs must be measured too.

The 16 MB machine is the proposed **new PC high-end target**, not a retroactive change to Townscraft's existing minimum spec.
