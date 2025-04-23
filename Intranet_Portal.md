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

✅ **Section 1 Complete.**  
Would you like me to now write **Section 2: Navigation, UI Layout & Component Architecture**?

