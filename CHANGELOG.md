# 📜 CoderX Assets Changelog

All notable updates, new challenge additions, and problem set releases to `coderx-assets` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.1.0] - 2026-08-29

### Added
- Added 37 React challenge problem JSON files covering:
  - JSX Basics (`react-jsx-basics.json`)
  - `useState` & State Updates (`react-state-usestate.json`)
  - `useEffect` Lifecycle & Cleanup (`react-useeffect-lifecycle.json`)
  - Controlled Forms (`react-forms-controlled.json`)
  - Context API (`react-context-api.json`)
  - Custom Hooks (`react-custom-hooks.json`)
  - `useMemo` & `useCallback` Performance Optimization
  - Error Boundaries, Portals & Component Composition
- Added `templates/` directory with starter problem JSON templates for React and Express challenges.

### Changed
- Standardized all `src/App.jsx` templates with explicit `export default App;` exports and top-level React hook imports.
- Updated `total_tests` metadata field for automated test progress tracking.

---

## [1.0.0] - 2026-08-20

### Added
- Initial public release of `coderx-assets` repository.
- Contribution guidelines (`README.md`) for WebContainer in-browser problem challenges.
- Pre-packaged binary support for `react`, `express`, and `react-express` stack environments.
