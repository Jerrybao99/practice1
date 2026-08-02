# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.1] - 2026-07-08

### Added
- 添加 intention 子仓库（data/intention），原名 quanttide-roadmap-of-data-cloud，重命名为 quanttide-intention-of-data-engineering
- 添加 library 子仓库（data/library），原名 awesome-free-data-analytics-solutions，重命名为 quanttide-library-of-data-engineering

### Changed
- 将 archive、context、report 子仓库从 docs/ 移动至 data/ 目录

## [0.1.0] - 2026-04-28

### Added
- 添加 essay 子仓库（docs/essay），发布 v0.1.0 版本
  - 包含《数据合同》文章（intro/contract.md）
  - 包含《制衣厂数据清洗》文章（processing/cleaning.md）

### Changed
- 更新 archive, specification, tutorial, report 子模块

### Removed
- `Makefile`
- Profile 子模块

## [0.0.3] - 2026-04-22

### Added
- ROADMAP.md file

### Changed
- Updated archive, specification, tutorial, and report submodules

### Removed
- `Makefile`
- Profile submodule

## [0.0.2] - 2026-04-22

### Added
- Gallery submodule for data engineering examples
- Context submodule for competitor research
- Archive submodule for historical records

### Changed
- Moved toolkit submodule from `src/` to `packages/`
- Renamed `src/` directory to `apps/`
- Renamed example directories: removed `quanttide-example-of-` prefix

### Removed
- Alternative submodule
- Fixtures submodule
- `tests/conftest.py`

### Fixed
- Cleaned up duplicate entries in `.gitmodules`

## [0.0.1] - 2026-03-04

### Added
- Initial release
- Test fixtures support with dirty/clean data
- Profile submodule for data engineering profile
- Specification and fixtures submodules integration
- Test fixtures structure and validation
- Makefile for project automation
- Python test fixtures module

[Unreleased]: https://github.com/quanttide/quanttide-data/compare/v0.1.1...HEAD
[0.1.1]: https://github.com/quanttide/quanttide-data/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/quanttide/quanttide-data/compare/0.0.3...v0.1.0
[0.0.3]: https://github.com/quanttide/quanttide-data/compare/0.0.2...v0.0.3
[0.0.2]: https://github.com/quanttide/quanttide-data/compare/0.0.1...v0.0.2
[0.0.1]: https://github.com/quanttide/quanttide-data/releases/tag/0.0.1
