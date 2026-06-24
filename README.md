# Contribution [#1799]: [ Deserialization optimization, instead of discarding fields, puts non-existent fields into a specified field.
]

**Contribution Number:** [1 ]  
**Student:** [Tatenda Sekabanja]  
**Issue:** [https://github.com/apache/fory/issues/1799]  
**Status:** [Phase I ] [In Progress]

---

## Why I Chose This Issue
I would love to do something outside my normal area of web development. I have a lot of experience working with Java in a large codebase so I am confident I can take this on. I'm interested in making programming languages better/ programming easier for developers, and hope to apply and improve my software architecture skills. I actually ran into a serialization problem last year when working on a chat app.

---

## Understanding the Issue

### Problem Description

The root cause is in the compatible deserialization path. When a remote TypeDef contains fields that the local class does not have, CompatibleCodecBuilder currently ignores those fields. The matching logic is in CompatibleCodecBuilder.setFieldValue(...): if descriptor.getField() == null, the builder drops the value instead of preserving it. That means the downstream object loses unknown upstream attributes during deserialization.

### Expected Behavior

Preserve unknown fields

### Current Behavior

Unknown fields are ignored

### Affected Components

[Which parts of the codebase are involved?]

---

## Reproduction Process

### Environment Setup

Install maven, java, gradle

### Steps to Reproduce

1. Write a test with a mismatch in upstream and downstream fields
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach
store the 'extra' fields and use them later in serialization


### Analysis

the builder drops the value instead of preserving it. 
### Proposed Solution
Capture unmapped remote fields into a separate object and store them. Use this object during deserialization
### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The problem is schema-mismatch compatibility: when a producer with 10 fields sends data to a consumer with 9 fields, the 9th upstream field should be preserved in a container and re-emitted if the consumer serializes again, rather than being dropped.

**Match:** Existing compatibility paths: TypeResolver.buildMetaSharedTypeInfo() → CompatibleCodecBuilder → generated CompatibleSerializer.
Existing field mapping: CompatibleCodecBuilder.buildRemoteFieldInfos() and buildLocalFieldInfosByRemoteOrder() already compare remote and local descriptors.
Existing dispatch: TypeResolver already chooses compatible vs. local serializers on read; we extend it on write.

**Plan:** [Step-by-step implementation plan]
1. create foryextrafields class
2. modify desirialization - check if there is an unmapped remote field and initialize foryextrafields
3. modify serialization dispatch - if foryextrafields is present, then get corresponding typeInfo , and forward the write to the compatible serializer
4. 

**Implement:** [Link to your brWanch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

Round-trip test — Verify that unknown fields are preserved instead of lost.
Reference-sharing test — Verify that object identity is preserved between local and captured fields.
Zero-overhead test — Verify that classes without a capture sink maintain the same serialization behavior as before.

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [1] Progress

Working on adding the foryextrafields class and incorporating it with the serializers.

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** CompatibleCodecBuilder.java, WriteContext.Java,TypeResolver.java,CompatibleSerializer.java,FieldSkipper.java,ForyExtraFields.java,StaticGeneratedStructSerializer.java,SchEemaEvolutionTest.java
- **Key commits:** In progress 
- **Approach decisions:** I wanted to keep the typeresolver logic intact, and avoid modifying existing code.

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
