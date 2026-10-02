# AI Multi-Agents — Patient Request Automation

> An AI-powered multi-agent workflow for automated patient request routing, processing, data retrieval, and communication.

## Project Overview

This project implements a multi-agent AI workflow designed to automate the processing of patient requests.

A patient submits a request through Tally. The request is analyzed by a Smart Router, which identifies the type of request and forwards it to the appropriate specialized agent.

The system currently supports two main use cases:

* FAQ Management
* Appointment Management

Each agent is responsible for a specific task, while MongoDB provides access to the required data and dedicated action agents manage communication through Gmail and Slack.

## Project Objectives

The main objectives of the project are to:

* Automate the processing of patient requests.
* Automatically route each request to the appropriate agent.
* Separate responsibilities between specialized agents.
* Retrieve the required information from MongoDB.
* Automate communication with patients and the internal team.
* Create a Notion ticket when a FAQ request cannot be answered from the available data.

## System Architecture

```text
                              +-------------------+
                              |       TALLY       |
                              |   Patient Input   |
                              +---------+---------+
                                        |
                                        v
                              +-------------------+
                              |   SMART ROUTER    |
                              | Request Analysis  |
                              |   and Routing     |
                              +---------+---------+
                                        |
                         +--------------+--------------+
                         |                             |
                         v                             v
                +-----------------+           +--------------------+
                |    FAQ AGENT    |           | APPOINTMENT AGENT  |
                +--------+--------+           +----------+---------+
                         |                               |
                         v                               v
                    +---------+                     +---------+
                    | MongoDB |                     | MongoDB |
                    +----+----+                     +----+----+
                         |                               |
                  +------+-------+                  +----+-------+
                  |              |                  |            |
                  v              v                  v            v
          Information       Information        Slack Agent   Email Agent
             Found            Not Found              |            |
                  |              |                   v            v
                  v              v                 Slack        Gmail
            Email Agent       Notion                 |            |
                  |            Ticket               Team        Patient
                  v
                Gmail
                  |
                  v
               Patient
```

## Use Case 1 — FAQ

The FAQ workflow is designed to answer patient questions using the information available in MongoDB.

### Workflow

```text
Patient
   |
   v
Tally
   |
   v
Smart Router
   |
   v
FAQ Agent
   |
   v
MongoDB
   |
   +---------------- Information Found ----------------> Email Agent
   |                                                       |
   |                                                       v
   |                                                     Gmail
   |                                                       |
   |                                                       v
   |                                                    Patient
   |
   +---------------- Information Not Found ------------> Notion
                                                           |
                                                           v
                                                         Ticket
```

### Logic

1. The patient submits a question through Tally.
2. The Smart Router identifies the request as a FAQ.
3. The request is forwarded to the FAQ Agent.
4. The FAQ Agent searches for the required information in MongoDB.
5. If the information is available, the Email Agent sends the response through Gmail.
6. If the information is not available, a ticket is created in Notion.

## Use Case 2 — Appointments

The appointment workflow handles patient requests related to appointments.

### Workflow

```text
Patient
   |
   v
Tally
   |
   v
Smart Router
   |
   v
Appointment Agent
   |
   v
MongoDB
   |
   +--------------------> Slack Agent -----> Slack -----> Team
   |
   +--------------------> Email Agent -----> Gmail -----> Patient
```

### Logic

1. The patient submits an appointment request through Tally.
2. The Smart Router identifies the request as an appointment.
3. The request is forwarded to the Appointment Agent.
4. The Appointment Agent consults MongoDB.
5. The result is processed by the corresponding action agents:

   * Slack Agent: notification to the team.
   * Email Agent: response to the patient through Gmail.

## Multi-Agent Architecture

The system follows a specialized-agent approach, where each agent has a clearly defined responsibility.

| Component         | Responsibility                                                     |
| ----------------- | ------------------------------------------------------------------ |
| Smart Router      | Analyzes the incoming request and selects the appropriate workflow |
| FAQ Agent         | Processes FAQ requests and searches MongoDB                        |
| Appointment Agent | Processes appointment requests and uses MongoDB                    |
| Email Agent       | Sends patient communications through Gmail                         |
| Slack Agent       | Sends team notifications through Slack                             |

The agents use an LLM to understand and process the requests associated with their responsibilities.

## Data Layer

### MongoDB

MongoDB serves as the data source used by the specialized agents.

It is consulted to:

* Retrieve information required for FAQ responses.
* Retrieve information related to appointment requests.

For FAQ requests, the workflow checks whether the requested information is available.

```text
FAQ Request
     |
     v
  MongoDB
     |
 +---+-----+
 |         |
 v         v
Found    Not Found
 |          |
 v          v
Gmail      Notion
Response   Ticket
```

### Notion

Notion is used to create a ticket when a FAQ question cannot be answered from the available data.

## Communication and Actions

### Gmail

Used by the Email Agent to communicate the processed response to the patient.

### Slack

Used by the Slack Agent to notify the internal team for appointment-related requests.

### Notion

Used to create a ticket for FAQ requests when the required information is not available in the data source.

## Technology Stack

| Technology               | Role                                 |
| ------------------------ | ------------------------------------ |
| Tally                    | Patient request collection           |
| AI / LLM                 | Request understanding and processing |
| Multi-Agent Architecture | Specialized task execution           |
| MongoDB                  | Data retrieval and storage           |
| Gmail                    | Patient communication                |
| Slack                    | Team notification                    |
| Notion                   | Ticket creation                      |

## Key Benefits

### Automation

Reduces manual handling of patient requests.

### Intelligent Routing

The Smart Router directs each request to the appropriate specialized agent.

### Specialization

Each agent focuses on a clearly defined responsibility.

### Centralized Data

MongoDB provides a centralized data source for the specialized agents.

### Automated Communication

Responses and notifications are automatically delivered through Gmail and Slack.

### Unanswered FAQ Tracking

FAQ requests without available information are automatically converted into Notion tickets.

## End-to-End Workflow

```text
                 PATIENT REQUEST
                        |
                        v
                      TALLY
                        |
                        v
                 SMART ROUTER
                        |
              +---------+---------+
              |                   |
              v                   v
          FAQ AGENT        APPOINTMENT AGENT
              |                   |
              v                   v
           MONGODB             MONGODB
              |                   |
        +-----+-----+       +-----+-----+
        |           |       |           |
        v           v       v           v
     Gmail       Notion   Slack       Gmail
        |           |       |           |
        v           v       v           v
     Patient     Ticket    Team      Patient
```

## Demo

The project can be demonstrated through two complete scenarios.

### Scenario 1 — FAQ

```text
Tally
  |
  v
Smart Router
  |
  v
FAQ Agent
  |
  v
MongoDB
  |
  +---- Information Found ----> Gmail ----> Patient
  |
  +---- Information Not Found -> Notion Ticket
```

### Scenario 2 — Appointment

```text
Tally
  |
  v
Smart Router
  |
  v
Appointment Agent
  |
  v
MongoDB
  |
  +----> Slack Agent ----> Slack ----> Team
  |
  +----> Email Agent ----> Gmail ----> Patient
```

## Conclusion

This project demonstrates how a multi-agent AI architecture can automate different patient request workflows by combining intelligent routing, specialized agents, centralized data, and automated actions.

> A modular, automated, and easily extensible AI workflow for patient request management.

## Project

AI Multi-Agents — Patient Request Automation

Developed as a practical project focused on Agentic AI, multi-agent architectures, LLM-based processing, and workflow automation.
