# Installation

This page walks you through installing Game Export Pipeline into Blender and verifying the setup.

## Requirements

| Requirement | Version |
|---|---|
| Blender | 4.2.0 or newer |
| Operating system | Windows, macOS, Linux |
| Render pipeline | Any (addon is engine-agnostic) |

!!! note "Blender 4.2 Extension system"
    Game Export Pipeline ships as a standard Blender addon (`.zip`). It is compatible with both the legacy **Preferences → Add-ons** installer and the new **Extension** system introduced in Blender 4.2. Both methods are covered below.

## Install from a `.zip` file

1. Open Blender and go to **Edit → Preferences → Add-ons**.
2. Click **Install…** (top-right corner of the Add-ons panel).
3. Navigate to the downloaded `game_export_pipeline_v1.5.1.zip` file, select it, and click **Install Add-on**.
4. The addon appears in the list. Tick the checkbox next to **Import-Export: Game Asset Export Pipeline** to enable it.
5. Click **Save Preferences** to keep it enabled across restarts.

!!! warning "Install the zip, not the extracted folder"
    Blender expects the compressed `.zip` file, not the extracted `game_export_pipeline_v1.5.1/` folder. If you accidentally extracted it, re-zip the folder before installing.

## Verify the installation

After enabling the addon, open any **3D Viewport** and press **N** to open the N-Panel (or click the small arrow on the right edge of the viewport). You should see a **GameExport** tab in the panel list.

If the tab is missing, confirm the addon is ticked in **Edit → Preferences → Add-ons** and that there are no error messages in the Blender **Info** log (`Window → Toggle System Console` on Windows, or check the terminal on macOS/Linux).

## Update to a newer version

To update, disable and remove the existing version first:

1. Open **Edit → Preferences → Add-ons**.
2. Find **Game Asset Export Pipeline**, expand it, and click **Remove**.
3. Install the new `.zip` following the steps above.

!!! tip "Your scene settings are preserved"
    Engine preset, export path, validation thresholds, and all other settings are stored per-scene in the `.blend` file — not in the addon itself. Updating the addon does not wipe your settings.

## Next step

Continue to the [5-minute Quick Start](quick-start.md) to export your first asset.
