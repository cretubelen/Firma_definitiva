# Biometric Signature Integration for Microsoft Dynamics 365 Business Central


Here is a quick demonstration of the final product:

[▶️ Video demonstration](./media/demo.mp4)


> **Bachelor's Thesis — Computer Engineering · 2024/2025**

A Microsoft Dynamics 365 Business Central extension that integrates **biometric signature capture** into sales documents through **Power Apps**, automating document generation, email delivery and the conversion of signed sales quotes into orders.

---

## Overview

This project was developed as my Bachelor's Thesis in Computer Engineering during my internship at **Innova Advanced Consulting S.L.**

The objective was to design and implement a solution that allows customers to sign sales documents digitally through a Power Apps application while keeping the entire process integrated with **Microsoft Dynamics 365 Business Central**.

The solution connects three main components:

- **Power Apps** — provides the user interface for viewing documents and capturing customer signatures.
- **Power Automate** — orchestrates the automation workflows triggered from the application.
- **Business Central** — acts as the ERP and central data repository, extended using the **AL programming language**.

The final solution covers the complete workflow from document selection and signature capture to PDF generation, email delivery and document conversion.

---

## Key Features

### ✍️ Biometric signature capture

Customers can sign sales documents directly from the Power Apps application using a digital ink input control.

The captured signature is stored in Business Central and associated with the corresponding sales document.

### 📄 Signed document generation

Once a document has been signed, the system generates a PDF containing the original document information together with the customer's signature.

The generated document is prepared automatically for delivery.

### 📧 Automatic email delivery

The signed PDF is automatically sent to the customer's email address, reducing manual document handling and providing a traceable digital workflow.

### 🔄 Automatic quote-to-order conversion

When a sales quote has been successfully signed and the required information is available, the system automatically converts the quote into a sales order in Business Central.

### 📦 Signed delivery notes

The solution also supports the signing of sales shipment documents.

A delivery note must be signed before it can be used as the basis for the subsequent invoicing process.

### 🔌 Custom Business Central APIs

Custom API Pages were implemented to expose the required Business Central data to Power Apps using the OData/REST architecture.

These APIs expose both standard Business Central information and the additional fields introduced by the extension.

### 🧪 Automated testing

The Business Central extension includes automated AL tests using Microsoft's testing framework and `Library Assert`.

The workflows were additionally validated through Power Automate executions and user-oriented testing.

---

## Architecture

The solution follows a modular architecture based on the Microsoft ecosystem:

```
flowchart LR
    A[Customer] --> B[Power Apps]

    B --> C[Power Automate]

    C --> D[Business Central]

    D --> E[AL Extension]

    E --> F[Sales Quotes]
    E --> G[Sales Shipments]
    E --> H[Custom APIs]
    E --> I[PDF Reports]

    I --> C
    C --> J[Customer Email]
```

### Component responsibilities

| Component            | Responsibility                                                           |
| -------------------- | ------------------------------------------------------------------------ |
| **Power Apps**       | Document visualization, navigation and biometric signature capture       |
| **Power Automate**   | Workflow orchestration and communication between the application and ERP |
| **Business Central** | Business logic, document management and data persistence                 |
| **AL Extension**     | Custom fields, pages, APIs, codeunits, reports and permissions           |
| **Custom APIs**      | Data exchange between Business Central and Power Apps                    |
| **Reports**          | Generation of signed PDF documents                                       |
| **AL Test Tool**     | Automated validation of the Business Central extension                   |

The architecture was deliberately designed around separation of responsibilities, allowing each component to focus on a specific part of the workflow.

---

## Functional Modules

The application is divided into two main functional modules.

### 1. Sales Quotes

The first module manages the complete signing workflow for sales quotes.

```
Sales Quote
     │
     ▼
Power Apps
     │
     ▼
Customer signs document
     │
     ▼
Signature stored in Business Central
     │
     ▼
Signed PDF generated
     │
     ▼
PDF sent to customer
     │
     ▼
Quote converted into Sales Order
```

The module introduces additional information into the standard Business Central sales structure, including the customer's email address and biometric signature.

### 2. Sales Shipments

The second module manages the signing of delivery notes.

```
Sales Shipment
     │
     ▼
Customer receives goods
     │
     ▼
Customer signs through Power Apps
     │
     ▼
Signature stored in Business Central
     │
     ▼
Signed document generated
     │
     ▼
Document sent by email
     │
     ▼
Shipment available for invoicing
```

This creates a controlled workflow in which signed delivery documents can be validated before continuing with the invoicing process.

---

## Technology Stack

### Microsoft Dynamics 365 Business Central

Business Central acts as the central ERP system and data repository.

The standard Business Central data model was extended rather than replaced, allowing the solution to integrate directly with existing sales processes.

### AL

The backend functionality was implemented using **AL**, Microsoft's programming language for Business Central extensions.

The extension includes:

- Table extensions
- Page extensions
- Custom API Pages
- Codeunits
- Reports and report extensions
- Permission sets
- Automated tests
- Translation resources

The project follows Business Central extension conventions and uses the `ABCT` prefix to identify the custom objects developed specifically for the solution.

### Power Apps

Power Apps provides the presentation layer of the solution.

The application allows users to:

- Browse sales documents.
- View document details.
- Access customer information.
- Display product information.
- Capture biometric signatures.
- Trigger automated workflows.

### Power Automate

Power Automate is responsible for orchestrating the processes between Power Apps and Business Central.

The workflows handle operations such as:

- Saving signatures.
- Generating signed documents.
- Sending PDFs by email.
- Triggering Business Central operations.
- Automating the quote-to-order workflow.

### OData / REST APIs

Custom API Pages expose the required Business Central entities to Power Apps.

The APIs support structured access to the required data while keeping the integration within the Business Central extension architecture.

---

## Business Central Extension

The extension builds on top of the standard Business Central sales entities.

Additional fields were introduced to support the signature workflow, including:

```
Sales Header
├── Customer information
├── Sales information
├── Customer email
└── Biometric signature

Sales Shipment Header
├── Shipment information
├── Customer information
├── Customer email
└── Biometric signature
```

The solution reuses Business Central's existing document lifecycle while adding the information and logic required for digital signing.

---

## Project Structure

The repository is organized as a Business Central AL extension:

```
Firma_definitiva/
│
├── .AL-Go/
├── .github/
├── .vscode/
│
├── FirmaBiometrica_TFG/
│   └── Business Central extension source
│
├── Firma_definitiva.Test/
│   └── Automated AL tests
│
├── src/
│   └── report/
│       └── layout/
│
├── app.json
├── al.code-workspace
├── CODEOWNERS
├── SECURITY.md
├── SUPPORT.md
└── README.md
```

Within the AL extension, the source code is organized according to the type of Business Central object:

```
src/
├── codeunit/        # Business logic and document workflows
├── page/             # Custom pages and API pages
├── pageextension/    # Extensions of standard BC pages
├── permissionset/    # Required permissions
├── report/           # PDF/document generation
├── reportextension/  # Extensions to standard reports
├── tableextension/   # Additional fields for BC tables
└── Translations/     # Translation resources
```

---

## Data Flow

The main signing workflow can be summarized as follows:

```
sequenceDiagram
    participant C as Customer
    participant PA as Power Apps
    participant PF as Power Automate
    participant BC as Business Central
    participant E as Email

    C->>PA: Open sales document
    C->>PA: Draw signature
    PA->>PF: Submit signature
    PF->>BC: Store signature
    BC->>PF: Generate signed PDF
    PF->>E: Send signed document
    E->>C: Receive PDF

    alt Sales Quote
        BC->>BC: Convert quote to order
    end
```

This architecture allows the user interface, automation layer and ERP logic to remain separated while working together as a single workflow.

---

## Testing

Testing was carried out at different levels.

### AL Automated Tests

The Business Central extension includes automated tests using Microsoft's AL testing framework and `Library Assert`.

Tests validate important parts of the business logic, including:

- Signature persistence.
- Document processing.
- Quote-to-order conversion.
- Data validation.
- Error scenarios.

The repository also includes a dedicated test project:

```
Firma_definitiva.Test/
```

### Power Automate Testing

Power Automate workflows were executed independently to validate that each automation step completed correctly and that errors could be identified during execution.

### User Validation

The solution was also validated through user-oriented tests using representative document workflows.

---

## Requirements

This project is primarily intended as a **Business Central extension development project** rather than a standalone application.

The development environment used:

- Microsoft Dynamics 365 Business Central
- Visual Studio Code
- AL Language extension
- Power Apps
- Power Automate
- GitHub
- AL-Go for GitHub

The `app.json` configuration targets Business Central application version `25.4` and AL runtime `14.0`.

---

## Getting Started

### 1. Clone the repository

```
git clone https://github.com/cretubelen/Firma_definitiva.git
cd Firma_definitiva
```

### 2. Open the project

Open the project using **Visual Studio Code** with the **AL Language** extension installed.

```
code .
```

### 3. Connect to a Business Central environment

Configure the appropriate Business Central development environment through the AL project configuration.

> The Power Apps application and Power Automate flows depend on the Business Central environment and are not standalone local services. Their configuration therefore needs to be adapted to the target Business Central tenant.

### 4. Build and publish

Use the AL development tools provided by Visual Studio Code to compile, publish and debug the extension against the target Business Central environment.

---

## Project Context

This project was developed as a **Bachelor's Thesis in Computer Engineering** during the 2024/2025 academic year.

**Title:**
*Incorporación de la firma biométrica en documentos mediante Power Apps*

**Author:**
Andrea Belen Cretu Toma

**Academic supervisor:**
Cristina Campos Sancho

**Company supervisor:**
Sergi Vilar Domenech

**Company:**
Innova Advanced Consulting S.L.

**Reading date:**
18 June 2025

The project was developed within a 300-hour internship and followed a structured software development process covering requirements analysis, system design, implementation, integration and validation.

---

## Future Improvements

The implemented solution provides a foundation that could be extended with additional functionality, including:

- More granular user and permission management.
- Signature validation mechanisms.
- Support for additional document types such as invoices or contracts.
- Further mobile and responsive improvements in Power Apps.
- Automatic reminders for pending signatures.
- Additional notification mechanisms.
- Extended document tracking and audit capabilities.

These improvements were identified as potential future extensions of the original project.

---

## Academic Documentation

The complete Bachelor's Thesis describes the requirements analysis, architecture, Business Central extension, Power Apps implementation, Power Automate workflows, testing strategy and project results.

The project documentation covers the complete development process from the initial requirements to the final integrated solution.

---

## Acknowledgements

Special thanks to **Innova Advanced Consulting S.L.** for providing the professional environment in which this project was developed, and to my academic and company supervisors for their guidance and feedback throughout the project.

---

## Author

**Andrea Belen Cretu Toma**

Software Engineer · Geospatial Technologies · Machine Learning
