# MS Access CRM System - Database Schema

## Tables

### tblUsers
Primary table for managing system users and their access levels.

```sql
CREATE TABLE tblUsers (
    UserID AUTOINCREMENT PRIMARY KEY,
    UserName TEXT(100) NOT NULL UNIQUE,
    FirstName TEXT(100),
    LastName TEXT(100),
    Email TEXT(150) NOT NULL UNIQUE,
    Phone TEXT(30),
    Department TEXT(100),
    Role TEXT(50),
    CanCreateCompanies YESNO DEFAULT TRUE,
    CanEditCompanies YESNO DEFAULT TRUE,
    CanDeleteCompanies YESNO DEFAULT FALSE,
    CanCreateContacts YESNO DEFAULT TRUE,
    CanEditContacts YESNO DEFAULT TRUE,
    CanDeleteContacts YESNO DEFAULT FALSE,
    CanCreateActivities YESNO DEFAULT TRUE,
    CanEditActivities YESNO DEFAULT TRUE,
    CanViewAllActivities YESNO DEFAULT FALSE,
    CanCreateTasks YESNO DEFAULT TRUE,
    CanEditTasks YESNO DEFAULT TRUE,
    CanAssignTasks YESNO DEFAULT FALSE,
    CanViewReports YESNO DEFAULT TRUE,
    IsActive YESNO DEFAULT TRUE,
    CreatedDate DATETIME DEFAULT NOW(),
    LastLoginDate DATETIME,
    Notes MEMO
);
```

**Fields:**
- **UserID** - Unique identifier (AutoNumber)
- **UserName** - Login username (required, unique)
- **FirstName** - User's first name
- **LastName** - User's last name
- **Email** - Email address (required, unique)
- **Phone** - Phone number
- **Department** - Department assignment (e.g., Sales, Marketing)
- **Role** - User role (Admin, Manager, Sales Rep, Viewer)
- **Can*** Fields - Permission controls for CRUD operations
- **IsActive** - Enable/disable user account
- **CreatedDate** - When account was created
- **LastLoginDate** - Last login timestamp
- **Notes** - Additional notes about user

---

### tblCompanies
Stores company/organization information.

```sql
CREATE TABLE tblCompanies (
    CompanyID AUTOINCREMENT PRIMARY KEY,
    CompanyName TEXT(150) NOT NULL,
    Industry TEXT(100),
    Phone TEXT(30),
    Email TEXT(150),
    Website TEXT(150),
    Address TEXT(255),
    City TEXT(100),
    State TEXT(100),
    Country TEXT(100),
    CompanyStatus TEXT(50),
    AnnualRevenue CURRENCY,
    EmployeeCount TEXT(50),
    OwnerID LONG,
    AssignedToID LONG,
    CreatedDate DATETIME DEFAULT NOW(),
    ModifiedDate DATETIME,
    Notes MEMO,
    FOREIGN KEY (OwnerID) REFERENCES tblUsers(UserID),
    FOREIGN KEY (AssignedToID) REFERENCES tblUsers(UserID)
);
```

**Fields:**
- **CompanyID** - Unique identifier
- **CompanyName** - Company legal name
- **Industry** - Industry classification
- **Phone, Email, Website** - Contact information
- **Address, City, State, Country** - Location
- **CompanyStatus** - Status (Active, Lead, Prospect, Inactive, Lost)
- **AnnualRevenue** - Revenue amount
- **EmployeeCount** - Number of employees
- **OwnerID** - User who created/owns the company
- **AssignedToID** - User responsible for account
- **CreatedDate** - Record creation date
- **ModifiedDate** - Last modification date
- **Notes** - Additional notes

---

### tblContacts
Stores individual contact information linked to companies.

```sql
CREATE TABLE tblContacts (
    ContactID AUTOINCREMENT PRIMARY KEY,
    CompanyID LONG NOT NULL,
    FirstName TEXT(100) NOT NULL,
    LastName TEXT(100),
    JobTitle TEXT(100),
    Department TEXT(100),
    Phone TEXT(30),
    Mobile TEXT(30),
    Email TEXT(150),
    LinkedIn TEXT(200),
    PreferredContact TEXT(50),
    ContactStatus TEXT(50),
    AssignedToID LONG,
    CreatedDate DATETIME DEFAULT NOW(),
    ModifiedDate DATETIME,
    Notes MEMO,
    FOREIGN KEY (CompanyID) REFERENCES tblCompanies(CompanyID),
    FOREIGN KEY (AssignedToID) REFERENCES tblUsers(UserID)
);
```

**Fields:**
- **ContactID** - Unique identifier
- **CompanyID** - Link to company (required)
- **FirstName, LastName** - Contact name
- **JobTitle, Department** - Position information
- **Phone, Mobile, Email** - Contact methods
- **LinkedIn** - LinkedIn profile URL
- **PreferredContact** - Preferred contact method
- **ContactStatus** - Status (Active, Inactive, Do Not Call)
- **AssignedToID** - User responsible for contact
- **CreatedDate, ModifiedDate** - Date tracking
- **Notes** - Additional notes

---

### tblActivities
Logs all customer interactions and follow-ups.

```sql
CREATE TABLE tblActivities (
    ActivityID AUTOINCREMENT PRIMARY KEY,
    CompanyID LONG NOT NULL,
    ContactID LONG,
    ActivityType TEXT(50) NOT NULL,
    ActivityDate DATETIME NOT NULL,
    Summary TEXT(255),
    Details MEMO,
    Outcome TEXT(255),
    NextActionDate DATETIME,
    CreatedByID LONG,
    CreatedDate DATETIME DEFAULT NOW(),
    ModifiedDate DATETIME,
    FOREIGN KEY (CompanyID) REFERENCES tblCompanies(CompanyID),
    FOREIGN KEY (ContactID) REFERENCES tblContacts(ContactID),
    FOREIGN KEY (CreatedByID) REFERENCES tblUsers(UserID)
);
```

**Fields:**
- **ActivityID** - Unique identifier
- **CompanyID** - Link to company (required)
- **ContactID** - Link to contact (optional)
- **ActivityType** - Type (Call, Email, Meeting, Follow-up, Note, Demo, Proposal)
- **ActivityDate** - When activity occurred
- **Summary** - Quick summary
- **Details** - Full activity notes
- **Outcome** - Result/outcome
- **NextActionDate** - When next action is due
- **CreatedByID** - User who logged activity
- **CreatedDate, ModifiedDate** - Date tracking

---

### tblTasks
Tracks tasks and to-do items for users.

```sql
CREATE TABLE tblTasks (
    TaskID AUTOINCREMENT PRIMARY KEY,
    CompanyID LONG,
    ContactID LONG,
    TaskTitle TEXT(200) NOT NULL,
    TaskDescription MEMO,
    TaskType TEXT(50),
    DueDate DATETIME NOT NULL,
    TaskStatus TEXT(50),
    Priority TEXT(50),
    AssignedToID LONG NOT NULL,
    CreatedByID LONG,
    CreatedDate DATETIME DEFAULT NOW(),
    ModifiedDate DATETIME,
    CompletedDate DATETIME,
    FOREIGN KEY (CompanyID) REFERENCES tblCompanies(CompanyID),
    FOREIGN KEY (ContactID) REFERENCES tblContacts(ContactID),
    FOREIGN KEY (AssignedToID) REFERENCES tblUsers(UserID),
    FOREIGN KEY (CreatedByID) REFERENCES tblUsers(UserID)
);
```

**Fields:**
- **TaskID** - Unique identifier
- **CompanyID, ContactID** - Links to company/contact
- **TaskTitle** - Task name (required)
- **TaskDescription** - Full task details
- **TaskType** - Type (Follow-up, Proposal, Demo, Meeting, Call)
- **DueDate** - Due date (required)
- **TaskStatus** - Status (Open, In Progress, Complete, Overdue)
- **Priority** - Priority level (High, Medium, Low)
- **AssignedToID** - User responsible for task (required)
- **CreatedByID** - User who created task
- **CreatedDate, ModifiedDate** - Date tracking
- **CompletedDate** - When task was completed

---

## Relationships

```
tblUsers
    ├─ One-to-Many → tblCompanies (OwnerID, AssignedToID)
    ├─ One-to-Many → tblContacts (AssignedToID)
    ├─ One-to-Many → tblActivities (CreatedByID)
    └─ One-to-Many → tblTasks (CreatedByID, AssignedToID)

tblCompanies
    ├─ One-to-Many → tblContacts
    ├─ One-to-Many → tblActivities
    └─ One-to-Many → tblTasks

tblContacts
    ├─ One-to-Many → tblActivities
    └─ One-to-Many → tblTasks

tblTasks
    └─ Multiple users can be assigned/create tasks
```

---

## User Roles (Recommended)

| Role | Description | Typical Permissions |
|------|-------------|-------------------|
| **Admin** | Full system access | All permissions enabled |
| **Manager** | Can create, edit, delete, view all | Most delete permissions enabled |
| **Sales Rep** | Can create, edit own records | Limited delete, view only assigned |
| **Viewer** | Read-only access | View reports and data only |

---

## Quick Reference: Role Permissions

| Permission | Admin | Manager | Sales Rep | Viewer |
|-----------|-------|---------|-----------|--------|
| Create Companies | ✓ | ✓ | ✓ | ✗ |
| Edit Companies | ✓ | ✓ | ✓ | ✗ |
| Delete Companies | ✓ | ✓ | ✗ | ✗ |
| Create Contacts | ✓ | ✓ | ✓ | ✗ |
| Edit Contacts | ✓ | ✓ | ✓ | ✗ |
| Delete Contacts | ✓ | ✓ | ✗ | ✗ |
| Create Activities | ✓ | ✓ | ✓ | ✗ |
| View All Activities | ✓ | ✓ | Assigned only | ✗ |
| Create Tasks | ✓ | ✓ | ✓ | ✗ |
| Assign Tasks | ✓ | ✓ | ✗ | ✗ |
| View Reports | ✓ | ✓ | ✓ | ✓ |

