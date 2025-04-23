# BEC Arabia Intranet Portal – Full Execution & Documentation Plan

**Classification:** BEC Arabia - External - Unrestricted Use

This document serves as a complete execution and implementation guide for the new Intranet Portal at BEC Arabia. It is tailored for developers and includes:

- UI/UX architecture
- SQL database schema
- Component-level code snippets (Blazor/C#)
- Best practices and tips
- DevOps & deployment guidelines

---

## ✅ Section 1: Project Overview

### 🎯 Objective

Redesign and enhance BEC Arabia’s intranet to improve:

- Internal communication
- HR and managerial operations
- Employee engagement
- Centralized access to resources and support

> 🧠 **Pro Tip:** Focus first on clarity and responsiveness for mobile & desktop. Base layout design on a grid system and modular UI components.

---

### 📌 Goals

- Centralize **HR**, **IT support**, and **employee resources** in one place  
- Enable **real-time features** (attendance, time-off, approvals)  
- Support **role-based access** for Employees, Managers, Directors  

---

### ⚙️ Tech Stack

| Layer        | Technology              |
|--------------|--------------------------|
| Frontend     | Blazor Server (.NET 8)   |
| Backend      | .NET 8 + EF Core         |
| Database     | Azure SQL                |
| Auth         | Azure AD / ASP.NET Identity |
| Hosting      | Azure App Service        |
| CI/CD        | GitHub Actions + Azure Pipelines |

> 🛠 **Dev Tip:** Use feature branches (`feature/ui-hero-carousel`) and PR reviews. Include a test plan in PR descriptions.

---

### 📁 Suggested Repo Structure

```plaintext
/IntranetPortal
├── /Client        # Blazor UI
├── /Server        # .NET APIs & services
├── /Shared        # Shared DTOs, enums, models
├── /Data          # EF migrations, DBContext
├── /Docs          # Markdown-based documentation
├── /Tests         # Unit/integration test projects
```

> 📁 **Structure Best Practice:**  
Group `.razor` files by features/modules like `/Pages/HR`, `/Pages/News`, `/Pages/Support`.

---

### ✅ Minimum Viable Modules (MVP)

- 📰 Internal & External News Section  
- 🎂 Birthday Announcements (auto-updated)  
- 🧾 HR Support (payslips, attendance, time-off requests)  
- 🖼 Hero Image Carousel (dynamic from DB)  
- ✅ Manager Approvals Portal  
- 🔗 IT & HR Quick Links Panel

---

### 🚀 Development Roadmap

| Phase | Description                        |
|-------|------------------------------------|
| 1     | UI/UX Enhancements & Layout        |
| 2     | Functional HR & Support Modules    |
| 3     | Hierarchical Workflows Integration |
| 4     | Testing, DevOps, Launch            |

---

### 🔐 Role-Based Access Matrix

| Role      | Permissions                                    |
|-----------|------------------------------------------------|
| Employee  | View pages, request time-off, view payslips    |
| Manager   | Approve requests, view team attendance/reports |
| Director  | Access analytics, edit content, full dashboard |

> 🛡 **Security Tip:**  
Use **policy-based authorization** for roles. Avoid relying only on `[Authorize(Roles="")]`—implement permission-based claims for flexibility.

---

### 💡 Pro Tips

- Use `Blazored.LocalStorage` for user state/session caching  
- Enable logging via `ILogger<T>` + Application Insights early  
- Keep layout reusable using `@layout MainLayout` in all pages  
- Implement lazy loading for heavy data-bound components  
- Apply mobile-first responsive CSS from the start (`min-width`, `max-width` patterns)

---

## 🚧 Section 2: Navigation, UI Layout & Component Architecture

### 🧱 Layout Overview

The layout follows a classic intranet pattern:

- Left Sidebar with role-specific links
- Top Navigation Bar (dropdowns)
- Responsive `MainLayout.razor`

```razor
<!-- MainLayout.razor -->
@inherits LayoutComponentBase

<div class="app-layout">
    <Sidebar />
    <div class="main-content">
        <HeaderNav />
        <div class="body-wrapper">
            @Body
        </div>
    </div>
</div>
```

> 🧠 **Pro Tip:** Use CSS Grid or Flexbox to make the layout responsive. Hide or collapse Sidebar for mobile.

---

### 📌 Sidebar (Dynamic by Role)

```razor
<!-- Sidebar.razor -->
@inject AuthenticationStateProvider AuthStateProvider

@if (UserRole == "Employee")
{
    <NavLink href="/resources">Resources</NavLink>
    <NavLink href="/payslips">Payslips</NavLink>
}
@if (UserRole == "Manager")
{
    <NavLink href="/approvals">Approvals</NavLink>
    <NavLink href="/team">Team Overview</NavLink>
}
```

> 🔄 Bind `UserRole` from claims or role service. Use a shared method to resolve roles.

---

### 🔽 Top Navigation Bar

```razor
<!-- HeaderNav.razor -->
<DropdownMenu title="Quick Links">
    <DropdownItem href="/support/it">IT Support</DropdownItem>
    <DropdownItem href="/support/hr">HR Support</DropdownItem>
</DropdownMenu>

<DropdownMenu title="Resources">
    <DropdownItem href="/docs">Company Docs</DropdownItem>
    <DropdownItem href="/news">Internal News</DropdownItem>
</DropdownMenu>
```

---

### 📁 UI Component Folder Structure

```plaintext
/Client
├── /Pages
│   ├── /HR
│   ├── /News
│   ├── /Support
├── /Shared
│   ├── HeaderNav.razor
│   ├── Sidebar.razor
│   ├── Footer.razor
```

> 🧠 **Best Practice:** Always use `@layout MainLayout` in page components to ensure structure and consistency.

---

## 📰 Section 3: News Center, Carousel & Dynamic Content Setup

### 🧩 Internal & External News Pages

Create two Razor components:

```plaintext
/Pages/News/InternalNews.razor
/Pages/News/ExternalNews.razor
```

Both pages should fetch and display news articles from the database, separated by category.

```razor
@foreach (var newsItem in InternalNews)
{
    <div class="news-card">
        <h4>@newsItem.Title</h4>
        <p>@newsItem.Summary</p>
        <NavLink href="@($"/news/{newsItem.Id}")">Read more</NavLink>
    </div>
}
```

> 🔄 Add pagination or "Load More" support using `@onclick` to improve UX.

---

### 🧾 SQL Schema: News Table

```sql
CREATE TABLE News (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(200),
    Summary NVARCHAR(400),
    Content NVARCHAR(MAX),
    Type NVARCHAR(50), -- Internal or External
    PublishedDate DATETIME,
    CreatedBy NVARCHAR(100)
);
```

> 💡 **Data Tip:** Index the `PublishedDate` column for faster sorting.

---

### 🎡 Hero Carousel Component

Show rotating promotional images at the top of the intranet homepage. Connect them to a `HeroImages` table.

#### Razor UI

```razor
<!-- HeroCarousel.razor -->
<div class="carousel-wrapper">
    @foreach (var image in Images)
    {
        <div class="carousel-slide">
            <img src="@image.Url" alt="@image.Caption" />
            <p>@image.Caption</p>
        </div>
    }
</div>
```

#### SQL Table

```sql
CREATE TABLE HeroImages (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Url NVARCHAR(300),
    Caption NVARCHAR(150),
    ActiveFlag BIT,
    DisplayOrder INT
);
```

> 🧠 **Pro Tip:** Limit results to top 5 images ordered by `DisplayOrder`. Enable dynamic activation toggle via admin panel.

---

### 🎂 Birthday Announcement (Dynamic Component)

```razor
@inject IEmployeeService EmployeeService

@code {
    private List<Employee> BirthdaysThisMonth;

    protected override async Task OnInitializedAsync()
    {
        BirthdaysThisMonth = await EmployeeService.GetThisMonthBirthdays();
    }
}

<ul>
    @foreach (var emp in BirthdaysThisMonth)
    {
        <li>@emp.Name - @emp.DateOfBirth.ToString("MMMM dd")</li>
    }
</ul>
```

#### SQL Query for Birthday Component

```sql
SELECT Name, DateOfBirth
FROM Employees
WHERE MONTH(DateOfBirth) = MONTH(GETDATE());
```

> 📅 **Performance Tip:** Cache birthday data for 24 hours using MemoryCache to reduce load on large datasets.

---

## 🧾 Section 4: HR Support Module – Payslips, Time-Off, Attendance

### 👥 Employee Self-Service Dashboard

This dashboard gives employees access to:
- Downloadable payslips
- View attendance history
- Request time-off

### 📄 Payslips Page

```razor
<!-- /Pages/HR/Payslips.razor -->
@inject IPayslipService PayslipService

@code {
    private List<Payslip> Payslips;

    protected override async Task OnInitializedAsync()
    {
        Payslips = await PayslipService.GetUserPayslips();
    }
}

@foreach (var payslip in Payslips)
{
    <div class="payslip-entry">
        <span>@payslip.Month</span>
        <a href="@payslip.FileUrl" download>Download</a>
    </div>
}
```

#### SQL Table: Payslips

```sql
CREATE TABLE Payslips (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    Month VARCHAR(10),
    FileUrl NVARCHAR(300),
    UploadedOn DATETIME
);
```

> 📁 Store payslip files in Azure Blob Storage and link via `FileUrl`.

---

### 🕒 Attendance Page

```razor
<!-- /Pages/HR/Attendance.razor -->
@inject IAttendanceService AttendanceService

@code {
    private List<AttendanceRecord> Records;

    protected override async Task OnInitializedAsync()
    {
        Records = await AttendanceService.GetCurrentUserAttendance();
    }
}

<table>
    <thead><tr><th>Date</th><th>Status</th></tr></thead>
    <tbody>
        @foreach (var record in Records)
        {
            <tr><td>@record.Date.ToShortDateString()</td><td>@record.Status</td></tr>
        }
    </tbody>
</table>
```

#### SQL Table: Attendance

```sql
CREATE TABLE Attendance (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    Date DATE,
    Status VARCHAR(10) -- Present, Absent, Leave
);
```

---

### 📆 Time-Off Request Page

```razor
<!-- /Pages/HR/TimeOffRequest.razor -->
@inject ITimeOffService TimeOffService

@code {
    private TimeOffRequestModel Model = new();

    private async Task SubmitRequest()
    {
        var result = await TimeOffService.SubmitRequest(Model);
        if (result) { /* Show success message */ }
    }
}

<EditForm Model="Model" OnValidSubmit="SubmitRequest">
    <InputDate @bind-Value="Model.FromDate" />
    <InputDate @bind-Value="Model.ToDate" />
    <InputTextArea @bind-Value="Model.Reason" />
    <button type="submit">Submit Request</button>
</EditForm>
```

#### SQL Table: TimeOffRequests

```sql
CREATE TABLE TimeOffRequests (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    FromDate DATE,
    ToDate DATE,
    Reason TEXT,
    Status VARCHAR(20), -- Pending, Approved, Rejected
    ApproverId INT,
    SubmittedOn DATETIME
);
```

---

> 🔐 **Access Tip:** Use role-based visibility. Managers see their team's requests; employees see their own only.

---

## 📊 Section 5: Managerial Dashboard – Approvals & Hierarchical Views

### 🎯 Objective
Enable managers to:
- View team attendance and time-off status
- Approve/reject leave requests
- Gain visibility into direct reports only

> 🧠 **Pro Tip:** Filter all views using `Employee.ManagerId = CurrentUser.Id` to enforce scope.

---

### 👁 View Team Requests Page

```razor
<!-- /Pages/Manager/TeamRequests.razor -->
@inject ITimeOffService TimeOffService
@code {
    private List<TimeOffRequest> TeamRequests;

    protected override async Task OnInitializedAsync()
    {
        TeamRequests = await TimeOffService.GetRequestsForManager();
    }
}

<table>
    <thead><tr><th>Employee</th><th>From</th><th>To</th><th>Status</th><th>Actions</th></tr></thead>
    <tbody>
        @foreach (var request in TeamRequests)
        {
            <tr>
                <td>@request.EmployeeName</td>
                <td>@request.FromDate.ToShortDateString()</td>
                <td>@request.ToDate.ToShortDateString()</td>
                <td>@request.Status</td>
                <td>
                    <button @onclick="() => ApproveRequest(request.Id)">Approve</button>
                    <button @onclick="() => RejectRequest(request.Id)">Reject</button>
                </td>
            </tr>
        }
    </tbody>
</table>
```

---

### ✅ Approval Logic Example

```csharp
public async Task<bool> ApproveRequest(int requestId)
{
    var request = await _context.TimeOffRequests.FindAsync(requestId);
    request.Status = "Approved";
    request.ApproverId = CurrentUser.Id;
    await _context.SaveChangesAsync();
    return true;
}
```

---

### 📄 Team Hierarchy View (Optional Module)

Display managers' direct reports with role filters.

```sql
SELECT Id, Name, Role, Department FROM Employees WHERE ManagerId = @ManagerId;
```

> 🔄 Consider using TreeView or collapsible panels for nested org structure.

---

### 🧾 SQL Schema Reminder: Employees Table

```sql
CREATE TABLE Employees (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Name NVARCHAR(150),
    Role NVARCHAR(100),
    Email NVARCHAR(150),
    DateOfBirth DATE,
    ManagerId INT
);
```

## 🔗 Section 6: IT & HR Quick Links Portal

### 🧩 Quick Access Sections

This section provides categorized quick-access cards for:
- IT Support
- HR Support

Each links to their respective contact/request modules.

---

### 📁 Folder Paths
```plaintext
/Pages/Support/ITSupport.razor
/Pages/Support/HRSupport.razor
```

---

### 🖥 IT Support Page

```razor
<!-- /Pages/Support/ITSupport.razor -->
<div class="support-box">
    <h3>Open IT Support Ticket</h3>
    <NavLink href="/support/it/open">New Request</NavLink>
    <NavLink href="/support/it/requests">View My Tickets</NavLink>
</div>
```

> 🛠 Use enums to classify request types: Hardware, Software, Email, Internet

---

### 🧾 HR Support Page

```razor
<!-- /Pages/Support/HRSupport.razor -->
<div class="support-box">
    <h3>HR Services</h3>
    <NavLink href="/support/hr/policies">HR Policies</NavLink>
    <NavLink href="/support/hr/contact">Contact HR</NavLink>
    <NavLink href="/support/hr/forms">Request Forms</NavLink>
</div>
```

---

### ✅ Sample Ticket Submission Model

```csharp
public class SupportTicket
{
    public int Id { get; set; }
    public string Category { get; set; } // e.g. IT, HR
    public string Title { get; set; }
    public string Description { get; set; }
    public string SubmittedBy { get; set; }
    public DateTime SubmittedOn { get; set; }
    public string Status { get; set; } // Pending, Resolved
}
```

---

### 📄 SQL Table: SupportTickets

```sql
CREATE TABLE SupportTickets (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Category NVARCHAR(50),
    Title NVARCHAR(150),
    Description NVARCHAR(MAX),
    SubmittedBy NVARCHAR(100),
    SubmittedOn DATETIME,
    Status NVARCHAR(50)
);
```

> 💡 **UX Tip:** Add status badges (Pending, In Progress, Closed) with color indicators to boost clarity.

---

## 🧠 Section 7: Database Structure Summary & Optimization Tips

### 🗃 Core Tables Overview

Below are the core tables required for the BEC Arabia Intranet Portal and their purposes:

| Table Name         | Purpose                                      |
|--------------------|----------------------------------------------|
| Employees          | Master employee records                      |
| Payslips           | Monthly payroll document links               |
| Attendance         | Daily attendance status                      |
| TimeOffRequests    | Time-off/leave request tracking              |
| News               | Internal and external news announcements     |
| HeroImages         | Homepage carousel images                     |
| SupportTickets     | IT and HR support ticketing system           |

---

### 📊 Detailed Schema Summary

#### Employees
```sql
CREATE TABLE Employees (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Name NVARCHAR(150),
    Role NVARCHAR(100),
    Email NVARCHAR(150),
    DateOfBirth DATE,
    ManagerId INT
);
```

#### Payslips
```sql
CREATE TABLE Payslips (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    Month VARCHAR(10),
    FileUrl NVARCHAR(300),
    UploadedOn DATETIME
);
```

#### Attendance
```sql
CREATE TABLE Attendance (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    Date DATE,
    Status VARCHAR(10) -- Present, Absent, Leave
);
```

#### TimeOffRequests
```sql
CREATE TABLE TimeOffRequests (
    Id INT PRIMARY KEY IDENTITY(1,1),
    EmployeeId INT,
    FromDate DATE,
    ToDate DATE,
    Reason TEXT,
    Status VARCHAR(20),
    ApproverId INT,
    SubmittedOn DATETIME
);
```

#### News
```sql
CREATE TABLE News (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(200),
    Summary NVARCHAR(400),
    Content NVARCHAR(MAX),
    Type NVARCHAR(50),
    PublishedDate DATETIME,
    CreatedBy NVARCHAR(100)
);
```

#### HeroImages
```sql
CREATE TABLE HeroImages (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Url NVARCHAR(300),
    Caption NVARCHAR(150),
    ActiveFlag BIT,
    DisplayOrder INT
);
```

#### SupportTickets
```sql
CREATE TABLE SupportTickets (
    Id INT PRIMARY KEY IDENTITY(1,1),
    Category NVARCHAR(50),
    Title NVARCHAR(150),
    Description NVARCHAR(MAX),
    SubmittedBy NVARCHAR(100),
    SubmittedOn DATETIME,
    Status NVARCHAR(50)
);
```

---

### 📌 Indexing Best Practices

- Add non-clustered indexes on foreign key columns like `EmployeeId`, `ManagerId`
- Add filtered index on `News.Type` for faster category lookup
- Use datetime indexes on `PublishedDate`, `SubmittedOn`, `UploadedOn`
- Add composite index on `(Status, Category)` in `SupportTickets` for quick filtering

```sql
CREATE INDEX IX_TimeOff_EmployeeId ON TimeOffRequests(EmployeeId);
CREATE INDEX IX_Attendance_EmpDate ON Attendance(EmployeeId, Date);
CREATE INDEX IX_Support_Status_Category ON SupportTickets(Status, Category);
```

---

### 🔐 Security & Integrity Constraints

- Use `FOREIGN KEY` constraints for `EmployeeId` fields in all related tables
- Use `CHECK` constraints for columns like `Status` and `Type` to prevent invalid values
- Enforce `NOT NULL` on critical fields like `Name`, `Date`, `Url`

---

### 🧠 Optimization Tips

- Enable `ROWVERSION` columns in heavy-write tables for concurrency checks
- Schedule weekly `UPDATE STATISTICS` and `INDEX REBUILD` jobs
- Apply table partitioning if any table exceeds 1M rows (News or Attendance)
- Use stored procedures for batch operations (monthly payslip generation)


---

## 🔒 Section 8: Authentication, Authorization & Role Management

### 🎯 Objective
Secure the portal using robust authentication and authorization practices, ensuring:
- Only valid users can access the system
- Permissions are enforced by user roles (Employee, Manager, Director)

---

### 🔐 Authentication Options

#### ✅ Option 1: Azure Active Directory (Preferred for Enterprises)
- Integrate with Microsoft Entra ID
- Use Single Sign-On (SSO) via MS365 credentials
- Map user roles from Azure AD Groups

```bash
# Azure CLI to register the app
az ad app create --display-name "BECIntranetPortal"
```

In `appsettings.json`:
```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "becarabia.com",
  "TenantId": "{tenant-guid}",
  "ClientId": "{app-guid}",
  "CallbackPath": "/signin-oidc"
}
```

In `Program.cs`:
```csharp
builder.Services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApp(builder.Configuration.GetSection("AzureAd"));
```

---

#### ✅ Option 2: ASP.NET Identity (Standalone Login)
- Ideal for small teams without enterprise identity
- Role and claim-based logic stored in SQL Server

```bash
# Scaffold Identity
 dotnet aspnet-codegenerator identity -dc ApplicationDbContext
```

Configure roles on startup:
```csharp
var roleManager = services.BuildServiceProvider().GetRequiredService<RoleManager<IdentityRole>>();
await roleManager.CreateAsync(new IdentityRole("Employee"));
await roleManager.CreateAsync(new IdentityRole("Manager"));
await roleManager.CreateAsync(new IdentityRole("Director"));
```

---

### 🧾 User & Role Schema (if using Identity)
```sql
AspNetUsers        -- Stores user credentials & profile
AspNetRoles        -- List of system roles
AspNetUserRoles    -- Many-to-many mapping
```

---

### 🔐 Policy-Based Authorization

Instead of only using `[Authorize(Roles="Manager")]`, define flexible policies:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("CanApproveTimeOff", policy =>
        policy.RequireRole("Manager", "Director"));
});
```

Usage:
```csharp
[Authorize(Policy = "CanApproveTimeOff")]
public IActionResult Approvals() { ... }
```

> 🧠 **Pro Tip:** Use policies to encapsulate logic like `OnlyDirectReportManager`, `HROnly`, `AdminOnly` instead of repeating role checks.

---

### 🔄 Claims Enrichment
Add custom claims like `Department`, `TeamId`, or `EmployeeId` for advanced filtering:
```csharp
claims.Add(new Claim("Department", user.Department));
claims.Add(new Claim("EmployeeId", user.Id.ToString()));
```

---

### 📋 UI Role Check in Blazor
```razor
@if (User.IsInRole("Director"))
{
    <NavLink href="/admin">Admin Panel</NavLink>
}
```
---

## 🚀 Section 9: DevOps, Deployment & Maintenance Strategy

### 🎯 Objective
Ensure a reliable and secure CI/CD pipeline, automated testing, and best practices for Azure-hosted deployment.

---

### 🧪 CI/CD Pipeline

#### Tools:
- **GitHub Actions** for automation
- **Azure App Service** for hosting
- **Azure SQL Database** for persistence

#### Sample GitHub Workflow (`.github/workflows/deploy.yml`):
```yaml
name: Deploy Intranet Portal

on:
  push:
    branches: [main]

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '8.0.x'
    - name: Build
      run: dotnet build --configuration Release
    - name: Publish
      run: dotnet publish -c Release -o ./publish
    - name: Deploy to Azure Web App
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'bec-intranet-app'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ./publish
```

> 🧠 **Dev Tip:** Use GitHub secrets to securely store Azure publish profiles, DB strings, and API keys.

---

### 🌐 Azure App Service Configuration

#### Key settings:
- Enable **Always On**
- Set deployment slot: `staging` and `production`
- Enable diagnostics logging to Azure Monitor
- Bind custom domain: `intranet.becarabia.com`

---

### 🛠 Database Deployment

- Use **EF Core Migrations**:
```bash
dotnet ef migrations add InitialCreate
dotnet ef database update
```

> 🧠 Maintain separate migration folders per schema module (HR, News, Support).

---

### 📅 Scheduled Maintenance

- **Daily Backups**: Enable Azure SQL long-term backup retention
- **Weekly Index Rebuild**: Automate with SQL Agent Job
- **Monthly Health Checks**: Run integration test suite

---

### 📊 Monitoring & Alerting

- **Application Insights**: Setup request traces, exception logs, page load performance
- **Azure Monitor Alerts**: CPU usage > 75%, error rate > 5%, DB connection failures

---

### 📦 Versioning & Hotfixes

- Tag major releases: `v1.0.0`, `v1.1.0`
- Patch urgent fixes with suffix: `v1.1.0-hotfix1`
- Maintain changelog in `/Docs/CHANGELOG.md`

---

## 🧪 Section 10: Testing Strategy & QA Automation

### 🎯 Objective
Ensure software quality through continuous and automated testing, validating each feature through unit, integration, and end-to-end test coverage.

---

### 🧱 Test Types

| Type           | Tools/Frameworks            | Description                                      |
|----------------|-----------------------------|--------------------------------------------------|
| Unit Tests     | xUnit, Moq                  | Test individual methods/services in isolation    |
| Integration    | xUnit, EF Core In-Memory DB | Test services with DB/DI interaction             |
| UI Component   | bUnit                        | Test Razor components and UI state logic         |
| End-to-End     | Playwright, Selenium        | Simulate user interaction, full system validation|

---

### 🧪 Unit Test Example (xUnit)
```csharp
public class PayslipServiceTests
{
    [Fact]
    public async Task GetUserPayslips_ReturnsCorrectResults()
    {
        var result = await _service.GetUserPayslips("ahmed@becarabia.com");
        Assert.NotNull(result);
        Assert.All(result, r => Assert.Contains(".pdf", r.FileUrl));
    }
}
```

> 🧠 **Best Practice:** Mock dependencies using `Moq` or `FakeItEasy` to isolate the unit logic.

---

### 🔌 Integration Test Example
```csharp
public class TimeOffRequestTests : IClassFixture<CustomWebApplicationFactory>
{
    private readonly HttpClient _client;
    public TimeOffRequestTests(CustomWebApplicationFactory factory) => _client = factory.CreateClient();

    [Fact]
    public async Task SubmitTimeOffRequest_Returns200()
    {
        var request = new { fromDate = "2024-12-01", toDate = "2024-12-05", reason = "Vacation" };
        var response = await _client.PostAsJsonAsync("/api/timeoff", request);
        Assert.Equal(HttpStatusCode.OK, response.StatusCode);
    }
}
```

---

### 🖼 Component Test (bUnit)
```csharp
[Fact]
public void Carousel_ShouldRenderImages()
{
    var images = new List<HeroImage> {
        new() { Url = "/img1.jpg", Caption = "Test 1" },
        new() { Url = "/img2.jpg", Caption = "Test 2" }
    };
    var cut = RenderComponent<HeroCarousel>(parameters => parameters.Add(p => p.Images, images));
    cut.MarkupMatches("<div class='carousel-slide'>...</div>");
}
```

---

### 🧭 End-to-End (E2E) Automation

- **Tool:** Playwright (preferred) or Selenium WebDriver
- **Scenarios:** Login, navigation, form validation, time-off submission
- Use CI integration to run nightly or post-deployment

```bash
npx playwright test --project=chromium
```

---

### 📊 Test Coverage & Reporting

- Generate coverage with `coverlet`
```bash
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
```
- View results in `ReportGenerator`, SonarQube, or GitHub Code Scanning tab

---

### ✅ Test Success Criteria
- 90%+ coverage for business logic services
- All page components pass rendering/unit tests
- E2E automation covers happy path + edge cases
- No test fails in `main` branch CI pipeline


---

## 🧾 Section 11: Admin Panel & Content Management Features

### 🎯 Objective
Enable power users and directors to manage core site content (news, hero images, featured workshops) through a restricted and authenticated admin interface.

---

### 🧱 Key Modules

| Module             | Purpose                                 |
|--------------------|------------------------------------------|
| News Manager       | Create/edit/publish news articles        |
| Hero Carousel Admin| Manage rotating homepage images          |
| Workshop Manager   | Feature and update training sessions     |
| Feedback Viewer    | View and respond to employee feedback    |

> 🧠 **Role Enforcement:** Admin routes should be `[Authorize(Roles = "Director")]` only or protected with a custom `IsAdmin` policy.

---

### 🧾 Admin Navigation
```razor
@if (User.IsInRole("Director"))
{
    <NavLink href="/admin/news">Manage News</NavLink>
    <NavLink href="/admin/hero">Manage Hero Images</NavLink>
    <NavLink href="/admin/workshops">Workshops</NavLink>
    <NavLink href="/admin/feedback">Feedback Panel</NavLink>
}
```

---

### 📰 News Management Interface
```razor
<!-- AdminNews.razor -->
@foreach (var item in NewsItems)
{
    <div class="news-admin">
        <h4>@item.Title</h4>
        <button @onclick="() => EditNews(item.Id)">Edit</button>
        <button @onclick="() => DeleteNews(item.Id)">Delete</button>
    </div>
}
```

> 🔁 Use modal popups for inline editing to reduce page transitions.

---

### 🎡 Hero Image Manager
```razor
<!-- AdminHeroCarousel.razor -->
@foreach (var img in CarouselImages)
{
    <img src="@img.Url" alt="@img.Caption" width="150" />
    <InputText @bind-Value="img.Caption" />
    <InputCheckbox @bind-Value="img.ActiveFlag" />
    <button @onclick="() => SaveImage(img)">Save</button>
}
```

---

### 🧑‍🏫 Featured Workshop Manager
```razor
<!-- AdminWorkshops.razor -->
@foreach (var w in Workshops)
{
    <InputText @bind-Value="w.Title" />
    <InputTextArea @bind-Value="w.Description" />
    <button @onclick="() => SaveWorkshop(w)">Update</button>
}
```

---

### 💬 Feedback Viewer (Read-only for now)
```razor
<!-- AdminFeedback.razor -->
@foreach (var entry in FeedbackList)
{
    <div class="feedback-box">
        <p><strong>@entry.Name</strong> said: @entry.Message</p>
        <p><em>@entry.SubmittedOn.ToString("f")</em></p>
    </div>
}
```

> 🧠 Future Enhancement: Add reply interface or internal notes panel per feedback entry.

---

## ✅ Section 12: Project Wrap-Up, Recommendations & Future Enhancements

### 🧩 Final Checklist Before Go-Live

| Task                                | Status        |
|-------------------------------------|----------------|
| Full QA sign-off                    | ✅ Completed   |
| Security audit (roles, data access) | ✅ Reviewed    |
| Admin users provisioned             | ✅ Configured  |
| Content uploaded (News, Hero, HR)   | ✅ Populated   |
| Azure Monitoring and Alerts         | ✅ Enabled     |

---

### 📌 Recommendations for Admin Team

- Schedule **monthly content reviews** for news and workshops
- Ensure one designated HR + IT contact per department for support workflows
- Encourage managers to validate employee roles quarterly
- Use the **Feedback module** to improve intranet usefulness
- Organize quarterly intranet usage training sessions

---

### 🧭 Suggested Future Enhancements

| Feature                             | Rationale                                       |
|-------------------------------------|--------------------------------------------------|
| Live chat for HR & IT               | Faster query handling, improve support workflow |
| AI-powered document search          | Speed up employee self-service access           |
| Mobile-responsive intranet version  | Expand accessibility on mobile/tablets          |
| Department dashboards (HR, IT, BD)  | Role-specific operational insights              |
| Integrated announcements ticker     | Boost engagement for urgent updates             |
| Gamification for feedback/rewards   | Improve interaction & user retention            |

---

### 📘 Final Note to Developers

This document is your full blueprint. Every component, layout, role, API, and workflow has been designed to support a professional-grade internal portal tailored for BEC Arabia’s real-world operations.

> 🧠 **Dev Tip:** Keep the documentation (`/Docs`) updated as you iterate. Well-documented systems = scalable and maintainable codebases.

🚀 Thank you for your contribution to building a world-class intranet!


---

## 📋 Developer Do's and Don'ts

### ✅ Do's

| ✅ Do This                                                | Why It Matters                                                  |
|----------------------------------------------------------|------------------------------------------------------------------|
| Use role-based and policy-based access control           | Enhances security and modular permission logic                  |
| Keep UI responsive using Flex/Grid                       | Ensures usability across devices and screen sizes               |
| Write unit/integration tests for each feature            | Prevents regressions and simplifies debugging                   |
| Maintain consistent naming conventions (PascalCase)      | Improves code readability and team collaboration                |
| Commit to `main` only after PR review and CI pass        | Reduces bugs and enforces accountability                        |
| Use centralized exception logging (e.g. Application Insights) | Helps diagnose and track errors in production               |

---

### ❌ Don'ts

| ❌ Don’t Do This                                          | Reason                                                           |
|----------------------------------------------------------|------------------------------------------------------------------|
| Hard-code secrets or connection strings in source files  | Violates security best practices and risks data breaches         |
| Use `[Authorize(Roles="Employee,Manager")]` everywhere  | Makes scaling role logic difficult, use policies instead         |
| Rely solely on client-side validation                    | Leaves app vulnerable to malicious submissions                   |
| Push directly to production branch                       | Risk of instability and untested features being deployed         |
| Store large blobs in SQL tables                          | Degrades performance; use Azure Blob Storage or similar          |
| Overuse nested layouts or deep component nesting         | Reduces maintainability and performance                         |

---

## 🧠 Tips & Tricks for Smooth Execution

| 💡 Tip                                                  | Description                                                                 |
|----------------------------------------------------------|-----------------------------------------------------------------------------|
| Use `@layout` in every page component                   | Maintains consistent page structure throughout the application              |
| Parameterize services (e.g. with DI)                    | Promotes testability and cleaner abstractions                              |
| Add caching to infrequent queries (e.g. birthday list)  | Boosts performance and lowers DB load                                     |
| Use enums for request types and statuses                | Simplifies code, avoids typo errors, and improves consistency              |
| Group `.razor` pages by feature                         | Keeps the codebase modular and organized for future expansion              |
| Schedule regular backups and index rebuilds             | Preserves data and ensures long-term performance of the SQL database       |

---

✅ **Do's, Don'ts, and Tips Section Complete.**






