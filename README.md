# Craftable Infinite Shulker Box

A lightweight Minecraft Forge mod that:

- allows every vanilla shulker box (undyed and all 16 colors) to be placed inside another shulker box;
- applies the same behavior to sided inventory insertion used by automation such as hoppers; and
- adds a shaped recipe for one undyed shulker box made from nine chests.

The implementation deliberately relies on vanilla inventory/NBT handling. It adds no custom item, block, capability, packet, or persistent data format.

## Supported versions

A separate JAR is published for each supported Minecraft version. Use the JAR that matches your Minecraft version; they are not interchangeable.

| Minecraft | Forge   | Java | JAR                                                    |
| --------- | ------- | ---- | ------------------------------------------------------ |
| 1.18.2    | 40.3.12 | 17   | `craftable_infinite_shulker_box-mc1.18.2-<version>.jar` |
| 1.19.2    | 43.5.2  | 17   | `craftable_infinite_shulker_box-mc1.19.2-<version>.jar` |
| 1.19.4    | 45.4.3  | 17   | `craftable_infinite_shulker_box-mc1.19.4-<version>.jar` |
| 1.20.1    | 47.4.23 | 17   | `craftable_infinite_shulker_box-mc1.20.1-<version>.jar` |
| 1.21.1    | 52.1.16 | 21   | `craftable_infinite_shulker_box-mc1.21.1-<version>.jar` |

Each JAR accepts any Forge release in its major version line (for example Forge 47 for 1.20.1), and its `mods.toml` restricts it to its own Minecraft version so the wrong JAR fails fast with a clear message instead of misbehaving.

Install the JAR in the `mods` directory on both the client and server. Installing it on both sides is recommended because GUI validation occurs on both sides and automation is server-side.

## Recipe

Fill every slot of a crafting table with one `minecraft:chest` (nine chests total) to craft one `minecraft:shulker_box`. The recipe unlocks when a player obtains a chest.

## Build

Build every supported version at once:

```shell
./gradlew build
```

Collect all JARs into a single directory (`build/libs`):

```shell
./gradlew collectJars
```

Build only one version:

```shell
./gradlew :versions:1.20.1:build
```

Each version's JAR is written to `versions/<mc_version>/build/libs/`.

IDE run configurations are generated per version, for example
`./gradlew :versions:1.20.1:genIntellijRuns` or `:versions:1.20.1:genEclipseRuns`.

### Building on a low-memory machine

ForgeGradle forks external JVMs (Access Transformer, renamer, decompiler) that do
**not** inherit `org.gradle.jvmargs`. On a machine with little RAM those forks can
die with `OutOfMemoryError`, which leaves a truncated JAR in the ForgeGradle cache
and produces a confusing "Could not find net.minecraftforge:forge:..." error on the
next run. Give the forked JVMs their own heap:

```shell
JAVA_TOOL_OPTIONS=-Xmx1600m ./gradlew build
```

If a build already failed this way, delete the affected cache entry
(`~/.gradle/caches/forge_gradle/minecraft_user_repo/net/minecraftforge/forge/<version>_mapped_*`)
before retrying.

## Project layout

```
common/                  Java sources + Mixin config shared by every version
gradle/forge-mod.gradle  the ForgeGradle build logic shared by every version
versions/<mc_version>/   per-version gradle.properties + data-pack resources
```

The Java code is shared: both Mixin targets (`ShulkerBoxSlot.mayPlace` and
`ShulkerBoxBlockEntity.canPlaceItemThroughFace`) have identical signatures in all
supported Minecraft versions, so only build settings and data-pack resources differ.

## Implementation notes

- `ShulkerBoxSlotMixin` permits vanilla shulker-box items in the shulker GUI.
- `ShulkerBoxBlockEntityMixin` permits them through sided inventory insertion.
- The recipe and recipe-unlock advancement are ordinary data-pack JSON resources.
- Mixins only intercept the vanilla shulker-box rejection case; behavior for other items remains untouched.

### What differs between versions

These are the version-specific details the per-version resources handle:

- **Data pack format**: 9 (1.18.2), 10 (1.19.2), 12 (1.19.4), 15 (1.20.1), 48 (1.21.1).
- **Data folder names**: 1.21 renamed the folders to singular, so 1.21.1 uses
  `data/<namespace>/recipe/` and `advancement/` while older versions use
  `recipes/` and `advancements/`.
- **Recipe result**: 1.21.1 uses `"result": {"id": ..., "count": ...}`; earlier
  versions use `"result": {"item": ..., "count": ...}`.
- **Recipe metadata**: `category` and `show_notification` only exist from 1.19.4
  on, so the 1.18.2 and 1.19.2 recipes omit them. The only valid crafting
  categories are `building`, `redstone`, `equipment` and `misc`.
- **Advancement item predicate**: 1.21.1 takes an ID/tag string
  (`"items": "minecraft:chest"`); earlier versions take a list.
- **Java / Mixin**: 1.20.5+ ships Java 21, and Mixin 0.8.5 has no `JAVA_21`
  compatibility level, so 1.21.1 uses the Mixin 0.8.7 that Forge 52 bundles.
- **Reobfuscation**: pre-1.20.5 runs on Searge names, so those JARs are
  reobfuscated and ship a refmap. 1.20.5+ runs on the same official mappings used
  at compile time, so 1.21.1 is neither reobfuscated nor ships a refmap.

## Manual verification

Repeat for each version you intend to ship:

1. Obtain a chest and confirm the recipe appears in the recipe book.
2. Fill a 3x3 crafting grid with chests and craft the undyed shulker box.
3. Insert undyed and colored shulker boxes through the shulker GUI.
4. Create at least three nesting levels, then place, break, and reopen each box to verify contents persist.
5. Use a hopper to insert a shulker box into a placed shulker box.

## 利用規約・ライセンス

本Modに特定のオープンソースライセンス（MIT等）は設定しておりません。

- **ゲームプレイ・動画・配信等での利用**: 自由にご利用いただけます。
- **改変・二次配布・Modpack同梱・コード流用**: **必ず事前に一言お声がけ・ご連絡（GitHub Issue等）をいただければ改変・利用可能です。**
- **禁止事項**: 無断での改変・無断再配布・転載・自作発言・販売は禁止いたします。
