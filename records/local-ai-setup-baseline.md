# Decision Record: local-ai-setup-baseline

## Metadata
- Created At: 2026-10-27
- Scope: Initial support and installation constraints for the local AI setup script targeting Ryzen AI Max+ 395.

## Notes
- This file is append-only discussion history.
- Do not add mutable tracking fields here (status, remaining work, open action items).
- Do not keep open-question backlogs here. If clarification is needed, ask in chat and append the resolved facts.
- If a fact becomes a binding implementation constraint, promote it to DECISIONS.yml.
- Keep each entry as short as the discussion allows.
- Evidence and detailed promotion metadata are optional; omit them when the entry stays clear without them.

Record boundary guidance:
- Keep one record focused on one cohesive decision theme. If an independent question, component, or follow-up appears, start a new record with a new `discussion-id` instead of extending unrelated history. Mention the prior record in `Evidence / references` when the new discussion continues from it.
- Use a practical reviewability signal: when a record reaches roughly 10 entries or 1,000 words, close it at a natural boundary and continue in a new discussion ID. This is a heuristic, not a hard parser-enforced limit; stay in the current record only when the next same-theme entry remains concise and reviewable.

Append rules:
- Append at EOF only; do not edit earlier sections.
- Do not add status tracking or remaining-work items.

## Entry List

### Entry 0001 (2026-10-27)
- Why now: This is the first discussion for replacing the starter decision list with project-specific constraints. The user explicitly limited this turn to discussion; no decision promotion or implementation is performed.
- Findings / trade-offs:
  - The root `DECISIONS.yml` is still the template and declares itself the canonical project-level decision list. No installer, test script, prior discussion record, or scoped decision-file rule was found.
  - The user set Ubuntu 24.04 LTS and Ryzen AI Max+ 395 as the target, with llama.cpp, Ollama, vLLM, one Stable Diffusion WebUI, and ComfyUI. `install.sh` is to run the full setup without arguments and show usage with `--help`; remote `curl | bash` use should install local components under the invoking project directory. ROCm and OS dependencies may use `sudo`; AI applications should be user-owned. Compatible existing versions should be preserved, and incompatible versions should not be silently changed.
  - Clarification: Python versions should be chosen per application. Use `proto` when available or `mise` when available; install app-specific Python/PyTorch environments when required. Exact behavior when both or neither manager is present remains for validation. Versions should be pinned and installation should be non-destructive.
  - AMD's current ROCm 10.1.0 compatibility matrix lists Ryzen AI Max+ 395 (`gfx1151`) and Ubuntu 24.04.5 with HWE kernel 7.0. This is ROCm hardware/OS support, not a vLLM wheel compatibility statement. The Ryzen-specific AMD PyTorch instructions still document a production tuple of ROCm 7.2.1, PyTorch 2.9.1, and Python 3.12, and require kernel 6.14-1018 or newer. The differing documentation/version scopes mean the exact host runtime and kernel must be reconciled before pinning.
  - vLLM's stable installation docs list ROCm 7.0 wheels for vLLM 0.14–0.18 and ROCm 7.2.1 wheels only for nightly builds; Ryzen AI Max support requires ROCm 7.0.2 or newer. The latest vLLM developer docs list a ROCm 10.0 nightly variant, not a 10.1 wheel. vLLM recommends a fresh Python environment because its wheel bundles PyTorch and may be binary-incompatible with other ROCm/PyTorch builds; its docs direct users to build from source for a different ROCm version. The `ROCm/vllm` repository says it is retired and directs users to upstream `vllm-project/vllm`.
  - Ollama's AMD support docs list Ryzen AI Max+ 395 and require ROCm v7 on Linux, but the exact combination with ROCm 10.1 needs validation. ComfyUI documents manual installation for AMD GPUs on Linux.
  - SD.Next is the preferred candidate for the additional WebUI: its documentation explicitly supports the ROCm backend and recommends Python 3.12, and its upstream repository was pushed on 2026-10-26. Forge Neo is also active, but its project page says AMD GPU issues are not supported, making it a weaker fit for this target. If SD.Next fails validation on `gfx1151`, ComfyUI alone remains the fallback.
- Current conclusion: Candidate direction, not yet binding: install ROCm/OS dependencies at host level with `sudo`; keep AI applications and per-app Python/PyTorch environments user-owned within the invoked project; pin tested versions and preserve compatible existing installations; prefer SD.Next plus ComfyUI for image generation. If no compatible stable vLLM wheel exists for the selected ROCm, build a fixed commit from upstream vLLM only after validating the build and GPU smoke test. `install.test.sh` is expected to test without `sudo` or real system installations. Exact package versions, manager precedence/failure behavior, and the vLLM source-build target remain subject to discussion-validation.
- Promotion to DECISIONS.yml: none
- Evidence / references:
  - [AMD ROCm 10.1.0 compatibility matrix](https://rocm.docs.amd.com/en/latest/compatibility/compatibility-matrix.html)
  - [AMD PyTorch for Ryzen installation](https://rocm.docs.amd.com/projects/radeon-ryzen/en/latest/docs/install/installryz/native_linux/install-pytorch.html)
  - [vLLM stable GPU installation](https://docs.vllm.ai/en/stable/getting_started/installation/gpu/) and [latest developer GPU installation](https://docs.vllm.ai/en/latest/getting_started/installation/gpu/)
  - [Ollama GPU support](https://docs.ollama.com/gpu), [ComfyUI](https://github.com/Comfy-Org/ComfyUI), and [SD.Next installation](https://vladmandic.github.io/sdnext-docs/Installation/)
  - [Forge Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo) and [retired ROCm/vLLM repository](https://github.com/ROCm/vllm)

## Append Template (Copy and Append at EOF)

### Entry {next-sequence} ({timestamp})
- Why now:
- Findings / trade-offs:
- Current conclusion:
- Promotion to DECISIONS.yml:
- Evidence / references (optional):

### Entry 0002 (2026-10-27)
- Why now: The user refined the runtime-version direction and confirmed SD.Next as the additional image-generation UI before discussion validation.
- Findings / trade-offs: Within Ubuntu 24.04 LTS, prefer the newest ROCm 10.x release that is supported for Ryzen AI Max+ 395 and can be validated with the selected application set. Preserve the previously agreed pinned, non-destructive installation policy: resolve and test an exact version before pinning it, rather than following a moving latest version during each install. If vLLM has no suitable stable wheel for the selected ROCm release, a fixed upstream commit source build is the preferred fallback, subject to build and GPU smoke testing. SD.Next is approved; no other scope changes were requested. Focus validation on the common host ROCm/runtime boundary, per-app Python/PyTorch isolation, Ubuntu 24.04 kernel requirements, and the vLLM build path. Other OS releases and model downloads remain out of scope.
- Current conclusion: Candidate direction refined to prioritize the newest validated ROCm 10.x release within Ubuntu 24.04 LTS, with exact pins and no silent upgrades. Retain the five targets as llama.cpp, Ollama, vLLM, SD.Next, and ComfyUI. Validation must determine whether any target needs a bundled runtime or a source build rather than assume that ROCm SDK compatibility alone proves application compatibility.
- Promotion to DECISIONS.yml: none
- Evidence / references (optional): User clarification in this discussion; see Entry 0001 for the compatibility sources reviewed.

### Entry 0003 (2026-10-27)
- Why now: The pre-promotion audit found that one host ROCm user-space version may not satisfy every application, and the user clarified the acceptable isolation boundary.
- Findings / trade-offs: The user approves app-specific ROCm user-space runtimes where needed, in addition to app-specific Python/PyTorch. This allows the host-level ROCm driver/runtime to prioritize the newest supported 10.x release while each application uses a pinned compatible user-space stack. The host driver still has to support every selected app runtime; that compatibility cannot be inferred from the ROCm SDK hardware matrix alone.
- Current conclusion: Candidate direction is now one supported host ROCm/driver on Ubuntu 24.04 LTS, prioritizing the newest validated 10.x release, with pinned per-app ROCm user-space runtimes permitted when required. No app may be silently omitted or trigger a host downgrade when no validated path exists.
- Promotion to DECISIONS.yml: none
- Evidence / references (optional): User clarification in this discussion; AMD ROCm installation documentation describes both system-wide and Python virtual-environment installation scopes.

### Entry 0004 (2026-10-27)
- Why now: Pre-promotion validation was requested after the user clarified ROCm 10.x priority, per-app ROCm user-space runtimes, and SD.Next approval.
- Findings / trade-offs:
  - Landscape coverage was checked across the project baseline and official/project documentation for the Ubuntu/Ryzen hardware matrix, AMD PyTorch, vLLM stable and developer wheels, Ollama AMD support, llama.cpp HIP builds, SD.Next ROCm/Python support, and ComfyUI AMD support. The bounded scan covers all five requested applications and the installer/runtime boundaries; no existing implementation or tests were available to review.
  - The narrowed focus is justified: host driver/OS support must be distinguished from app-specific ROCm user-space, PyTorch, and wheel support. SD.Next remains the image WebUI candidate because it documents ROCm support; ComfyUI remains the second image application. Other OS releases and model downloads remain outside scope.
  - The direction still serves the original objective: keep all five applications in scope, prioritize the newest validated ROCm 10.x release on Ubuntu 24.04 LTS, pin exact versions, permit per-app ROCm/Python/PyTorch environments, and use a pinned upstream vLLM source build when no suitable stable wheel exists. This is a version-selection policy, not a claim that every application already supports ROCm 10.1.
  - Current constraints do not contradict this direction: the project-level `DECISIONS.yml` has only its starter example and establishes itself as canonical. Project-specific rules belong in `DECISIONS.yml`; the records retain rationale, and the `AGENTS.md` additions are only an index.
  - Remaining implementation risk is explicit rather than directional: the AMD hardware matrix alone does not prove app compatibility; vLLM's current wheel matrix does not list 10.1, Ollama documentation still describes ROCm v7, Ryzen PyTorch instructions document 7.2.1, and llama.cpp documents a generic HIP path without a `gfx1151`/10.1 guarantee. Each selected user-space runtime must be compatible with the common host driver and verified before installation. If no verified route exists for a required target, report it without silently skipping the app or downgrading the host.
- Current conclusion: PASS for the discussion direction; exact package versions and executable compatibility remain for implementation-time verification. Promotion targets to prepare are: `platform-001-supported-host` (Ryzen AI Max+ 395 and Ubuntu 24.04 LTS); `platform-002-rocm-version-policy` (newest validated 10.x host release, exact pins, app-specific ROCm user-space allowed, explicit compatibility failure); `runtime-001-per-app-python-pytorch` (app-specific versions managed through existing `proto`/`mise`); `applications-001-supported-ai-stack` (llama.cpp, Ollama, vLLM, SD.Next, ComfyUI); `installation-001-installer-contract` (no-argument setup, `--help`, remote use, local app scope, privilege boundary, and non-destructive behavior); and `testing-001-install-test` (test without system installation side effects). No decision is promoted in this validation step.
- Promotion to DECISIONS.yml: none
- Evidence / references (optional):
  - [llama.cpp HIP build documentation](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
  - [ROCm 10.1 installation methods](https://rocm.docs.amd.com/en/latest/install/rocm.html)
  - See Entry 0001 for the AMD, vLLM, Ollama, SD.Next, ComfyUI, and Forge references reviewed.
