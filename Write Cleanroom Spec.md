Act as an Expert Product Manager and Functional Systems Analyst. I am providing you with two inputs:

1. A zipped archive of a complete codebase.
2. The complete history of release notes for this repository.

Your objective is to reverse-engineer these inputs into a definitive, exhaustive Functional Requirements Document (FRD). This document will serve as the sole foundation for a clean-room rewrite of the system by a separate engineering team.

You must adhere strictly to the following rules:

**1. Zero Technical Implementation Details**
Describe exactly *what* the system does, never *how* it does it. Do not mention programming languages, frameworks, libraries, database engines, architectural patterns, or specific algorithms.
*Incorrect:* "The system uses a PostgreSQL database to store user profiles and authenticates via JWT."
*Correct:* "The system maintains persistent user profiles and requires secure, token-based authentication for access."

**2. Clean-Room Exhaustiveness**
If a feature, edge case, validation rule, or capability exists in the code or is mentioned in the release notes, it must be documented. The rewrite team will only build what is in your document. Omissions will result in lost functionality. Use the release notes specifically to uncover hidden business logic, edge cases that were patched, and the exact current state of features. Ignore deprecated features that were completely removed.

**3. Precision and Depth**
Do not generalize. Instead of saying "Users can manage their accounts," detail every specific capability: "Users can update their email address (triggering a re-verification workflow), reset their password, delete their account (triggering a 30-day soft-deletion period), and view their login history."

Structure your output exactly as follows:

**I. Product Overview**
A high-level summary of the system's purpose, its primary value proposition, and its core operational domain.

**II. User Roles and Permissions**
A complete inventory of every actor in the system (e.g., Guest, Standard User, Admin, System/Cron). Detail the exact permissions, access levels, and restrictions for each role.

**III. Conceptual Domain Model**
Define the core entities the system manages (e.g., "Invoices", "Customers", "Projects") and how they relate to one another. Describe the attributes of these entities in plain language, including required fields and default states.

**IV. Comprehensive Feature Catalog**
Grouped by logical modules or user journeys. For every single feature, provide:
- Feature Name
- Actor (Who uses it)
- Trigger (What initiates it)
- Expected Outcome (What it accomplishes)
- Alternative Flows / Error Handling (What happens when inputs are invalid or prerequisites are not met)

**V. Business Rules and Constraints**
Extract all hardcoded logic, validation rules, rate limits, thresholds, and state-machine transitions. (e.g., "A draft post cannot be published unless it has at least one tag and a title exceeding 5 characters.")

**VI. External Interfaces and Integrations**
Conceptually describe any external systems this tool interacts with (e.g., "Payment Gateway", "Email Provider") and the exact functional triggers for those interactions, without naming specific third-party vendors unless the tool is explicitly built around them.

Take a deep breath, analyze the provided files thoroughly, and generate the complete Functional Requirements Document.
{{input}}
