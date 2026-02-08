# Design Document: Medical AI Health Guidance Tool

## Overview

The Medical AI Health Guidance Tool is designed as a conversational AI system that provides educational health information while maintaining strict safety boundaries. The system architecture emphasizes content filtering, safety validation, and clear communication of limitations. The design follows a layered approach with distinct components for input processing, content generation, safety validation, and response formatting.

### Core Design Principles

1. **Safety First**: All outputs must pass through safety validation to ensure no diagnosis, prescriptions, or dosages are provided
2. **Educational Focus**: Content is framed as general information rather than personalized medical advice
3. **Transparency**: Clear disclaimers and limitations are communicated with every response
4. **Empowerment**: Information is presented to help users make informed decisions and have better conversations with healthcare providers
5. **Accessibility**: Language and explanations are tailored for general audiences without medical training

## Architecture

The system follows a pipeline architecture with the following stages:

```
User Input → Input Processor → Content Generator → Safety Validator → Response Formatter → User Output
                                       ↓
                                Knowledge Base
```

### Component Flow

1. **Input Processor**: Parses and structures user-reported symptoms and questions
2. **Content Generator**: Creates educational content based on symptoms using the Knowledge Base
3. **Safety Validator**: Ensures generated content complies with safety boundaries
4. **Response Formatter**: Structures the final response with appropriate disclaimers and formatting
5. **Knowledge Base**: Stores validated medical information, wellness practices, and safety rules

### Architectural Decisions

- **Pipeline over Monolithic**: Separating concerns allows independent validation and testing of each stage
- **Safety Validator as Gate**: No content reaches users without passing safety validation
- **Stateless Design**: Each request is processed independently to avoid context-based errors
- **Template-Based Disclaimers**: Standardized disclaimers ensure consistency and legal compliance

## Components and Interfaces

### 1. Input Processor

**Responsibility**: Parse and structure user input into a standardized format

**Interface**:
```
Input: raw_user_text (string)
Output: SymptomQuery {
  symptoms: List<string>,
  questions: List<string>,
  context: Map<string, string>
}
```

**Key Functions**:
- Extract symptom descriptions from natural language
- Identify specific questions or concerns
- Detect urgency indicators (severe pain, difficulty breathing, etc.)
- Normalize terminology for consistent processing

### 2. Content Generator

**Responsibility**: Generate educational health guidance based on structured input

**Interface**:
```
Input: SymptomQuery
Output: GuidanceContent {
  possible_causes: List<Explanation>,
  lifestyle_recommendations: LifestyleAdvice,
  wellness_practices: WellnessPractices,
  medicine_information: MedicineInfo,
  urgency_level: UrgencyLevel
}
```

**Key Functions**:
- Query Knowledge Base for relevant information
- Generate explanations in simple language
- Select appropriate wellness practices
- Determine urgency level based on symptoms

**Sub-components**:
- **Explanation Generator**: Creates simple explanations of possible causes
- **Lifestyle Advisor**: Generates diet, exercise, and preventive recommendations
- **Wellness Recommender**: Selects appropriate yoga, breathing, and meditation practices
- **Medicine Educator**: Provides general information about commonly used medicines

### 3. Safety Validator

**Responsibility**: Ensure all generated content complies with safety boundaries

**Interface**:
```
Input: GuidanceContent
Output: ValidationResult {
  is_safe: boolean,
  violations: List<SafetyViolation>,
  sanitized_content: GuidanceContent
}
```

**Safety Rules**:
1. No disease diagnosis statements
2. No prescription recommendations
3. No specific dosage information
4. No claims of treatment or cure
5. No medical advice framed as definitive

**Key Functions**:
- Scan content for prohibited patterns (diagnosis keywords, dosage numbers, prescription language)
- Validate that explanations present multiple possibilities rather than single diagnoses
- Ensure recommendations are framed as suggestions, not prescriptions
- Flag and remove or rephrase violating content

**Detection Patterns**:
- Diagnosis patterns: "You have [disease]", "This is [condition]", "You are suffering from [diagnosis]"
- Prescription patterns: "Take [medicine]", "You should use [drug]", "Prescribed [medication]"
- Dosage patterns: "[number] mg", "[number] tablets", "twice daily", specific quantities

### 4. Response Formatter

**Responsibility**: Structure validated content into a user-friendly response with disclaimers

**Interface**:
```
Input: GuidanceContent, ValidationResult
Output: FormattedResponse {
  content_sections: List<Section>,
  disclaimer: string,
  urgency_notice: string (optional)
}
```

**Key Functions**:
- Organize content into clear sections (Possible Causes, Recommendations, Wellness Practices, etc.)
- Apply calm and supportive tone
- Insert appropriate disclaimers
- Add urgency notices when needed
- Format for readability

**Response Structure**:
```
[Urgency Notice - if applicable]

[Empathetic Acknowledgment]

**Possible Causes:**
[Explanations in simple language]

**Lifestyle Recommendations:**
- Precautions: [...]
- Diet: [...]
- Preventive Care: [...]

**Wellness Practices:**
- Yoga: [...]
- Breathing Exercises: [...]
- Meditation: [...]
- Stress Management: [...]

**About Medicines/Remedies:**
[General educational information]

---
**Important Disclaimer:**
[Standard disclaimer text emphasizing educational nature and recommending professional consultation]
```

### 5. Knowledge Base

**Responsibility**: Store and retrieve validated medical information

**Data Structure**:
```
SymptomKnowledge {
  symptom_id: string,
  common_causes: List<Cause>,
  lifestyle_advice: LifestyleAdvice,
  wellness_practices: List<Practice>,
  related_medicines: List<MedicineInfo>,
  urgency_indicators: List<string>
}

MedicineInfo {
  name: string,
  general_use: string,
  common_category: string,
  safety_notes: List<string>,
  contraindications: List<string>
}

WellnessPractice {
  name: string,
  type: enum(Yoga, Breathing, Meditation, StressManagement),
  description: string,
  benefits: List<string>,
  precautions: List<string>
}
```

**Key Functions**:
- Query by symptom keywords
- Retrieve related wellness practices
- Fetch medicine information
- Access safety rules and patterns

## Data Models

### Core Data Types

```
SymptomQuery {
  symptoms: List<string>           // Extracted symptom descriptions
  questions: List<string>          // Specific user questions
  context: Map<string, string>     // Additional context (age group, duration, etc.)
  timestamp: DateTime
}

Explanation {
  cause: string                    // Possible cause in simple terms
  description: string              // Detailed explanation
  commonality: enum(Common, Occasional, Rare)
}

LifestyleAdvice {
  precautions: List<string>
  diet_suggestions: List<string>
  lifestyle_changes: List<string>
  preventive_care: List<string>
}

WellnessPractices {
  yoga: List<Practice>
  breathing: List<Practice>
  meditation: List<Practice>
  stress_management: List<Practice>
}

Practice {
  name: string
  description: string
  instructions: string
  benefits: List<string>
  precautions: List<string>
  duration: string                 // Suggested duration
}

MedicineInfo {
  name: string
  category: string                 // e.g., "pain reliever", "antihistamine"
  general_use: string
  common_forms: List<string>       // e.g., "tablet", "syrup"
  safety_notes: List<string>
  contraindications: List<string>
}

UrgencyLevel = enum {
  Routine,                         // Normal consultation recommended
  Elevated,                        // Prompt consultation recommended
  Urgent                           // Immediate medical attention recommended
}

SafetyViolation {
  type: enum(Diagnosis, Prescription, Dosage, Treatment_Claim)
  location: string                 // Where in content
  violating_text: string
  severity: enum(Critical, High, Medium)
}

FormattedResponse {
  acknowledgment: string
  possible_causes: List<Explanation>
  lifestyle_recommendations: LifestyleAdvice
  wellness_practices: WellnessPractices
  medicine_information: List<MedicineInfo>
  disclaimer: string
  urgency_notice: string (optional)
  tone: enum(Calm, Supportive, Empathetic)
}
```

### State Management

The system is designed to be stateless for safety reasons. Each request is processed independently without maintaining conversation history. This prevents:
- Context-based diagnostic conclusions
- Accumulation of medical advice that could be misinterpreted as treatment plans
- Liability from ongoing "treatment" relationships

If conversation history is needed for user experience, it should be:
- Stored client-side only
- Used only for UI convenience (showing previous queries)
- Never used to influence medical content generation


## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Simple Language Usage

*For any* symptom query and generated explanation, the explanation text should not contain medical jargon or technical terminology from a predefined list of complex medical terms, ensuring accessibility to general audiences.

**Validates: Requirements 1.1, 1.3**

### Property 2: Multiple Possible Causes

*For any* symptom query, the generated response should present at least two possible explanations for the symptoms, demonstrating that the system avoids single definitive conclusions.

**Validates: Requirements 1.2**

### Property 3: No Diagnosis Language

*For any* symptom query and generated response, the response text should not contain diagnosis assertion patterns such as "You have [disease]", "This is [condition]", "You are suffering from [diagnosis]", or similar definitive diagnostic statements.

**Validates: Requirements 1.4, 7.1**

### Property 4: Complete Recommendation Sections

*For any* symptom query, the generated response should include all four recommendation categories: precautions, diet suggestions, lifestyle changes, and preventive care measures.

**Validates: Requirements 2.1, 2.2, 2.3, 2.4**

### Property 5: Suggestion Language Pattern

*For any* recommendation in the generated response, the language should use suggestion patterns (e.g., "you might", "consider", "may help", "could try") rather than prescription patterns (e.g., "you must", "take", "prescribed", "required").

**Validates: Requirements 2.5**

### Property 6: Medicine Information Educational Framing

*For any* medicine mentioned in the response, the information provided should be framed as general educational content (e.g., "commonly used for", "generally known as") rather than personalized recommendations (e.g., "you should take", "prescribed for you").

**Validates: Requirements 3.1, 3.2**

### Property 7: No Dosage Information

*For any* generated response, the text should not contain specific dosage patterns including numbers followed by dosage units (e.g., "mg", "ml", "tablets"), frequency instructions (e.g., "twice daily", "every 6 hours"), or quantity specifications.

**Validates: Requirements 3.3, 7.3**

### Property 8: No Prescription Recommendations

*For any* generated response, the text should not contain prescription language patterns such as "take [medicine]", "you should use [drug]", "prescribed [medication]", or directive statements about specific medicines.

**Validates: Requirements 3.4, 7.2**

### Property 9: Medicine Safety Information Inclusion

*For any* medicine or remedy mentioned in the response, the information should include either safety notes, contraindications, or warnings to ensure users are aware of potential risks.

**Validates: Requirements 3.5, 8.2**

### Property 10: Complete Wellness Practice Sections

*For any* symptom query, the generated response should include recommendations for all four wellness practice categories: yoga, breathing exercises, meditation, and stress management.

**Validates: Requirements 4.1, 4.2, 4.3, 4.4**

### Property 11: Wellness Practice Benefit Explanations

*For any* wellness practice recommended in the response, the practice description should include an explanation of how it may support physical or mental balance.

**Validates: Requirements 4.5**

### Property 12: Physical Practice Safety Precautions

*For any* physical wellness practice (yoga, breathing exercises) recommended in the response, the practice description should include safety precautions or contraindications.

**Validates: Requirements 4.6**

### Property 13: No Alarmist Language

*For any* generated response, the text should not contain alarmist keywords or anxiety-inducing phrases (e.g., "fatal", "deadly", "dangerous" used without appropriate context, "you will die", "life-threatening" for non-urgent symptoms) that could cause unnecessary panic.

**Validates: Requirements 5.3**

### Property 14: Structured Response Format

*For any* generated response, the content should follow the defined structure with clearly identifiable sections: Possible Causes, Lifestyle Recommendations, Wellness Practices, Medicine Information (if applicable), and Disclaimer.

**Validates: Requirements 5.4**

### Property 15: Disclaimer Presence and Content

*For any* health guidance response, the output should include a disclaimer that explicitly states the educational nature of the information, recommends consultation with a qualified healthcare professional, and appears in the response.

**Validates: Requirements 6.1, 6.2, 6.4**

### Property 16: Urgency Emphasis for Critical Symptoms

*For any* symptom query containing urgency indicators (e.g., "severe chest pain", "difficulty breathing", "loss of consciousness"), the response should include prominent language emphasizing the importance of seeking immediate medical attention.

**Validates: Requirements 6.3**

### Property 17: No Medical Replacement Claims

*For any* generated response, the text should not contain language claiming to replace professional medical diagnosis or treatment (e.g., "instead of seeing a doctor", "no need for medical consultation", "this replaces professional advice").

**Validates: Requirements 6.5**

### Property 18: Uncertainty Acknowledgment

*For any* symptom query where the system has low confidence or limited information, the response should include explicit acknowledgment of limitations and recommend professional consultation for accurate assessment.

**Validates: Requirements 8.3**

### Property 19: Experimental Treatment Context

*For any* response that mentions experimental or unproven treatments, the text should clearly label them as such (e.g., "experimental", "not yet proven", "under research") to distinguish from established treatments.

**Validates: Requirements 8.4**

## Error Handling

### Input Validation Errors

**Empty or Invalid Input**:
- Detect when user input is empty, too short, or contains no meaningful symptom information
- Response: Politely ask the user to describe their symptoms or concerns
- Example: "I'd be happy to help. Could you please describe the symptoms you're experiencing?"

**Inappropriate Requests**:
- Detect when users explicitly request diagnosis, prescriptions, or dosages
- Response: Decline politely and redirect to professional consultation
- Example: "I can't provide a diagnosis, but I can share general information about symptoms. For a proper diagnosis, please consult with a qualified healthcare provider."

**Emergency Indicators**:
- Detect severe symptoms requiring immediate attention (chest pain, difficulty breathing, severe bleeding, loss of consciousness)
- Response: Provide urgent medical attention notice prominently
- Example: "⚠️ These symptoms may require immediate medical attention. Please call emergency services or go to the nearest emergency room right away."

### Content Generation Errors

**Knowledge Base Lookup Failure**:
- When symptom information cannot be found in the knowledge base
- Response: Acknowledge limitation and recommend professional consultation
- Example: "I don't have enough information about these specific symptoms. I recommend consulting with a healthcare provider who can properly evaluate your condition."

**Insufficient Information**:
- When user provides vague or ambiguous symptom descriptions
- Response: Ask clarifying questions while maintaining safety boundaries
- Example: "To provide more helpful information, could you describe when these symptoms started and how they affect you?"

### Safety Validation Errors

**Safety Violation Detected**:
- When generated content fails safety validation
- Action: Remove or rephrase violating content, regenerate if necessary
- Fallback: If content cannot be sanitized, return generic response with disclaimer and professional consultation recommendation

**Multiple Violation Attempts**:
- When content repeatedly fails safety validation (indicates systemic issue)
- Action: Log error for review, return safe fallback response
- Fallback Response: "I want to make sure I provide safe and appropriate information. For guidance on your specific situation, please consult with a qualified healthcare provider."

### System Errors

**Service Unavailability**:
- When knowledge base or content generation services are unavailable
- Response: Inform user of temporary unavailability and suggest trying again
- Example: "I'm having trouble accessing information right now. Please try again in a moment, or consult with a healthcare provider for immediate assistance."

**Timeout Errors**:
- When content generation takes too long
- Response: Apologize and suggest simplified query or professional consultation
- Example: "I'm taking longer than expected to process your request. You might try describing your main symptom, or consult with a healthcare provider for personalized guidance."

### Error Response Principles

1. **Always Include Disclaimer**: Even error responses should include the standard disclaimer
2. **Maintain Calm Tone**: Error messages should not alarm or frustrate users
3. **Provide Alternatives**: Always suggest next steps (rephrase query, consult professional, try again)
4. **Never Guess**: If uncertain, acknowledge limitation rather than providing potentially incorrect information
5. **Log for Improvement**: All errors should be logged for system improvement while protecting user privacy

## Testing Strategy

### Dual Testing Approach

The system requires both unit testing and property-based testing to ensure comprehensive coverage:

- **Unit Tests**: Verify specific examples, edge cases, and error conditions
- **Property Tests**: Verify universal properties across all inputs

Together, these approaches provide comprehensive coverage where unit tests catch concrete bugs and property tests verify general correctness.

### Property-Based Testing

**Framework**: Use a property-based testing library appropriate for the implementation language (e.g., Hypothesis for Python, fast-check for TypeScript/JavaScript, QuickCheck for Haskell).

**Configuration**:
- Minimum 100 iterations per property test (due to randomization)
- Each property test must reference its design document property
- Tag format: `Feature: medical-ai-health-guidance, Property {number}: {property_text}`

**Test Data Generation**:
- Generate random symptom descriptions with varying complexity
- Generate responses with different content patterns
- Include edge cases: empty strings, very long inputs, special characters
- Include urgency indicators in some generated inputs
- Generate medicine names and wellness practices

**Property Test Implementation**:
Each of the 19 correctness properties listed above must be implemented as a single property-based test that:
1. Generates random valid inputs
2. Processes them through the system
3. Verifies the property holds for all generated cases

### Unit Testing

**Focus Areas**:
- Specific symptom examples and their expected response structure
- Edge cases: empty input, extremely long input, special characters
- Error conditions: invalid requests, service failures, timeouts
- Integration points: Knowledge Base queries, Safety Validator interactions
- Disclaimer formatting and placement
- Urgency detection for specific critical symptoms

**Example Unit Tests**:
1. Test that "severe chest pain" triggers urgency notice
2. Test that empty input returns appropriate error message
3. Test that request for "diagnosis" is declined with redirection
4. Test that disclaimer appears in all valid responses
5. Test that medicine information includes safety notes
6. Test that wellness practices include precautions

**Balance**:
- Avoid writing too many unit tests for input variations (property tests handle this)
- Focus unit tests on specific integration scenarios and concrete examples
- Use unit tests to validate error handling paths
- Use unit tests for specific regulatory or compliance requirements

### Integration Testing

**Component Integration**:
- Test full pipeline: Input Processor → Content Generator → Safety Validator → Response Formatter
- Verify data flows correctly between components
- Test error propagation and handling across components

**Knowledge Base Integration**:
- Test queries return expected data structures
- Test handling of missing or incomplete data
- Test concurrent access patterns

**Safety Validator Integration**:
- Test that all content passes through validator
- Test that violations are correctly detected and handled
- Test sanitization and regeneration logic

### Test Coverage Goals

- **Code Coverage**: Minimum 85% line coverage, 90% branch coverage
- **Property Coverage**: All 19 correctness properties must have corresponding property tests
- **Requirement Coverage**: All testable acceptance criteria must be covered by either unit or property tests
- **Error Path Coverage**: All error handling paths must be tested

### Continuous Testing

- Run property tests with 100 iterations in CI/CD pipeline
- Run full test suite on every commit
- Monitor test execution time and optimize slow tests
- Track flaky tests and investigate root causes
- Maintain test data generators to ensure realistic inputs

### Safety Testing

**Adversarial Testing**:
- Deliberately attempt to elicit diagnosis, prescriptions, or dosages
- Test with medical terminology to ensure proper handling
- Test with urgent symptoms to verify appropriate responses
- Test boundary cases between educational information and medical advice

**Compliance Testing**:
- Verify all responses meet regulatory requirements
- Test disclaimer presence and content
- Verify no medical advice claims
- Document test results for compliance audits

### Performance Testing

While not the primary focus, basic performance testing should include:
- Response time for typical queries (target: < 2 seconds)
- Throughput under concurrent load
- Memory usage patterns
- Knowledge Base query performance

Performance issues should not compromise safety—if a choice must be made, prioritize safety over speed.
