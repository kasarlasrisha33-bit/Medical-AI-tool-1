# Requirements Document

## Introduction

The Medical AI Health Guidance Tool is an educational system designed to provide safe, informative health guidance based on user-reported symptoms. The system focuses on explaining possible causes, suggesting lifestyle modifications, and recommending wellness practices while maintaining clear boundaries around medical diagnosis and prescription. The tool serves as an educational resource that empowers users with knowledge while consistently emphasizing the importance of professional medical consultation.

## Glossary

- **System**: The Medical AI Health Guidance Tool
- **User**: An individual seeking health information and guidance
- **Symptom**: A physical or mental condition reported by the User
- **Health_Guidance**: Educational information about symptoms, causes, and wellness recommendations
- **Disclaimer**: A statement clarifying the educational nature of the system and recommending professional consultation
- **Wellness_Practice**: Activities including yoga, breathing exercises, meditation, and stress management
- **Traditional_Remedy**: Commonly known natural or home remedies used for health purposes
- **Medicine_Information**: General educational information about commonly used medicines
- **Lifestyle_Recommendation**: Suggestions for diet, exercise, sleep, and daily habits
- **Precaution**: Preventive measures or safety considerations related to symptoms

## Requirements

### Requirement 1: Symptom Explanation

**User Story:** As a user, I want to understand possible causes of my symptoms in simple terms, so that I can be better informed about my health condition.

#### Acceptance Criteria

1. WHEN a User reports symptoms, THE System SHALL provide explanations of possible causes in non-technical language
2. WHEN explaining causes, THE System SHALL present multiple possible explanations without asserting a specific diagnosis
3. WHEN providing explanations, THE System SHALL avoid medical jargon and use terminology understandable to a general audience
4. THE System SHALL NOT state or imply a specific disease diagnosis based on reported symptoms

### Requirement 2: Lifestyle and Preventive Recommendations

**User Story:** As a user, I want to receive practical lifestyle recommendations, so that I can take proactive steps to improve my health.

#### Acceptance Criteria

1. WHEN a User reports symptoms, THE System SHALL suggest relevant precautions to prevent worsening of the condition
2. WHEN providing recommendations, THE System SHALL include diet suggestions appropriate to the reported symptoms
3. WHEN providing recommendations, THE System SHALL include lifestyle changes that may support overall health
4. WHEN providing recommendations, THE System SHALL include preventive care measures relevant to the symptoms
5. THE System SHALL present recommendations as suggestions rather than medical prescriptions

### Requirement 3: Medicine and Remedy Information

**User Story:** As a user, I want to learn about commonly used medicines and traditional remedies, so that I can have informed conversations with healthcare providers.

#### Acceptance Criteria

1. WHEN discussing medicines, THE System SHALL provide general educational information about commonly used medicines
2. WHEN discussing traditional remedies, THE System SHALL provide information about commonly known natural or home remedies
3. THE System SHALL NOT provide specific dosage instructions for any medicine or remedy
4. THE System SHALL NOT prescribe or recommend specific medicines for the User's condition
5. WHEN mentioning medicines or remedies, THE System SHALL include appropriate safety considerations

### Requirement 4: Wellness Practice Recommendations

**User Story:** As a user, I want to receive recommendations for yoga, breathing exercises, and meditation practices, so that I can support my physical and mental well-being.

#### Acceptance Criteria

1. WHEN a User reports symptoms, THE System SHALL recommend suitable yoga practices relevant to the symptoms
2. WHEN a User reports symptoms, THE System SHALL recommend appropriate breathing exercises
3. WHEN a User reports symptoms, THE System SHALL recommend meditation techniques suitable for the User's condition
4. WHEN a User reports symptoms, THE System SHALL recommend stress-management practices
5. WHEN recommending Wellness_Practices, THE System SHALL explain how each practice may support physical and mental balance
6. WHEN recommending physical practices, THE System SHALL include appropriate safety precautions

### Requirement 5: Communication Tone and Style

**User Story:** As a user, I want to receive guidance in a calm and supportive manner, so that I feel reassured and empowered rather than anxious.

#### Acceptance Criteria

1. THE System SHALL maintain a calm and reassuring tone in all communications
2. THE System SHALL use supportive language that empowers the User
3. THE System SHALL avoid alarmist or anxiety-inducing language when discussing symptoms
4. THE System SHALL present information in a clear and organized manner
5. THE System SHALL acknowledge the User's concerns with empathy

### Requirement 6: Medical Disclaimer and Professional Consultation

**User Story:** As a user, I want to be clearly informed about the limitations of the tool, so that I understand when to seek professional medical help.

#### Acceptance Criteria

1. WHEN providing Health_Guidance, THE System SHALL include a Disclaimer stating the educational nature of the information
2. THE System SHALL recommend consultation with a qualified healthcare professional in the Disclaimer
3. WHEN symptoms suggest potential urgency, THE System SHALL emphasize the importance of seeking immediate medical attention
4. THE System SHALL display the Disclaimer prominently with each guidance response
5. THE System SHALL NOT present itself as a replacement for professional medical diagnosis or treatment

### Requirement 7: Safety Boundaries

**User Story:** As a system administrator, I want the system to maintain strict safety boundaries, so that users are not misled or harmed by inappropriate medical advice.

#### Acceptance Criteria

1. THE System SHALL NOT diagnose diseases or medical conditions
2. THE System SHALL NOT provide prescription recommendations
3. THE System SHALL NOT provide specific medication dosages
4. WHEN a User requests diagnosis, THE System SHALL decline and redirect to professional consultation
5. WHEN a User requests prescriptions, THE System SHALL decline and redirect to professional consultation
6. WHEN a User requests dosage information, THE System SHALL decline and redirect to professional consultation

### Requirement 8: Content Accuracy and Reliability

**User Story:** As a user, I want to receive accurate and reliable health information, so that I can trust the guidance provided.

#### Acceptance Criteria

1. THE System SHALL provide Health_Guidance based on generally accepted medical knowledge
2. WHEN providing information about medicines or remedies, THE System SHALL include known contraindications or warnings
3. WHEN uncertain about information accuracy, THE System SHALL acknowledge limitations and recommend professional consultation
4. THE System SHALL avoid providing information about experimental or unproven treatments without appropriate context
