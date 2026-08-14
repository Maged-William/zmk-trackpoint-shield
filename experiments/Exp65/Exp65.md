# Exp65: Driver rename cleanup + pipeline verification

**Status:** Success

## Hypothesis

After renaming `zmk-pmw3610-driver` → `zmk-trackpoint-driver` on GitHub and
moving the driver to I2C-only, removing every PMW3610 reference from the driver
repo, then pinning the renamed repo in the shield, the GitHub Actions pipeline
still produces all three firmware artifacts.

## Plan

1. Driver repo (`main`): drop `PMW3610_ALT` from `Kconfig`, drop the
   `pmw3610.c` source line + dead include dir from `CMakeLists.txt`, delete
   `src/pixart.h` and `dts/bindings/pixart,pmw3610-alt.yml`, rewrite README.
2. Shield repo (`Exp65`): point `config/west.yml` at `zmk-trackpoint-driver`
   revision `e523466` (latest main) instead of `zmk-pmw3610-driver` `6f84b62`.
3. Trigger the GH Actions build and verify all jobs succeed.

## Findings

- Driver cleanup committed & pushed as `e523466` (28 insertions, 217 deletions).
- Zero `pmw3610|pixart` references remain in the driver repo.
- The shield's `trackball@42` node has **no** `irq-gpios`: P0.06 is the
  EXT_POWER NPN gate on this shield (Exp48 wiring), so MOT-interrupt mode would
  conflict. The driver's polling fallback (`cfg->irq_gpio.port == NULL`) handles
  it. dabase (ATtiny85 variant, no power gates) does use MOT on P0.06 — checked
  and left as-is.
- Revision bump `6f84b62` → `e523466` finally exercises the Exp60/62/64 driver
  commits (curve/speed param re-write, no error backoff, MOT data-ready) which
  were never built through this repo before.
- dabase config also pinned to `e523466` (same west.yml fix, Exp64 branch).

## Result

All 5 jobs green: Fetch Build Keyboards, Build (poweron), Build (poweroff),
Build (settings_reset), Merge Output Artifacts. `firmware` artifact produced.

## Next steps

Flash the `poweron` artifact and verify MOT-free polling still drives the
cursor with the power-gated Pro Mini.
