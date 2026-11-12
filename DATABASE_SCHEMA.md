# NOVENTRA BUSINESS OS
## Database Schema Specification
### Version 1.0

Status: Initial Data Model Design
Database Concept: Relational Database
Preferred Technology: PostgreSQL

---

# 1. DATABASE OBJECTIVE

The NOVENTRA database must provide a secure, structured, and scalable foundation for managing users, businesses, projects, AI operations, and digital commerce.

The database must support multiple users and multiple businesses while maintaining strict data isolation.

# 2. DATABASE DESIGN PRINCIPLES

- Relational data structure.
- Unique identifiers for records.
- Referential integrity.
- Data ownership.
- Secure access control.
- Consistent naming conventions.
- Appropriate indexing.
- Data validation.
- Auditability.
- Scalability.

# 3. CORE ENTITIES

The initial database will contain the following entities:

1. Users
2. User Profiles
3. Businesses
4. Business Memberships
5. Business Projects
6. Business Analyses
7. Business Models
8. Brand Identities
9. Websites
10. Marketing Plans
11. Products
12. Customers
13. Orders
14. AI Agents
15. AI Tasks
16. AI Interactions
17. Project Tasks
18. Subscriptions
19. Notifications
20. Audit Logs

# 4. USERS

Table: users

Purpose:
Store platform account information.

Fields:

- id: Unique user identifier.
- email: User email address.
- password_hash: Secure password hash, when authentication is managed internally.
- account_status: Account status.
- email_verified_at: Email verification timestamp.
- created_at: Account creation timestamp.
- updated_at: Last update timestamp.

Requirements:

- Email addresses must be unique.
- Passwords must never be stored as plain text.
- Authentication credentials must be protected.
- Sensitive account operations must be audited.

# 5. USER PROFILES

Table: user_profiles

Purpose:
Store additional user information.

Fields:

- id
- user_id
- display_name
- preferred_language
- timezone
- profile_image
- created_at
- updated_at

Relationship:

One user has one profile.

# 6. BUSINESSES

## Table: businesses

Purpose:
Represent independent businesses managed through NOVENTRA.

Fields:
- id (Primary Key)
- name
- description
- industry
- business_type
- owner_user_id (Foreign Key -> users.id)
- status
- created_at
- updated_at

Relationships:
- One user may own multiple businesses.
- Each business must have one designated owner.
- Each business may have multiple users through business_memberships.
- The owner_user_id must reference a valid user.
- The designated owner must have an active Owner membership in business_memberships.

Requirements:
- Every business must have a unique identifier.
- Business ownership must be verified by the backend.
- Only authorized users may view or modify business information.
- Business data must remain isolated from other businesses.
- Ownership changes must update the ownership record and membership consistently.
- Ownership changes must be recorded in the audit log.
- Business status changes must be authorized and logged.
# 7. BUSINESS MEMBERSHIPS
## Table: business_memberships

Purpose:
Manage user access and permissions for businesses.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- user_id (Foreign Key -> users.id)
- role
- membership_status
- created_at
- updated_at

Possible roles:
- Owner
- Administrator
- Manager
- Editor
- Viewer

Requirements:
- Each membership must belong to one business and one user.
- A user must not have duplicate memberships for the same business.
- The combination of business_id and user_id must be unique.
- All permissions must be verified by the backend.
- Users must only access businesses where they have an active membership.
- Inactive or revoked memberships must not grant access.
- Ownership transfers must require explicit authorization.
- Every ownership transfer must be recorded in the audit log.
- Critical permission changes must be logged.
- Database-level security policies must enforce business data isolation where supported.
# 8. BUSINESS PROJECTS
## Table: business_projects

Purpose:
Store individual business development projects.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- title
- description
- project_category
- project_status
- created_by (Foreign Key -> users.id)
- created_at
- updated_at

Relationships:
- One business may contain multiple projects.
- Each project must belong to exactly one business.
- Each project must be created by a valid user.
- The creator must have an active membership in the associated business.

Requirements:
- Every project must be associated with a valid business.
- Project access must be controlled by business membership permissions.
- Users must not access projects belonging to unauthorized businesses.
- Project creation, updates, and deletion must be authorized by the backend.
- Project ownership and business association must not be changed without authorization.
- Project deletion must follow defined retention and recovery policies.
- Important project changes must be recorded in the audit log.
- Database-level security policies must enforce business data isolation where supported.


# 9. BUSINESS ANALYSES

## Table: business_analyses

Purpose:
Store structured business analysis results generated by AI agents or authorized users.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id)
- created_by (Foreign Key -> users.id)
- ai_task_id (Foreign Key -> ai_tasks.id, nullable)
- analysis_type
- input_data
- result_data
- edited_result_data (nullable)
- assumptions
- risks
- validation_status
- model_identifier (nullable)
- version_number
- reviewed_by (Foreign Key -> users.id, nullable)
- reviewed_at (nullable)
- created_at
- updated_at

Relationships:
- Each analysis belongs to exactly one business.
- Each analysis belongs to exactly one business project.
- The associated project must belong to the same business.
- Each analysis must have a valid creator.
- An analysis may optionally reference an AI task.
- The referenced AI task, when provided, must belong to the same business.
- Review information must reference an authorized user.

Requirements:
- AI-generated results must be clearly identifiable.
- Analysis history must be preserved.
- Each new analysis run must create a new historical record rather than silently replacing an earlier analysis.
- Original AI-generated results must remain distinguishable from user-edited results.
- Authorized users must be able to review and edit relevant outputs.
- User edits must be traceable, including the editor and edit timestamp.
- Validation status must distinguish pending, approved, rejected, and needs_revision states.
- Version numbers must support analysis history and version tracking.
- Analysis data must be isolated by business.
- Users must only access analyses belonging to businesses where they have authorized membership.
- All access and modifications must be authorized by the backend.
- Structured input and output data must use a consistent format.
- Sensitive business and personal information must be protected.
- Secrets, passwords, and API keys must never be stored in analysis records.
- Important analysis changes and approvals must be recorded in audit_logs.
- Data retention must follow applicable privacy and retention policies.
- Database-level security policies must enforce business data isolation where supported.

# 10. BUSINESS MODELS

## Table: business_models

Purpose:
Store structured business model information for each business project, including AI-generated and user-edited business model versions.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id)
- created_by (Foreign Key -> users.id)
- analysis_id (Foreign Key -> business_analyses.id, nullable)
- value_proposition
- customer_segments
- revenue_streams
- cost_structure
- channels
- key_activities
- key_resources
- key_partnerships
- customer_relationships
- status
- version_number
- generation_source
- reviewed_by (Foreign Key -> users.id, nullable)
- created_at
- updated_at

Relationships:
- Each business model belongs to exactly one business.
- Each business model belongs to exactly one business project.
- The associated project must belong to the same business.
- Each business model must have a valid creator.
- A business model may reference a business analysis.
- The referenced analysis, when provided, must belong to the same business and project.
- Review information must reference an authorized user.

Requirements:
- Business models must be isolated by business.
- Users must have active membership and appropriate permissions to access or modify business models.
- All operations must be authorized by the backend.
- Previous versions must be preserved.
- Each significant revision must create a new version.
- AI-generated content must be distinguishable from user-created or edited content.
- Users must be able to review and edit business model information.
- Important changes must be recorded in audit_logs.
- Status values must follow a predefined lifecycle.
- Financial values must use appropriate decimal precision and ISO currency codes where applicable.
- Sensitive business information must be protected.
- Database-level security policies must enforce business data isolation where supported.

# 11. BRAND IDENTITIES


## Table: brand_identities

Purpose:
Store structured brand identity information for businesses, including AI-generated branding, visual assets, brand guidelines, and customizable design settings.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id, nullable)
- created_by (Foreign Key -> users.id)
- brand_name
- tagline
- brand_description
- brand_voice
- target_audience
- primary_color
- secondary_color
- accent_colors
- color_palette
- typography
- logo_asset
- logo_variations
- visual_style
- brand_guidelines
- generation_source
- status
- version_number
- is_default
- created_at
- updated_at

Relationships:
- Each brand identity belongs to exactly one business.
- A brand identity may optionally belong to a business project.
- The associated project, when provided, must belong to the same business.
- Each brand identity must have a valid creator.
- One business may have multiple brand identity versions or brand profiles.
- Only one active default brand identity should exist per business.

Requirements:
- Brand identities must be isolated by business.
- Users must have active membership and appropriate permissions to manage brand identities.
- All operations must be authorized by the backend.
- Users must be able to customize brand names, taglines, colors, typography, and visual styles.
- Color values must use validated formats such as HEX.
- Color palettes must support multiple colors and named theme presets.
- The system must support future custom themes and reusable design tokens.
- Logo assets must be stored securely in file storage.
- Uploaded assets must be validated for file type and size.
- AI-generated and user-created branding must be distinguishable.
- Previous brand identity versions must be preserved.
- Important changes must be recorded in audit_logs.
- Brand guidelines must support structured and extensible data.
- Brand identity data must be available to authorized website and 3D commerce builders.
- Database-level security policies must enforce business data isolation where supported.
# 12. WEBSITES

## Table: websites

Purpose:
Manage website projects, drafts, published versions, content, design configurations, and deployment information for each business.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id)
- brand_identity_id (Foreign Key -> brand_identities.id, nullable)
- created_by (Foreign Key -> users.id)
- website_name
- website_type
- domain_name (nullable)
- page_structure
- website_content
- design_settings
- seo_settings
- publication_status
- deployment_status
- published_url (nullable)
- version_number
- published_at (nullable)
- created_at
- updated_at

Relationships:
- Each website belongs to exactly one business.
- Each website belongs to exactly one business project.
- The associated project must belong to the same business.
- Each website must have a valid creator.
- A website may reference a brand identity belonging to the same business.
- One business may have multiple websites.

Requirements:
- Website data must be isolated by business.
- Users must have appropriate membership permissions to manage websites.
- All operations must be authorized by the backend.
- Drafts and published versions must be distinguishable.
- Previous published versions should be recoverable.
- Website content and page structures must support structured data.
- Design settings must support customizable themes and responsive layouts.
- SEO settings must support metadata, descriptions, and search engine configuration.
- Domain ownership must be verified before connecting custom domains.
- Publishing must require appropriate user authorization.
- Deployment failures must be recorded and reported.
- Website assets must be stored securely.
- Important changes and publication events must be recorded in audit_logs.
- Database-level security policies must enforce business data isolation where supported.

# 13. MARKETING PLANS

## Table: marketing_plans

Purpose:
Store AI-generated and user-managed marketing strategies, campaigns, content plans, and marketing performance information for each business.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id)
- created_by (Foreign Key -> users.id)
- analysis_id (Foreign Key -> business_analyses.id, nullable)
- brand_identity_id (Foreign Key -> brand_identities.id, nullable)
- target_audience
- objectives
- content_strategy
- marketing_channels
- marketing_calendar
- campaign_data
- budget
- currency
- performance_metrics
- generation_source
- status
- version_number
- created_at
- updated_at

Relationships:
- Each marketing plan belongs to exactly one business.
- Each marketing plan belongs to exactly one business project.
- The associated project must belong to the same business.
- Each marketing plan must have a valid creator.
- A marketing plan may reference a business analysis.
- A marketing plan may reference a brand identity.
- Referenced analyses and brand identities must belong to the same business.

Requirements:
- Marketing plans must be isolated by business.
- Users must have appropriate membership permissions to access and manage marketing plans.
- All operations must be authorized by the backend.
- AI-generated and user-created content must be distinguishable.
- Users must be able to review and edit marketing strategies.
- Previous plan versions must be preserved.
- Marketing channels must support future integrations with external platforms.
- Marketing calendars must support scheduled campaigns and content publication.
- Budget values must use decimal precision and valid ISO currency codes.
- Campaign performance metrics must use a consistent structured format.
- Publishing advertisements or sending external messages must require appropriate authorization.
- Spending money or exceeding an approved budget must require explicit human approval.
- Important changes and approvals must be recorded in audit_logs.
- Sensitive business and customer information must be protected.
- Database-level security policies must enforce business data isolation where supported.
# 14. PRODUCTS

## Table: products

Purpose:
Manage products and services offered by a business.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- name
- description
- product_type
- price
- currency
- inventory_quantity (nullable)
- status
- created_at
- updated_at

Relationships:
- Each product must belong to exactly one business.
- One business may have multiple products.
- Product ownership is determined by its business_id.

Requirements:
- Every product must be associated with a valid business.
- Product access must follow the permissions of its associated business.
- Users must not access or modify products belonging to unauthorized businesses.
- Product creation, updates, and deletion must be authorized by the backend.
- Price and inventory changes must be validated.
- Price must not be negative.
- Currency must use a valid ISO 4217 currency code.
- Inventory quantity must not be negative when inventory tracking is enabled.
- Important product changes must be recorded in the audit log.
- Database-level security policies must enforce business data isolation where supported.

# 15. CUSTOMERS

## Table: customers

Purpose:
Store customer records belonging to individual businesses.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- name
- email (nullable)
- phone (nullable)
- customer_status
- created_at
- updated_at

Relationships:
- Each customer must belong to exactly one business.
- One business may have multiple customers.
- Customer records must remain associated with their original business.

Requirements:
- Every customer record must reference a valid business.
- Customer data must be isolated by business_id.
- Access must be authorized through active business membership.
- Users must not access customer records belonging to unauthorized businesses.
- Only authorized users may create, view, update, or delete customer records.
- Personal information must be protected against unauthorized access.
- Email and phone fields must be validated when provided.
- Personal data must only be collected and processed for legitimate business purposes.
- Data retention and deletion must follow applicable privacy requirements.
- Sensitive customer information must not be exposed in application logs.
- Important customer record changes must be recorded in the audit log without unnecessarily storing personal data in audit entries.
- Database-level security policies must enforce business data isolation where supported.
# 16. ORDERS

## Table: orders

Purpose:
Manage commerce orders for individual businesses.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- customer_id (Foreign Key -> customers.id)
- order_number
- order_status
- subtotal
- tax_amount
- total_amount
- currency
- payment_status
- created_at
- updated_at

Relationships:
- Each order must belong to exactly one business.
- Each order must reference a valid customer belonging to the same business.
- One customer may have multiple orders.
- One business may have multiple orders.
- Each order may contain multiple order items.

Requirements:
- Order access must follow business membership permissions.
- Users must not access orders belonging to unauthorized businesses.
- The backend must verify that customer_id belongs to the same business_id.
- Order numbers must be unique within each business.
- Monetary values must use precise decimal types, not floating-point types.
- Subtotal, tax_amount, and total_amount must not be negative.
- Currency must use a valid ISO 4217 currency code.
- Total amounts must be calculated and validated by the backend.
- Payment status must only be updated through authorized payment workflows.
- Completed financial records must not be silently modified or deleted.
- Important order and payment status changes must be recorded in the audit log.
- Database-level security policies must enforce business data isolation where supported.

Order Items:
Order items must be stored in a separate order_items table.
Each order item must reference its parent order and preserve product details, quantity, unit price, and line total at the time of purchase.
# 17. ORDER ITEMS
## Table: order_items

Purpose:
Store individual products or services included in an order.

Fields:
- id (Primary Key)
- order_id (Foreign Key -> orders.id)
- product_id (Foreign Key -> products.id, nullable)
- product_name_snapshot
- quantity
- unit_price
- total_price
- currency
- created_at
- updated_at

Relationships:
- Each order item must belong to exactly one order.
- Each order may contain multiple order items.
- Each order item may reference a product.
- Product details must be preserved as a snapshot at purchase time.

Requirements:
- Every order item must reference a valid order.
- The referenced product, when provided, must belong to the same business as the parent order.
- Product name and price snapshots must remain unchanged after purchase.
- Quantity must be greater than zero.
- Unit price and total price must not be negative.
- Monetary values must use precise decimal types.
- Currency must match the parent order currency.
- Total price must be calculated and validated by the backend.
- Historical order items must not change when product details are updated.
- Access permissions must follow the parent order and business.
- Unauthorized users must not access or modify order items.
- Important changes must be recorded in the audit log.

# 18. AI AGENTS

## Table: ai_agents

Purpose:
Define and manage AI agents available within NOVENTRA.

Fields:
- id (Primary Key)
- name
- description
- agent_type
- configuration
- permission_level
- status
- created_at
- updated_at

Relationships:
- One AI agent may execute multiple AI tasks.
- Each AI task must reference a valid AI agent.
- AI agents must operate within explicitly assigned business contexts.

Requirements:
- Every AI agent must have a clearly defined role and responsibility.
- Agent permissions must follow the principle of least privilege.
- Agents must only access business data explicitly authorized for their tasks.
- Agent permissions must be validated by the backend.
- Agents must not access data belonging to unauthorized businesses.
- Agent configurations must not contain exposed API keys, passwords, tokens, or other secrets.
- Secrets must be stored in secure server-side secret management systems.
- Agent configuration changes must be authorized and logged.
- Disabled agents must not receive new tasks.
- Agent execution must be monitored and logged.
- Agents must not independently perform sensitive financial or external actions without required human approval.
- AI-generated outputs must be associated with their originating tasks.
- Database-level security policies must enforce business data isolation where supported.

# 19. AI TASKS

## Table: ai_tasks

Purpose:
Track tasks assigned to AI agents within individual businesses.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- project_id (Foreign Key -> business_projects.id, nullable)
- agent_id (Foreign Key -> ai_agents.id)
- created_by (Foreign Key -> users.id)
- task_type
- task_input
- task_status
- result_reference
- created_at
- updated_at
- completed_at

Possible statuses:
- Pending
- Running
- Waiting for Approval
- Completed
- Failed
- Cancelled

Relationships:
- Each AI task must belong to exactly one business.
- Each AI task must be assigned to one valid AI agent.
- A task may optionally belong to a business project.
- Each task must have a valid creator.
- The project, when provided, must belong to the same business.

Requirements:
- AI task access must follow business membership permissions.
- Users must not access AI tasks belonging to unauthorized businesses.
- The backend must validate business_id, project_id, agent_id, and created_by.
- AI agents must only process tasks authorized for their assigned business.
- Task input and output must remain isolated between businesses.
- Sensitive business and customer data must be protected.
- AI task execution must be logged.
- Failed tasks must preserve error information for authorized review.
- Tasks requiring external actions or high-impact decisions must enter Waiting for Approval.
- AI agents must not independently execute financial transactions, payments, or other sensitive actions without required human approval.
- Task status transitions must be validated by the backend.
- Important task events must be recorded in the audit log.
- Database-level security policies must enforce business data isolation where supported.
# 20. AI INTERACTIONS

## Table: ai_interactions

Purpose:
Maintain secure and traceable records of interactions between users and AI agents.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- user_id (Foreign Key -> users.id)
- agent_id (Foreign Key -> ai_agents.id)
- task_id (Foreign Key -> ai_tasks.id, nullable)
- interaction_type
- input_reference
- output_reference
- model_identifier
- token_usage
- interaction_status
- created_at

Relationships:
- Each interaction must belong to exactly one business.
- Each interaction must be associated with a valid user.
- Each interaction must reference a valid AI agent.
- An interaction may optionally reference an AI task.
- The user must have an active membership in the associated business.
- The referenced task, when provided, must belong to the same business.

Requirements:
- AI interaction records must be isolated by business.
- Users must not access interactions belonging to unauthorized businesses.
- All access must be authorized by the backend.
- Input and output references must point to securely stored records.
- Sensitive information must be protected according to privacy and retention policies.
- API keys, passwords, authentication tokens, and other secrets must never be stored in interaction records.
- Model identifiers and token usage must be recorded for monitoring and cost analysis.
- Interaction records must support authorized auditing and troubleshooting.
- Access to interaction records must be logged where appropriate.
- Data retention and deletion must follow applicable privacy requirements.
- Database-level security policies must enforce business data isolation where supported.

# 21. PROJECT TASKS

## Table: project_tasks

Purpose:
Track activities and tasks within business development projects.

Fields:
- id (Primary Key)
- project_id (Foreign Key -> business_projects.id)
- title
- description
- assigned_to (Foreign Key -> users.id, nullable)
- priority
- task_status
- due_date
- created_at
- updated_at

Relationships:
- Each task must belong to exactly one business project.
- One project A task may be assigned to one user.
- The assigned user must have an active membership in the business associated with the project.

Requirements:
- Every task must be associated with a valid project.
- Task access must follow the permissions of the parent business project.
- Users must not access tasks belonging to unauthorized businesses.
- Only authorized users may create, update, assign, or delete tasks.
- Task assignments must be validated by the backend.
- Task priority and status changes must be authorized.
- Important task changes must be recorded in the audit log.
- Database-level security policies must enforce business data isolation where supported.
# 22. SUBSCRIPTIONS

## Table: subscriptions

Purpose:
Manage subscription plans, billing periods, subscription status, and billing provider references for businesses using NOVENTRA.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- user_id (Foreign Key -> users.id)
- plan_id (Foreign Key -> subscription_plans.id)
- subscription_status
- billing_interval
- started_at
- current_period_start
- current_period_end
- trial_ends_at (nullable)
- canceled_at (nullable)
- payment_provider
- provider_customer_reference (nullable)
- provider_subscription_reference (nullable)
- created_at
- updated_at

Relationships:
- Each subscription belongs to exactly one business.
- Each subscription is associated with a valid user who manages the subscription.
- Each subscription references one valid subscription plan.
- A business may have subscription history.
- The subscription manager must have appropriate business permissions.

Requirements:
- Subscription data must be isolated by business.
- Subscription status must follow a predefined lifecycle.
- Billing intervals must support monthly and yearly subscriptions.
- Subscription renewals and cancellations must be tracked.
- Plan changes must be recorded.
- Payment credentials must be managed by a compliant payment provider rather than stored directly.
- Only non-sensitive payment provider references may be stored.
- Payment processing must use secure provider integrations.
- Subscription limits and entitlements must be enforced by the backend.
- Failed payments and renewal events must be recorded.
- Subscription changes must be recorded in audit_logs.
- Users must have appropriate permissions to manage subscriptions.
- Database-level security policies must enforce business data isolation where supported.

# 23. NOTIFICATIONS

## Table: notifications

Purpose:
Store and manage system, business, security, AI task, subscription, and user activity notifications.

Fields:
- id (Primary Key)
- user_id (Foreign Key -> users.id)
- business_id (Foreign Key -> businesses.id, nullable)
- notification_type
- title
- message
- priority
- related_resource_type (nullable)
- related_resource_id (nullable)
- action_url (nullable)
- delivery_status
- read_at (nullable)
- created_at
- updated_at

Relationships:
- Each notification belongs to exactly one user.
- A notification may optionally be associated with a business.
- The associated user must have appropriate access to the business when the notification is business-related.
- Notifications may reference related business resources.

Requirements:
- Users must only access their own notifications.
- Business-related notifications must respect business membership and authorization.
- Notifications must be generated by trusted backend services.
- Notification types and priorities must follow predefined values.
- Read and unread states must be supported.
- Notifications must support filtering and pagination.
- Sensitive information must not be exposed through notification messages.
- Notification delivery must be tracked.
- Duplicate notifications should be controlled.
- Users must be able to manage notification preferences.
- Notification retention must follow applicable data retention policies.
- Important security notifications must not be silently discarded.
- Access must be authorized by the backend.

# 24. AUDIT LOGS

## Table: audit_logs

Purpose:
Record important security, administrative, AI-related, and business events for auditing, accountability, and incident investigation.

Fields:
- id (Primary Key)
- actor_user_id (Foreign Key -> users.id, nullable)
- business_id (Foreign Key -> businesses.id, nullable)
- action_type
- resource_type
- resource_id (nullable)
- event_details
- event_status
- ip_address (nullable)
- user_agent (nullable)
- request_id (nullable)
- created_at

Relationships:
- Each audit record may be associated with a user.
- Each audit record may be associated with a business.
- System-generated events may have no actor_user_id.
- Business-related events must reference a valid business.
- Resource references identify the affected record.

Requirements:
- Audit records must be protected against unauthorized modification and deletion.
- Normal users must never be allowed to modify or delete audit records.
- Audit records must be accessible only to authorized administrators and security personnel.
- Every business-related audit record must be isolated by business.
- Record important authentication, authorization, account, membership, business, project, AI, financial, and administrative events.
- Record successful and failed security-sensitive operations.
- Event details must use structured data with a consistent format.
- Passwords, API keys, access tokens, payment credentials, and other secrets must never be stored in audit records.
- Sensitive personal information must be minimized or redacted.
- Audit timestamps must use UTC.
- Audit records must support filtering by business, actor, action type, resource type, and date.
- Audit records must be retained according to the security and data retention policies.
- Audit events should be generated by trusted backend services.
- Critical audit records should be protected against tampering through appropriate database permissions and immutable or append-only storage controls.
- Access to audit records must itself be monitored.
- Audit logging failures for critical security operations must be handled safely and monitored.

# 25. MAIN RELATIONSHIPS

Users:
- One user may own multiple businesses.
- One user may belong to multiple businesses.

Businesses:
- One business may have multiple projects.
- One business may have multiple members.
- One business may have multiple products.
- One business may have multiple customers.
- One business may have multiple orders.

Projects:
- One project may have multiple analyses.
- One project may have multiple business models.
- One project may have multiple website drafts.
- One project may have multiple marketing plans.
- One project may have multiple tasks.

AI:
- One AI agent may execute multiple tasks.
- One task may generate multiple interaction records.

Orders:
- One order may contain multiple order items.
- One customer may have multiple orders.
- ## 25.9 Approval Request Relationships

- One business can have multiple approval requests.
- Each approval request belongs to exactly one business.
- One user can create multiple approval requests.
- Each approval request must reference an authorized requesting user.
- One AI task can be associated with multiple approval requests.
- An approval request may optionally reference one AI task.
- One user can review multiple approval requests.
- Each approval request may have one reviewing user.
- The requesting user, reviewing user, and referenced AI task must be authorized within the same business context.
- Every approval decision must be recorded in audit_logs.
- Approval requests must follow the business-level access control rules.

# 26. DATA SECURITY REQUIREMENTS

- Use secure authentication.
- Enforce authorization on the server.
- Verify business ownership and membership.
- Prevent cross-tenant data access.
- Use database constraints.
- Apply appropriate row-level security where suitable.
- Protect sensitive personal information.
- Encrypt data in transit.
- Use secure secret management.
- Apply data retention policies.
- Maintain appropriate audit records.

# 27. DATA INTEGRITY

The implementation must consider:

- Primary keys.
- Foreign keys.
- Unique constraints.
- Not-null constraints.
- Check constraints.
- Appropriate indexes.
- Transaction management.
- Cascading behavior.
- Data validation.
- 
## 27.1 ADVANCED RELATIONAL INTEGRITY CONSTRAINTS

The database must enforce strict relational integrity and business-level data isolation.

### Business Ownership and Tenant Isolation

- Every business-owned record must be associated with a valid business.
- Records belonging to one business must never be accessible through another business context.
- Business membership must be validated before granting access to business resources.
- Cross-business references must be rejected.
- Critical tenant isolation rules must be enforced at both database and backend levels.

### Composite Foreign Key Requirements

Where necessary, composite foreign keys must enforce relationships between records belonging to the same business.

Examples include:

- A business project and its related business records.
- An order and its associated customer.
- An order item and its referenced product.
- An AI task and its associated business project.
- An AI interaction and its referenced AI task.
- An approval request and its referenced AI task.
- A website and its associated brand identity.

### User Authorization Integrity

- Business owners must have valid Owner memberships.
- Assigned project task users must be active members of the relevant business.
- AI task creators must have appropriate business permissions.
- Approval request creators and reviewers must be authorized within the relevant business.
- Unauthorized ownership transfers must be rejected.

### Subscription Integrity

- Subscription plans are platform-level records and are not owned by individual businesses.
- Each subscription must reference a valid subscription plan.
- Only authorized users may manage business subscriptions.
- Subscription limits and entitlements must be validated by the backend.

### Database Enforcement

- Use foreign key constraints for valid references.
- Use unique constraints to prevent duplicate records where required.
- Use CHECK constraints for valid numeric ranges and supported values.
- Use NOT NULL constraints for mandatory fields.
- Use composite constraints wherever necessary to prevent cross-business references.
- Use database transactions for operations involving multiple related records.
- Use Row Level Security (RLS) where supported.
- Test tenant isolation with multiple independent business accounts.
# 28. DATABASE IMPLEMENTATION ROADMAP

Stage 1:
Finalize the logical data model.

Stage 2:
Select the database technology.

Stage 3:
Create database tables and relationships.

Stage 4:
Implement migrations.

Stage 5:
Implement backend data access.

Stage 6:
Implement authorization and tenant isolation.

Stage 7:
Test data integrity and security.

# 29. FUTURE EXPANSION

Future database capabilities may include:

- Advanced inventory management.
- Financial transactions.
- Business analytics.
- 3D product assets.
- Virtual store configurations.
- Agent orchestration records.
- External integrations.
- Advanced subscription management.

These entities will be added when their corresponding features are implemented.

---

NOVENTRA BUSINESS OS

One Intelligence. Multiple Businesses.

Build. Connect. Manage. Scale.
## Table: subscription_plans

Purpose:
Define the subscription plans, pricing, features, usage limits, and access entitlements available to NOVENTRA customers.

Fields:
- id (Primary Key)
- plan_name
- plan_code
- description
- price
- currency
- billing_interval
- feature_limits
- ai_usage_limits
- storage_limit
- user_limit
- business_limit
- available_features
- plan_status
- is_public
- created_at
- updated_at

Relationships:
- One subscription plan may be associated with multiple subscriptions.
- Each subscription references exactly one subscription plan.

Requirements:
- Plan codes must be unique.
- Prices must use decimal precision rather than floating-point values.
- Currency must use valid ISO 4217 currency codes.
- Billing intervals must support monthly and yearly options.
- Feature and usage limits must use a consistent structured format.
- AI usage limits must support configurable token or usage quotas.
- Subscription entitlements must be enforced by the backend.
- Plan changes must not silently modify existing customer subscription records.
- Historical subscription pricing must remain traceable.
- Inactive plans must not be available for new subscriptions.
- Public plan information must not expose internal configuration or sensitive data.
- Plan creation and modification must be restricted to authorized administrators.
- Important plan changes must be recorded in audit_logs.
# 30. APPROVAL REQUESTS

Purpose: Store and manage approval requests for sensitive or high-impact actions initiated by AI agents or authorized system processes.

Fields:
- id (Primary Key)
- business_id (Foreign Key -> businesses.id)
- requested_by (Foreign Key -> users.id)
- ai_task_id (Foreign Key -> ai_tasks.id, Nullable)
- action_type
- action_payload (JSONB)
- risk_level
- approval_status
- reviewed_by (Foreign Key -> users.id, Nullable)
- reviewed_at (Nullable)
- decision_reason (Nullable)
- expires_at (Nullable)
- created_at
- updated_at

Relationships:
- Each approval request belongs to exactly one business.
- Each approval request is created by one authorized user or system process.
- An approval request may optionally reference one AI task.
- An approval request may be reviewed by one authorized user.
- One business can have multiple approval requests.

Requirements:
- The requesting user must have appropriate permissions within the business.
- The referenced AI task, if present, must belong to the same business.
- Supported approval statuses: Pending, Approved, Rejected, Expired, Cancelled.
- Supported risk levels: Low, Medium, High, Critical.
- Sensitive actions must not execute before approval when approval is required.
- Approval decisions must be recorded with reviewer identity and timestamp.
- Approval requests must be protected from unauthorized modification.
- Expired requests must not be approved or executed.
- Approval decisions must be recorded in audit_logs.
- Action payloads must be validated before execution.
- Sensitive information must not be stored unnecessarily in action_payload.
- All approval operations must enforce business-level access control.
