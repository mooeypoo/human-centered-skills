# Skill: RTL-Safe UI Design

## Skill Type
Human-Centered Analysis

## Domain
Localization / UI Layout / Global Software Design

---

## Purpose

Detect user interface assumptions that break or behave incorrectly in right-to-left (RTL) languages.

Languages such as Arabic, Hebrew, Persian, and Urdu use right-to-left writing direction. Many software interfaces are originally designed for left-to-right (LTR) languages and may contain layout, navigation, or iconography assumptions that fail when directionality changes.

This skill analyzes UI design, markup, and styling to identify issues that may prevent an interface from functioning correctly in RTL contexts.

The goal is to ensure that the interface supports **bidirectional layouts and text rendering**.

---

## When to Use

Use this skill when analyzing:

- UI layouts or design mockups
- frontend components
- HTML templates
- CSS styling rules
- design systems
- navigation patterns
- icon usage
- user-generated content rendering

Signals that this skill may apply include:

- directional navigation (next / previous)
- arrow icons
- horizontal layout flows
- text alignment rules
- CSS margin or padding rules
- chat interfaces
- forms and labels
- mixed-language content

---

## Analysis Method

Follow this reasoning process when analyzing an interface.

### 1. Determine Whether RTL Support Exists

Check whether the system supports RTL languages.

Look for:

- `dir="rtl"` support in markup
- dynamic switching between LTR and RTL
- locale-based layout switching
- CSS logical properties

If RTL is not supported, the system may fail entirely for RTL users.

---

### 2. Examine Layout Direction Assumptions

Inspect the layout structure and spacing rules.

Look for:

- fixed `margin-left` or `padding-left`
- absolute positioning based on left alignment
- horizontal flows assuming left-to-right ordering
- navigation elements anchored only on the left side

Preferred approach:

Use **CSS logical properties** such as:
```
margin-inline-start
margin-inline-end
padding-inline-start
```

These adapt automatically to direction changes.

---

### 3. Examine Icon Directionality

Icons often encode directional meaning.

Check for icons such as:

- arrows
- progress indicators
- navigation chevrons
- playback controls

Examples:

- “Next” arrows pointing right
- breadcrumb separators
- progress timelines

**Risk**

In RTL interfaces, navigation direction reverses.

Better approach:

Use dedicated icons for the Right to Left use case. Beware of mirroring icons, as not all icons should be flipped, and automatically mirroring icons can result with malformed iconography.

---

### 4. Examine Text Alignment Rules

Inspect alignment settings.

Look for:

- `text-align: left`
- forced left alignment in components
- layout assumptions that text begins on the left edge

Better approach:

Use logical alignment:
```
text-align: start
text-align: end
```

These adapt to reading direction.

---

### 5. Examine Mixed-Direction Text

Many systems display content containing multiple scripts.

Examples include:

- usernames
- product names
- URLs
- numbers embedded in RTL text

Look for issues involving:

- punctuation appearing in the wrong position
- numbers jumping unexpectedly
- broken sentence order

Recommended solutions include:

- `dir="auto"`
- `<bdi>` (bidirectional isolation)
- Unicode bidirectional control characters when needed

---

### 6. Examine User-Generated Content

User-generated text can contain arbitrary scripts.

Check whether content containers support:

- automatic direction detection
- proper isolation (for example, by wrapping user-generated content in `<bdi>` tags)
- correct rendering of mixed scripts

Example:
```
<span>Hello <bdi>{{ username }}</bdi>!</span>
```

Without isolation, text direction can leak into surrounding UI elements and cause confusing or broken rendering.

---

### 7. Examine Form Field Directionality

Not all form fields should follow the overall UI direction. Some fields, such as email addresses, URLs, and phone numbers, should always be left-to-right (LTR), even in RTL interfaces. This is because their content is inherently LTR and mixing directionality can cause confusion or input errors.

Check for:

- Email fields: should have `dir="ltr"` and `inputmode="email"`.
- URL fields: should have `dir="ltr"` and `inputmode="url"`.
- Phone number fields: should have `dir="ltr"` and `inputmode="tel"`.
- Username fields: if restricted to Latin characters, should have `dir="ltr"`.

Example:
```
<input type="email" dir="ltr" inputmode="email" />
<input type="text" name="username" dir="ltr" />
<input type="tel" dir="ltr" inputmode="tel" />
```

Other fields, such as names or addresses, should follow the UI direction unless there is a specific reason to force LTR.

**Risk**

If these fields inherit RTL direction, users may have trouble entering or reading their input, leading to errors or frustration.

**Better Approach**

Explicitly set directionality for fields that require it, and test forms in both LTR and RTL modes to ensure correct behavior.

---

### 8. Avoid Mixing Content Types in Inputs

Mixing different content types (such as numbers and currency symbols, or Latin and non-Latin scripts) in a single input field can cause confusion, especially in RTL interfaces. For example, entering "$100" or "100₪" in a single field can result in unpredictable cursor movement, rendering issues, or user errors due to bidirectional text rules.

**Best Practice:**

- Separate fields for different data types. For currency, provide one field for the amount (numbers only) and a separate dropdown or selector for the currency type (USD, EUR, NIS, etc.).
- For phone numbers, usernames, or codes, restrict input to the expected character set and avoid mixing scripts or symbols.

**Example:**
```
<input type="number" name="amount" />
<select name="currency">
	<option value="USD">USD</option>
	<option value="EUR">EUR</option>
	<option value="EGP">EGP</option>
</select>
```

**Risk**

Mixing content types in a single field can break input validation, confuse users, and cause severe rendering issues in RTL contexts due to bidirectional text handling.

**Better Approach**

Design forms so that each field contains only one type of content, and use appropriate input types and validation for each.

---

## Common RTL Issues to Detect

### Directional Navigation Icons

Example:
```
Next →
Previous ←
```

**Risk**

In RTL interfaces, navigation direction is reversed. A right-pointing arrow may represent “back”.

Better approach

Mirror icons based on layout direction.

---

### Hardcoded Left Alignment

Example:
```
text-align: left
margin-left: 12px
```

**Risk**

Layouts will appear visually incorrect when direction switches.

Better approach

Use logical CSS properties.

---

### Breadcrumb Direction

Example:
```
Home > Products > Shoes
```

**Risk**

In RTL languages, breadcrumb direction may need to reverse.

---

### Chat Layout Assumptions

Many chat interfaces assume:

- messages appear on the right for "sent"
- messages appear on the left for "received"

In RTL languages this may conflict with reading direction.

Chat UI must clearly distinguish **speaker alignment vs reading direction**.

---

### Mixed Direction Content

Example:
```
مرحبا user123!
```

**Risk**

Usernames, numbers, and punctuation can reorder visually due to bidirectional text rules.

Better approach

Wrap user-generated segments in `<bdi>` or equivalent isolation.

---

## Output Format

When reporting issues, use the following structure.
```
RTL Compatibility Issue Detected

Type:
(layout / iconography / text direction / alignment)

Location:
(file, component, UI element)

Detected Assumption:
Description of the LTR assumption.

Why It Matters:
Explanation of how this breaks in RTL languages.

Suggested Improvement:
Recommendation for RTL-safe design.
```

---

## Example Analysis

### Example Input

UI navigation component:
```
Next button
[ → Next ]

CSS:
margin-left: 16px
text-align: left
```

---

### Example Output

Issue 1

Type  
Icon direction

Detected Assumption  
Right arrow represents forward navigation.

Why It Matters  
In RTL languages, forward navigation typically points left.

Suggested Improvement  
Use mirrored icons or direction-aware icons.

---

Issue 2

Type  
Layout assumption

Detected Assumption  
Layout spacing defined using `margin-left`.

Why It Matters  
Spacing will not adapt when direction switches.

Suggested Improvement  
Use logical properties such as `margin-inline-start`.

---

## Non-Goals

This skill does not:

- translate interface text
- enforce specific design systems
- automatically rewrite UI code

The goal is to **detect layout and directionality assumptions** that may break in RTL contexts.

---

## Related Skills

- Detect Localization Assumptions
- Unicode-Safe Text Processing
- Text Expansion-Aware Layouts
