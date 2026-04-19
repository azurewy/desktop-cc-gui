## 1. Rust Config Ownership Refactor

- [ ] 1.1 收口 `src-tauri/src/codex/config.rs`，将 private feature flags 与 official `unified_exec` passthrough 分离，禁止再批量暴露 `collab` / `collaboration_modes` / `steer` / `collaboration_mode_enforcement`
- [ ] 1.2 修改 `src-tauri/src/shared/settings_core.rs`，移除四个 private flags 的 external read/write 覆盖，只保留 app-local settings source-of-truth
- [ ] 1.3 确认 `src-tauri/src/shared/codex_core.rs`、`src-tauri/src/codex/session_runtime.rs`、`src-tauri/src/shared_sessions.rs` 仍从 app settings 读取 `codex_mode_enforcement_enabled`，不依赖 external config

## 2. Settings Surface Cleanup

- [ ] 2.1 清理 `experimentalCollabEnabled` 的产品能力面暴露，移除或降级“多代理”假开关，确保它不再参与真实行为与 external config 同步
- [ ] 2.2 更新 `src/features/settings/components/SettingsView.tsx` 与 i18n 文案，明确 app-local 开关和 official Codex config passthrough 的边界
- [ ] 2.3 校验 collaboration modes / steer / enforcement 相关前端 wiring 仍只依赖 app-local settings，不受 external historical flags 影响

## 3. Official Passthrough Preservation

- [ ] 3.1 为 `experimentalUnifiedExecEnabled` 保留显式 passthrough 路径，并在实现中单列 ownership，避免继续复用旧的批量 feature 同步模型
- [ ] 3.2 校验 external config reload、settings restore 与 config path 相关流程对 `unified_exec` 的行为一致性

## 4. Verification

- [ ] 4.1 补 Rust / TypeScript targeted tests，覆盖：
  - private flags 不再写入 `config.toml`
  - historical private flags 不再反向覆盖 app settings
  - `unified_exec` 仍可按显式路径透传
- [ ] 4.2 运行 `npm run lint`、`npm run typecheck`、相关 `vitest` 与 `cargo test`，记录结果
- [ ] 4.3 手工验证设置页与 `~/.codex/config.toml`：
  - 切换 collaboration modes / steer / enforcement 后不再出现私有字段写回
  - 切换 unified exec 后仅该官方字段发生预期变化
