# Fork changes

This repository is a fork of [gwihlidal/vk-sync-rs](https://github.com/gwihlidal/vk-sync-rs), used as a vendored submodule. Changes relative to upstream `0.1.6`:

## Port to the vendored ash 0.38

- `Cargo.toml`: `ash` now uses `path = "../ash/ash"` — the vendored [ash](../ash) fork
  (`0.38.0+1.4.352`) — instead of `ash 0.29` from crates.io. Upstream `0.1.6` targeted
  ash 0.29, so this required an API port.
- `src/cmd.rs`: `pipeline_barrier`, `set_event`, `reset_event`, and `wait_events` now take
  `&ash::Device` instead of `&ash::vk::DeviceFnV1_0` (in ash 0.38 the raw function-pointer
  tables are `pub(crate)` inside `Device`), and call the slice-based wrapper methods
  instead of raw `count`/pointer arguments. The same Vulkan commands are issued with
  unchanged argument values and order. **Breaking** for callers of `vk_sync::cmd`.
- `src/lib.rs`: ash 0.38 barrier structs carry a lifetime (`MemoryBarrier<'a>`,
  `PhantomData`-only), so `get_memory_barrier`, `get_buffer_memory_barrier`, and
  `get_image_memory_barrier` now have explicit lifetimes, e.g.
  `fn get_memory_barrier<'a>(barrier: &GlobalBarrier<'a>) -> (..., MemoryBarrier<'a>)`.

## Removed NVX access types

`AccessType::CommandBufferReadNVX` and `AccessType::CommandBufferWriteNVX` were removed,
along with their `get_access_info`/`is_write_access` entries: the
`VK_NVX_device_generated_commands` stage/access constants no longer exist in the Vulkan
1.4 headers the vendored ash is generated from.

## Documentation

- `src/cmd.rs`: fixed a copy-paste in the `wait_events` doc comment (it referred to
  `vkCmdPipelineBarrier` instead of `vkCmdWaitEvents`).
