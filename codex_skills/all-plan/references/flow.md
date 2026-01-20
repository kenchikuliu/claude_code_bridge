# All Plan (Codex Version)

Collaborative planning with all mounted CLIs (Codex, Claude, Gemini, OpenCode) for comprehensive solution design. Codex serves as the primary coordinator.

**Usage**: For complex features or architectural decisions requiring diverse perspectives.

---

## Brainstorming Principles

This enhanced all-plan integrates Superpowers brainstorming:
- **Socratic Ladder**: Deep requirement mining through structured questions
- **Superpowers Lenses**: Systematic alternative exploration
- **Anti-pattern Detection**: Proactive risk identification

**Flexibility**: All additions are optional. Skip/condense when requirements are clear.

---

## Input Parameters

From `$ARGUMENTS`:
- `requirement`: User's initial requirement or feature request
- `context`: Optional project context or constraints

---

## Execution Flow

### Phase 1: Requirement Refinement & Project Analysis

**1.1 Clarify Requirements**

Ask user for clarification if needed:
- What is the core problem to solve?
- What are the constraints (time, resources, compatibility)?
- What are the success criteria?

**Socratic Ladder** (Answer 5-7 key questions):

*Note: If requirements are already clear and constraints explicit, you may condense or skip this section. However, always confirm success criteria.*

1. What problem are we solving? (Core problem)
2. Why now? What changes if we don't do this? (Urgency/impact)
3. What are we NOT doing? (Scope boundaries)
4. What could go wrong? (Failure modes)
5. What are the hidden constraints? (Non-functional requirements)
6. What alternatives exist? (Other approaches)
7. How will we know it works? (Success metrics)

**Superpowers Lenses** (Explore 2-3 alternative framings):

*Note: Use at least one lens; skip any that contradict project constraints.*

- Amplify 10x: What if scale/performance must be 10x better?
- Remove dependency: What if we can't use [key technology]?
- Invert flow: What if we reverse the typical approach?

**1.2 Analyze Project Context**

Use available tools to understand:
- Existing codebase structure (Glob, Grep, Read)
- Current architecture patterns
- Dependencies and tech stack
- Related existing implementations

**1.3 Research (if needed)**

If the requirement involves:
- New technologies or frameworks
- Industry best practices
- Performance benchmarks
- Security considerations

Use WebSearch to gather relevant information.

**1.4 Formulate Complete Brief**

Create a comprehensive design brief:
```
DESIGN BRIEF
============
Problem: [clear problem statement]
Context: [project context, tech stack, constraints]
Requirements:
- [requirement 1]
- [requirement 2]
- [requirement 3]
Success Criteria:
- [criterion 1]
- [criterion 2]
Research Findings: [if applicable]
```

Save as `design_brief`.

---

### Phase 1.5: CLI Availability Check

**Check which CLIs are available:**

```bash
# Check Claude
lping && echo "Claude: available" || echo "Claude: unavailable"

# Check Gemini
gping && echo "Gemini: available" || echo "Gemini: unavailable"

# Check OpenCode
oping && echo "OpenCode: available" || echo "OpenCode: unavailable"
```

**Determine dispatch strategy:**
- If 2+ other CLIs available: Proceed with full parallel design (Phase 2)
- If 1 other CLI available: Proceed with simplified parallel design
- If 0 other CLIs available: Skip to Phase 5 (Codex-only design)

Record available CLIs for Phase 2 dispatch.

---

### Phase 2: Parallel Independent Design

Send the design brief to available mounted CLIs for independent design.

**Note**: Only dispatch to CLIs that passed the availability check in Phase 1.5.

**2.1 Dispatch to Claude** (if available)

```bash
# Only run if Claude is available
lask --sync -q <<'EOF'
Design a solution for this requirement:

[design_brief]

Provide:
- Goal (1 sentence)
- Primary Solution (your recommended approach)
- Alternative Approach (using one Superpowers Lens or different framing)
- Implementation steps (3-7 key steps)
- Technical considerations
- Tradeoffs (pros/cons of each approach)
- Potential risks
- Acceptance criteria (max 3)

Be specific and concrete. Focus on design alternatives and tradeoffs.
EOF
```

Save response as `claude_design`.

**2.2 Dispatch to Gemini** (if available)

```bash
# Only run if Gemini is available
gask <<'EOF'
Design a solution for this requirement:

[design_brief]

Provide:
- Goal (1 sentence)
- Primary Solution (your recommended approach)
- Alternative Approach (using one Superpowers Lens or different framing)
- Implementation steps (3-7 key steps)
- Technical considerations
- Tradeoffs (pros/cons of each approach)
- Potential risks
- Acceptance criteria (max 3)

Be specific and concrete. Focus on design alternatives and tradeoffs.
EOF
```

Wait for response. Save as `gemini_design`.

**2.3 Dispatch to OpenCode** (if available)

```bash
# Only run if OpenCode is available
oask <<'EOF'
Design a solution for this requirement:

[design_brief]

Provide:
- Goal (1 sentence)
- Primary Solution (your recommended approach)
- Alternative Approach (using one Superpowers Lens or different framing)
- Implementation steps (3-7 key steps)
- Technical considerations
- Tradeoffs (pros/cons of each approach)
- Potential risks
- Acceptance criteria (max 3)

Be specific and concrete. Focus on design alternatives and tradeoffs.
EOF
```

Wait for response. Save as `opencode_design`.

**2.4 Codex's Independent Design**

While waiting for responses, create YOUR own design (do not look at others yet):
- Goal (1 sentence)
- Architecture approach
- Implementation steps (3-7 key steps)
- Technical considerations
- Potential risks
- Acceptance criteria (max 3)

Save as `codex_design`.

---

### Phase 3: Collect & Analyze All Designs

**3.1 Collect All Responses**

Collect designs from available CLIs only:
- If Claude available: Claude design → `claude_design`
- If Gemini available: Gemini design → `gemini_design`
- If OpenCode available: OpenCode design → `opencode_design`
- Codex design (always) → `codex_design`

**Note**: If no other CLIs are available (Codex-only), skip to Phase 5 with Codex's design.

**3.2 Comparative Analysis**

Analyze all available designs (Codex + available CLIs):

Create a comparison matrix:
```
DESIGN COMPARISON
=================

1. Goals Alignment
   - Common goals across all designs
   - Unique perspectives from each

2. Architecture Approaches
   - Overlapping patterns
   - Divergent approaches
   - Pros/cons of each

3. Implementation Steps
   - Common steps (high confidence)
   - Unique steps (need evaluation)
   - Missing steps in some designs

4. Technical Considerations
   - Shared concerns
   - Unique insights from each CLI
   - Critical issues identified

5. Risk Assessment
   - Commonly identified risks
   - Unique risks from each perspective
   - Risk mitigation strategies

6. Acceptance Criteria
   - Overlapping criteria
   - Additional criteria to consider

7. Anti-pattern Analysis
   - Common anti-patterns identified across all designs
   - Unique anti-patterns from each CLI
   - Mitigation strategies comparison
   - Critical risks flagged by multiple CLIs
```

Save as `comparative_analysis`.

---

### Phase 4: Iterative Refinement

**Note**: This phase requires at least one other CLI for review. If Claude is available, use Claude. Otherwise, use the first available CLI (Gemini or OpenCode). If no other CLIs are available, skip to Phase 5.

**4.1 Draft Merged Design**

Based on comparative analysis, create initial merged design:
```
MERGED DESIGN (Draft v1)
========================
Goal: [synthesized goal]

Architecture: [best approach from analysis]

Implementation Steps:
1. [step 1]
2. [step 2]
3. [step 3]
...

Technical Considerations:
- [consideration 1]
- [consideration 2]

Risks & Mitigations:
- Risk: [risk 1] → Mitigation: [mitigation 1]
- Risk: [risk 2] → Mitigation: [mitigation 2]

Acceptance Criteria:
- [criterion 1]
- [criterion 2]
- [criterion 3]

Discarded Alternatives (brief):
- [Alternative 1]: [1-line reason for rejection]
- [Alternative 2]: [1-line reason for rejection]

Open Questions:
- [question 1]
- [question 2]
```

Save as `merged_design_v1`.

**4.2 Discussion Round 1 - Review & Critique**

**Select reviewer CLI** (in priority order):
1. If Claude available: use `lask --sync -q`
2. Else if Gemini available: use `gask`
3. Else if OpenCode available: use `oask`

```bash
# Use the selected reviewer CLI command below
<reviewer_cli> <<'EOF'
Review this merged design based on all CLI inputs:

COMPARATIVE ANALYSIS:
[comparative_analysis]

MERGED DESIGN v1:
[merged_design_v1]

Analyze:
1. Does this design capture the best ideas from all perspectives?
2. Are there any conflicts or contradictions?
3. What's missing or unclear?
4. Are the implementation steps logical and complete?
5. Are risks adequately addressed?

Provide specific recommendations for improvement.
EOF
```

Save as `reviewer_feedback_1`.

**4.3 Discussion Round 2 - Resolve & Finalize**

Based on reviewer's feedback, refine the design:

```bash
# Use the same reviewer CLI as Round 1
<reviewer_cli> <<'EOF'
Refined design based on your feedback:

MERGED DESIGN v2:
[merged_design_v2]

Changes made:
- [change 1]
- [change 2]

Remaining concerns:
- [concern 1 if any]

Final approval or additional suggestions?
EOF
```

Save as `reviewer_feedback_2`.

---

### Phase 5: Final Output

**5.1 Finalize Design**

Incorporate reviewer's final feedback (if any) and create the complete solution design.

**5.2 Output Format**

Return the final comprehensive plan:

```
FINAL SOLUTION DESIGN
=====================

## Goal
[Clear, concise goal statement]

## Architecture
[Chosen architecture approach with rationale]

## Implementation Plan

### Step 1: [Title]
- Actions: [specific actions]
- Deliverables: [what will be produced]
- Dependencies: [what's needed first]

### Step 2: [Title]
- Actions: [specific actions]
- Deliverables: [what will be produced]
- Dependencies: [what's needed first]

[Continue for all steps...]

## Technical Considerations
- [consideration 1]
- [consideration 2]
- [consideration 3]

## Risk Management
| Risk | Impact | Mitigation |
|------|--------|------------|
| [risk 1] | [High/Med/Low] | [mitigation strategy] |
| [risk 2] | [High/Med/Low] | [mitigation strategy] |

## Acceptance Criteria
- [ ] [criterion 1]
- [ ] [criterion 2]
- [ ] [criterion 3]

## Design Contributors
- Codex: [key contributions]
- Claude: [key contributions]
- Gemini: [key contributions]
- OpenCode: [key contributions]

## Validation Checklist
Before finalizing:
- [ ] Total flow.md length < 500 lines
- [ ] All Phase outputs unchanged (backward compatible)
- [ ] No new required inputs (backward compatible)
- [ ] Socratic Ladder questions answered (or skip justified)
- [ ] At least one alternative explored
- [ ] Anti-patterns identified and mitigated
- [ ] Design rationale documented
```

---

## Principles

1. **Comprehensive Requirement Analysis**: Thoroughly understand and refine requirements before design
2. **True Independence**: All CLIs design independently without seeing others' work first
3. **Diverse Perspectives**: Leverage unique strengths of each CLI (Codex: code, Claude: context, Gemini: analysis, OpenCode: alternatives)
4. **Evidence-Based Synthesis**: Merge based on comparative analysis, not arbitrary choices
5. **Iterative Refinement**: Use reviewer discussion to validate and improve merged design
6. **Concrete Deliverables**: Output actionable implementation plan, not just discussion notes
7. **Attribution**: Acknowledge contributions from each CLI to maintain transparency
8. **Research When Needed**: Don't hesitate to use WebSearch for external knowledge
9. **Max 2 Iteration Rounds**: Avoid endless discussion; converge on practical solution
10. **Plan Mode Only**: No code edits or file operations in this skill

---

## Notes

- This skill is designed for complex features or architectural decisions
- For simple tasks, use dual-design or direct implementation instead
- Codex uses `lask --sync` for synchronous communication with Claude
- `gask` and `oask` may require waiting for async responses
- If any CLI is not available, proceed with available CLIs and note the absence
