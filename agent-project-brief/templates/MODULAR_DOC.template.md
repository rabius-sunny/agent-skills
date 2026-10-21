# [Subsystem / Topic Name]

[1-2 sentences summarizing how this subsystem works in the project].

## 1. Architecture & Model

[Table or ASCII/Mermaid diagram mapping out the core concepts, layers, or lifetimes].

| Layer / Concept | Location | Source of Truth | Lifetime / Scope | Purpose |
| --- | --- | --- | --- | --- |
| [Layer 1] | `path/to/file.ts` | [Source] | [Lifetime] | [Purpose] |
| [Layer 2] | `path/to/file.ts` | [Source] | [Lifetime] | [Purpose] |

## 2. Core Workflow / Lifecycle

```
[Step 1: Input] ──► [Step 2: Guard] ──► [Step 3: Execute] ──► [Step 4: Side Effects] ──► [Step 5: Return]
```

Step-by-step description of how data moves through this subsystem:
1. **[Step 1 Name]**: [Details].
2. **[Step 2 Name]**: [Details].
3. **[Step 3 Name]**: [Details].

## 3. Mandatory Rules & Code Conventions

- **[Rule 1]**: [Clear imperative instruction].
- **[Rule 2]**: [Clear imperative instruction].

```typescript
// Minimal representative pattern
export async function examplePattern() {
  // 1. Validate
  // 2. Guard
  // 3. Mutate
}
```

## 4. Known Pitfalls & Edge Cases

- **[Pitfall 1]**: [What goes wrong and how to avoid it].
- **[Pitfall 2]**: [What goes wrong and how to avoid it].
