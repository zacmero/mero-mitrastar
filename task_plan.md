# MitraStar repository reset — 2026-10-07

## Objective

Recognize and document the DSL-100HN-T1-NV, clean the copied Sagemcom
workspace while retaining reusable methods, and define Ethernet-first steps
toward executing a tiny program in RAM.

## Phases

1. Inventory the copied repository and current host topology — complete.
2. Document this unit and attempt source verification — complete; claims remain
   unverified because original sources could not be retrieved.
3. Preserve predecessor provenance and replace the active workspace — complete.
4. Write the direct-Ethernet runbook and staged investigation roadmap — complete.
5. Verify repository hygiene and document remaining prerequisites — complete.
6. Verify laptop SSH and inspect the newly connected Ethernet link — complete;
   MITRA-NET-001 records the verified connection and bounded TCP/HTTP baseline.

## Decisions

- User-reported UI identity is evidence about this unit; partner research is
  evidence about other units until independently checked.
- Preserve the original Git commit and a reference to its reusable Ethernet
  methodology. Retire old executable tooling with hardcoded Sagemcom targets.
- Do not change host networking until the actual cable-facing machine and
  interface are identified. The current host routes 192.168.15.1 through its
  household gateway, not a directly connected 192.168.15.0/24 interface.
- Keep raw captures, configuration exports, secrets, and build output out of
  the tracked workspace.
- Owner clarified current access is Wi-Fi, not Ethernet. Finish repo work
  before rewiring, SSH testing, or direct-device connection.
- `arch-local` is the laptop-to-main SSH alias. Do not assume it addresses the
  laptop from the main machine.
- Owner subsequently connected the laptop to the main network via Wi-Fi and
  to the MitraStar via Ethernet, and authorized SSH/link testing now.

## Errors / constraints

- Web search failed twice with a response decoding error. Use an alternative
  research transport; record inaccessible sources as unverified.
- Cable-facing laptop hostname/user/interface are not yet provided.
- Alternate search connector returned an empty error response. Direct search
  attempts did not identify the articles; Kitz returned bot verification and
  Hack N Roll's current homepage did not establish the cited investigation.
- First hygiene check found four untracked, previously ignored old probe
  artifacts outside the archive. Preserved them under .local/predecessor and
  repeated the check.
- Default-key SSH to cris@192.168.1.80 failed authentication. The existing
  mero_stb_isolated_lab key succeeded with IdentitiesOnly=yes. No tunnel needed.
- RTK/nmap are absent on the laptop. Main commands used rtk proxy ssh with
  native remote payloads; bounded source-bound stdlib TCP checks replaced nmap.

## Completion

Repo reset, documentation, roadmap, archive verification, and authorized direct
connection checks are complete locally. No Git commit/push has been made.
Next investigation: authenticated read-only UI inventory (MITRA-UI-002).
