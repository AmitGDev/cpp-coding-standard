# C++ Style Guide

**The coding standard for all of AmitGDev C++ repositories.**

This repository is the **single source of truth** for the common C++ development standards used across those repositories. It defines the formatting, static-analysis, naming, and related tooling policies that all C++ projects should follow.

Rather than maintaining separate `.clang-format` and `.clang-tidy` configurations in each project, the individual repositories consume the configurations maintained here. A change to this repository therefore represents a change to the common C++ standard across the projects that adopt it.

The standard targets **C++23** and uses the **Google C++ Style Guide** as its coding-style baseline, with explicit configuration for deterministic formatting and static analysis.

The configurations are intended for **LLVM 23.1** tooling.

## Purpose

The purpose of this repository is to provide one consistent, centrally maintained C++ standard across all of Amit G.'s repositories.

It establishes common rules for:

* Source-code formatting.
* Naming conventions.
* Static analysis.
* Modern C++ practices.
* Correctness and bug prevention.
* Performance.
* Portability.
* Concurrency.
* Selected security and reliability practices.
* Practical interoperability with operating-system and third-party-library interfaces.

Centralizing these rules provides several benefits:

* All repositories use the same baseline.
* Improvements to the standard can be propagated consistently.
* Formatting does not vary between projects.
* Static-analysis behavior is predictable.
* CI and local development use the same rules.
* Projects do not accumulate subtly different copies of the standard.
* Changes to the common standard can be versioned and adopted deliberately.

The repository is therefore a **standards repository**, rather than a library or application. It does not provide runtime C++ functionality.

## What This Repository Provides

* `.clang-format` - deterministic C++ source formatting.
* `.clang-tidy` - static analysis and coding-rule enforcement.
* `README.md` - documentation, usage, versioning, and maintenance guidance.

The configuration files are intended to be consumed directly by the C++ repositories that adopt this standard.

## Design Goals

The standard is intended to provide a common baseline that makes code:

* Consistent across repositories.
* Easy to review and maintain.
* Explicit in its intent.
* Safer through static analysis and modern C++ practices.
* Suitable for production systems and platform-facing code.
* Consistent with the Google C++ Style Guide.
* Practical for real-world compiler, operating-system, and third-party-library interfaces.

The standard is deliberately strict about static analysis. Warnings are treated as errors, and the enabled check families provide broad coverage across correctness, safety, concurrency, performance, portability, readability, and modern C++ practices.

Checks are disabled only for documented reasons such as duplication, compatibility with the selected Google-style baseline, platform interoperability, noise, or known false positives.

## Repository Layout

```text
cpp-pro-style/
├── .clang-format
├── .clang-tidy
└── README.md
```

The remainder of this document describes the policies defined by the two configuration files and how consuming repositories should use them.

---

## Formatting Standard

`.clang-format` is based on the **Google** Clang-Format style:

```yaml
BasedOnStyle: Google
```

The configuration then explicitly defines the formatting behavior required by this standard.

### Language

The formatter is configured for C++:

```yaml
Language: Cpp
Standard: Latest
```

`Standard: Latest` allows Clang-Format to parse the latest C++ language constructs supported by the installed LLVM version. The coding standard itself targets C++23.

### Line Length and Indentation

* Column limit: 80 characters.
* Indentation: 2 spaces.
* Tab width: 2.
* Tabs are never used.
* Continuation indentation: 4 spaces.

### Namespaces

* Namespace contents are not additionally indented.
* Compact namespaces are disabled.
* Namespace comments are maintained by Clang-Format.

### Classes and Structs

The configuration uses:

* Google-style access modifier placement.
* Logical empty lines before access modifiers.
* Explicit formatting for empty records.

### Functions

The configuration uses:

* Google-style function formatting.
* Inline short functions may remain on one line.
* Trailing return types are not forced by formatting.
* Wrapped function names are not additionally indented.

### Control Flow

Short control-flow statements are intentionally formatted explicitly rather than compressed onto a single line.

The configuration disables single-line formatting for:

* Short `if` statements.
* Short loops.
* Short `case` labels.

### Braces

Braces use the attached style:

```cpp
if (condition) {
  DoSomething();
}
```

Brace wrapping is explicitly configured for classes, structs, functions, namespaces, control statements, lambdas, and other constructs.

### Includes

Includes are sorted case-sensitively and regrouped.

The configured categories distinguish between:

1. The current file's header.
2. C system headers.
3. C++ standard-library headers.
4. Platform/system headers.
5. Third-party libraries.
6. Other project headers.

### Pointers and References

Pointers and references use left alignment:

```cpp
int* pointer;
const std::string& value;
```

Pointer and reference alignment is explicitly configured rather than derived from existing source.

### Modern C++ Features

The formatter explicitly handles modern C++ constructs including:

* C++11 braced initializers.
* Lambdas.
* Templates.
* Concepts.
* `requires` clauses.
* Attributes.
* Modern constructor initializers.

### Comments and Empty Lines

Comments are reflowed, and the configuration limits consecutive empty lines.

Trailing comments are aligned according to the configured policy.

### Formatting Source of Truth

The complete formatting policy is defined by `.clang-format`.

The README documents the principal characteristics but intentionally does not reproduce every formatter option. `.clang-format` is the authoritative source for formatting behavior.

---

# Static Analysis Standard

`.clang-tidy` provides broad static analysis and uses:

```yaml
WarningsAsErrors: '*'
```

Therefore, diagnostics produced by the configured checks are treated as errors.

The configuration is organized into nine explicit groups. The grouping in `.clang-tidy` is intentional and documents the reason for enabling or disabling each class of check.

## 1. Base Check Families

The primary static-analysis coverage comes from:

* `bugprone-*`
* `boost-*`
* `clang-analyzer-*`
* `concurrency-*`
* `cppcoreguidelines-*`
* `google-*`
* `llvm-*`
* `misc-*`
* `modernize-*`
* `performance-*`
* `portability-*`
* `readability-*`

These provide the main coverage for correctness, potential bugs, concurrency, C++ Core Guidelines, Google coding conventions, general C++ issues, modern C++, performance, portability, and readability.

The `google-*` family is particularly important because the Google C++ Style Guide is the coding-style baseline of this repository.

## 2. Optional HICPP Strictness Checks

The repository does not enable the complete HICPP check family.

It enables the following selected HICPP checks:

* `hicpp-use-override`
* `hicpp-explicit-conversions`
* `hicpp-deprecated-headers`
* `hicpp-braces-around-statements`
* `hicpp-exception-baseclass`
* `hicpp-signed-bitwise`

These provide additional diagnostics without adopting the complete HICPP rule set.

## 3. Extra Security and Best-Practice Checks

The following CERT checks are additionally enabled:

* `cert-err58-cpp`
* `cert-oop54-cpp`

These complement the broader analysis with additional correctness and security-oriented diagnostics.

## 4. Disabled Duplicate Checks

Some checks are disabled because equivalent or substantially overlapping diagnostics are already covered by enabled checks.

The purpose is to avoid duplicate diagnostics, not to weaken the underlying analysis.

Examples include:

* `cppcoreguidelines-avoid-magic-numbers`
* `readability-magic-numbers`
* `cppcoreguidelines-avoid-c-arrays`
* `modernize-avoid-c-arrays`
* `cppcoreguidelines-explicit-constructor`
* `misc-explicit-constructor`

A disabled duplicate should not automatically be interpreted as an accepted practice. Where another enabled diagnostic provides the relevant coverage, that diagnostic remains part of the standard.

## 5. Disabled HICPP Aliases

The following HICPP checks are disabled:

* `hicpp-avoid-c-arrays`
* `hicpp-avoid-goto`
* `hicpp-no-array-decay`
* `hicpp-uppercase-literal-suffix`
* `hicpp-vararg`
* `hicpp-static-assert`

These exclusions keep the HICPP portion of the configuration limited to the selected checks and avoid additional or overlapping diagnostics.

## 6. Checks Disabled to Match the Google-Style Baseline

The Google C++ Style Guide is the coding-style baseline for this repository.

Some Clang-Tidy checks promote conventions that are not part of that baseline. They are disabled so that Clang-Tidy does not impose a competing convention.

The following checks are disabled:

* `modernize-use-trailing-return-type`
* `modernize-use-scoped-lock`
* `readability-implicit-bool-conversion`
* `misc-use-anonymous-namespace`
* `llvm-header-guard`

### Trailing Return Types

Trailing return types are not required universally.

The standard therefore does not require:

```cpp
auto GetValue() -> int;
```

instead of:

```cpp
int GetValue();
```

### Internal Linkage

The standard does not require an unnamed namespace instead of `static`.

Both are valid mechanisms for providing internal linkage, and the standard does not impose one universally.

### LLVM Header Guards

`llvm-header-guard` is disabled because its required naming convention does not match the Google-style header-guard convention used by this standard.

It cannot be configured to enforce the desired:

```text
PROJECT_PATH_FILE_H_
```

form.

Enabling the check would therefore impose an incompatible naming convention.

These exclusions preserve alignment with the selected Google C++ Style Guide baseline.

## 7. Checks Requiring GSL or Restricting Windows API Interoperability

The following checks are disabled:

* `cppcoreguidelines-owning-memory`
* `cppcoreguidelines-pro-type-reinterpret-cast`

These checks can interfere with legitimate low-level and platform-facing C++ code.

Windows and system APIs may legitimately require:

* Raw pointers.
* Explicit ownership conventions.
* ABI-level casts.
* `reinterpret_cast`.

GSL-specific ownership enforcement is also not assumed by this standard.

These exclusions preserve legitimate platform and library interoperability while leaving the broader static analysis enabled.

## 8. Other Disabled Checks

Additional checks are disabled because they are outside the intended baseline or are not considered appropriate for universal enforcement.

These include:

* `modernize-use-nodiscard`

Magic-number diagnostics are also disabled:

* `cppcoreguidelines-avoid-magic-numbers`
* `readability-magic-numbers`

The standard does not require every numeric literal to be replaced by a named constant. Whether a literal should be named depends on its meaning and context.

Test, unit-test, and mock files are additionally ignored by the configured magic-number analysis:

```yaml
IgnoredFilePatterns: '.*(test|unittest|mock).*'
```

## 9. Specific Analyzer False Positives

The following individual Clang Static Analyzer diagnostics are disabled:

* `clang-analyzer-optin.core.EnumCastOutOfRange`
* `clang-analyzer-cplusplus.Move`

These are narrowly scoped exclusions for known false positives or unacceptable diagnostic noise in the supported codebases.

The analyzer families themselves remain enabled.

These exclusions should be reevaluated when upgrading LLVM.

---

# Naming Conventions

The naming configuration follows the Google C++ Style Guide.

| Entity                | Convention    | Example            |
| --------------------- | ------------- | ------------------ |
| Variables             | `snake_case`  | `file_name`        |
| Parameters            | `snake_case`  | `buffer_size`      |
| Free functions        | `CamelCase`   | `CreateWidget()`   |
| Methods               | `CamelCase`   | `GetValue()`       |
| Classes               | `CamelCase`   | `WidgetFactory`    |
| Structs               | `CamelCase`   | `WindowInfo`       |
| Namespaces            | `snake_case`  | `platform_support` |
| Class members         | `snake_case_` | `buffer_size_`     |
| Struct members        | `snake_case`  | `width`            |
| `constexpr` variables | `kCamelCase`  | `kMaxRetries`      |
| Enum constants        | `kCamelCase`  | `kReadOnly`        |

### Class Members

Class data members use a trailing underscore:

```cpp
class Widget {
 public:
  void SetValue(int value);

 private:
  int value_;
};
```

### Struct Members

Struct data members use `snake_case` without a trailing underscore:

```cpp
struct WindowInfo {
  int width;
  int height;
};
```

Clang-Tidy can enforce the case convention for struct members, but `readability-identifier-naming` cannot enforce the absence of a trailing underscore.

Consequently, a struct member named `width_` may not be diagnosed solely because of its trailing underscore.

Strict enforcement of this distinction requires an additional validation mechanism, such as a repository linting script or a custom Clang-Tidy check.

---

# Additional Clang-Tidy Configuration

## Cognitive Complexity

Cognitive-complexity analysis:

* Ignores macros.
* Uses a threshold of `25`.

## Structured Bindings

`modernize-use-structured-bindings` ignores macros.

## Performance

`performance-unnecessary-value-param` allows:

```text
std::string_view
std::unique_ptr
std::shared_ptr
std::optional
```

These types are intentionally excluded from that diagnostic.

## Modern C++ Recommendations

The configuration enables:

* `modernize-use-std-format` for applicable iostream usage.
* `modernize-use-std-span` for suitable pointer-plus-size interfaces.

The `std::span` check uses a minimum array size of three.

---

# Header Analysis

The configuration uses:

```yaml
HeaderFilterRegex: '.*'
```

This allows Clang-Tidy to analyze project headers while its normal system-header handling prevents system headers from becoming ordinary project diagnostics.

This makes the shared configuration usable across different consuming repositories without requiring each repository to customize the header filter.

---

# Consuming Repositories

This repository is intended to be consumed by all of Amit G.'s C++ repositories.

A consuming project should expose the shared configuration files at its repository root:

```text
my-project/
├── .clang-format
├── .clang-tidy
├── CMakeLists.txt
└── src/
```

Keeping the files at the repository root allows Clang-Format and Clang-Tidy to discover them through their normal configuration lookup mechanisms.

The shared configuration files should not be independently modified in consuming repositories.

Possible integration mechanisms include:

* Git submodules.
* A scripted configuration-sync step.
* A package or artifact dependency.
* A repository bootstrap script.
* CI checkout of a pinned revision.

The integration mechanism is project-specific. The standard itself remains centralized in this repository.

---

# Local Usage

## Clang-Format

Format a file in place:

```powershell
clang-format -i path\to\file.cpp
```

Preview formatting without modifying the file:

```powershell
clang-format path\to\file.cpp
```

## Clang-Tidy

Run Clang-Tidy using a compilation database:

```powershell
clang-tidy -p build path\to\file.cpp
```

For reliable analysis, consuming repositories should generate a complete `compile_commands.json`.

The compilation database should contain the same:

* Compiler definitions.
* Include paths.
* Language standard.
* Target architecture.
* Target-specific compiler options.

used by the actual build.

---

# CI Enforcement

CI should enforce both formatting and static analysis.

A typical pipeline should:

1. Obtain the pinned C++ Pro Style configuration.
2. Generate the compilation database.
3. Verify source formatting.
4. Run Clang-Tidy against the project sources.
5. Fail if formatting differs.
6. Fail if Clang-Tidy produces diagnostics.
7. Use the same standard version as local development.

Example formatting check:

```powershell
$diff = clang-format --dry-run --Werror path\to\file.cpp 2>&1

if ($LASTEXITCODE -ne 0) {
    Write-Error $diff
    exit $LASTEXITCODE
}
```

Because `.clang-tidy` specifies:

```yaml
WarningsAsErrors: '*'
```

Clang-Tidy diagnostics are configured to fail the CI invocation.

The exact CI commands depend on the consuming repository's build system and compiler environment. Those project-specific details belong in the consuming repository.

---

# Versioning

The standard is versioned independently from consuming repositories.

Projects requiring reproducible builds and stable CI behavior should pin a release tag or commit rather than tracking a moving branch.

For example:

```text
cpp-pro-style v1.2.0
```

A consuming repository should avoid tracking `main` unless automatic adoption of future standard changes is intentional.

An upgrade should be an explicit change in the consuming repository.

This makes changes such as:

* Formatting changes.
* Newly enabled diagnostics.
* Changed diagnostics.
* Required source modifications.

visible during review.

---

# Updating the Standard

When changing the standard:

1. Update `.clang-format`, `.clang-tidy`, or both.
2. Document non-obvious decisions in the configuration comments.
3. Validate the configuration against representative consuming repositories.
4. Verify compatibility with the supported LLVM version.
5. Commit the change.
6. Publish a new version when the change can affect consumers.
7. Upgrade consuming repositories deliberately.

Changes should favor consistency and meaningful diagnostics over novelty.

A Clang-Tidy check should be enabled when it provides useful signal for the consuming codebases and fits the selected baseline.

A check should remain disabled when it is:

* Already covered by another diagnostic.
* An unnecessary duplicate.
* Incompatible with the Google-style baseline.
* Incompatible with legitimate platform or library interfaces.
* Dependent on a library or type system not required by this standard.
* Too noisy for reliable enforcement.
* A known false positive.

---

# Project-Specific Extensions

C++ Pro Style defines the **common baseline for all consuming repositories**, not every rule required by every individual project.

A consuming repository may add local requirements for:

* A particular compiler or SDK.
* Windows, Linux, macOS, or another target platform.
* Generated code.
* A specific framework.
* A third-party library.
* Project architecture.
* Deployment requirements.
* Additional security or reliability requirements.

Project-specific rules should remain clearly separated from the shared baseline.

They should not silently modify the common standard or create a conflicting replacement for it.

---

# Source of Truth

The configuration files are authoritative:

* `.clang-format` defines formatting behavior.
* `.clang-tidy` defines static-analysis checks and their configuration.
* `README.md` documents the purpose, organization, rationale, usage, and maintenance of the standard.

When this README and a configuration file disagree, the configuration file is authoritative and the README should be corrected.

The goal is that every C++ repository adopting C++ Pro Style uses the same centrally maintained standard rather than maintaining independent interpretations of it.

---

# License

The configuration files are distributed under the MIT License.

Copyright © 2026 Amit Gefen.
