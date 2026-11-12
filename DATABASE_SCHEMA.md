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

Table: business_analyses

Purpose:
Store business analysis results.

Fields:

- id
- project_id
- analysis_type
- input_data
- result_data
- assumptions
- risks
- validation_status
- created_at

Requirements:

- AI-generated results must be identifiable.
- Analysis history should be preserved.
- Users must be able to review and edit relevant outputs.

# 10. BUSINESS MODELS

Table: business_models

Purpose:
Store business model information.

Fields:

- id
- project_id
- value_proposition
- customer_segments
- revenue_streams
- cost_structure
- channels
- key_activities
- status
- created_at
- updated_at

# 11. BRAND IDENTITIES

Table: brand_identities

Purpose:
Store branding information.

Fields:

- id
- business_id
- brand_name
- tagline
- brand_description
- primary_color
- secondary_color
- typography
- logo_asset
- brand_guidelines
- created_at
- updated_at

# 12. WEBSITES

Table: websites

Purpose:
Manage website projects and drafts.

Fields:

- id
- business_id
- project_id
- website_name
- website_type
- page_structure
- website_content
- design_settings
- publication_status
- created_at
- updated_at

# 13. MARKETING PLANS

Table: marketing_plans

Purpose:
Store marketing strategies and campaigns.

Fields:

- id
- business_id
- project_id
- target_audience
- objectives
- content_strategy
- marketing_calendar
- campaign_data
- status
- created_at
- updated_at

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

Table: ai_interactions

Purpose:
Maintain authorized AI interaction records.

Fields:

- id
- user_id
- businessagent_id
- interaction_type
- input_reference
- output_reference
- model_identifier
- token_usage
- created_at

Sensitive information must be handled according to privacy and retention policies.

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

Table: subscriptions

Purpose:
Manage customer subscription information.

Fields:

- id
- user_id
- business_id
- plan_name
- subscription_status
- billing_interval
- started_at
- expires_at
- created_at

Payment credentials must be managed by a compliant payment provider rather than stored directly.

# 23. NOTIFICATIONS

Table: notifications

Purpose:
Store user notifications.

Fields:

- id
- user_id
- notification_type
- title
- message
- read_at
- created_at

# 24. AUDIT LOGS

Table: audit_logs

Purpose:
Record important security and business events.

Fields:

- id
- actor_user_id
- business_id
- action_type
- resource_type
- resource_id
- event_details
- created_at

Audit records must be protected against unauthorized modification.

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
