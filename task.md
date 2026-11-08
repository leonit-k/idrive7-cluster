# Frontend-only implementation handoff

Transform the Cluster Centre display to match the dashboard style in `cluster_inspiration.jpg`. Add a default-first “Vehicle overview” page with the reference’s dark LCD look, blue “Vehicle” header, fuel range at the top, compact meter rows, and a footer with time and outside temperature. Use existing readings only: boost, coolant temperature, oil temperature, fuel range, electric power (`kw`), and battery charge (`soc`). Show electric power and charge together in the last row. Oil pressure is not currently available; do not substitute a new signal or diagnostic request for it.

## Constraints

- Use only readings the repo already produces. Do not add vehicle signals, diagnostic requests, providers, or change signal manifests.
- Reuse the existing app data and renderer interfaces.
- Preserve current app behavior outside the requested visual change.

## Repo context

- This repo contains three BMW iDrive 7 apps: Cluster Info (HUD/map overlay), Cluster Layouts (full cluster layouts), and Cluster Centre (custom centre pages).
- Vehicle readings reach the apps mainly through BMW’s named SOME/IP signal interface. Some existing readings also come from UDS diagnostic requests or from other apps via shared files. The apps do not decode raw CAN frames.
- Cluster Layouts and Cluster Centre share their vehicle-data implementation: `shared/data.js` is the common source, copied into each app by `node tools/sync_shared.js`.
- `kit/runtime/car.js` provides common readings: speed, RPM, gear/stage, oil and coolant temperatures, torque, and longitudinal/lateral G force.
- `shared/data.js` adds existing readings such as drive mode/programme, outside temperature, gearbox temperature, battery temperature, power distribution, fuel/electric ranges, steering angle, yaw rate, engine status, boost, intake/fuel/timing values, and (where available) xDrive and hybrid battery data. Some are car- or provider-dependent and may be unavailable in a particular vehicle.
- Cluster Info has a smaller data set and its own overlay implementation.

## Likely frontend files

- Cluster Layouts: `apps/cluster_layouts/layout_*.js`, `layout_common.js`, `engine.js`, `shaders.js`
- Cluster Centre: `apps/cluster_centre/pages.js` and page/layout rendering code
- Cluster Info: `apps/cluster_info/overlay.js`

## Preview and verification options

- Layout PNG: `node tools/preview.js --layout digital_dash --look sport --out preview.png`
- Centre page PNG: `node tools/preview_centre.js --page temps --out page.png`
- App behavior with stubbed signals: `node tools/test_app.js`, `node tools/test_centre.js`, or `node kit/tools/test_cluster_info.js`
- Built-app stub: `node tools/test_built.js` (requires the BMW SDK in `oap-sdk/`).
- PNG previews use sample data, not live car data. Confirm behavior on the head unit for real signal and renderer behavior.

Keep the implementation confined to Cluster Centre presentation and page settings. Do not change signal acquisition or diagnostics. The requested overview page is now implemented in `apps/cluster_centre/pages.js` and enabled first by default through `apps/cluster_centre/main.js` and `apps/cluster_centre/app.json`.
