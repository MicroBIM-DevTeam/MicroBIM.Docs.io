---
_layout: landing
title: MicroBIM Fire – Developer Docs
---

# 👨‍💻 MicroBIM Developer Documentation

Welcome to the official documentation for **MicroBIM Fire**.

> This documentation is intended **for developers only**.  
> It provides technical references and documentation for the MicroBIM codebase and tools.

---

## 🔗 Follow Us

<div style="display: flex; gap: 1rem; flex-wrap: wrap;">
  <a href="https://www.instagram.com/microbimfire/">
    <img src="https://img.shields.io/badge/-Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white" />
  </a>
  <a href="https://www.youtube.com/channel/UCgGCn-xxaQlFwA4JlBHB_NA">
    <img src="https://img.shields.io/badge/-YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white" />
  </a>
  <a href="https://www.linkedin.com/company/microbimfire">
    <img src="https://img.shields.io/badge/-LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" />
  </a>
  <a href="https://x.com/MicroBimFire">
    <img src="https://img.shields.io/badge/-X-black?style=for-the-badge&logo=x&logoColor=white" />
  </a>
  <a href="https://www.facebook.com/MicroBim_Fire/">
    <img src="https://img.shields.io/badge/-Facebook-1877F2?style=for-the-badge&logo=facebook&logoColor=white" />
  </a>
</div>




<br><br>
<br><br>
# 📌 Code Policy – MicroBIM

This document outlines the coding standards and collaboration practices for our team to ensure consistency, quality, and maintainability across all projects.

---

## 1. Code Formatting

- Use **4 spaces** for indentation (no tabs).
- Follow standard **C# naming conventions**:
  - `PascalCase` for class names, methods, and properties.
  - `camelCase` for local variables and parameters.
  - `ALL_CAPS` for constants.
- Place `using` directives **inside the namespace**.
- Always include **XML documentation** for public members.
- Use **expression-bodied members** for simple one-liners when appropriate.

**Tools**: All code must be auto-formatted using the IDE's code formatting tools (e.g., Visual Studio or Rider). Consider using `.editorconfig` to enforce formatting.

---

## 2. Branching Strategy

- **Main branches**:
  - `main`: Stable production-ready code.
  - `develop`: Latest development changes.
- **Feature branches**:
  - Naming convention: `feature/short-description`
  - Branch off: `develop`
- **Bugfix branches**:
  - Naming convention: `bugfix/short-description`
  - Branch off: `develop` or `main` depending on context
- **Release branches**:
  - Naming convention: `release/x.y.z`

🔁 All branches must be regularly updated with `develop` or `main` before creating a pull request.

---

## 3. Pull Request Process

- PRs should target `develop` unless it's a hotfix.
- Each PR should be linked to a work item (task, bug, story).
- PR title must be **clear and descriptive**.
- Provide a meaningful description including:
  - Purpose of the change
  - Any blockers or pending tasks
  - Screenshots or test results (if applicable)

---

## 4. Review Guidelines

- At least **one reviewer** is required before merging.
- Use **suggestions** instead of just comments when possible.
- Focus on:
  - Code clarity
  - Business logic correctness
  - Performance and scalability
  - Potential bugs or edge cases
- Reviewers should respond within **24 working hours**.
- ❗ No code should be merged without a proper review.

---

## 5. Testing Requirements

- All features must include **unit tests**.
- Critical logic should be covered by **integration tests**.
- ✅ All tests must pass before a PR is approved.
- Use **mocking** where external dependencies are involved.
- Prefer **automated testing** over manual testing whenever possible.

---

## 6. Commit Message Conventions

- Use **imperative tone**: `Add user authentication`, not `Added` or `Adding`.
- Use the following structure:

[Type] Short summary (max 72 characters)

Optional longer description. Wrap at 100 characters.

References: #TicketNumber


- Common types:
- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code refactoring (no feature or fix)
- `test`: Adding or updating tests
- `docs`: Documentation only changes
- `chore`: Maintenance tasks (build config, CI, etc.)

---

## 7. Logging and Error Handling

- Use a consistent logging framework such as **Serilog** or **NLog** across the entire codebase.
- All exceptions and errors **must be logged** with sufficient detail to allow effective debugging:
- Include exception message, stack trace, and contextual data.
- ❌ Never silently swallow exceptions:
- ❌ `catch { }`
- ✅ `catch (Exception ex) { _logger.LogError(ex, "Error occurred while processing user request."); }`
- When necessary, rethrow exceptions using `throw;` to preserve the stack trace.

---

## 8. Code Comments and Documentation

- Use **XML documentation comments** (`///`) on all public classes, methods, properties, and interfaces.
- These will be used for IntelliSense and API documentation.
- Example:
  ```csharp
  /// <summary>
  /// Gets a list of users based on the specified role.
  /// </summary>
  /// <param name="role">The role to filter users by.</param>
  /// <returns>A list of users in the given role.</returns>
  public List<User> GetUsersByRole(string role) { ... }
  ```

- Use inline comments **sparingly**:
- ✅ `// This workaround is needed due to a bug in the third-party library.`
- ❌ `// Increase i by 1`
- Always keep comments **up to date**. Outdated or misleading comments must be removed or corrected.

---

## 9. Standardized Operation Results – `MbResult<T>`

To ensure consistent handling of operation outcomes and avoid using `null` or exception-based flow for regular control, **all methods that return the result of an operation must use** the `MbResult<T>` class from `MB.Common.Utils.Result`.

### ✅ Usage Guidelines

- Use `MbResult<T>.Success(value)` when the operation completes successfully.
- Use `MbResult<T>.Failure(errorMessage)` when the operation fails with a meaningful error message.
- Never return `null` or raw objects for operations that can fail.
- Avoid throwing exceptions for expected control flow; instead, use `MbResult<T>` to indicate success or failure.

### 📌 Benefits

- Encourages **safe and predictable** method contracts.
- Improves readability and exception traceability.
- Works across all Revit versions with **conditional compilation support**.

### 🧪 Example

```csharp
public static MbResult<ObservableCollection<FamilySymbol>> GetFamilyTypesByCategoryOrderedByName(this Document doc, BuiltInCategory category)
{
  if (doc == null)
      return MbResult<ObservableCollection<FamilySymbol>>.Failure("The Revit document cannot be null.");

  try
  {
      var familyTypes = new FilteredElementCollector(doc)
          .OfClass(typeof(FamilySymbol))
          .OfCategory(category)
          .Cast<FamilySymbol>()
          .OrderBy(f => f.Name)
          .ToList();

      return MbResult<ObservableCollection<FamilySymbol>>.Success(new ObservableCollection<FamilySymbol>(familyTypes));
  }
  catch (Exception ex)
  {
      return MbResult<ObservableCollection<FamilySymbol>>.Failure($"An error occurred: {ex.Message}");
  }
}
```

### ⚠ Enforcement
Any method that involves possible failure or data lookup must use MbResult<T> instead of returning raw types or throwing exceptions directly.

---

## 📦 Download NuGet Package

You can download the latest release of the `MicroBIM.Revit.Template` package below:

[![Download Now](https://img.shields.io/badge/Download-v1.6.0-blue?style=for-the-badge&logo=nuget&logoColor=white)](downloads/MicroBIM.Revit.Template.1.6.0.nupkg)

## 📥 How to Install

To install the MicroBIM.Revit.Template package, follow these steps:

```bash
dotnet new uninstall MicroBIM.Revit.Template
```
```bash
dotnet new install MicroBIM.Revit.Template.1.6.0.nupkg
```
