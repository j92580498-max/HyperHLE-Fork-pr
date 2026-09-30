# Fixing Game Launch in an HLE Emulator

## Inputs (fill in before running)
- Repository: `<REPO_URL>`
- Main branch: `<BRANCH>` (trunk / main / master)
- App to test with: `<PATH_TO_.app>`

## Task
The repository contains an HLE emulator for early iPhone OS apps (architecturally similar to touchHLE, written in Rust). Determine the supported platforms (Windows / macOS / Linux / Android) from the repository structure.

Problem: the specified .app bundle fails to launch or never reaches gameplay (black screen, hang, or crash). Find the root cause and fix the emulator code so the game reaches gameplay and the UI, sprites, and screens render correctly.

Diagnostic signal: if the frame counter in the logs (the number of present/swap calls) does not grow, the game is not displaying anything. Work out why the main loop or frame presentation (EAGL presentRenderbuffer, Core Animation compositing, SDL window swap) is not executing, and fix that spot.

## What to Do

1. **Project analysis**
   - Study the repository structure: emulator sources, frameworks, the GLES layer, dynamic libraries, option files, AGENTS.md, CONTRIBUTING.md, developer docs, dev scripts, and tests.
   - Identify the failure stage: Mach-O loading, dyld/linking, UIKit/UIApplication initialization, run loop, OpenGL ES/EAGL, resource loading (textures, PVRTC, nib, audio), memory/CPU emulation.
   - Run `cargo run --release -- <PATH_TO_.app>` with verbose logging. Find the first error, panic, unimplemented API (unimplemented function/selector), or the point where frames stop rendering.

2. **Documentation**
   - Read the repository's developer docs and AGENTS.md.
   - For API behavior, use the Apple documentation archive (Apple Developer Archive: iPhone OS 2.x/3.x, UIKit, OpenGL ES, Core Animation, Foundation).

3. **References**
   - Use GitHub Code Search (https://github.com/search?type=code) to find open implementations of similar mechanisms: EAGL/GLES compositing, texture formats, run loops, ObjC runtimes in emulators.
   - Use them only as a reference for behavior and architecture. Do not copy code: check licenses (MPL 2.0 etc.) and do not transfer code from third-party projects. Write your own implementation.

4. **Code fix**
   - Fix the cause that prevents the game from launching or reaching gameplay.
   - Verify that all graphics, sprites, and UI elements render correctly (no black screen, artifacts, wrong colors, or broken textures).
   - Do not break support for other apps. Run `cargo test` and `cargo clippy`.

5. **Testing**
   - Run the repository's existing tests and dev scripts.
   - If the repository has a tap-automation script (e.g. `tools/ai-tap-sequence.py`), use it. If not, find an equivalent or write a small script that performs a sequence of taps and checks that frames keep rendering and the process does not crash.
   - If errors occur, go back to step 4.

6. **Proof**
   - Run the fixed build and take screenshots showing the game reached gameplay and elements render correctly. If the repository already contains proof-screenshot examples, follow their format.
   - Attach the test script output and logs showing the frame counter increasing.
   - Without screenshots and test results, the fix is not considered confirmed.

7. **Pull Request**
   - Create a separate branch from `<BRANCH>`.
   - Commit your changes and add an entry to CHANGELOG.md, if one exists.
   - Open a PR in `<REPO_URL>` with base = `<BRANCH>`. If the repository is a fork, GitHub defaults to opening the PR against the parent repository. Make sure to select the repository specified above.
   - In the PR description: what was fixed, why, how it was tested (script/test output), and screenshots.
   - Do not push directly to the main branch.

## Definition of Done
The game launches on the emulator and reaches gameplay, all elements render without errors, the tests and tap script pass without failures, and the changes are submitted as a PR with screenshots and test results.
