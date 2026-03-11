# Skill: Detect Localization Assumptions

## Skill Type
Human-Centered Analysis

## Domain
Localization / Global Software Design

---

## Purpose

Detect hidden assumptions about language, culture, writing systems, or user identity in software systems.

Many localization problems are not caused by translation errors. They originate earlier in the development process, when system design decisions embed implicit assumptions about users.

This skill helps identify those assumptions so developers can design systems that better support global users.

The goal is not to translate text, but to analyze **how software models people, language, and communication**.

---

## When to Use

Use this skill when analyzing:

- product specifications
- UI designs or mockups
- frontend code
- backend schemas
- API contracts
- validation rules
- form structures
- data models
- UX flows

Signals that this skill may apply include:

- user-facing forms
- language selection logic
- identity or profile fields
- character limits
- text validation rules
- UI layouts containing text
- locale or country logic

---

## Analysis Method

Follow this reasoning process when analyzing a system.

### 1. Identify User-Facing Data

Locate fields and elements that interact directly with users.

Examples include:

- names
- usernames
- addresses
- profile information
- UI labels
- messages
- text input fields
- dates and numbers

These areas frequently encode localization assumptions.

---

## Quick Detection Checklist

Before performing a deeper analysis, quickly check whether the system includes any of the following:

- language inferred from location
- ASCII-only validation rules
- fixed character limits
- UI elements with fixed text width
- schemas that assume first/last name structure
- layouts designed only for left-to-right languages
- string concatenation used for UI messages
- UI and content language treated as the same concept
- missing language metadata on content
- markup missing `lang` or directionality attributes

If any of these appear, perform the full analysis.

---

### 2. Identify Implicit Defaults

Look for hidden defaults that assume a specific language or cultural context.

Common examples:

- a single default language
- language derived from country
- ASCII-only validation
- fixed date formats
- fixed text lengths

Defaults often hide the most impactful localization risks.

---

### 3. Check HTML Language Properties

Inspect how the UI declares language and directionality at the markup level.

Check for:

- missing or hardcoded `lang` attribute on the `<html>` element
- `lang` not updated dynamically when the user switches language
- absence of `dir="auto"` or `dir="rtl"` support on the document or container elements
- missing `hreflang` attributes on links to alternate-language versions
- content sections in a different language missing their own `lang` attribute
- `xml:lang` missing when serving XHTML
- missing `<bdi>` or CSS-based bidirectional isolation on user-generated content

These properties affect browser rendering, text-to-speech pronunciation, search engine indexing, and assistive technology behavior. When they are absent or incorrect, users may see wrong fonts, broken hyphenation, or inaccessible content.

---

### 4. Examine UI Layout Constraints

Review UI elements that may assume certain text characteristics.

Check for:

- fixed-width buttons
- labels with limited space
- truncated text
- layouts designed only for short English words
- alignment assumptions for left-to-right languages

Many languages expand text by **30–60%** compared to English.

---

### 5. Examine Identity Assumptions

Review how the system models human identity.

Look for assumptions such as:

- first name / last name structure
- mandatory gender fields
- fixed address formats
- a single personal name
- language inferred from location

Human identity structures vary widely across cultures.

---

### 6. Examine Text Processing Rules

Inspect logic that manipulates text.

Look for operations involving:

- string length
- substring extraction
- case conversion
- sorting
- character validation

These operations can behave differently with Unicode text.

---

## Common Localization Assumptions to Detect

### Locale-Specific Formatting Assumptions

Example:
```
date format: MM/DD/YYYY
price = "$" + amount
```

**Risk**

Date, number, and currency formats vary globally.

Examples include:
- DD/MM/YYYY vs MM/DD/YYYY
- decimal separators (1.5 vs 1,5)
- currency placement (€10 vs 10€)
- calendar systems

Hardcoding formats can cause confusion or incorrect interpretation.

**Better Approach**

Use locale-aware formatting libraries and store dates or numeric values in standardized formats internally.

### Language Derived From Country

Example:
```
user.language = country.defaultLanguage
```

**Risk**

Many countries are multilingual. Users may also prefer a language unrelated to their physical location.

**Better Approach**

Allow users to explicitly choose their preferred language.

### English-Centric Pluralization

---

### Grammatical Gender in Translations

Example:
```
t("invited_message", userName)
// English: "{0} is invited"
// French: "{0} est invité" (male), "{0} est invitée" (female)
```

**Risk**

Many languages require different words, endings, or even sentence structures depending on the gender of the subject or object. If the translation system does not allow for gender variants, the result will be grammatically incorrect or awkward for many users.

**Better Approach**

Support gender-aware translation keys and pass gender metadata to the translation function. Use localization frameworks that support gender variants, and provide translators with context about the gender of variables.

---

Example:
```
"You have " + count + " messages"
```
or
```
if (count == 1) message else messages
```

**Risk**

Languages have different plural rules. Some languages have multiple plural forms, while others treat zero differently.

**Better Approach**

Use plural-aware localization frameworks rather than manual plural logic.

---

### UI Language Mixed With Content Language

Example:
```
locale = user.preferredLanguage
renderPage(locale)  // applies to both UI labels and user content
```

**Risk**

In content-driven applications such as blogs, forums, knowledge bases, or comment systems, the content language is independent of the interface language. A user may read a blog post written in Japanese while browsing the site with an English UI. Treating both as a single language setting can cause issues such as:

- spell-check or hyphenation applied in the wrong language
- incorrect `lang` attributes on content blocks
- screen readers announcing user-generated content in the UI language
- search and filtering that ignores the actual content language

**Better Approach**

Separate UI language from content language. Let the interface locale control chrome elements (menus, buttons, labels) while preserving the original language metadata on content items. Tag user-generated or editorial content with its own `lang` attribute independently of the site locale.

---

### ASCII-Only Validation

Example:
```
username regex: [A-Za-z0-9]
```

**Risk**

This excludes many writing systems used worldwide.

---

### Fixed Character Limits

---

### Phone Number Format Assumptions

Example:
```
phone = input("Enter your phone number:")
// Validation: must be 10 digits, format (XXX) XXX-XXXX
```

**Risk**

Phone numbers vary in length, format, and allowed characters across countries. Hardcoding a single format or length will exclude many users. Some countries have variable-length numbers, require country codes, or use different separators.

**Better Approach**

Store phone numbers as strings or integers, allow for international formats, and use libraries (like libphonenumber) for validation and formatting. Always collect country code separately or as part of the number.

---

Example:
```
username maxLength = 20
```

**Risk**

Some scripts require more characters to represent the same meaning. Also consider emoji sequences and combining characters.

---

### Western Name Structures

Example schema:
```
first_name
last_name
```

**Risk**

Many cultures use different naming structures or single names.

**Better Approach**

Use a flexible name model such as:
```
full_name
optional structured components
non-mandatory fields
```


---

### Left-To-Right Layout Assumptions

---

### CSS Logical vs Physical Properties

Example:
```
// Physical properties (problematic for RTL)
margin-left: 1em;
padding-right: 2em;

// Logical properties (RTL/LTR safe)
margin-inline-start: 1em;
padding-inline-end: 2em;
```

**Risk**

Using physical CSS properties like `left`, `right`, `margin-left`, or `padding-right` hardcodes a left-to-right layout and breaks in right-to-left languages. This causes misaligned or unreadable interfaces for RTL users.

**Better Approach**

Use CSS logical properties (`margin-inline-start`, `padding-inline-end`, etc.) so layouts adapt automatically to text direction. This is essential for global-ready UIs.

---

Examples include:

- icons pointing only to the right
- layout margins fixed for left alignment
- navigation arrows that assume left-to-right reading order

**Risk**

Interfaces may break in right-to-left languages such as Arabic or Hebrew.

---

### Text Embedded in Images

Marketing graphics or UI assets containing baked-in text.

**Risk**

These assets cannot easily be localized.

---

### Concatenated Strings

Example:
```
"Hello " + username
```

**Risk**

Word order differs across languages.

**Better Approach**

Use parameterized translation strings.

---

### Fixed Parameter Order in Translations

Example:
```
t("welcome_message", userName, itemCount)
// English: "{0} has {1} items"
// Translated: "{1} items belong to {0}"
```

**Risk**

Even when using parameterized translation strings instead of concatenation, assuming that parameters appear in the same order across all languages creates brittle translations. Translators are forced to work around a fixed sentence structure, leading to awkward or unnatural phrasing. Languages vary widely in word order — subject-object-verb, verb-subject-object, and other patterns may require parameters to appear in a completely different sequence.

This also means translation strings must never be assembled from smaller translated fragments. Building a sentence by combining separately translated pieces — "puzzle" translations — produces broken output because each language constructs sentences differently. A phrase that works as three composable parts in English may need to be a single, completely different sentence in another language. Each translation string should represent a complete, self-contained message that the translator can restructure freely.

**Better Approach**

Use named parameters or indexed placeholders that translators can freely reorder.
```
// Named parameters
t("welcome_message", { user: userName, count: itemCount })
// English: "{user} has {count} items"
// Translated: "{count} items belong to {user}"
```

This gives translators full control over sentence structure without requiring code changes.

---

### Context-Blind Translation Reuse

---

### Missing Translation Fallback Strategy

Example:
```
// Translation key missing in target language
t("welcome_message")
// UI shows: "welcome_message" or blank or crashes
```

**Risk**

If a translation is missing, users may see raw keys, empty strings, or even application errors. This creates a poor or confusing experience, especially for non-English users.

**Better Approach**

Implement a fallback strategy: show a default language (often English), a clear placeholder, or a helpful error message. Monitor for missing translations and provide tools for translators to fill gaps quickly.

---

Example:
```
// Shared translation key
t("save")  // used on a file save button
t("save")  // used on a discount savings banner
t("save")  // used on a "save to favorites" action
```

**Risk**

A word that has a single meaning in English may map to entirely different words in other languages depending on context. For example, English uses "save" for saving a file, saving money, and saving a bookmark — but many languages use a distinct word for each. Reusing a single translation key across these contexts forces translators to pick one word that may be confusing or incorrect in some placements.

This also applies to short labels like "open," "post," "share," "set," or "run," which are especially prone to context-dependent meaning differences.

**Better Approach**

Use separate translation keys for each distinct usage context, even when the source language string is identical. Provide translators with descriptions or comments indicating where and how each string is used.
```
t("file_save_button")       // "Save" on a file action
t("savings_banner_title")   // "Save" as in discount
t("favorites_save_action")  // "Save" as in bookmark
```

---

## Output Format

When reporting issues, use the following structure.
```
Localization Assumption Detected

Type:
(identity / language / layout / formatting / data model)

Location:
(file, component, schema, or specification section)

Detected Assumption:
Description of the implicit assumption.

Why It Matters:
Explanation of the localization or global design risk.

Suggested Improvement:
Recommendation for a more flexible or inclusive design.
```

---

## Example Analysis

### Example Input

Signup form specification:
```
Fields:
First Name
Last Name
Country
Language (auto-selected from country)
Username (max 20 characters, alphanumeric only)
```

---

### Example Output

Issue 1

Type  
Identity assumption

Detected Assumption  
Names are structured as first name + last name.

Why It Matters  
Many cultures use different naming conventions or single names.

Suggested Improvement  
Allow flexible name structures or support full name fields.

---

Issue 2

Type  
Language assumption

Detected Assumption  
Language derived from country.

Why It Matters  
Users in multilingual countries may prefer different languages.

Suggested Improvement  
Allow explicit language selection.

---

Issue 3

Type  
Text validation assumption

Detected Assumption  
Usernames limited to ASCII characters.

Why It Matters  
This excludes many global writing systems.

Suggested Improvement  
Allow Unicode usernames or define a broader character policy.

---

## Non-Goals

This skill does not:

- perform translation
- rewrite UI copy
- automatically fix localization issues

The goal is to **surface hidden assumptions** so developers can make informed design decisions.

---

## Related Skills

- RTL-Safe UI Design
- Unicode-Safe Text Processing
- Text Expansion-Aware Layouts
- Internationalization Data Models
