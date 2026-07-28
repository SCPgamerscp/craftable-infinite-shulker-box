# Craftable Infinite Shulker Box

A lightweight Minecraft 1.20.1 Forge mod that:

- allows every vanilla shulker box (undyed and all 16 colors) to be placed inside another shulker box;
- applies the same behavior to sided inventory insertion used by automation such as hoppers; and
- adds a shaped recipe for one undyed shulker box made from nine chests.

The implementation deliberately relies on vanilla inventory/NBT handling. It adds no custom item, block, capability, packet, or persistent data format.

## Requirements

- Minecraft 1.20.1
- Forge 47.4.17 or another compatible Forge 47 release
- Java 17

Install the built JAR in the `mods` directory on both the client and server. Installing it on both sides is recommended because GUI validation occurs on both sides and automation is server-side.

## Recipe

Fill every slot of a crafting table with one `minecraft:chest` (nine chests total) to craft one `minecraft:shulker_box`. The recipe unlocks when a player obtains a chest.

## Build

```shell
./gradlew build
```

The output is `build/libs/craftable_infinite_shulker_box-1.0.0.jar`.

IDE run configurations can be generated with `./gradlew genIntellijRuns` or `./gradlew genEclipseRuns`.

## Implementation notes

- `ShulkerBoxSlotMixin` permits vanilla shulker-box items in the shulker GUI.
- `ShulkerBoxBlockEntityMixin` permits them through sided inventory insertion.
- The recipe and recipe-unlock advancement are ordinary data-pack JSON resources.
- Mixins only intercept the vanilla shulker-box rejection case; behavior for other items remains untouched.

For ports to another Minecraft version or loader, copy the JSON resources and adapt the two small Mixin targets/signatures to that environment's mappings and inventory APIs.

## Manual verification

1. Obtain a chest and confirm the recipe appears in the recipe book.
2. Fill a 3x3 crafting grid with chests and craft the undyed shulker box.
3. Insert undyed and colored shulker boxes through the shulker GUI.
4. Create at least three nesting levels, then place, break, and reopen each box to verify contents persist.
5. Use a hopper to insert a shulker box into a placed shulker box.

## License

MIT
