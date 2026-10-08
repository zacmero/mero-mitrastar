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

## Follow-up — MITRA-UI-002

Owner committed/pushed the reset and requested proceeding with the management
inventory. The working tree was clean at the start of this phase.

7. Recheck laptop/device identity and fetch observed frame/menu pages — complete.
8. Inspect read-only status, version, diagnostics, and management surfaces — complete;
   active browser login confirmed and protected pages inspected.
9. Record capabilities, access barriers, and the next shell-access experiment —
   complete; MITRA-UI-002 written, temporary browser/tunnel removed, checks passed.

Do not assume the owner's browser login authenticates a separate HTTP client.
Follow observed UI resources; do not submit configuration forms.

## Follow-up errors resolved

- HTTP helper initially imported SimpleCookie from html.cookies; corrected to
  http.cookies before any login. Noninteractive tool stdin was closed; used an
  echo-disabled transient PTY for sensitive stdin.
- Inactive uiApply login helper did not match the actual clicklogin handler.
  Active flow includes a page-issued SID in the MD5 input. Mero Browser executed
  the real login flow successfully; the owner-supplied password was valid.
- Wrapper's installed version accepts snippets on stdin, not -c. Used the
  repo wrapper's supported stdin interface, retaining the Mero workspace contract.
- Browser frame coordinate filling was unreliable; native CDP DOM focus and a
  verified population check succeeded before submission.

## MITRA-UI-002 completion

Authenticated inventory is complete. No visible shell control was found; next
is MITRA-ACCESS-003. Dedicated browser/daemon/tunnel/profile and the remote
transient helper were removed. Local docs/evidence/credential checks passed.
New repository changes remain local and uncommitted.

## Follow-up — MITRA-ACCESS-003: diagnostic handler investigation

Owner authorized inspecting the diagnostic route first and emphasized the
reported successful Snake firmware modification as a retained alternative.

10. Map active diagnostic validation, request fields, and result handling — complete.
11. Recheck Ethernet identity, authenticate, and run one loopback ping — complete;
    local TraceRoute and DNS lookup also completed with bounded targets.
12. Record backend evidence/limitations and compare next steps with Snake route —
    complete; see docs/experiments/mitra-access-003.md. No shell obtained.

Keep this experiment bounded. Do not submit firmware, change router policy,
reset/reboot, or test disruptive payloads. Browser login credentials remain
authorized from the previous turn and must stay out of commands/tracked files.

Source retrieval: built-in web search still returns a response decoding error.
Requested original article URLs while independent diagnostic work proceeds.

Pulled source-index commit 076d8fb with local changes preserved. Original Boina
articles were retrieved via the author's Medium RSS feed. His Snake route uses
physical SPI extraction/reprogramming, not an Ethernet shell exploit. Diagnostic
baseline is complete; server-side validation and this unit's ABI remain unknown.
# Follow-up — network/software sequence

Owner requested recording and executing all five network/software stages
methodically. The detailed bounds and WAN visibility alternatives are in
docs/tooling/network-software-plan.md. Persistent router/network changes remain
outside this instruction.

13. MITRA-SERVICES-004: full TCP coverage and initial UDP discovery — complete;
    longer TCP 161 check and any justified protocol follow-ups remain open.
14. MITRA-WEB-005: initial referenced resource map — complete; nine pages,
    six scripts, 55 candidate paths; server-side implementation unavailable.
15. MITRA-DIAG-006: harmless variants — complete; supplied -s 0 changed ping
    payload to zero bytes. General command handling and shell access remain open.
16. MITRA-PASSIVE-007: two bounded LAN captures — complete; no suitable
    router-originated WAN request identified and the DSL path is unobserved.
17. MITRA-FW-008: initial official source check — complete; exact-build source
    identification and offline analysis remain open.

The owner stopped Codex router testing, proposed a Gemini handoff, then asked
to finish documentation here first. No message was sent to Gemini and no
marker-command test was submitted. See docs/experiments/network-software-004-008.md.

18. Owner subsequently requested the complete handoff file and commit/push —
    HANDOFF.md prepared with access/restoration procedures, evidence, next tests,
    interpretation criteria, and private local prerequisites. The owner will
    instruct Gemini directly. No new device testing or Herdr prompt was issued.
19. MITRA-DIAG-009: TCP 161 recheck and command-punctuation comparison — complete;
    TCP 161 confirmed timeout at 2.0s; '127.0.0.1; printf ...' returned empty
    output (no execution demonstrated). Baseline ping confirmed functional.
20. MITRA-PASSIVE-010: clean 30-second idle capture and action attribution — complete;
    autonomous IPv6 Router Advertisement/RDNSS discovered; no provisioning request in the observed interval.
21. MITRA-RES-011: offline resource and handler inventory — complete; 63 paths and
    603 strings audited in Sophia; absence there did not cover the later-discovered legacy interface.
22. MITRA-FW-012: Firmware and vulnerability research — complete; no exact-build
    firmware/GPL acquired; historical SSH route not reached, build applicability untested.
23. MITRA-IPV6-013: eight IPv6 link-local ports checked — complete; only port 80 open among those;
    port 7547 actively refused (RST); ports 21, 22, 23, and 443 remain filtered.
24. MITRA-BACKUP-014: Legacy configurator, support authentication, and romfile export — complete;
    authenticated user 'support', downloaded romfile.cfg (93,315 bytes), confirmed internal ACL.

25. Review/publication of experiments 009–014 — complete documentation prepared;
    export hash/ACL fields verified, scope/daemon/firmware claims corrected.
    Separate master worktree used because the other agent is on romfile-analysis.
26. MITRA-SSH-015 — complete for initial access: owner-authorized native LAN SSH opened Dropbear and the support vendor console. Software version and uptime queried. Linux shell and persistence remain unverified. See docs/experiments/mitra-ssh-015.md.

- [x] MITRA-SSH-015: read native SSH controls and identify handler, session key, client restriction, and rollback.
- [x] Obtain explicit policy-change instruction; submit restricted LAN SSH and verify live page, Dropbear banner, and authenticated support vendor console.
- [x] Verify export freshness and inventory supported read-only console commands (016/018).
- [ ] Controlled saved-policy persistence test remains unperformed; short uptime with retained SSH is supporting evidence only.
- [x] Archive owner-supplied board photographs; acquisition power state was not independently observed.

27. MITRA-CLI-016 — complete: read-only vendor command map and system/network snapshots.
28. Pending: establish exact-build console/SSH/telnetd handler behavior through matching code or firmware; do not invoke unexplained flags.

29. MITRA-BOARD-017 — complete: one canonical checkout, 17-photo archive/index/hash verification, visual part identification.
- [x] Archive owner board photographs in this repo, preserving original HEIC bytes.
- [ ] Measure UART candidate ground, logic voltage/activity, and pin assignments before adapter attachment.

# Software follow-up round — complete

30. Complete: reverified laptop/device topology and existing SSH/HTTP state.
31. Complete: native backup generation took 14.229 seconds; fresh export matches live restricted LAN SSH.
32. Complete for this round: authenticated vendor exec/SFTP requests rejected; route display and referenced web status queried. Primary-source search did not establish telnetd flag semantics.
33. Complete: MITRA-SOFTWARE-018 records results, limitations, private hashes, and next questions in the canonical repo.

No reset/reboot, policy change, service activation, firmware write, or hardware hookup is included in this round.

34. Next software round: exact-build firmware/console handler evidence and remaining referenced status/log views. No matching firmware acquired yet; do not infer `sys telnetd -t` behavior from unrelated implementations.
