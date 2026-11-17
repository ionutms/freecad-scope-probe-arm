# Scope probe arm

A 3D-printable articulated arm that holds an oscilloscope probe. **One tensioned wire runs through the centre of every ball joint.** Pulled tight, the wire presses each ball into its conical seat and the friction locks the whole arm at once; slacken the wire and every joint moves freely. There are no screws, bearings or springs in the joints: only printed parts, one wire, and a printed screw at the base that tensions it.

This is the **wire pen repo** (working title: wire pen probe stand). Like [freecad-jumper-wire-pen](https://github.com/ionutms/freecad-jumper-wire-pen) it is **one FreeCAD macro that makes every printed part**, designed for FDM printing without supports. The pose panel uses the rotation gizmo of [freecad-wire-harness](https://github.com/ionutms/freecad-wire-harness).

![The start pose: the second arm folded back over the first, the probe standing up beside the base](images/start_pose.png)

> **Status:** not test-printed yet. In FreeCAD 1.1 on Windows every part is a valid single solid, the neutral pose and the start pose have no overlaps, and the wire path is clear of the material. The holding margins are calculated, not measured, and in the worst case they are **below 1** (see [Holding margins](#holding-margins-read-this-before-printing)).

## The arm

![The arm stretched out (neutral pose)](images/stretched_iso.png)

Default chain, two booms (joint names in brackets):

```
base cup -> M0 -> DC -> M1 -> BOOM 1 -> M2 -> DC2 -> M3 -> BOOM 2 -> M4 -> DC3 -> M5 -> probe holder
  [J0a]    [J0b] [J1a] [J1b]   [T1]    [J2a] [J2b] [J2c] [J2d]  [T2]  [J3a] [J3b] [J3c] [J3d]
```

14 joints. A **J** joint is a ball in a cone seat (tilt up to 25 degrees in any direction, plus spin). A **T** joint is the twist of a boom about its rod. Stretched out, the probe holder is about 490 mm from the mast; folded (the picture above) it stands next to the base.

![Top view of the stretched arm](images/stretched_top.png)

## Parts

All printed parts are single solids; the masses are for 100 % fill in PETG (1.27 g/cm3). Sizes are the bounding box of the STEP file in mm.

| Part | Print | Each | Model | What it does |
| --- | --- | --- | --- | --- |
| Stand base | 1 | 109.6 g | [stand_base.step](models/stand_base.step) | Square deck (130 x 130 mm plus a socket on one side), 20 mm high, open underneath: the pocket takes the ballast. It holds the stand cup (the first ball seat) and the wire path: two fixed rounded drums between lugs, no bearings. |
| Stand end | 1 | 21.6 g | [stand_end.step](models/stand_end.step) | The tensioner at the base: it stands on the deck, with two ears in two slots, and carries the slider and the screw. |
| Slider | 1 | 6.1 g | [slider.step](models/slider.step) | Holds the crimp ferrule at the end of the wire and runs in the stand end. |
| Tension screw | 1 | 5.6 g | [tension_screw.step](models/tension_screw.step) | Printed thread with a knob; turning it pulls the slider and tensions the wire (400 N at full size). |
| M link | 6 | 18.1 g | [m_link.step](models/m_link.step) | Ball - rod - ball, 55 mm between the ball centres by default (36 .. 120 mm), with stop rings that limit the tilt. |
| DC connector | 3 | 10.9 g | [dc_connector.step](models/dc_connector.step) | Two cups back to back, 49 mm between the ball centres. |
| Boom end piece | 4 | 19.8 g | [end_piece.step](models/end_piece.step) | The elbow of a boom: a cup block and a bend for the wire, hollow, with open windows. |
| Boom rod | 2 | 4.5 g | [rod.step](models/rod.step) | Joins the two end pieces of a boom; a boom is 35 mm from cup centre to cup centre. |
| Probe holder | 1 | 24.6 g | [probe_holder.step](models/probe_holder.step) | The last cup, with a pocket for the wire ferrule and a C-clamp for a 13 mm probe body. |
| Clamp screw | 1 | 1.5 g | [clamp_screw.step](models/clamp_screw.step) | Printed screw that closes the clamp by hand. |

Not printed: about 860 mm of wire (a 2.5 mm stainless wire or string) with two crimp ferrules, and the ballast for the pocket under the base (see [Known limits](#known-limits)). Total printed weight with the default settings: **398.5 g**.

## 3D models

The `models/` folder has a STEP file for every printed part, made at the default sizes. The files do not include the wire, the ferrules or the stand-in probe. The base, stand end, slider, tension screw, probe holder and clamp screw are turned the way they are printed (flat on the bed plane z = 0). The M link, DC connector, end piece and rod are in the frame of the macro (the ball centres are on the z axis), not in a print orientation.

To make the files again after changing a setting, run this in the FreeCAD Python console (it builds the model without the panel and writes the folder):

```python
import os
mac = os.path.join(App.getUserMacroDir(True), "ProbeStand_OneBoom_Combined.FCMacro")
src = open(mac, encoding="utf-8").read().replace("OPEN_PANEL = True", "OPEN_PANEL = False").replace("RUN_CHECKS = True", "RUN_CHECKS = False")
ns = {"__name__": "__main__", "__file__": mac}
exec(compile(src, mac, "exec"), ns)
ns["export_step"](r"C:\path\to\models")      # one STEP per part; the console prints how many of each to print
```

`ns["export_stl"](folder)` writes STL files in the same way, but it writes every M link and DC connector separately at its place in the assembly; use the STEP files for single parts.

## Install and run

1. Install [FreeCAD](https://www.freecad.org/) (1.1 was used).
2. Copy `ProbeStand_OneBoom_Combined.FCMacro` into your FreeCAD macro folder (**Macro > Macros...** shows it).
3. Copy `wire_harness.py` from [freecad-wire-harness](https://github.com/ionutms/freecad-wire-harness) into the same folder. It draws the rotation gizmo. Without it the panel still opens, but the pose can then only be set from the Python console (`CTRL.set_value`).
4. **Macro > Macros...**, pick `ProbeStand_OneBoom_Combined`, **Execute**.

The first run takes about 25 to 30 seconds. The slow parts (the threaded parts, the base, the holder) are cached in the temporary folder `ProbeStandCache`, keyed by the text of the macro, so later runs take a few seconds. The macro builds the model, prints a report (volumes, masses, wire length, holding margins) and opens the **Probe stand** panel.

## Posing the arm

- Pick a joint in the drop-down list (the `<` and `>` buttons step through them) or click a part, then drag a ring of the gizmo. Every move is checked for new collisions and stops at the last clear angle.
- **Gizmo step** sets the angle of one arrow-key step and the snap of a ring drag; **Gizmo size** scales the gizmo.
- **Reset joint** and **Reset all** go back to the neutral (stretched) pose. The arm starts in `START_POSE`, the pose in the first picture.
- **M length** + **Rebuild** changes the length of all M links (36 .. 120 mm) and rebuilds the model (about 20 seconds). The pose is kept if it is still free of collisions.

## Settings

All settings are plain variables at the top of the macro.

| Setting | Default | Meaning |
| --- | --- | --- |
| `BALL_D` | 26 | Ball diameter of the finished model; everything scales with it (8 = a miniature, not rechecked). |
| `NBOOM` | 2 | Number of booms (1 .. 3). |
| `ARM_LINKS` | 2 | What joins one boom to the next: 2 = an M - DC - M stack, 0 = one loose ball. |
| `ELBOW_ANG` | (90, 0) | Direction in which the stack after boom 1 and boom 2 points, in degrees about the rod axis (90 = sideways to -y, 270 = +y, 0 = up, 180 = down). |
| `TIP_ANG` | 90 | The same for the tip side after the last boom. `TIP_ANG = ELBOW_ANG[0]` gives the stretched arm, `ELBOW_ANG[0] + 180` folds it back. |
| `TIP_LINKS` | 2 | Tip side: 2 = M - DC - M, 1 = a single M link. |
| `M_LEN` | 55 | Ball-centre distance of the M links, mm. |
| `WIRE_THICK`, `WIRE_T` | 2.5, 400 | Wire diameter (mm) and tension (N, full size). |
| `START_POSE` | T1 68.125, T2 180 | Pose at start; `{}` = all joints neutral. A pose that collides is dropped. |
| `OPEN_PANEL`, `RUN_CHECKS` | True, True | Open the panel; print the report and the holding margins. |

## Printing

- The macro is built so that no supports are needed: cones and fillets are at most 45 degrees from vertical, holes are teardrops, windows are hexagons with a 45-degree roof. A few tiny flat overhangs remain (a few mm2 per part, listed in the header of the macro). This was measured on the models, not tested on a printer.
- The base prints upside down (deck on the bed). The stand end and the probe holder stand on their end faces with the cup up, the rod stands on its spigot, the clamp screw on its knob, the slider and the tension screw on their ends. If a screw breaks, print it lying down with 100 % infill.
- The slider and the tension screw carry the full wire tension, so print them with many walls and full infill.
- PETG, 0.2 mm layers and 4 walls were assumed.

## Holding margins (read this before printing)

For every joint the macro compares the holding torque of the wire friction with the load torque: a 100 g probe, 1 N pushes down and sideways at the probe tip, and the weight of everything beyond the joint. It takes 4000 random poses with every ball tilted up to its limit and keeps the worst. A margin below 1 means that in the worst of those poses the joint may slip. With the default two-boom arm:

| Joint | J0a | J0b | J1a | J1b | J2a | J2b | J2c | J2d | J3a | J3b | J3c | J3d |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Margin | 0.43 | 0.46 | 0.50 | 0.54 | 0.29 | 0.34 | 0.39 | 0.48 | 0.32 | 0.44 | 0.65 | 1.30 |

With one boom the margins were more than twice as good (the worst joint, J2a, was 0.71). Each boom costs about half of the wire tension (0.456: the wire rubs in the two bends of the end pieces), and the load arm of the probe grows from 267 mm to 492 mm. What helps: less friction in the bends (a PTFE tube or a lubricated wire: `MU_W` 0.10 gives 0.73 per boom instead of 0.456), TPU seats, a lighter probe, shorter M links, or a folded pose. The wire friction is an assumption (`MU_W` = 0.25), so real numbers need a print test.

## Known limits

- Not printed and not tested with a real probe.
- Ballast: the 0.4 kg named in the macro was worked out for one boom (100 g at 267 mm). For the stretched two-boom arm (probe at y = -492 mm) a rough estimate is 1.5 to 2 kg, not verified. Clamp the base to the table, or fill the pocket with steel shot in epoxy or steel discs. The folded start pose needs much less.
- At the rod stubs of the booms the wire path comes into the material at 16 of 3245 sample points (8 per rod). A flexible wire follows the bend, but a stiff one may rub there.
- The miniature (`BALL_D = 8`) was not rechecked after the second arm was added. On small models the hollow end pieces switch themselves off, because the wire channel becomes too wide.
- The collision test uses FreeCAD boolean operations and is slow in some blocked poses. The three parts of one boom (two end pieces and the rod) are never tested against each other: they only turn about their common axis, and the boolean operation between them froze FreeCAD at some twist angles.

## Files

| File | |
| --- | --- |
| `ProbeStand_OneBoom_Combined.FCMacro` | The whole project: parameters, every part, the wire path, the pose panel, the collision test, the holding margins and the STL and STEP export. The header of the file explains the design decisions. |
| `models/` | One STEP file per printed part. |
| `images/` | Pictures used in this README. |

## License

MIT, see `LICENSE`.
