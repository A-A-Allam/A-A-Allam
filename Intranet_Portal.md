BEC Arabia Intranet Portal – Full Execution & Documentation Plan

Classification: BEC Arabia - External - Unrestricted Use

This document is intended to serve as the complete execution and developer-ready implementation guide for the new Intranet Portal at BEC Arabia. It includes:

UI/UX architecture

Database schema (SQL)

Component-level code snippets (Blazor/C#)

Best practices and tips

DevOps & deployment notes

Section 1: Project Overview ✅

🎯 Objective

Redesign and enhance BEC Arabia’s intranet to improve internal communication, HR functions, employee engagement, and content accessibility—all in a single, intuitive portal.

📌 Goals

Centralize HR, IT support, and employee resources

Integrate real-time functionality (attendance, time-off, news)

Role-based experiences for Employees, Managers, and Directors

⚙️ Tech Stack

Layer

Technology

Frontend

Blazor Server

Backend

.NET 8 + EF Core

Database

Azure SQL

Auth

Azure AD / Identity

Hosting

Azure App Service

Deployment

GitHub + Azure CI/CD

📁 Repo Structure (Suggested)

/IntranetPortal
├── /Client        # Blazor UI
├── /Server        # .NET API services
├── /Shared        # Models & shared logic
├── /Data          # EF Core migrations, DB context
├── /Docs          # Markdown technical docs
├── /Tests         # Unit & integration tests

✅ MVP Modules

News Center (internal/external)

Birthday announcements (auto-refresh)

HR Support Portal (payslips, time-off)

Dynamic Hero Carousel

Managerial dashboard for approvals

Quick Links (IT/HR)

🚀 Roadmap

Phase

Focus

1

UI/UX Enhancements

2

HR & Support Integration

3

Employee Hierarchy + Workflow

4

DevOps, Tests, Launch

🔒 Role-Based Access Matrix

Role

Permissions

Employee

View content, request time-off

Manager

Approve requests, view team reports

Director

Full access, analytics, content management

✅ Pro Tips

Use Blazored.LocalStorage for smooth session management

Structure your .razor files into folders per module (/Pages/HR, /Pages/News, etc.)

Enable logging from the start using ILogger<T> and Application Insights

Use Policy-based authorization for multi-role logic in controllers

Would you like me to proceed with Section 2: Navigation, UI Layout & Component Architecture?

