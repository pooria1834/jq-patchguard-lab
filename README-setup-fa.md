# jq PatchGuard lab starter

## Three upstream revisions
- A c5fd64ba975ee35df17bb0c335efb00bffce4d91
- B c9a51565214eece8f1053089739aea73145bfd6b
- C 71c2ab509a8628dbbad4bc7b3f98a64aa90d3297

## Setup
1. `sudo apt-get install git autoconf automake libtool pkg-config make gcc flex bison libonig-dev`.
2. Clone upstream **next to** this lab: `git clone https://github.com/jqlang/jq.git ../jq-upstream`.
3. From this lab root: `scripts/import_jq_revision.sh c5fd64ba975ee35df17bb0c335efb00bffce4d91`.
4. Check that `jq-src/PGMakefile` and `tests/fixtures/jq/cases.yaml` exist.
5. Commit A to the new repo's main, then create test/stack-fix branch and import B and commit it.
6. Push test/stack-fix and open a PR against main.
7. Two GitHub workflows under `.github/workflows` must be on main **before** the PR is opened.
8. Expect stack fixed, heap remains => exit 10, provided the sanitizer witnesses reproduce.
9. For lab only, advance main to B, create test/heap-fix branch, import C, open a PR => expected PASS 0 if no additional issues.
10. Optionally introduce a deliberate JSON formatting regression from C to check exit 20.

## Build and arguments
jq uses Autotools. `PGMakefile` is a helper that runs `autoreconf`, `configure`, and `make jq`, using CFLAGS/LDFLAGS set by PatchGuard. The binary must be an ELF (`file jq`). Oniguruma headers are provided by libonig-dev; decNumber is enabled.

The corpus uses independently defined, per-case jq argv lists. The independent oracle is a deliberately limited Python implementation of the expected cases, not the candidate jq binary. It fails closed for unsupported formats. Verify expectations with independent documentation and manual testing, not by copying outputs from candidate binaries.

The second workflow only opens Draft regression PRs for valid new minimized witnesses and requires Actions permission to create pull requests. Validation happens in Docker; compilation of jq may take significantly longer than record-parser. The source commit sequence is maintained within the lab under `jq-src/`; the external upstream clone is not pushed.
