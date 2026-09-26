# SeaORM

## Map and tooling

`src/` implements the ORM; `sea-orm-macros/` derives entity APIs and `sea-orm-codegen/` generates code. The root Cargo workspace includes these and `examples/quickstart/`. CLI, migration, Rocket, and other examples have separate manifests; a root workspace pass does not cover every subproject. `tests/` holds backend integration cases, `issues/` reproductions, and `build-tools/` disposable database setup.

Use Cargo with Rust 1.85+ (the manifest minimum), stable Clippy, and nightly rustfmt. Read `DEVELOPMENT.md` and the relevant `.github/workflows/rust.yml` matrix before changing backend/runtime feature combinations. Preserve SQL backend differences, generated entity compatibility, and feature-gated builds.

## Verification

- Local library baseline: `cargo test --lib`, `cargo test --doc`, and `cargo test --workspace`; use a focused test while iterating.
- Static gates: `cargo clippy --all -- -D warnings`, `cargo +nightly fmt --all -- --check`; TOML changes use `taplo fmt --check` with taplo installed.
- CLI changes: `cargo test --manifest-path sea-orm-cli/Cargo.toml`; use the migration manifest for that crate's checks.
- SQLite integration example: `DATABASE_URL='sqlite::memory:' cargo test --test crud_tests --features tests-features,sqlx-sqlite,runtime-tokio`. MySQL/Postgres cases need a disposable database and the matching CI features. Never point integration tests or migration commands at an existing production database.
- For feature/API changes, include the relevant CI compile/backend matrix. `cargo run --manifest-path sea-orm-cli/Cargo.toml -- --help` is a CLI smoke check; actual migrate/generate commands can change a database or files.

Do not copy the old formatting fallback that uses `git checkout` to discard generated-file edits. Inspect formatting diffs and preserve existing work instead.

## Finishing work

Follow the nearby implementation and keep changes within the requested scope. Carry authorized changes through the relevant checks, fixing failures caused by the change. For a bug, reproduce the affected behavior and add a focused regression check when useful. Make routine reversible choices without another approval; ask only when missing information materially changes correctness, scope, or authorization, and name the exact source of any blocking rule. Report what changed, checks actually run, and concrete unverified behavior; repeat checks when new edits or evidence warrant it.
