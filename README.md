# Everybody Votes

Senior Design project for an interactive polling and voting application inspired by the Wii Everybody Votes Channel.

**Course:** CS 5001 — Senior Design I  
**Team Members:** Hannah Krzywkowski and Aryan Balyan  
**Faculty Advisor:** Dr. Hrishikesh Vinayak Bhide

## 1. Project Overview

Everybody Votes is a web application where users can answer polls and view voting results. Guests can participate without creating an account, while registered users may access additional features such as predictions, voting history, and personal statistics.

The project focuses on creating a simple, accessible, and privacy-conscious voting experience.

## 2. Planned Features

- Guest and registered user voting
- Administrator-created and scheduled polls
- Multiple-choice questions with 2–6 answer options
- Voting results with counts and percentages
- Predictions, voting history, and personal statistics
- Optional demographic comparisons with privacy protections
- Accessible and responsive user interface

## 3. Project Documentation

### Project Information

- [Project Description](Project-Description.md)

### Team Documents

- [Team Contract](Team/Team_Contract.md)
- [Hannah Krzywkowski — Professional Biography](Team/Hannah-Krzywkowski-Professional-Biography.md)

### Requirements and Specifications

- [Constraints Essay](Other_Design_Docs/03_Constraints_Essay.md)
- [User Stories and Use Cases](Other_Design_Docs/04_User_Stories.md)
- [Requirements Document](Other_Design_Docs/09_Requirements_Document.md)
- [Technical Specification](Other_Design_Docs/09_Technical_Specification.md)

### Design Documents

- [D0 — High-Level Design](Design_Diagrams/D0_High_Level_Design.md)
- [D1 — Detailed Design](Design_Diagrams/D1_Detailed_Design.md)
- [D2 — Detailed Design](Design_Diagrams/D2_Detailed_Design.md)
- [Peer Review Notes](Design_Diagrams/Peer_Review_Notes.md)

### Design Diagrams

- [D0 — Block Diagram](Design_Diagrams/D0_Block_Diagram.svg)
- [D0 — Data Flow Diagram](Design_Diagrams/D0_Data_Flow.svg)
- [D1 — Data Model](Design_Diagrams/D1_Data_Model.svg)
- [D2 — Poll Lifecycle](Design_Diagrams/D2_Poll_Lifecycle.svg)
- [D2 — Vote and Error Flow](Design_Diagrams/D2_Vote_Error_Flow.svg)

### Project Planning and Testing

- [Task List](Planning/08_Tasklist.md)
- [Milestones, Timeline, and Effort Matrix](Planning/08_Assignment8_Plan.md)
- [Test and Validation Plan](Planning/10_Test_Plan.md)

### Ethics and Responsible Use

- [Ethics and Responsible-Use Statement](Ethics_Statement.md)

## 4. Technology Stack

The planned application uses:

- **Backend:** Python and Flask
- **Frontend:** HTML, CSS, Jinja2, and optional JavaScript
- **Database:** SQLite
- **Authentication:** Flask sessions and password hashing
- **Security:** CSRF protection, role-based access, and secure password storage
- **Testing:** Python unittest or pytest

The technology stack is planned for Spring 2027 implementation. Specific dependency versions will be selected and documented during development.

## 5. Ethics, Licensing, and Attribution

### Ethics and Responsible Use

The project's privacy, security, licensing, attribution, and AI-use decisions are documented in the [Ethics and Responsible-Use Statement](Ethics_Statement.md).

The statement addresses data collection and consent, retention and deletion, security protections, licensing obligations, third-party attribution, and responsible AI use.

### Third-Party Libraries and Attribution

The following libraries and tools are planned for implementation:

| Library or Tool | Source | License |
|---|---|---|
| Python | [Python Software Foundation](https://www.python.org/) | Python-2.0 |
| Flask | [Pallets](https://github.com/pallets/flask) | BSD-3-Clause |
| Jinja2 | [Pallets](https://github.com/pallets/jinja) | BSD-3-Clause |
| Werkzeug | [Pallets](https://github.com/pallets/werkzeug) | BSD-3-Clause |
| Flask-WTF | [Pallets](https://github.com/pallets-eco/flask-wtf) | BSD-3-Clause |
| SQLite | [SQLite Project](https://www.sqlite.org/) | Public domain |
| pytest (optional) | [pytest-dev](https://github.com/pytest-dev/pytest) | MIT |

No third-party datasets, creative assets, or adapted tutorial code have been identified in the current design documents. Original project diagrams, poll content, and design materials are team-created.

The attribution list will be updated if additional libraries, datasets, assets, or external code are used during implementation.

### Project License

Everybody Votes uses the [MIT License](LICENSE) for the team's original project work. Third-party dependencies retain their respective licenses.

## 6. Project Status

The project is currently in the design and planning phase for CS 5001 Senior Design I.

Fall 2026 work includes project requirements, architecture, detailed design, planning, testing strategy, and responsible-use documentation.

Implementation, integration, and testing are planned for CS 5002 Senior Design Project II in Spring 2027.
