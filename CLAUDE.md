# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

## Additional Development Notes

### Running a Single Test

**Unit test by spec name** (filter applies to `describe`/`it` strings):
```bash
npx gulp unittest --headless -t "AnnotationLayer"
```

**Integration test by file filter:**
```bash
npx gulp integrationtest -t annotation_spec
```

Other useful test flags: `--noChrome`, `--noFirefox`, `--coverage`, `--headless`.

### Auto-fix Linting

```bash
npx gulp lint --fix
```

Runs ESLint, Prettier, Stylelint, and SVGLint with auto-fixing.

### Clean Build Artifacts

```bash
npx gulp clean
```

### Non-obvious ESLint Rules

The config enforces several patterns that will cause lint failures if missed:

- Use `Object.hasOwn(obj, key)` — not `obj.hasOwnProperty(key)`
- Use `typeof x` — not `x instanceof Object`
- Use static factory methods: `Ref.get(...)`, `Name.get(...)`, `Cmd.get(...)` — not `new Ref(...)` etc.
- Exports must be separate from declarations (no `export class Foo {}` inline)
- No focused tests: `fdescribe`/`fit` will error

### Preprocessor Directives

The Babel plugin lives in [external/builder/babel-plugin-pdfjs-preprocessor.mjs](external/builder/babel-plugin-pdfjs-preprocessor.mjs). It supports `#if`/`#elif`/`#else`/`#endif` block comments and inline `PDFJSDev.test()` runtime checks. When adding build-conditional code, prefer the inline form:

```javascript
if (typeof PDFJSDev === "undefined" || PDFJSDev.test("GENERIC")) {
  // only in generic build
}
```

Dead `PDFJSDev` branches are stripped at build time; the `typeof PDFJSDev !== "undefined"` guard is required so Node.js/test environments don't throw.
