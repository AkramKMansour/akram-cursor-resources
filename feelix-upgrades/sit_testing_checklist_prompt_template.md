# SIT Testing Checklist Generator Template

## Instructions for AI Assistant

I need to create a **targeted** SIT (System Integration Testing) checklist for my React project that focuses on recently changed components and can be used for production-like testing.

Please generate a detailed testing checklist in markdown file called `SIT_TESTING_CHECKLIST_[PROJECT_NAME]`, similar to a comprehensive testing playbook, covering:

### 0. Git History Analysis & Component Targeting

**CRITICAL FIRST STEP**: Before analyzing the entire codebase, identify which components have actually been modified to focus testing efforts efficiently.

#### **Git History Commands to Execute (Full Branch History):**
```bash
# Find branch fork-point against default branch (master fallback to main)
BASE=$(git merge-base --fork-point origin/master HEAD || git merge-base origin/master HEAD || git merge-base origin/main HEAD)
# List all commits and changed files from fork-point to HEAD
git log --oneline --name-only ${BASE}..HEAD
# List unique files changed in the branch
git diff --name-only ${BASE}..HEAD
```

#### **Changed Component Analysis:**
1. **Extract Component Files**: Filter git results for `.tsx`, `.jsx`, `.ts`, `.js` files in relevant directories
2. **Categorize Changes**: Group by:
   - **New Components**: Files that are completely new
   - **Modified Components**: Existing files with changes
   - **Related Dependencies**: Files that import/depend on changed components
   - **Shared Services**: API, utility, or service files that affect multiple components

#### **Scope Determination:**
Based on git analysis, determine testing scope:
- **Feature Branch Testing**: Focus only on files changed in the current branch vs main
- **Release Testing**: Analyze all changes since last release tag
- **Hotfix Testing**: Very targeted - only the specific fix and its immediate dependencies
- **Integration Testing**: Include components that consume changed APIs or services

#### **Change Impact Assessment:**
For each changed file, analyze:
- **UI Component Changes**: Visual/interaction changes requiring UI testing
- **Logic Changes**: Business logic requiring functional testing  
- **API Changes**: Backend integration requiring network/error testing
- **Props/Interface Changes**: Components consuming the changed component need testing
- **State Management Changes**: Components sharing state with changed components

#### **Testing Priority Matrix:**
Create prioritized testing focus:
1. **High Priority**: Directly modified components with user-facing changes
2. **Medium Priority**: Components that import/consume changed components
3. **Low Priority**: Shared utilities with minimal UI impact
4. **Cross-cutting**: Authentication, error handling, permissions if touched

#### **Documentation of Changes:**
Include in the final checklist:
```markdown
## 🎯 **Testing Scope Based on Recent Changes**
*(Git Analysis: [date range] | [branch] | [commit range])*

### **🔴 High Priority - Direct Changes:**
- Component1.tsx *(Modified: lines 45-67, 89-102)*
- Component2.tsx *(New component)*

### **🟡 Medium Priority - Dependent Components:**  
- ParentComponent.tsx *(Imports Component1)*
- SiblingComponent.tsx *(Shares API with Component1)*

### **⚪ Cross-cutting Concerns:**
- services/apiHandler.ts *(Modified error handling)*
```

### 1. Targeted Codebase Analysis
- **Search for actual component implementations** - Use codebase search to find how components are actually implemented
- **Analyze conditional rendering logic** - Look for `if/else`, ternary operators, and state-based rendering
- **Check component props and state interfaces** - Identify what states actually exist vs assumed states
- **Map error handling patterns** - Find how errors are actually displayed (alerts, modals, inline messages)
- **Verify loading state implementations** - Check if loading states exist and how they're implemented
- **Examine authentication/permission logic** - Look for actual access control code, not theoretical permission levels
- Group components by pages/sections as they appear in the SIT environment
- Identify all user interaction points and data flows

### 2. Component State Documentation
For each component found, provide comprehensive state coverage including:
- **Data States**: Empty, populated, loading, error conditions
- **Interaction States**: Enabled, disabled, focused, clicked, hover states
- **Validation States**: Valid, invalid, error messages, field-level validation
- **Permission States**: Read-only, edit, admin, no access scenarios
- **Network States**: Success, error, timeout, offline handling
- **Visual States**: Expanded, collapsed, selected, highlighted states
- **Form States**: Pristine, dirty, submitting, submitted conditions

**MANDATORY CODE REFERENCES**: Every component section MUST include specific file and line references in the format:
```markdown
### **Component Name** *(Code Reference: ComponentFile.tsx lines 45-67)*
#### **Sub-section** *(Code Reference: ComponentFile.tsx lines 120-145, AnotherFile.tsx lines 89-102)*
```

**CRITICAL CODE VERIFICATION REQUIREMENTS**: Before adding ANY test case, verify it exists in the actual code:

**❌ DO NOT ASSUME these common patterns exist without verification:**
- Table loading states (many apps show spinner INSTEAD OF table, not within table)
- **Button state variations** (Save disabled/Cancel enabled, different error states, permission-based disabling)
- **Form state tracking** (pristine/dirty states, modification detection)
- Auto-dismiss alerts (verify setTimeout or similar auto-dismiss logic exists)
- Multiple alert stacking (check if multiple alerts can actually display simultaneously)
- Granular permission levels (verify actual permission checking code, not theoretical roles)
- Modal loading states (check if modals actually fetch data or are static)
- **Complex button interactions** (loading indicators on individual buttons vs parent components)

**✅ VERIFY THESE PATTERNS BY SEARCHING FOR:**
- Loading state variables: `isLoading`, `loading`, `pending`
- Conditional rendering: `? <Spinner /> :`, `&& loading`, `if (loading)`
- Error display logic: `error &&`, `errors.map`, `showError`
- Permission checks: `hasPermission`, `canEdit`, `role ===`, `access`
- Alert dismissal: `onDismiss`, `setTimeout`, `autoHide`
- Multiple states: Array.map for alerts, multiple conditional renders
- **Button state logic**: `disabled={condition}`, button-specific `isLoading` props
- **Form state tracking**: `dirty`, `pristine`, `modified`, change detection
- **Parent vs child loading**: Where exactly is loading state managed?

**Examples of code-driven verification:**
- Don't separate "1 item" vs "multiple items" unless code has `length === 1` vs `length > 1` logic
- Don't add "disabled button state" unless you find `disabled={condition}` in the code
- Don't add "table error state" unless you find error handling within table rendering logic
- **Don't assume "Save disabled, Cancel enabled"** unless you find different `disabled` conditions for each button
- **Don't assume "form modification tracking"** unless you find actual change detection logic
- **Don't create different button states** unless buttons actually have different props/logic

### 3. Page-Level Organization
Structure the checklist by:
- **Main Application Pages**: Primary user interfaces and workflows
- **Component Displays**: Break down each page into testable component parts
- **Cross-cutting Concerns**: Authentication, permissions, responsive design
- **User Interaction Flows**: Complete end-to-end user journeys

### 4. Production Environment Focus
- **Exclude development-only features** such as sandboxes or local development tools
- **Focus on SIT/production environment scenarios** - only test what exists in production builds
- **Verify authentication flow in code** - check actual auth implementation, not assumed patterns
- **Map actual API error handling** - search for error handler functions and HTTP status code handling
- **Check network state management** - look for actual offline/timeout handling, not theoretical scenarios

### 5. Comprehensive Testing Coverage
Include testing for (but ONLY if verified to exist in code):
- **Authentication & Permission States**: Based on actual auth implementation found in code
- **Network & Data States**: Match actual error handler logic and API response patterns
- **Responsive Design States**: Desktop, tablet, mobile viewports (if responsive design exists)
- **Browser Compatibility States**: Cross-browser testing requirements
- **Performance States**: Loading performance, large datasets, concurrent users
- **User Interaction Flows**: Complete user journeys and error recovery

**VALIDATION STEP**: For each section, provide:
1. **Code reference with exact file and line numbers** in the section header (e.g., `*(Code Reference: ComponentName.tsx lines 45-67)*`)
2. **Brief explanation** of why the test case is valid (e.g., "Found loading state in component state interface" or "Error handling verified in errorHandler.ts")
3. **Specific line references** for complex behaviors (e.g., "Save button disabled logic (lines 206-207, 358-370)")

### 6. Formatting Requirements
- Use markdown formatting with clear section headers
- **MANDATORY**: Include code references in EVERY section header using format: `*(Code Reference: FileName.tsx lines X-Y)*`
- Use checkboxes `- [ ]` for each testable state
- Include descriptive state descriptions explaining what to validate
- Use hierarchical structure with clear nesting (##, ###, ####)
- Maintain consistent naming conventions for professional documentation
- **Add specific line references** for complex test cases within the test description (e.g., "Save button disabled (lines 206-207)")
- **Avoid duplication**: When multiple components share identical patterns, group them together with a note and reference all relevant files
- **Reference previous sections**: Use cross-references instead of repeating identical test cases

### 7. Project-Specific Customization
Consider the following about my project:
- Current React version and component architecture
- Authentication system and user permission levels
- Integration points with external systems
- Specific browser or device requirements
- Accessibility requirements or compliance standards

### 8. Success Criteria
The generated checklist should:
- **Be git-history driven**: Focus testing efforts on components that have actually changed
- **Be 100% code-verified**: Every test case must be traced back to actual code implementation
- **Include change impact analysis**: Document which components changed and why they need testing
- **Provide targeted testing scope**: Clearly separate high/medium/low priority testing based on git analysis
- **Include code references**: Every section header must include specific file names and line numbers
- **Provide line-level traceability**: Complex behaviors should reference exact line numbers within test descriptions
- **Avoid assumptions**: No test cases based on "common patterns" without code verification
- **Be specific enough** to avoid ambiguity in testing scenarios
- **Eliminate redundancy**: No duplicate test cases unless code logic justifies different behavior
- **Reflect actual error handling**: Network/API error states must match actual error handler implementation
- **Match real component states**: Loading, disabled, validation states must exist in component interfaces
- **Enable instant verification**: QA testers can immediately locate relevant code using provided references
- **Be suitable for sharing** with QA teams and stakeholders
- **Provide clear progress tracking** with actionable test cases
- **Show testing efficiency**: Document how git analysis reduced testing scope compared to full regression testing

**QUALITY CHECK**: Before finalizing, review each test case and ask: "Can I point to specific code that implements this behavior?" If not, remove the test case.

---

## **🔄 Recommended Workflow**

### **Phase 1: Git Analysis (5-10 minutes)**
1. **Run git history commands** to identify changed files
2. **Filter for relevant components** (.tsx, .jsx, .ts files in src/)
3. **Categorize changes** by priority (high/medium/low)
4. **Document the scope** in the testing checklist header

### **Phase 2: Targeted Code Analysis (15-20 minutes)**
1. **Focus on high-priority changed components first**
2. **Use codebase_search** to understand component behavior
3. **Find component dependencies** with grep_search for imports
4. **Read actual component files** to verify states and logic
5. **Skip unchanged areas** unless they're critical dependencies

### **Phase 3: Test Case Generation (20-30 minutes)**
1. **Create test cases ONLY for verified behaviors** in changed components
2. **Include dependency testing** for components that import changed ones
3. **Reference exact code locations** in every test case
4. **Prioritize testing** based on change impact analysis
5. **Cross-reference similar patterns** to avoid duplication

### **Phase 4: Efficiency Documentation (5 minutes)**
1. **Document testing scope reduction** (e.g., "Focused on 3 components vs 15 total")
2. **Note any cross-cutting concerns** that affect multiple areas
3. **Provide change summary** for stakeholder communication

### **Example Output Structure:**
```markdown
# SIT Testing Checklist - [PROJECT_NAME]
*(Generated: [date] | Git Analysis: full branch (BASE..HEAD) | Scope: Feature branch testing)*

## 🎯 **Testing Scope Reduction**
- **Total Components in Project**: 15
- **Changed Components**: 3 (high priority) + 2 dependencies (medium priority)
- **Testing Efficiency**: ~67% reduction in testing scope

## **🔴 High Priority Testing - Direct Changes**
[Components that were actually modified]

## **🟡 Medium Priority Testing - Dependencies**  
[Components that import/consume changed components]

## **⚪ Minimal Testing - Cross-cutting**
[Only if shared services were modified]
```

---

## **📋 Code Verification Examples**

**Use these examples as templates for verifying test cases:**

### **✅ GOOD - Code-Verified Test Cases:**
- **"Modal Saving State"** *(Code Reference: AddEditModal.tsx lines 45-67)* - Verified: `isFormSaving` state in Modal component interface, renders `<Spinner />` when true
- **"Alert Dismissible State"** *(Code Reference: AlertComponent.tsx lines 23-34)* - Verified: `onDismiss={this.handleDismiss}` prop in Alert component
- **"Validation Error State"** *(Code Reference: FormField.tsx lines 89-102)* - Verified: `errors.fieldName && <ErrorMessage>` conditional rendering
- **"401 Authentication Error"** *(Code Reference: services/errorHandler.ts lines 20-35)* - Verified: errorHandler.ts handles `status === 401` with specific message

### **❌ BAD - Assumption-Based Test Cases:**
- **"Table Loading State"** *(Missing Code Reference)* - INVALID: No loading state within table, parent shows spinner instead of table
- **"Save disabled, Cancel enabled"** *(Missing Code Reference)* - INVALID: Both buttons have identical `disabled={props.isLoading}` logic
- **"Modified State - Both buttons enabled"** *(Missing Code Reference)* - INVALID: No form modification tracking exists
- **"Save Error State - Save enabled, error styling"** *(Missing Code Reference)* - INVALID: No error styling on buttons, errors shown as alerts
- **"Auto-dismiss Alert"** *(Missing Code Reference)* - INVALID: No setTimeout or auto-dismiss logic found in code
- **"Admin Permission Level"** *(Missing Code Reference)* - INVALID: No granular permission checking, only auth/unauth states
- **"Multiple Alerts Stacking"** *(Missing Code Reference)* - INVALID: Each component manages single alert state

### **🔍 Verification Commands to Use:**

#### **Git History Analysis Commands (Full Branch):**
```bash
run_terminal_cmd: "BASE=$(git merge-base --fork-point origin/master HEAD || git merge-base origin/master HEAD || git merge-base origin/main HEAD)"
run_terminal_cmd: "git log --oneline --name-only ${BASE}..HEAD"
run_terminal_cmd: "git diff --name-only ${BASE}..HEAD"
```

#### **Component Analysis Commands:**
```
codebase_search: "How does [component] handle loading states?"
codebase_search: "What error states exist in [component]?"
codebase_search: "How are permissions checked in this application?"
codebase_search: "Which components import [changed-component]?" (find dependencies)
grep_search: "import.*[ComponentName]" (find component dependencies)
grep_search: "isLoading|loading|pending" (to find loading states)
grep_search: "disabled=" (to find disabled button states)  
grep_search: "onDismiss|setTimeout" (to find dismissible alerts)
grep_search: "dirty|pristine|modified" (to find form state tracking)
read_file: [specific component file] (to see exact button/form logic)
```

**MANDATORY VERIFICATION STEP**: For every button/form test case, you MUST:
1. Read the actual component file 
2. Find the exact `disabled={}` or similar logic
3. Verify what conditions actually control the button states
4. **Add code reference to section header** with file name and line numbers
5. **Include specific line references** for complex logic within test descriptions
6. Only test states that actually exist in the code

---

## **⚠️ Common Button/Form Test Case Pitfalls**

**BEFORE creating button/form test cases, verify these common mistakes:**

### **🚫 Button State Assumptions:**
- ❌ **"Save disabled, Cancel enabled initially"** → ✅ Check if both buttons have same or different `disabled` logic
- ❌ **"Buttons show loading indicators"** → ✅ Check if individual buttons have loading states or parent handles loading
- ❌ **"Error styling on buttons"** → ✅ Check if buttons have error styles or errors shown elsewhere
- ❌ **"Permission-based button disabling"** → ✅ Check for actual permission checking code

### **🚫 Form State Assumptions:**
- ❌ **"Modified state tracking"** → ✅ Search for `dirty`, `pristine`, change detection logic
- ❌ **"Form validation states"** → ✅ Find actual validation logic, not assumed patterns
- ❌ **"Field-level vs form-level errors"** → ✅ Check where errors are actually displayed

### **🚫 Loading State Assumptions:**
- ❌ **"Component-level loading"** → ✅ Check if loading handled by parent, child, or shared component
- ❌ **"Skeleton loading"** → ✅ Verify if skeleton exists or just spinner replacement

### **✅ Required Verification for Button/Form Test Cases:**
```typescript
// Find this EXACT pattern in code before creating test case:
<Button disabled={someCondition} onClick={handler}>Save</Button>
<Button disabled={differentCondition} onClick={handler}>Cancel</Button>
// If both buttons have SAME condition, don't create different test cases!
```

**Example of proper code-referenced test case:**
```markdown
#### **Save/Cancel Button Row** *(Code Reference: ClientSettingsCard.tsx lines 66-87)*
- [X] **Hidden State** - Footer not visible when `isEditable=false`
- [X] **Visible State** - Footer with both buttons visible when `isEditable=true`
- [X] **Enabled State** - Both buttons enabled when `isLoading=false`
- [X] **Loading State** - Both buttons disabled when `isLoading=true` (entire card body shows spinner)
```

#Reason: This template ensures 100% code-verified SIT testing by requiring actual implementation verification before creating test cases, eliminating assumption-based testing scenarios that waste QA time.
