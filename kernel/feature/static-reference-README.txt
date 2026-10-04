Fixed query-only SELinux reference for the Maga-King KernelSU fork

Upstream base: tiann/KernelSU main, cd4af89c43005f33df91ba7cb66e00e67a4d0c1b.
The fixed-reference implementation and blob generator are reused from
Maga-King/KernelSU-Next. Official KernelSU already provides selinux_hide and
the dynamic backup_sepolicy; no new query interception hooks are introduced.

Enable CONFIG_KSU_STATIC_SELINUX_REFERENCE=y when building the kernel.
The option defaults to n. KernelSU's normal runtime selinux_hide feature must
also be enabled. Disabled builds keep the original dynamic policy backup.

reference.policy is the same offline-sanitized query reference used by the
KernelSU-Next fork. It was rebuilt from OriginOS DSU partition CIL on
2026-10-04, excluding live root-module injections. Format 30, 2278330 bytes.
SHA256: 954080162a620cb6273a5f583d36db9bb3ada65a3ea0abfd2eba5bc8d5dc5849
It is NOT an enforcement policy and must never be flashed or loaded as one.

Only backup_sepolicy construction changes. The active enforcement policy
still comes from the upstream policy duplication and real KernelSU rules.
The reference uses its own policydb and sidtab, without borrowing allocations
from the active policy. Policy format, class names/numbers and permission
names/numbers must match; otherwise the code uses the upstream dynamic backup.
Allocation or parsing failure also falls back. There is no boot-time cleaning.

This changes the query view, not actual permissions or security risks.
It is not a promise of universal ROM compatibility or perfect root hiding.
Apps which pre-query permissions can change behavior, and the existing
upstream setcurrent hook also checks contexts against this reference.
ROM updates may require a new offline reference even if the ABI still matches.

No install-time network risk scanner was found in this upstream ksud source.
No ksud or Manager behavior was changed. The official Manager remains usable;
Action-Build keeps downloading its existing official Manager artifact.

The fork's separate compile-only workflow checks feature n/y on 6.6 and 6.12.
Its ko artifacts are compile evidence, not packages to load on this phone.
Those tests are not added to the Action-Build kernel workflow.
