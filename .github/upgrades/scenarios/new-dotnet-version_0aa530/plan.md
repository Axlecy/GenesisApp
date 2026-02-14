# .NET 10.0 Upgrade Plan

## Table of Contents

- [Executive Summary](#executive-summary)
- [Migration Strategy](#migration-strategy)
- [Detailed Dependency Analysis](#detailed-dependency-analysis)
- [Project-by-Project Plans](#project-by-project-plans)
- [Package Update Reference](#package-update-reference)
- [Breaking Changes Catalog](#breaking-changes-catalog)
- [Testing & Validation Strategy](#testing--validation-strategy)
- [Complexity & Effort Assessment](#complexity--effort-assessment)
- [Risk Management](#risk-management)
- [Source Control Strategy](#source-control-strategy)
- [Success Criteria](#success-criteria)

---

## Executive Summary

### Overview
This plan outlines the upgrade path for the **BlazorWebAssApp** solution from **.NET 6.0** to **.NET 10.0 (LTS)**.

### Scope
- **Projects to upgrade**: 1 (BlazorWebAssApp.csproj)
- **Project type**: Blazor WebAssembly
- **Current framework**: .NET 6.0
- **Target framework**: .NET 10.0 (LTS)
- **Total issues**: 6 (1 Mandatory, 5 Potential)
- **Affected files**: 2
- **Packages requiring update**: 2

### Classification
**Complexity Level: SIMPLE**

This solution qualifies as a Simple upgrade because:
- Single project with minimal dependencies
- Only 2 NuGet packages requiring updates (both standard Microsoft packages)
- No custom features or complex technologies
- No inter-project dependencies
- Low code volume (11 lines)
- Standard Blazor WebAssembly template structure

### Recommended Strategy
**All-at-Once Migration** — Update all components in a single coordinated change since the solution is simple and self-contained.

---

## Migration Strategy

### Selected Strategy: All-at-Once Migration

Given the simplicity of this solution, we'll execute a coordinated upgrade in a single pass.

### Rationale
- **Single Project**: No cross-project dependencies to manage
- **Standard Template**: Blazor WASM project follows Microsoft's standard structure
- **Minimal Surface Area**: Only 2 files affected
- **Low Risk**: Breaking changes are minimal and well-documented
- **Quick Validation**: Can build and test immediately after changes

### Execution Phases

#### Phase 1: Pre-Migration Validation (5 minutes)
1. Verify .NET 10 SDK is installed
2. Ensure no breaking changes affect current code
3. Document current state (build success, tests passing)

#### Phase 2: Core Upgrade (10 minutes)
1. Update target framework in `.csproj` from `net6.0` to `net10.0`
2. Update NuGet packages to .NET 10 compatible versions
   - Microsoft.AspNetCore.Components.WebAssembly: 6.0.1 → 10.0.3
   - Microsoft.AspNetCore.Components.WebAssembly.DevServer: 6.0.1 → 10.0.3

#### Phase 3: Validation & Testing (10 minutes)
1. Restore NuGet packages
2. Build solution
3. Run tests (if available)
4. Manual smoke testing

#### Phase 4: Completion (5 minutes)
1. Commit changes to upgrade branch
2. Document any issues encountered
3. Create PR for review

### Rollback Strategy
If critical issues are discovered:
- Git branch `main` remains untouched
- Simply discard `upgrade-to-NET10` branch and restart
- All changes are isolated and reversible

---

## Detailed Dependency Analysis

### Project Dependency Graph

```
Level 0 (Foundation):
└── BlazorWebAssApp.csproj
    ├── Target: net6.0 → net10.0
    ├── Type: Blazor WebAssembly Application
    ├── Issues: 6 (1 mandatory, 5 potential)
    └── Dependencies: 0 project references
```

### Dependency Details

**BlazorWebAssApp.csproj**
- **Current Framework**: net6.0
- **Target Framework**: net10.0
- **Project Type**: Blazor WebAssembly Application
- **Project References**: None
- **NuGet Dependencies**: 2 packages
  - Microsoft.AspNetCore.Components.WebAssembly (6.0.1)
  - Microsoft.AspNetCore.Components.WebAssembly.DevServer (6.0.1)

### Migration Order
Since there's only one project with no internal dependencies, the migration order is straightforward:
1. **BlazorWebAssApp** — Standalone application, can be upgraded independently

### External Dependencies Assessment
All external dependencies are standard Microsoft packages with clear upgrade paths to .NET 10.

---

## Project-by-Project Plans

### BlazorWebAssApp.csproj

**Project Type**: Blazor WebAssembly Application  
**Current Framework**: net6.0  
**Target Framework**: net10.0  
**Complexity**: Low  
**Estimated Effort**: 15-20 minutes

#### Issues Summary
- **Total Issues**: 6
  - Mandatory: 1 (target framework change)
  - Potential: 5 (package updates and API behavioral changes)

#### Required Changes

##### 1. Target Framework Update (Mandatory)
**File**: `BlazorWebAssApp.csproj`  
**Action**: Update `<TargetFramework>` element
```xml
<!-- Before -->
<TargetFramework>net6.0</TargetFramework>

<!-- After -->
<TargetFramework>net10.0</TargetFramework>
```

##### 2. NuGet Package Updates (Potential)
**File**: `BlazorWebAssApp.csproj`

| Package | Current | Target | Action |
|---------|---------|--------|--------|
| Microsoft.AspNetCore.Components.WebAssembly | 6.0.1 | 10.0.3 | Update version |
| Microsoft.AspNetCore.Components.WebAssembly.DevServer | 6.0.1 | 10.0.3 | Update version |

**Implementation**:
```xml
<!-- Before -->
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly" Version="6.0.1" />
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly.DevServer" Version="6.0.1" PrivateAssets="all" />

<!-- After -->
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly" Version="10.0.3" />
<PackageReference Include="Microsoft.AspNetCore.Components.WebAssembly.DevServer" Version="10.0.3" PrivateAssets="all" />
```

##### 3. API Behavioral Changes (Potential)
**File**: `Program.cs` (Line 8)  
**Affected API**: `System.Uri` constructor

**Issue**: The `Uri` class has behavioral changes in .NET 10 related to URI parsing and validation.

**Current Code**:
```csharp
builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });
```

**Assessment**: 
- This usage is standard and should continue to work correctly
- The `BaseAddress` property provides a valid URI string
- No code changes required, but validation recommended after upgrade

**Action**: No changes needed. Validate during testing that HttpClient base address is set correctly.

#### Validation Steps
1. Build the project: `dotnet build`
2. Verify no compilation errors
3. Run the application: `dotnet run`
4. Test basic navigation and functionality
5. Verify HttpClient calls work correctly with base address

---

## Package Update Reference

### Packages Requiring Updates

| Package Name | Current Version | Target Version | Status | Notes |
|--------------|----------------|----------------|---------|-------|
| Microsoft.AspNetCore.Components.WebAssembly | 6.0.1 | 10.0.3 | ✅ Compatible | Standard Blazor WASM package |
| Microsoft.AspNetCore.Components.WebAssembly.DevServer | 6.0.1 | 10.0.3 | ✅ Compatible | Development server (PrivateAssets) |

### Update Commands

```bash
# Update all packages at once
dotnet add BlazorWebAssApp/BlazorWebAssApp.csproj package Microsoft.AspNetCore.Components.WebAssembly --version 10.0.3
dotnet add BlazorWebAssApp/BlazorWebAssApp.csproj package Microsoft.AspNetCore.Components.WebAssembly.DevServer --version 10.0.3
```

### Package Compatibility Notes

**Microsoft.AspNetCore.Components.WebAssembly**
- Core package for Blazor WebAssembly applications
- Version 10.0.3 is the stable release for .NET 10
- No breaking changes in typical usage patterns
- Reference: https://go.microsoft.com/fwlink/?linkid=2262530

**Microsoft.AspNetCore.Components.WebAssembly.DevServer**
- Development-time server for Blazor WASM
- Used only during development (PrivateAssets="all")
- Version 10.0.3 aligns with .NET 10 tooling
- No impact on production builds

---

## Breaking Changes Catalog

### API Behavioral Changes

#### System.Uri Constructor (Api.0003)

**Severity**: Potential  
**Affected File**: `Program.cs:8`  
**Affected Code**:
```csharp
builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });
```

**Description**:
The `System.Uri` class has behavioral changes in .NET 10 related to URI parsing, validation, and handling of edge cases.

**Impact Assessment**:
- **Low Impact** — The usage in this project is straightforward
- The `BaseAddress` property provides a valid, well-formed URI string
- Standard HTTP/HTTPS URIs are not affected by most behavioral changes

**Mitigation**:
- No code changes required for this standard usage
- Validate during testing that HttpClient initialization works correctly
- Monitor for any URI-related exceptions during runtime

**Testing Checklist**:
- ✅ Verify application starts without errors
- ✅ Confirm HttpClient base address is correctly set
- ✅ Test any API calls that use the HttpClient instance

**Reference**: 
- .NET 10 Breaking Changes: https://learn.microsoft.com/en-us/dotnet/core/compatibility/10.0

### No Critical Breaking Changes

This upgrade has **no critical breaking changes** that require immediate code modifications. All identified changes are potential behavioral differences that should be validated through testing.

---

## Testing & Validation Strategy

### Pre-Migration Baseline
1. **Document Current State**
   - Capture successful build output
   - Document application startup behavior
   - Note any existing warnings or issues

2. **Environment Verification**
   - Confirm .NET 10 SDK is installed: `dotnet --list-sdks`
   - Verify no global.json constraints blocking .NET 10

### Post-Migration Validation

#### Build Validation
```bash
# Clean build to ensure no cached artifacts
dotnet clean
dotnet restore
dotnet build --configuration Release
```

**Success Criteria**:
- Zero build errors
- No new warnings related to deprecated APIs
- All dependencies restored successfully

#### Runtime Validation
1. **Application Startup**
   ```bash
   dotnet run --project BlazorWebAssApp/BlazorWebAssApp.csproj
   ```
   - Application starts without exceptions
   - No console errors during initialization
   - Blazor components load correctly

2. **HttpClient Functionality**
   - Verify `HttpClient` is configured with correct `BaseAddress`
   - Test any API calls to ensure routing works
   - Check browser console for errors

3. **Component Rendering**
   - Navigate through all pages/routes
   - Verify components render correctly
   - Test any interactive elements

#### Regression Testing
Since this is a template-based Blazor WASM app:
- Test default Counter page functionality
- Test navigation between pages
- Verify styling/CSS loads correctly
- Check browser compatibility (Chrome, Edge, Firefox)

### Test Execution Order
1. SDK verification (1 min)
2. Clean build (2 min)
3. Application startup test (2 min)
4. Basic navigation test (3 min)
5. HttpClient validation (2 min)

**Total Estimated Testing Time**: 10 minutes

---

## Complexity & Effort Assessment

### Overall Complexity: **SIMPLE** ⭐

#### Complexity Factors

| Factor | Rating | Rationale |
|--------|--------|----------|
| Project Count | Low | Single project |
| Dependencies | Low | 2 standard Microsoft packages |
| Custom Code | Low | Minimal (11 lines) |
| Breaking Changes | Low | 3 potential behavioral changes, none critical |
| Technology Stack | Low | Standard Blazor WASM template |
| Test Coverage | N/A | No tests detected |

#### Effort Breakdown

| Phase | Time Estimate | Tasks |
|-------|--------------|-------|
| Pre-Migration | 5 minutes | SDK check, baseline documentation |
| Code Changes | 5 minutes | Update .csproj (TFM + packages) |
| Build & Restore | 5 minutes | Restore packages, compile |
| Testing | 10 minutes | Startup, navigation, functionality |
| Documentation | 5 minutes | Update README, commit messages |
| **Total** | **30 minutes** | End-to-end upgrade |

#### Risk-Adjusted Estimate
- **Optimistic**: 20 minutes (no issues encountered)
- **Realistic**: 30 minutes (minor troubleshooting)
- **Pessimistic**: 45 minutes (unexpected compatibility issues)

### Skill Level Required
**Junior to Mid-Level Developer**
- Basic understanding of .NET project structure
- Familiarity with NuGet package management
- Ability to read build errors and logs

---

## Risk Management

### Risk Assessment Matrix

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| Build failures after package update | Low | Medium | Incremental package updates; rollback if needed |
| Runtime errors from Uri behavioral changes | Low | Low | Thorough testing of HttpClient functionality |
| Missing .NET 10 SDK | Medium | High | Verify SDK before starting; install if needed |
| Compatibility issues with browser | Low | Low | Test in multiple browsers |

### Mitigation Strategies

#### Pre-Migration Safeguards
1. **Version Control**
   - All work on dedicated `upgrade-to-NET10` branch
   - Main branch remains stable
   - Easy rollback via branch deletion

2. **SDK Verification**
   ```bash
   dotnet --version
   dotnet --list-sdks
   ```
   - Ensure .NET 10 SDK is installed before proceeding
   - Install from: https://go.microsoft.com/fwlink/?linkid=2220939

3. **Baseline Documentation**
   - Capture current build output
   - Screenshot working application
   - Note any pre-existing warnings

#### Migration Safeguards
1. **Incremental Changes**
   - Update TFM first, build to confirm
   - Update packages second, restore and build
   - Commit after each successful step

2. **Validation Gates**
   - Must pass build before proceeding to testing
   - Must pass startup test before functional testing

#### Post-Migration Monitoring
1. **Immediate Validation**
   - Build must succeed with zero errors
   - Application must start without exceptions
   - HttpClient must initialize correctly

2. **Rollback Plan**
   ```bash
   # If critical issues found:
   git checkout main
   git branch -D upgrade-to-NET10
   ```

### Contingency Plans

**Scenario: Build Fails After TFM Update**
- Action: Check error messages for missing SDK
- Action: Verify no global.json version conflicts
- Fallback: Revert TFM change, investigate specific error

**Scenario: Package Restore Fails**
- Action: Clear NuGet cache: `dotnet nuget locals all --clear`
- Action: Verify network connectivity to NuGet.org
- Fallback: Use specific package versions if latest unavailable

**Scenario: Runtime Errors After Upgrade**
- Action: Check browser console for detailed error messages
- Action: Review Uri-related code in Program.cs
- Fallback: Add error handling around HttpClient initialization

### Success Indicators
✅ Build completes with 0 errors  
✅ Application starts without exceptions  
✅ All pages load correctly  
✅ HttpClient base address is configured  
✅ No console errors in browser

---

## Source Control Strategy

### Branch Strategy

**Source Branch**: `main`  
**Working Branch**: `upgrade-to-NET10`  
**Integration Branch**: `main` (via PR)

### Commit Strategy

Use atomic commits for each logical change:

1. **Initial Setup Commit**
   ```
   git commit -m "chore: prepare for .NET 10 upgrade - baseline state"
   ```
   - Commit any pending changes
   - Document current working state

2. **Target Framework Commit**
   ```
   git commit -m "feat: update target framework to .NET 10"
   ```
   - Change `<TargetFramework>` to net10.0
   - Build verification complete

3. **Package Update Commit**
   ```
   git commit -m "feat: update NuGet packages to .NET 10 versions"
   ```
   - Update both Blazor packages to 10.0.3
   - Restore and build successful

4. **Documentation Commit** (if needed)
   ```
   git commit -m "docs: update README for .NET 10"
   ```
   - Update any version references
   - Add upgrade notes

### Pull Request Process

**PR Title**: "Upgrade BlazorWebAssApp to .NET 10.0 (LTS)"

**PR Description Template**:
```markdown
## Overview
Upgrades the BlazorWebAssApp solution from .NET 6.0 to .NET 10.0 (LTS)

## Changes
- Updated target framework: net6.0 → net10.0
- Updated Microsoft.AspNetCore.Components.WebAssembly: 6.0.1 → 10.0.3
- Updated Microsoft.AspNetCore.Components.WebAssembly.DevServer: 6.0.1 → 10.0.3

## Testing Completed
- ✅ Build succeeds with zero errors
- ✅ Application starts without exceptions
- ✅ Navigation works correctly
- ✅ HttpClient properly configured

## Breaking Changes
No code changes required. Potential behavioral changes in System.Uri validated during testing.

## Rollback Plan
If issues found in production, revert this PR. All changes isolated to this branch.
```

### Review Checklist
- [ ] All commits have clear, descriptive messages
- [ ] Build succeeds on upgrade branch
- [ ] No new warnings introduced
- [ ] Application tested manually
- [ ] README updated (if applicable)
- [ ] No merge conflicts with main

### Merge Strategy
**Recommended**: Squash and merge (for clean history)  
**Alternative**: Regular merge (to preserve commit history)

### Post-Merge Actions
1. Delete `upgrade-to-NET10` branch
2. Pull latest `main` to local
3. Verify application works from `main`
4. Tag release: `git tag v10.0.0-upgrade`

---

## Success Criteria

### Build Success Criteria
✅ **Zero Build Errors**
   - `dotnet build` completes without errors
   - All dependencies resolve correctly
   - No missing SDK warnings

✅ **No Critical Warnings**
   - No new deprecation warnings
   - No API compatibility warnings
   - Existing warnings (if any) documented

✅ **Successful Package Restore**
   - All NuGet packages download successfully
   - Package versions match expectations (10.0.3)
   - No dependency conflicts

### Runtime Success Criteria
✅ **Application Startup**
   - Application launches without exceptions
   - No console errors during initialization
   - WebAssembly runtime loads successfully

✅ **HttpClient Configuration**
   - BaseAddress set correctly from HostEnvironment
   - No URI parsing errors
   - HTTP client available via dependency injection

✅ **Component Rendering**
   - All Blazor components render correctly
   - Navigation works between pages
   - No JavaScript errors in browser console

### Functional Success Criteria
✅ **Core Functionality**
   - Default pages load (Home, Counter, etc.)
   - Interactive elements work (Counter button)
   - Routing functions correctly

✅ **Cross-Browser Compatibility**
   - Works in Chrome/Edge
   - Works in Firefox
   - No browser-specific errors

### Documentation Success Criteria
✅ **Code Changes Documented**
   - Clear commit messages
   - PR description complete
   - Breaking changes documented

✅ **Version Information Updated**
   - Project file reflects net10.0
   - Package references show 10.0.3
   - README updated (if version mentioned)

### Source Control Success Criteria
✅ **Branch Management**
   - Changes isolated to upgrade branch
   - Main branch unaffected during work
   - No merge conflicts

✅ **Code Review Ready**
   - All tests pass
   - Changes are minimal and focused
   - Easy to review and understand

### Acceptance Criteria
The upgrade is considered **successful** when:

1. ✅ Project builds without errors on .NET 10
2. ✅ Application runs without runtime exceptions
3. ✅ All core functionality works as expected
4. ✅ No regressions from .NET 6 version
5. ✅ Code changes are committed and documented
6. ✅ PR is ready for review and merge

### Definition of Done
- All success criteria met
- Testing completed and documented
- PR approved and merged to main
- Upgrade branch deleted
- Team notified of .NET 10 availability
