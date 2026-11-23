This is not exactly ready yet sorry lads

THE README AND DOCUMENTATION WAS MADE WITH THE ASSISTANCE OF AI. I'm not very good at them, sorry in advance.

# PoseKit

Pose creator and editor for [GlovesTan3](https://thunderstore.io/c/h3vr/p/JerryAr/GlovesTan3/) by JerryAr, for Hot Dogs, Horseshoes & Hand Grenades (H3VR).

GlovesTan3 only ships hand poses for its own AKS-74n. PoseKit lets you make poses for **any gun**, without Unity:

* **Browser editor**, served by the game at `http://127.0.0.1:48261/`. It shows the real GlovesTan3 glove model, skinned to the hand rig, on the gun's real model streamed from the running game.
* **VR editor**: an in-game lockable panel, so every edit can be made without leaving VR.
* **Every slot GlovesTan3 uses**: Idle, Foregrip, and Fire start → Fire end (trigger blend), for each hand. Plus slide/bolt/charging-handle parts, shotgun pumps and magazines.
* **Live sync** onto the gun in your hand, in-game **captures**, finger presets, per-knuckle curl/spread/twist, and **auto copy** to similarly named guns (G17 → G18, G19…).
* **Sync with GitHub repo**: download community poses from [PoseKit-Poses](https://github.com/LionHeartChiptuner/PoseKit-Poses).

See **[docs/USAGE.md](docs/USAGE.md)** for the full guide.

## Repository layout

| Path | Contents |
| --- | --- |
| `plugin/` | BepInEx plugin (C#, .NET 3.5 / Unity 5.6): pose loader, capture, editor bridge, VR editor |
| `editor/index.html` | The browser editor, a single file (three.js from jsDelivr). It's embedded into the plugin DLL at build time. |
| `thunderstore/` | Thunderstore package files: `manifest.json`, `README.md`, `icon.png` |
| `docs/USAGE.md` | User guide |

## Building

Requirements:

* the [.NET SDK](https://dotnet.microsoft.com/download) (6 or newer); no NuGet packages are downloaded
* H3VR, and a mod profile (e.g. r2modman) with **BepInExPack_H3VR**, **Sodalite**, **GlovesTan3** and **H3MP** (needed to build, optional to play) installed. The build references their DLLs from your install; none of them are included in this repo.

1. Copy `plugin/local.props.example` to `plugin/local.props` and set:
   * `GameDir`: the folder containing `h3vr.exe`
   * `ProfileDir`: your mod profile folder, the one containing `BepInEx\`
2. Build:

   ```
   cd plugin
   dotnet build -c Release -o bin
   ```

   You can also pass the paths directly: `dotnet build -c Release -o bin -p:GameDir="..." -p:ProfileDir="..."`.

The result is `plugin/bin/GlovesTan3PoseKit.dll`, with the editor built in.

### Testing locally

Copy the DLL into `<ProfileDir>\BepInEx\plugins\PoseKit\` and start the game through your mod manager. Don't keep a Thunderstore-installed copy at the same time: both use the plugin ID `gt3posekit.loader`, so BepInEx would load only one.

## How it works (short version)

* **Pose sets** are JSON files in `BepInEx/config/GlovesTan3Poses/`. When you grab a matching gun, the plugin builds GlovesTan3 `HandPoseItem`s from them and attaches each one where GlovesTan3 looks: under the gun, under each part's `FVRInteractiveObject.GameObject`, under the pump, or under the magazine.
* Each frame, the plugin also points every glove at the poses attached to what its hand is actually holding, because GlovesTan3 otherwise keeps the poses it picked when the grab started.
* The **editor bridge** is a small HTTP server on `127.0.0.1` only. It serves the editor, item models, the hand rig and glove, and captures. It only answers the editor page it serves: no CORS, Origin and Host checks, a custom header on every data request, and a Content-Security-Policy on the page.
* **Internet use:** at game start the plugin reads the pose repository from GitHub (pose JSON only, nothing is uploaded) and asks before changing any pose files. Turn this off with `[Pose repo] CheckOnStart = false`. It starts no other programs, apart from opening the editor in your browser. The editor loads three.js from jsDelivr and can sync poses from GitHub.

## License

PoseKit is released under the [MIT License](LICENSE). It doesn't include or redistribute GlovesTan3, Sodalite, BepInEx or any game files; those keep their own licenses and are only referenced at build time.

## Credits

* [GlovesTan3](https://thunderstore.io/c/h3vr/p/JerryAr/GlovesTan3/) by **JerryAr**, which this builds on.
* [Sodalite](https://thunderstore.io/c/h3vr/p/nrgill28/Sodalite/) by **nrgill28**, for the wrist menu and the VR panel.
* [three.js](https://threejs.org/) for the browser editor.
