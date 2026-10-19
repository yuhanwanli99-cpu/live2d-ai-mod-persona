# live2d-ai-mod-persona

酒馆角色卡 Mod（稳定 id persona；工厂 `FACTORY`（`PersonaFactory`））的实现 crate。本仓库是 2026-10-19 从主仓 `/home/skystar/Live2D-Ai` 拆出来的独立本地仓库：**仓库根 = crate 根**（`Cargo.toml` 在根），许可证 AGPL-3.0-only（见 `LICENSE`）。

依赖方向只有「Mod → 主仓」：`live2d-ai-mod-system` 走 path 指向同父目录的 `../Live2D-Ai/crates/...`；没有指向旧第二仓 `../Live2D-Ai-mods` 的 path，也没有 Mod→Mod 的 path 依赖。本仓是独立 workspace（`Cargo.toml` 末尾空的 `[workspace]`），可以 `cd` 进来单独跑门禁。

## 挂回主仓（维护者手动，**没有动态加载器**）

1. 主仓根 `Cargo.toml` 的 `[workspace.dependencies]` 加一行：`live2d-ai-mod-persona` = { path = "../live2d-ai-mod-persona" }；
2. `crates/live2d-ai-desktop/Cargo.toml` 加同名依赖（`live2d-ai-mod-persona` = { workspace = true }）；
3. 把 `FACTORY`（`PersonaFactory`） 加进 `AVAILABLE_MOD_FACTORIES`；
4. 本 Mod 没有独立宿主路由，面板经通用的 `/api/v1/mods/persona/state` 与命令通道即可。

## 拆分偏差：跨 crate 共存契约测试取消

原 `src/tests_e2e.rs` 里「与 memory 共存（last-writer-wins）」那组测试直接 `use live2d_ai_mod_memory`（crate 的真实纯函数 `compose_injection` / `strip_memory_block`），属于 Mod→Mod 的 path 依赖，与「path 只指向主仓」纪律冲突，因此 `[dev-dependencies] live2d-ai-mod-memory` 已删除、那组测试已删除；坏卡 E2E 测试保留。原测试仍在旧第二仓与主仓 git 历史里，不是遗失。

## 门禁

```bash
cargo test --all-targets
cargo test --doc
cargo fmt -- --check
cargo clippy --all-targets -- -D warnings
```

远端未建、未 push；本地 `main` 分支一次提交。
