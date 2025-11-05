# Technical Design Document

## Document Information

### Document Revision History

| Version | Date       | Author        | Description                                                                 |
|--------|------------|---------------|-----------------------------------------------------------------------------|
| 1.1    | 2025-10-30 | dhc.qiangshi  | Completed system optimization adjustments, unified data formats, improved business logic, refactored authentication mechanism, and established a complete testing strategy. |
| 1.0    | 2025-08-19 | dhc.qiangshi  | Completed basic system architecture setup, including core modules such as user registration/login, account management, inquiries, project and file management. |

### Document Review History

| Version | Date       | Reviewer | Comments |
|--------|------------|----------|----------|
| 1.0    | 2025-07-28 |          |          |

## Table of Contents

- [Technical Design Document](#Technical-Design-Document)
  - [Document Information](#Document-Information)
    - [Document Revision History](#Document-Revision-History)
    - [Document Review History](#Document-Review-History)
  - [Table of Contents](#Table-of-Contents)
  - [1. Project Background and Overview](#1-Project-Background-and-Overview)
    - [1.1 Project Requirement Background](#11-Project-Requirement-Background)
    - [1.2 Terms and Definitions](#12-Terms-and-Definitions)
    - [1.3 Constraints](#13-Constraints)
      - [1.3.1 User Status Management Constraints](#131-User-Status-Management-Constraints)
        - [Portal Side](#Portal-Side)
        - [View Side](#View-Side)
    - [1.4 System Architecture](#14-System-Architecture)
      - [1.4.1 Portal-side Architecture Sequence Diagram](#141-Portal-side-Architecture-Sequence-Diagram)
      - [1.4.2 WeChat Authorization & Registration Flow](#142-WeChat-Authorization-&-Registration-Flow)
      - [1.4.3 View-side Business Sequence Diagram](#143-View-side-Business-Sequence-Diagram)
  - [2. Portal Public Module Design and Functions](#2-Portal-Public-Module-Design-and-Functions)
    - [2.1 WeChat Authorization Component Design](#21-WeChat-Authorization-Component-Design)
      - [2.1.1 WechatAuthService](#211-WechatAuthService)
      - [2.1.2 WechatMessageService](#212-WechatMessageService)
      - [2.1.3 Related Tables](#213-Related-Tables)
    - [2.2 Authentication & Authorization Component Design](#22-Authentication-&-Authorization-Component-Design)
      - [2.2.1 Component Relationship Description](#221-Component-Relationship-Description)
      - [2.2.2 AuthMiddleware](#222-AuthMiddleware)
      - [2.2.3 PermissionMiddleware](#223-PermissionMiddleware)
      - [2.2.4 UserPermissionService](#224-UserPermissionService)
      - [2.2.5 Class Relationship Diagram](#225-Class-Relationship-Diagram)
    - [2.3 Common Business Component Design](#23-Common-Business-Component-Design)
      - [2.3.1 CompanyService](#231-CompanyService)
      - [2.3.2 SmsVerificationService](#232-SmsVerificationService)
      - [2.3.3 Related Tables](#233-Related-Tables)
    - [2.4 System Integration Component Design](#24-System-Integration-Component-Design)
      - [2.4.1 InterSystemService](#241-InterSystemService)
    - [2.5 Laravel Built-in Feature Implementations](#25-Laravel-Built-in-Feature-Implementations)
      - [2.5.1 Rate Limiting](#251-Rate-Limiting)
      - [2.5.2 Logging](#252-Logging)
      - [2.5.3 System Configuration](#253-System-Configuration)
      - [2.5.4 Internationalization](#254-Internationalization)
    - [2.6 Exception Handling Component Design](#26-Exception-Handling-Component-Design)
      - [2.6.1 Unified Exception Handling](#261-Unified-Exception-Handling)
      - [2.6.2 Frontend Error Handling](#262-Frontend-Error-Handling)
    - [2.7 Summary of Public Modules](#27-Summary-of-Public-Modules)
      - [2.7.1 Component Dependency Diagram](#271-Component-Dependency-Diagram)
      - [2.7.2 Core Module List](#272-Core-Module-List)
  - [3. Portal Business Process Design](#3-Portal-Business-Process-Design)
    - [3.1 Registration Business Module](#31-Registration-Business-Module)
      - [3.1.1 Business Flow](#311-Business-Flow)
      - [3.1.2 Affected Tables](#312-Affected-Tables)
      - [3.1.3 Frontend Implementation](#313-Frontend-Implementation)
        - [3.1.3.1 View Directory Structure](#3131-View-Directory-Structure)
        - [3.1.3.2 Key Component Functions](#3132-Key-Component-Functions)
        - [3.1.3.3 API Design](#3133-API-Design)
      - [3.1.4 Feature Development and Implementation](#314-Feature-Development-and-Implementation)
        - [3.1.4.1 Class Relationship Diagram](#3141-Class-Relationship-Diagram)
        - [3.1.4.2 Code Implementation](#3142-Code-Implementation)
    - [3.2 Login Business Module](#32-Login-Business-Module)
      - [3.2.1 Business Flow](#321-Business-Flow)
        - [3.2.1.1 Business Background](#3211-Business-Background)
      - [3.2.2 Affected Tables](#322-Affected-Tables)
      - [3.2.3 Frontend Implementation](#323-Frontend-Implementation)
        - [3.2.3.1 View Directory Structure](#3231-View-Directory-Structure)
        - [3.2.3.2 Key Component Functions](#3232-Key-Component-Functions)
        - [3.2.3.3 API Design](#3233-API-Design)
      - [3.2.4 Feature Development and Implementation](#324-Feature-Development-and-Implementation)
        - [3.2.4.1 Class Relationship Diagram](#3241-Class-Relationship-Diagram)
        - [3.2.4.2 Code Implementation](#3242-Code-Implementation)
    - [3.3 MyInfo Module](#33-MyInfo-Module)
      - [3.3.1 Business Flow](#331-Business-Flow)
        - [3.3.1.1 Business Background](#3311-Business-Background)
      - [3.3.2 Affected Tables](#332-Affected-Tables)
      - [3.3.3 Frontend Implementation](#333-Frontend-Implementation)
        - [3.3.3.1 View Directory Structure](#3331-View-Directory-Structure)
        - [3.3.3.2 Key Component Functions](#3332-Key-Component-Functions)
        - [3.3.3.3 API Design](#3333-API-Design)
      - [3.3.4 Feature Development and Implementation](#334-Feature-Development-and-Implementation)
        - [3.3.4.1 Class Relationship Diagram](#3341-Class-Relationship-Diagram)
        - [3.3.4.2 Code Implementation](#3342-Code-Implementation)
    - [3.4 Inquiry Module](#34-Inquiry-Module)
      - [3.4.1 Business Flow](#341-Business-Flow)
        - [3.4.1.1 Business Background](#3411-Business-Background)
      - [3.4.2 Affected Tables](#342-Affected-Tables)
      - [3.4.3 Frontend Implementation](#343-Frontend-Implementation)
        - [3.4.3.1 View Directory Structure](#3431-View-Directory-Structure)
        - [3.4.3.2 Key Component Functions](#3432-Key-Component-Functions)
        - [3.4.3.3 API Design](#3433-API-Design)
      - [3.4.4 Feature Development and Implementation](#344-Feature-Development-and-Implementation)
        - [3.4.4.1 Class Relationship Diagram](#3441-Class-Relationship-Diagram)
        - [3.4.4.2 Code Implementation](#3442-Code-Implementation)
    - [3.5 Project Module](#35-Project-Module)
      - [3.5.1 Business Flow](#351-Business-Flow)
        - [3.5.1.1 Business Background](#3511-Business-Background)
      - [3.5.2 Affected Tables](#352-Affected-Tables)
      - [3.5.3 Frontend Implementation](#353-Frontend-Implementation)
        - [3.5.3.1 View Directory Structure](#3531-View-Directory-Structure)
        - [3.5.3.2 Key Component Functions](#3532-Key-Component-Functions)
        - [3.5.3.3 API Design](#3533-API-Design)
      - [3.5.4 Feature Development and Implementation](#354-Feature-Development-and-Implementation)
        - [3.5.4.1 Class Relationship Diagram](#3541-Class-Relationship-Diagram)
        - [3.5.4.2 Code Implementation](#3542-Code-Implementation)
    - [3.6 TKE Library Module](#36-TKE-Library-Module)
      - [3.6.1 Business Flow](#361-Business-Flow)
        - [3.6.1.1 Business Background](#3611-Business-Background)
      - [3.6.2 Affected Tables](#362-Affected-Tables)
      - [3.6.3 Frontend Implementation](#363-Frontend-Implementation)
        - [3.6.3.1 View Directory Structure](#3631-View-Directory-Structure)
        - [3.6.3.2 Key Component Functions](#3632-Key-Component-Functions)
        - [3.6.3.3 TKE Library API Design](#3633-TKE-Library-API-Design)
      - [3.6.4 Feature Development and Implementation](#364-Feature-Development-and-Implementation)
        - [3.6.4.1 Class Relationship Diagram](#3641-Class-Relationship-Diagram)
        - [3.6.4.2 Code Implementation](#3642-Code-Implementation)
    - [3.7 AGR Homepage](#37-AGR-Homepage)
      - [3.7.1 Business Flow](#371-Business-Flow)
        - [3.7.1.1 Business Background](#3711-Business-Background)
      - [3.7.2 Table Design](#372-Table-Design)
      - [3.7.3 Feature Development and Implementation](#373-Feature-Development-and-Implementation)
        - [3.7.3.1 Class Relationship Diagram](#3731-Class-Relationship-Diagram)
        - [3.7.3.2 API Design](#3732-API-Design)
        - [3.7.3.3 Code Implementation](#3733-Code-Implementation)
  - [4. Portal Testing Strategy](#4-Portal-Testing-Strategy)
    - [4.1 PHPUnit Unit Test Design](#41-PHPUnit-Unit-Test-Design)
      - [4.1.1 Service Layer Test Design](#411-Service-Layer-Test-Design)
        - [4.1.1.1 Registration Module Service Tests](#4111-Registration-Module-Service-Tests)
        - [4.1.1.2 Login Module Service Tests](#4112-Login-Module-Service-Tests)
        - [4.1.1.3 MyInfo Module Service Tests](#4113-MyInfo-Module-Service-Tests)
        - [4.1.1.4 Inquiry Module Service Tests](#4114-Inquiry-Module-Service-Tests)
        - [4.1.1.5 Project Module Service Tests](#4115-Project-Module-Service-Tests)
        - [4.1.1.6 TKE Library Module Service Tests](#4116-TKE-Library-Module-Service-Tests)
        - [4.1.1.7 AGR Homepage Module Service Tests](#4117-AGR-Homepage-Module-Service-Tests)
    - [4.2 BDD Test Framework (Cucumber)](#42-BDD-Test-Framework-Cucumber)
      - [4.2 BDD Test Framework (Cucumber)](#42-BDD-Test-Framework-Cucumber)
        - [4.2.1.1 Full Registration Flow](#4211-Full-Registration-Flow)
        - [4.2.1.2 Login and Company Switch](#4212-Login-and-Company-Switch)
        - [4.2.1.3 MyInfo Information Maintenance](#4213-MyInfo-Information-Maintenance)
        - [4.2.1.4 Inquiry Process](#4214-Inquiry-Process)
        - [4.2.1.5 Project Management](#4215-Project-Management)
        - [4.2.1.6 TKE Library File Access](#4216-TKE-Library-File-Access)
        - [4.2.1.7 AGR Redirect Flow](#4217-AGR-Redirect-Flow)
  - [5. View Public Component Design](#5-View-Public-Component-Design)
    - [5.1 Data Receiving Routing Component](#51-Data-Receiving-Routing-Component)
      - [5.1.1 ModChannelDataReceiver](#511-ModChannelDataReceiver)
      - [5.1.2 Data Distribution Routing Logic](#512-Data-Distribution-Routing-Logic)
    - [5.2 Business Data Handling Services (Handler)](#52-Business-Data-Handling-Services-Handler)
      - [5.2.1 UserRegistrationHandler](#521-UserRegistrationHandler)
      - [5.2.2 InquiryHandler](#522-InquiryHandler)
      - [5.2.3 ProjectHandler](#523-ProjectHandler)
      - [5.2.4 UserDataPullHandler](#524-UserDataPullHandler)
    - [5.3 Data Push Component](#53-Data-Push-Component)
      - [5.3.1 ModChannelDataSender](#531-ModChannelDataSender)
    - [5.4 Notification Service Component](#54-Notification-Service-Component)
      - [5.4.1 ModChannelNotificationService](#541-ModChannelNotificationService)
    - [5.5 ModChannel Integration Configuration Management](#55-ModChannel-Integration-Configuration-Management)
      - [5.5.1 Rate Limiting](#551-Rate-Limiting)
      - [5.5.2 System Configuration](#552-System-Configuration)
  - [6. CNView Business Process Design](#6-CNView-Business-Process-Design)
    - [6.1 Channel Partner Upload Module](#61-Channel-Partner-Upload-Module)
      - [6.1.1 Business Flow](#611-Business-Flow)
        - [6.1.1.1 Business Background](#6111-Business-Background)
      - [6.1.2 Affected Tables](#612-Affected-Tables)
      - [6.1.3 Frontend Implementation](#613-Frontend-Implementation)
        - [6.1.3.1 View Directory Structure](#6131-View-Directory-Structure)
        - [6.1.3.2 Key View Functions](#6132-Key-View-Functions)
        - [6.1.3.3 Routing Design](#6133-Routing-Design)
        - [6.1.3.4 Logging](#6134-Logging)
      - [6.1.5 Feature Development and Implementation](#615-Feature-Development-and-Implementation)
        - [6.1.5.1 Class Relationship Diagram](#6151-Class-Relationship-Diagram)
        - [6.1.5.2 Code Implementation](#6152-Code-Implementation)
    - [6.2 Channel Partner Dashboard Module](#62-Channel-Partner-Dashboard-Module)
      - [6.2.1 Business Flow](#621-Business-Flow)
        - [6.2.1.1 Business Background](#6211-Business-Background)
      - [6.2.2 Affected Tables](#622-Affected-Tables)
      - [6.2.3 Frontend Implementation](#623-Frontend-Implementation)
        - [6.2.3.1 View Directory Structure](#6231-View-Directory-Structure)
        - [6.2.3.2 Key View Functions](#6232-Key-View-Functions)
      - [6.2.4 Routing Design](#624-Routing-Design)
      - [6.2.5 Error Handling and Logging](#625-Error-Handling-and-Logging)
        - [6.2.5.1 Logging](#6251-Logging)
      - [6.2.6 Feature Development and Implementation](#626-Feature-Development-and-Implementation)
        - [6.2.6.1 Class Relationship Diagram](#6261-Class-Relationship-Diagram)
        - [6.2.6.2 Code Implementation](#6262-Code-Implementation)
    - [6.3 Channel Partner Approval Module](#63-Channel-Partner-Approval-Module)
      - [6.3.1 Business Flow](#631-Business-Flow)
        - [6.3.1.1 Business Background](#6311-Business-Background)
      - [6.3.2 Affected Tables](#632-Affected-Tables)
      - [6.3.3 Frontend Implementation](#633-Frontend-Implementation)
        - [6.3.3.1 View Directory Structure](#6331-View-Directory-Structure)
        - [6.3.3.2 Key View Functions](#6332-Key-View-Functions)
      - [6.3.4 Routing Design](#634-Routing-Design)
      - [6.3.5 Error Handling and Logging](#635-Error-Handling-and-Logging)
        - [6.3.5.1 Logging](#6351-Logging)
      - [6.3.6 Feature Development and Implementation](#636-Feature-Development-and-Implementation)
        - [6.3.6.1 Class Relationship Diagram](#6361-Class-Relationship-Diagram)
        - [6.3.6.2 Controller Implementation](#6362-Controller-Implementation)
    - [6.4 Channel Partner Status Summary Module](#64-Channel-Partner-Status-Summary-Module)
      - [6.4.1 Business Flow](#641-Business-Flow)
        - [6.4.1.1 Business Background](#6411-Business-Background)
      - [6.4.2 Affected Tables](#642-Affected-Tables)
      - [6.4.3 Frontend Implementation](#643-Frontend-Implementation)
        - [6.4.3.1 View Directory Structure](#6431-View-Directory-Structure)
        - [6.4.3.2 Key Component Functions](#6432-Key-Component-Functions)
        - [6.4.3.3 Routing Design](#6433-Routing-Design)
      - [6.4.4 Error Handling and Logging](#644-Error-Handling-and-Logging)
        - [6.4.4.1 Logging](#6441-Logging)
      - [6.4.5 Feature Development and Implementation](#645-Feature-Development-and-Implementation)
        - [6.4.5.1 Class Relationship Diagram](#6451-Class-Relationship-Diagram)
        - [6.4.5.2 Code Implementation](#6452-Code-Implementation)
    - [6.5 Pre-Lead Project Upload, Creation and Updates Module](#65-Pre-Lead-Project-Upload-Creation-and-Updates-Module)
      - [6.5.1 Business Flow](#651-Business-Flow)
        - [6.5.1.1 Business Background](#6511-Business-Background)
      - [6.5.2 Affected Tables](#652-Affected-Tables)
      - [6.5.3 Frontend Implementation](#653-Frontend-Implementation)
        - [6.5.3.1 View Directory Structure](#6531-View-Directory-Structure)
        - [6.5.3.2 Key Component Functions](#6532-Key-Component-Functions)
        - [6.5.3.3 Routing Design](#6533-Routing-Design)
      - [6.5.4 Error Handling and Logging](#654-Error-Handling-and-Logging)
        - [6.5.4.1 Logging](#6541-Logging)
      - [6.5.5 Feature Development and Implementation](#655-Feature-Development-and-Implementation)
        - [6.5.5.1 Class Relationship Diagram](#6551-Class-Relationship-Diagram)
        - [6.5.5.2 Code Implementation](#6552-Code-Implementation)
    - [6.6 Channel Partner Project Summary Module](#66-Channel-Partner-Project-Summary-Module)
      - [6.6.1 Business Flow](#661-Business-Flow)
        - [6.6.1.1 Business Background](#6611-Business-Background)
      - [6.6.2 Affected Tables](#662-Affected-Tables)
      - [6.6.3 Frontend Implementation](#663-Frontend-Implementation)
        - [6.6.3.1 View Directory Structure](#6631-View-Directory-Structure)
        - [6.6.3.2 Key Component Functions](#6632-Key-Component-Functions)
        - [6.6.3.3 Routing Design](#6633-Routing-Design)
      - [6.6.4 Error Handling and Logging](#664-Error-Handling-and-Logging)
        - [6.6.4.1 Logging](#6641-Logging)
      - [6.6.5 Feature Development and Implementation](#665-Feature-Development-and-Implementation)
        - [6.6.5.1 Class Relationship Diagram](#6651-Class-Relationship-Diagram)
        - [6.6.5.2 Code Implementation](#6652-Code-Implementation)
    - [6.7 Channel Partner Project Report Module](#67-Channel-Partner-Project-Report-Module)
      - [6.7.1 Business Flow](#671-Business-Flow)
        - [6.7.1.1 Business Background](#6711-Business-Background)
      - [6.7.2 Affected Tables](#672-Affected-Tables)
      - [6.7.3 Frontend Implementation](#673-Frontend-Implementation)
        - [6.7.3.1 View Directory Structure](#6731-View-Directory-Structure)
        - [6.7.3.2 Key Component Functions](#6732-Key-Component-Functions)
        - [6.7.3.3 Routing Design](#6733-Routing-Design)
      - [6.7.4 Error Handling and Logging](#674-Error-Handling-and-Logging)
        - [6.7.4.1 Logging](#6741-Logging)
      - [6.7.5 Feature Development and Implementation](#675-Feature-Development-and-Implementation)
        - [6.7.5.1 Class Relationship Diagram](#6751-Class-Relationship-Diagram)
        - [6.7.5.2 Code Implementation](#6752-Code-Implementation)
    - [6.8 Inquiry Module](#68-Inquiry-Module)
      - [6.8.1 Business Flow](#681-Business-Flow)
        - [6.8.1.1 Business Background](#6811-Business-Background)
      - [6.8.2 Affected Tables](#682-Affected-Tables)
      - [6.8.3 Frontend Implementation](#683-Frontend-Implementation)
        - [6.8.3.1 View Directory Structure](#6831-View-Directory-Structure)
        - [6.8.3.2 Key Component Functions](#6832-Key-Component-Functions)
        - [6.8.3.3 Routing Design](#6833-Routing-Design)
      - [6.8.4 Error Handling and Logging](#684-Error-Handling-and-Logging)
        - [6.8.4.1 Logging](#6841-Logging)
      - [6.8.5 Feature Development and Implementation](#685-Feature-Development-and-Implementation)
        - [6.8.5.1 Class Relationship Diagram](#6851-Class-Relationship-Diagram)
        - [6.8.5.2 Code Implementation](#6852-Code-Implementation)
    - [6.9 TKE Library Module](#69-TKE-Library-Module)
      - [6.9.1 Business Flow](#691-Business-Flow)
        - [6.9.1.1 Business Background](#6911-Business-Background)
      - [6.9.2 Affected Tables](#692-Affected-Tables)
      - [6.9.3 Frontend Implementation](#693-Frontend-Implementation)
        - [6.9.3.1 View Directory Structure](#6931-View-Directory-Structure)
        - [6.9.3.2 Key Component Functions](#6932-Key-Component-Functions)
        - [6.9.3.3 Routing Design](#6933-Routing-Design)
      - [6.9.4 Error Handling and Logging](#694-Error-Handling-and-Logging)
        - [6.9.4.1 Logging](#6941-Logging)
      - [6.9.5 Feature Development and Implementation](#695-Feature-Development-and-Implementation)
        - [6.9.5.1 Class Relationship Diagram](#6951-Class-Relationship-Diagram)
        - [6.9.5.2 Code Implementation](#6952-Code-Implementation)
  - [Chapter 7: View-side Testing Strategy](#Chapter-7-View-side-Testing-Strategy)
    - [7.1 Unit Tests (PHPUnit)](#71-Unit-Tests-PHPUnit)
      - [7.1.1 Service Layer Test Design](#711-Service-Layer-Test-Design)
        - [7.1.1.1 Channel Partner Upload Module Service Tests](#7111-Channel-Partner-Upload-Module-Service-Tests)
        - [7.1.1.2 Channel Partner Dashboard Module Service Tests](#7112-Channel-Partner-Dashboard-Module-Service-Tests)
        - [7.1.1.3 Channel Partner Approval Module Service Tests](#7113-Channel-Partner-Approval-Module-Service-Tests)
        - [7.1.1.4 Channel Partner Status Summary Module Service Tests](#7114-Channel-Partner-Status-Summary-Module-Service-Tests)
        - [7.1.1.5 Pre-Lead Project Upload/Creation Module Service Tests](#7115-Pre-Lead-Project-UploadCreation-Module-Service-Tests)
        - [7.1.1.6 Channel Partner Project Summary/Report Module Service Tests](#7116-Channel-Partner-Project-SummaryReport-Module-Service-Tests)
        - [7.1.1.7 Inquiry Module Service Tests](#7117-Inquiry-Module-Service-Tests)
        - [7.1.1.8 TKE Library Module Service Tests](#7118-TKE-Library-Module-Service-Tests)
    - [7.2 BDD Test Framework (Cucumber)](#72-BDD-Test-Framework-Cucumber)
      - [7.2.1 Feature File Design](#721-Feature-File-Design)
        - [7.2.1.1 Channel Partner Upload Feature File](#7211-Channel-Partner-Upload-Feature-File)
        - [7.2.1.2 Channel Partner Approval Feature File](#7212-Channel-Partner-Approval-Feature-File)
        - [7.2.1.3 Pre-Lead Project Feature File](#7213-Pre-Lead-Project-Feature-File)
        - [7.2.1.4 Inquiry Management Feature File](#7214-Inquiry-Management-Feature-File)
        - [7.2.1.5 TKE Library File Management Feature File](#7215-TKE-Library-File-Management-Feature-File)
        - [7.2.1.6 Portal Data Sync Feature File](#7216-Portal-Data-Sync-Feature-File)
  - [8. Appendix](#8-Appendix)
    - [8.1 Database Table Design](#81-Database-Table-Design)
      - [8.1.1 Portal-side](#811-Portal-side)
      - [8.1.2 View-side](#812-View-side)
      - [8.1.3 Portal & View ER Relationship Diagram](#813-Portal-&-View-ER-Relationship-Diagram)
    - [8.2 Email Notification Rules](#82-Email-Notification-Rules)
    - [8.3 WeChat Push Notification Rules](#83-WeChat-Push-Notification-Rules)
    - [8.4 Portal API Response Format Complete Specification](#84-Portal-API-Response-Format-Complete-Specification)
      - [8.4.1 Unified Response Format](#841-Unified-Response-Format)
      - [8.4.2 Full Definition of Business Status Codes](#842-Full-Definition-of-Business-Status-Codes)
      - [8.4.3 Frontend Handling Examples](#843-Frontend-Handling-Examples)
      - [8.4.4 Portal API Response Examples (VUE)](#844-Portal-API-Response-Examples-VUE)
      - [8.4.5 Portal and CNView Two-way API List](#845-Portal-and-CNView-Two-way-API-List)
        - [8.4.5.1 APIs Provided by Portal to CNView (CNView calls Portal)](#8451-APIs-Provided-by-Portal-to-CNView)
        - [8.4.5.2 APIs Provided by CNView to Portal (Portal calls CNView)](#8452-APIs-Provided-by-CNView-to-Portal)
        - [8.4.5.3 Portal actively pushing data to CNView APIs (Portal calls CNView)](#8453-Portal-actively-pushing-data-to-CNView-APIs)

## 1. Project Background and Overview

### 1.1 Project Requirement Background

This project aims to develop a Partner channel comprehensive management platform based on WeChat service accounts, providing TKE channel agents and distributors with full lifecycle business management services. Through a Portal-VIEW dual-side architecture [...] 

### 1.2 Terms and Definitions

| Term | Explanation |
|------|-------------|
| **openid** | The user's unique identifier under the WeChat service account |
| **Scene parameter** | QR code scene value, used to track registration source and sales assignment: sales QR code contains SalesId, company QR code contains company identifier |
| **Partner** | 1 user + 1 company = 1 complete business data record = Partner = 1 customer + 1 contact |
| **Partner Status** | pending (awaiting approval), active (activated), signed (contracted), declined (rejected) |
| **User Status** | active (activated), banned (blocked), deleted (deactivated) |

### 1.3 Constraints

#### 1.3.1 User Status Management Constraints

##### Portal Side

- Core concept: 1 user + 1 company = 1 complete business data record = Partner (applies to Portal side only)
- Data ownership: all business operations, permission control, and status management are based on the user_company association record
- Multi-company support: users and companies are many-to-many; bindings maintained via user_company table
- Independent status: each user_company record has an independent Status (pending/active/declined/signed), which do not affect each other
- Status-driven permissions:
  - pending: can edit personal and company info, can create Inquiry/Project, can only view own data
  - active: can edit only personal info, company info is read-only, can view entire company data, can accept/reject TKE projects
  - declined: no company binding, can only edit personal info, must re-apply
  - signed: automatically redirect to View AGR Homepage
- Assignment logic:
  - project assignment applies only to Partners with active status
  - TKE project assignment is based on CompanyId, not directly bound to UserId
- Data sync trigger:
  - only when user_company.Status changes from pending → active, historical Inquiry/Project data is synced to View side
- Deactivation impact:
  - user-initiated deactivation only unbinds current user_company relation, does not affect Customer/Contact already created on View side

##### View Side

- Core concept: Partner = Customer (company) + Contract (contact); status is driven by the approval process, independent of data source (Upload / Portal)
- Unified status model:
  - all new applications (regardless of Upload or Portal) start with status pending/inactive
  - approval → status becomes active, creates customer + contract
  - rejection → status becomes declined, no formal customer created, only rejection logged
  - declined records can be reactivated; reactivation logic is same as initial approval
- Portal Account control:
  - only when Portal Account is checked will users be created on Portal side and bound to user_company
  - if not checked → exists only on View side, no Portal login ability
  - Portal-side registration defaults to checked and is not editable; Upload data can be decided by sales whether to check
- Status change synchronization:
  - Approval:
    - create customer / contract;
    - if Portal Account is checked, push active status to Portal;
    - synchronize historical Inquiry / Project (only Portal-origin has such data);
    - send WeChat activation notification (only to Portal Account users).
  - Rejection:
    - do not create customer;
    - call Portal API to unbind user_company (Portal-origin only);
    - do not send WeChat notification;
    - record declined status for later reactivation.
- Data uniqueness:
  - TaxCode used as unique identifier;
  - active records must not be overwritten (regardless of Upload or Portal);
  - when TaxCode duplicates:
    - active → automatically skip;
    - inactive/declined → allow replacement (requires confirmation).
- Permissions and operations:
  - sales can for all pending Partners (Upload / Portal) perform: edit, assign, approve, reactivate;
  - approval page logic is identical; only data retrieval method differs:
    - Upload: read local mod_channel_partner_companies;
    - Portal: call Portal API to fetch in real time.

### 1.4 System Architecture

#### 1.4.1 Portal-side Architecture Sequence Diagram

```mermaid
sequenceDiagram
    actor User as Partner
    participant WeChat as WeChat Service Account
    participant Portal as Portal Side
    participant VIEW as VIEW Side
    actor Sales as TKE Sales

    %% 1. Registration
    rect rgb(255,255,204)
    Note over User,Portal: 🟨 【1. Registration】
    User->>WeChat: Scan QR to enter registration page
    WeChat->>Portal: Open registration page & check OpenID
    alt Not authorized
        WeChat-->>User: Popup authorization
        User->>WeChat: Grant authorization
    end
    WeChat->>Portal: Return OpenID
    Portal-->>User: Display registration form
    User->>Portal: Fill and submit
    Portal->>Portal: Query by phone number
    alt Exists
        Portal-->>User: Prompt "Account exists, please login directly"
    else New user
        Portal->>VIEW: [API: Query by TaxCode]
        alt TaxCode not exist
            VIEW-->>Portal: Not exist
            Portal->>VIEW: [API: New registration application]
            VIEW->>Sales: Send pending approval email
            Portal-->>User: Prompt "Registration successful", guide to follow service account
            User->>WeChat: Follow TKE official service account
            WeChat-->>User: Send welcome message
        else TaxCode exists but not bound
            VIEW-->>Portal: Return company info
            Portal-->>User: Prompt need phone + verification binding
            User->>Portal: Verify phone
            Portal->>Portal: [API: Complete binding operation]
            Portal->>VIEW: [API: New registration application]
            VIEW->>Sales: Send pending approval email
            Portal-->>User: Prompt "Registration successful", guide to follow service account
            User->>WeChat: Follow TKE official service account
            WeChat-->>User: Send welcome message
        else Already bound user
            VIEW-->>Portal: Already bound
            Portal-->>User: Prompt "Account exists, please login directly"
        end
    end
    Note over Sales: Approval
    Sales->>VIEW: View Channel partner dashboard
    Note left of Sales: 🟨 If partner was imported
    after scanning matches an imported record,
    its source will change to self-scan.
    VIEW-->>Sales: Display partner dashboard
    Sales->>VIEW: View Self-Scan request details
    VIEW->>Portal: [API: Get Portal data]
    Portal-->>VIEW: Return Portal data
    VIEW-->>Sales: Display new partner request details
    alt Approval passed
        VIEW->>Portal: [API: Get all partner data]
        Portal-->>VIEW: Return project/customer/contact/inquiry data
        VIEW->>VIEW: Store activation data
        VIEW->>Portal: [API: Return activation results]
        Portal->>Portal: Update partner status to active
        Portal-->>WeChat: Send activation notification
        WeChat-->>User: Prompt account activated
    else Approval rejected
        VIEW->>Portal: [API: Return rejection result]
        Portal-->>WeChat: Send rejection notification
        WeChat-->>User: Prompt registration application rejected, can reapply
    end
    end

    %% 2. Login
    rect rgb(204,255,255)
    Note over User,Portal: 🟦 【2. Login】
    alt WeChat authorization login
        User->>WeChat: WeChat authorization
        WeChat->>Portal: Return OpenID
        Portal->>Portal: Validate account
        alt Multiple companies
            alt Has historical company
                Portal->>Portal: Open last company
            else
                Portal->>Portal: Default to first company
            end
            Portal-->>User: Auto-enter company (can switch)
        else Single company
            Portal-->>User: Directly enter company
        end
    else Phone+verification login
        User->>Portal: Enter phone + verification
        Portal->>Portal: Validate account
        alt Multiple companies
            Portal-->>User: Display company list
            User->>Portal: Select manually
        else Single company
            Portal-->>User: Directly enter
        end
    end
    end

    %% 3. AGR login / Redirect (phase2)
    rect rgb(255,255,204)
    Note over User,VIEW: 🟨 【3. AGR Login (phase2)】
    alt Enter via Portal
        User->>Portal: Select AGR company in company selection screen
        Portal->>VIEW: Redirect to VIEW
        VIEW-->>User: Display AGR Homepage
        User->>VIEW: Project operations
        VIEW->>Portal: Return to Portal and reselect company
    else User directly login to View side
        User->>VIEW: Login directly with VIEW account
        VIEW-->>User: Display AGR Homepage (no Portal redirect)
        User->>VIEW: Project operations
    end
    end

    %% 4. Inquiry Management
    rect rgb(204,255,255)
    Note over User,VIEW: 🟦 【4. Inquiry Management】
    alt Unactivated user
        User->>Portal: Create inquiry
        Portal->>Portal: Store data locally
        Portal->>VIEW: [API: Send inquiry notification]
        VIEW-->>Sales: Email push
        Sales->>VIEW: View unanswered inquiries
        VIEW->>Portal: [API: Get inquiry info] 
        Portal-->>VIEW: Return inquiry info
        Sales->>VIEW: Reply inquiry
        VIEW->>Portal: [API: Update Portal]
        Portal->>Portal: Store data locally
        Portal-->>WeChat: New reply notification
        WeChat-->>User: Reminder to check
        User->>Portal: View inquiry
        Portal-->>User: Show inquiry details
    else Activated
        User->>Portal: Create inquiry
        Portal->>VIEW: [API: Push inquiry data]
        VIEW->>VIEW: Store inquiry data
        Sales->>VIEW: Reply inquiry
        VIEW->>VIEW: Store reply data
        VIEW->>Portal: [API: Return reply result]
        Portal-->>WeChat: Push notification
        WeChat-->>User: Notify new reply
        User->>Portal: View inquiry
        Portal->>VIEW: [API: Get inquiry info]
        VIEW-->>Portal: Return inquiry data
        Portal-->>User: Show inquiry details
    end
    end

    %% 5. Project Management
    rect rgb(255,255,204)
    Note over User,VIEW: 🟨 【5. Project Management】
    alt Unactivated
        User->>Portal: Create/Edit Pre-Lead project
        Portal->>Portal: Save in Portal temporarily
        Portal-->>User: Display data
    else Activated
        User->>Portal: View project list
        Portal->>VIEW: [API: Get project data]
        VIEW-->>Portal: Return project data (including assigned projects)
        Portal-->>User: Display project list
        User->>VIEW: Create/Edit Pre-Lead project
        VIEW->>VIEW: Store data
        Sales->>VIEW: Assign TKE project
        VIEW-->>Portal: [API: Return assignment info]
        Portal-->>WeChat: Push notification
        WeChat-->>User: Notify user
        User->>Portal: Accept/Reject
        Portal->>VIEW: [API: Sync decision]
        VIEW->>VIEW: Store data
    end
    end

    %% 6. File Management
    rect rgb(204,255,255)
    Note over User,VIEW: 🟦 【6. File Management (TKE Library)】
    Sales->>VIEW: Upload files (store in NAS)
    VIEW->>Portal: Sync basic file info
    User->>Portal: Open file library
    Portal->>VIEW: Pull file directory (by status: Active/Inactive/AGR)
    VIEW-->>Portal: Return file list and download links
    Portal-->>User: Display file entry
    User->>Portal: Download/preview file (NAS)
    end

    %% 7. Account Status Changes
    rect rgb(255,255,204)
    Note over VIEW,Portal: 🟨 【7. Account Status Changes】
    opt Portal account disabled
        User->>Portal: Deactivate account
        Portal->>Portal: Update status = disabled and unbind WeChat
        Portal->>VIEW: [API: Deactivate account info]
        VIEW->>VIEW: Portal account deactivated, VIEW-side contact data not affected
        VIEW-->>Portal: Return confirmation
        Portal-->>WeChat: Push account deactivation notification
        WeChat-->>User: Prompt "Account deactivated successfully"
    end
    opt VIEW customer/contacts deleted
        Sales->>VIEW: Delete customer/contacts
        VIEW->>Portal: [API: Deleted customer/contacts info]
        Portal->>Portal: Delete user and company binding
        Portal-->>WeChat: Push company unbind notification
        WeChat-->>User: Prompt "Company unbound"
    end
    opt VIEW AGR account disabled (phase2)
        Sales->>VIEW: Disable Partner account
        VIEW->>Portal: [API: Disable Portal account]
        Portal->>Portal: Update status = disabled
        Portal-->>WeChat: Push account disabled
        WeChat-->>User: Prompt "Account disabled"
    end
    end
```