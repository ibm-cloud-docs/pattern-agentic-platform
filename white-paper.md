---

copyright:
  years: 2025,2026
lastupdated: "2025-3-13"

keywords:

subcollection: pattern-agentic-platform

---

{{site.data.keyword.attribute-definition-list}}


# Agentic AI Workflow with {{site.data.keyword.wxorchestrate_full_notm}} and MCP on IBM Cloud
{: #agentic-ai-workflow}

Agentic AI is an artificial intelligence system that can accomplish a specific goal with limited supervision. It consists of AI agents—machine learning models that mimic human decision-making to solve problems in real time. AI Agent is an autonomous software entity capable of understanding, planning, and executing complex tasks with minimal human intervention. Unlike traditional AI, which is often reactive or limited to content generation, an agent possesses the capacity to act purposefully to reach a goal by designing its own workflow and interacting with external environments.

The critical components that distinguish an agent from a simple chatbot:

- **Perception:** Gathering data from the environment via APIs, or user interactions.

- **Reasoning:** Using LLMs (like Granite) to analyze perceived data, decompose complex goals into subtasks, and select the best course of action.

- **Goal setting:** Sets objectives based on predefined goals or user inputs and then develops a strategy to achieve these goals, often by using decision trees, reinforcement learning or other planning algorithms.

- **Decision-making** Evaluates multiple possible actions and chooses the optimal one based on factors such as efficiency, accuracy and predicted outcomes. It might use probabilistic models, utility functions or machine learning-based reasoning to determine the best course of action.

- **Execution** Executes action, either by interacting with external systems (APIs, data, robots) or providing responses to users.

- **Learning/Reflection:** Evaluating outcomes and adjusting future plans based on feedback or results.


![Agent AI Workflow Components](images/agentic-intro.svg){: caption="Agent Workflow" caption-side="bottom"}{: external download="images/wxo-architecture-system-design.svg"}


This architecture allows enterprises to handle unstructured workflows such as IT incident remediation or customer service with minimal human intervention, ensuring scalable and adaptive productivity.

## The Role of Model Context Protocol (MCP)
{: #role-mcp}

The Model Context Protocol (MCP) is a standardized open-source communication protocol that IBM has integrated into its agent architecture (such as watsonx Orchestrate) to solve the connector problem. MCP provides a standardized integration layer, allowing agents to connect to any external service (databases, local files, enterprise APIs) without rewriting code for each individual connection.

In a business environment, giving an agent full access to tools is risky. MCP plays a critical role in IBM's architecture by:

- **Observability:** Providing visibility into which tools an agent uses and how it uses them.

- **Security & Permissions:** Allowing for granular control and audit trails over tool execution, ensuring agents only perform authorized operations.

- **Reusability:** Tools built with MCP can be reused across different agent frameworks (like LangChain, LangGraph, or BeeAI) within an organization.


## Introduction
{: #intro}

The multi-agent orchestration with {{site.data.keyword.wxorchestrate_full_notm}} architecture shown in the figure follows a layered, service-oriented approach, separating client interaction, core agent logic, and remote/infrastructure supporting services.

![Multi-Agent Architecture with {{site.data.keyword.wxorchestrate_full_notm}}](images/wxo-architecture-system-design.svg){: caption="Multi-Agent Architecture with Watson Orchestrate" caption-side="bottom"}{: external download="images/wxo-architecture-system-design.svg"}


The architecture is logically divided into four main domains:

   - **Client Applications**: The boundary for user interaction and service consumption. This layer manages the initial user request and ensures secure access to the system. The user initiating the interaction via a User Request. The user interface (e.g., a web portal, mobile app, or API gateway) receives the User Request and forwards the request (as a User Prompt) to the Agent Workflows layer. Authentication service will authenticate the user before sending the request to the Agent workflows.
   - **Agent Workflows ({{site.data.keyword.wxorchestrate_full_notm}})**: The core intelligence layer responsible for interpreting user intent and coordinating actions across internal and external services. Agent workflows are defined using {{site.data.keyword.wxorchestrate_full_notm}} to interpret the user intent, breakdown complex tasks into steps and decide which tools and models to invoke. {{site.data.keyword.wxorchestrate_full_notm}} supports Prompts that define the instructions, context, and constraints given to the Agents. Knowledgebase manages structured and unstructured information used by the Agents for LLM responses using RAG. Knowledgebase can use a local vector store in {{site.data.keyword.wxorchestrate_full_notm}} or it can integrate with an external Vector Store. The Tools component is central to the architecture's ability to move beyond simple question-answering to performing real-world actions and retrieving live data. This layer is designed for interoperability, supporting three primary integration patterns:

      - **Standard Tools:** Direct implementations of functions or API connectors.

      - **MCP Stdio Servers:** Tools integrated via the Model Context Protocol running over standard input/output for local or containerized processes.

      - **MCP Remote Servers:** Tools accessed via the Model Context Protocol over network transports (such as SSE) for distributed capabilities.

      Together, these interfaces enable the agent to execute specific tasks and access distinct data sources across diverse environments.

   - **IaaS, PaaS, SaaS Services (Application/Data Plane)**: The layer hosting the external MCP servers, external applications, LLM models, and enterprise data required to execute the agent's tasks. This layer provides the computational power and external application functionality needed for task execution. LLMs can be hosted and inferenced as SaaS for multi-tenant or on a PaaS / IaaS (RedHat OpenShift AI, RHEL AI etc…) for single tenant solutions. MCP Servers that provide a list of domain specific tools like (Salesforce, HR, Trade Finance etc…) are hosted on PaaS such as ({{site.data.keyword.codeenginefull_notm}}, ROKS etc…) to perform domain specific tasks. Server applications with integration to backend systems and data stores that host the business logic and data, utilized by both the LLM and the external MCP Servers through APIs.
   - **Cloud Services (Control Plane/Infrastructure)**: The foundational services for security, observability, and management. A secure vault secrets manager stores sensitive information like API keys and credentials is provisioned to ensure secure key stores. The central authority for authentication, authorization, and managing user identities and service accounts across the entire platform are done using IAM services while the monitoring and logging are provisioned to provide observability across the layers.

   - **Network:** [IBM Cloud Satellite](https://cloud.ibm.com/docs/satellite?topic=satellite-link-location-cloud) and [Direct Link](https://cloud.ibm.com/docs/dl?topic=dl-get-started-with-ibm-cloud-dl) extends the access to existing enterprise Agents, MCP and APIs providing network security and secure access.

## Architecture Pattern
{: #arch-pattern}

The following architecture patterns describe the standards used in the Agentic AI workflow industry solutions

### Agentic AI workflow with Independent Software Vendors (ISVs) hosted on IBM Cloud
{: #agentic-workflow-isv}

{{site.data.keyword.wxorchestrate_full_notm}} support native (built on {{site.data.keyword.wxorchestrate_full_notm}}) as well as external agents (those running on any stack outside {{site.data.keyword.wxorchestrate_full_notm}}). {{site.data.keyword.wxorchestrate_full_notm}} Agent catalog comes with a wide range of predefined agents that include domain specific independent software vendors like SAP, Workday, Salesforce etc. that are hosted as services on IBM Cloud Infrastructure.

![Running Generative AI Agentic Workflow solutions](images/ai-isv-catalog.svg){: caption="Running Generative AI Agentic Workflow solutions" caption-side="bottom"}{: external download="images/wxo-architecture.svg"}

The Agentic AI workflow can utilize the existing ISV/external agents and tools to build a complete end to end client solutions. The architecture facilitates seamless, secure interaction between {{site.data.keyword.wxorchestrate_full_notm}} and your core enterprise applications.

**The Integration Bridge:** The Agent Catalog within {{site.data.keyword.wxorchestrate_full_notm}} acts as the direct conduit to external enterprise services.

**Supported Enterprise ISVs:** The catalog connects natively to major software vendors hosted on IBM Cloud, such as SAP, Workday, Salesforce etc.

**Secure Execution:** Because this workflow operates within the IBM Cloud Services ecosystem, all data exchanges between AI agents and these ISVs are protected by embedded Security Services that are Financial Service Validated(e.g., IBM Verify, Key Protect) and tracked via centralized Observability (e.g., Auditing, Logging).


This makes the integration of services hosted on IBM Cloud with Agents to be seamless and build with low-code/no-code solution as well as leveraging existing task performing services with minimal or no changes.

*Note: To onboard Agents to {{site.data.keyword.wxorchestrate_full_notm}} catalog follow the documentation - [IBM Agent Connect](https://connect.watson-orchestrate.ibm.com/introduction)*

### Agentic AI workflow for an enterprise solution on IBM Cloud
{: #agentic-workflow-ent-sol}

The architecture consists of layers of components to run Generative AI Agentic Workflow solutions built with {{site.data.keyword.wxorchestrate_full_notm}} and MCP services. Agents and MCP Services can be run locally in {{site.data.keyword.wxorchestrate_full_notm}} or can be imported from remote MCP servers that run on containerized platforms. Knowledgebase of an agent in Watson Orchestrate can be integrated with databases that support vectorization like DataStax and PostgreSQL.

![Running Generative AI Agentic Workflow solutions](images/wxo-architecture.svg){: caption="Running Generative AI Agentic Workflow solutions" caption-side="bottom"}{: external download="images/wxo-architecture.svg"}

The architecture comprises of:

**Client Application Platform**: Client Front end applications hosted on serverless {{site.data.keyword.codeenginefull_notm}} and Backend services supporting the client application hosted on containerized platform like Kubernetes will access Agents hosted on {{site.data.keyword.wxorchestrate_full_notm}} through embedded Java Scripts or through {{site.data.keyword.wxorchestrate_full_notm}} APIs.

**{{site.data.keyword.wxorchestrate_full_notm}} Service**: {{site.data.keyword.wxorchestrate_full_notm}} serves as the foundational control plane for enterprise Agentic AI.The platform allows enterprises to deploy autonomous workflows that are secure, scalable, and grounded in business data. The platform's capabilities are organized into three primary pillars

   - **Core Agentic Capabilities**: These features define the "brain" of the system, determining how agents perceive tasks, plan actions, and retrieve information.
      - [Agents](https://developer.watson-orchestrate.ibm.com/agents/overview){: external} - the autonomous units of the platform. You can build Custom Agents, integrate specialized Remote Agents, or deploy pre-configured Catalog Agents. Unlike standard bots, these agents utilize reasoning loops to decompose complex user goals into manageable sub-tasks.
      - [Agentic Workflows](https://developer.watson-orchestrate.ibm.com/tools/flows/overview){: external} The operational backbone that defines multi-step logic. Workflows allow agents to maintain context over long interactions, manage state, and execute sequential processes that require conditional decision-making.
      - [Prompts](https://developer.watson-orchestrate.ibm.com/agents/descriptions){: external} The governance layer for agent behavior. These system instructions (or "System Prompts") define the agent's persona, operational boundaries, and formatting rules, ensuring that autonomous actions remain aligned with business guidelines.
      - [Knowledge Base](https://developer.watson-orchestrate.ibm.com/knowledge_base/build_kb){: external} (RAG) The long-term memory of the system. By implementing Retrieval-Augmented Generation (RAG), agents can access, search, and cite domain-specific documents (PDFs, policies, manuals) to provide accurate, evidence-based responses.
   - **Tools & Integration Framework**: This feature enables to bridge the gap between AI reasoning and real-world execution.
      - [Tools](https://developer.watson-orchestrate.ibm.com/tools/overview){: external} The executable skills available to an agent. These range from simple Python scripts and data analysis functions to complex API connectors that allow agents to read from databases or write to enterprise software systems.
      - [MCP Integration](https://developer.watson-orchestrate.ibm.com/mcp_server/wxOmcp_integration){: external} A standardized protocol for limitless extensibility. The platform supports the Model Context Protocol (MCP) in two modes:

         - *Local MCP Servers*: Hosted directly within {{site.data.keyword.wxorchestrate_full_notm}} for tightly coupled integrations.

         - *Remote MCP Servers*: Secure connections to tools distributed across external IaaS, PaaS, or SaaS environments via the MCP Toolkit.
      - [Connections & Credentials](https://developer.watson-orchestrate.ibm.com/connections/overview){: external} The security vault for autonomous action. This component manages the authentication lifecycle, securely storing API keys, OAuth tokens, and secrets to ensure that every tool call is authorized and auditable.
   - **LLM Infrastructure Support**: To ensure flexibility and cost-efficiency, the platform decouples the orchestration layer from the inference layer.

      [LLM Integration](https://developer.watson-orchestrate.ibm.com/llm/getting_started_llm){: external} Enterprise-grade model support that adapts to your infrastructure needs:

      - IBM watsonx.ai: Managed SaaS inference for rapid deployment and scaling.
      - Red Hat OpenShift AI (RHOAI): Self-managed inference on {{site.data.keyword.vpc_short}} infrastructure, offering data sovereignty and GPU acceleration.
      - RHEL AI: Optimized, lightweight inference on Red Hat Enterprise Linux for edge or specific hardware configurations.

**Agentic AI Application Platform**: Agents, MCP, Microservice API servers are hosted on containerized platforms like Red Hat OpenShift, Kubernetes, and {{site.data.keyword.codeenginefull_notm}} service that are build on programming languages (nodejs, python etc…) and frameworks (LangGraph, Crew AI, Bee AI etc…).

**Model Inferencing Platform**: LLM models that are trained and fine-tuned are hosted on Red Hat OpenShift AI or VSI instance with GPU accelerated profiles like NVIDIA (H100, H200 etc..),  AMD (MI300x) and Intel (Gaudi 3). The models can be inferenced and integrated with Watson Orchestrate through cloud internet service.

**Management and Administration**: The Management VPC provides compute, storage, and network services like VPN to enable the consumer's or service provider's administrators to monitor, operate, and maintain the deployed Gen AI Platform services and applications on {{site.data.keyword.vpc_short}} infrastructure.

**Enterprise Network Connctivity**: IBM Cloud VPC establishes private enterprise connectivity from customer data centers to IBM Cloud for access to applications, data, and services seamlessly through [IBM Cloud Direct Link](https://cloud.ibm.com/docs/dl?topic=dl-get-started-with-ibm-cloud-dl). This dedicated pathway links your on-premises virtual machines (VMs) and cloud environments, while [IBM Cloud Satellite Link](https://cloud.ibm.com/docs/satellite) creates a secure tunnel for private administrative and management connectivity to your distributed Red Hat OpenShift clusters. Together, these hybrid networking solutions provide network isolation with the ability to separate applications based on attributes such as data classification, public versus private traffic flows, and internal application function.

## Cloud Capabilities supporting architecture
{: #cloud-capabilities}

The following heatmap highlights the cloud capabilities in scope for the multi-agent orchestration with W{{site.data.keyword.wxorchestrate_full_notm}} architecture following the IBM Cloud Architecture Design Framework.

![Cloud Domains in Scope](images/wxo-heatmap.svg){: caption="Cloud Domains in Scope" caption-side="bottom"}

- **Application Platforms**: Mobile, Edge, Enterprise Applications
- **Data**: Data Storage, Artificial Intelligence
- **Compute**: Virtual Servers, Containers, Serverless
- **Storage**: Primary Storage, Backup Storage
- **Networking**: Load balancing, Domain name service
- **Security**: Data Security, Identity & access, Application security, Infrastructure & endpoints, Governance, risk & compliance
- **DevOps**: Build & test, Delivery pipeline, Code repository
- **Resiliency**: Disaster recovery, High Availability
- **Service management**: Monitoring, Logging, Auditing/tracking, Automated deployment, Management/orchestration

### Supporting Requirements
{: #requirements}

The following table outlines the requirements that are addressed in this architecture.

| **Aspect** | **Requirements** |
| -------------------- | ------------------------------------------------------------------------------------------ |
| Compute | Provide properly isolated compute resources with adequate compute capacity for the applications. |
| Storage | Provide storage that meets the application and database performance requirements. |
| Networking | Deploy workloads in secure environment and enforce information flow policies \n Provide secure, encrypted connectivity to the cloud’s private network for management purposes. \n Distribute incoming application requests across available compute resources. |
| Security | Ensure all operator actions are executed securely through a bastion host. \n Protect the boundaries of the application against denial-of-service and application-layer attacks. \n Encrypt all application data in transit and at rest to protect from unauthorized disclosure. \n Encrypt all security data (operational and audit logs) to protect from unauthorized disclosure. \n Encrypt all data using customer managed keys to meet regulatory compliance requirements for additional security and customer control. \n Protect secrets through their entire lifecycle and secure them using access control measures. \n Firewalls must be restrictively configured to prevent all traffic, both inbound and outbound, except that which is required, documented, and approved. |
| DevOps | Delivering software and services at the speed the market demands requires teams to iterate and experiment rapidly. They must deploy new versions frequently, driven by feedback and data. |
| Resiliency | Support application availability targets and business continuity policies. \n Ensure availability of the application in the event of planned and unplanned outages. \n Backup application data to enable recovery in the event of unplanned outages. \n Provide highly available storage for security data (logs) and backup data. |
| Service Management | Monitor system and application health metrics and logs to detect issues that might impact the availability of the application. \n Generate alerts/notifications about issues that might impact the availability of applications to trigger appropriate responses to minimize down time. \n Monitor audit logs to track changes and detect potential security problems. \n Provide a mechanism to identify and send notifications about issues found in audit logs. |
{: caption="Multi-agent agentic AI workflow architecture requirements" caption-side="bottom"}

### Components
{: #components}

The following table outlines the products or services used in the architecture for each aspect.

| **Aspect** | **Architecture \n components** | **How the component is used** |
| -------------------- | ------------------------ | -------------------------------------------------------------------------- |
| Data | [{{site.data.keyword.wxorchestrate_full_notm}}](https://www.ibm.com/products/watsonx-orchestrate){: external} | Orchestrate AI agents, assistants and workflows across your business |
| | [IBM watsonx.ai](https://www.ibm.com/products/watsonx-ai){: external} | Brings together new generative AI capabilities powered by foundation models and traditional machine learning (ML) into a powerful studio spanning the AI lifecycle |
| | [{{site.data.keyword.lakehouse_full_notm}} with Milvus](https://www.ibm.com/products/watsonx-data){: external} | Enables data analytics for AI at scale and provides Milvus database to store vector embeddings for RAG patterns |
| | [IBM watsonx.governance](https://www.ibm.com/products/watsonx-governance){: external} | Direct, manage and monitor the artificial intelligence activities |
| Compute | [Virtual Servers for {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-about-advanced-virtual-servers&interface=ui) | Web, App, LLMs with GPU Accelerated VSI instances and database servers |
| | [{{site.data.keyword.codeenginefull_notm}}](/docs/codeengine?topic=codeengine-about) | Abstracts the operational burden of building, deploying, and managing workloads in Kubernetes so that developers can focus on what matters most to them: the source code |
| | [{{site.data.keyword.openshiftlong_notm}}](/docs/openshift?topic=openshift-getting-started) | A managed offering to create your own cluster of compute hosts where you can deploy and manage containerized apps on IBM Cloud |
| Storage | [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage) | Trained and fine-tuned models, Web app static content, backups, logs (application, operational, and audit logs) |
| | [{{site.data.keyword.block_storage_is_short}}](/docs/openshift?topic=openshift-vpc-block) | Web app storage if needed |
| Networking | [{{site.data.keyword.vpn_full}}](/docs/iaas-vpn?topic=iaas-vpn-getting-started) | Remote access to manage resources in private network |
| | [{{site.data.keyword.vpe_full}}](/docs/vpc?topic=vpc-about-vpe) | For private network access to Cloud Services, e.g., Key Protect, COS, etc. |
| | [{{site.data.keyword.alb_full}}](/docs/vpc?topic=vpc-load-balancers) | Application Load Balancing for web servers, app servers, and database servers |
| | [{{site.data.keyword.tg_full_notm}}](/docs/transit-gateway?topic=transit-gateway-getting-started) | Connects the Workload and Management VPCs within a region |
| | [{{site.data.keyword.cis_full_notm}}](/docs/cis?topic=cis-getting-started) | Global load balancing between regions |
| | [Access Control List](/docs/vpc?topic=vpc-using-acls) | To control all incoming and outgoing traffic in Virtual Private Cloud |
| Security | [IAM](docs/account?topic=account-cloudaccess) | IBM Cloud Identity & Access Management |
| | [{{site.data.keyword.keymanagementservicelong_notm}}](/docs/key-protect?topic=key-protect-about) | A full-service encryption solution that allows data to be secured and stored in IBM Cloud |
| | [BYO Bastion Host on VPC VSI](/docs/framework-financial-services?topic=framework-financial-services-vpc-architecture-connectivity-bastion-tutorial-teleport) | Remote access with Privileged Access Management |
| | [{{site.data.keyword.appid_full_notm}}](/docs/appid?topic=appid-getting-started) | Add authentication to web and mobile apps |
| | [{{site.data.keyword.secrets-manager_full_notm}}](/docs/secrets-manager?topic=secrets-manager-getting-started#getting-started) | Certificate and Secrets Management |
| | [{{site.data.keyword.sysdigsecure_full_notm}}](/docs/security-compliance?topic=security-compliance-getting-started) | Implement controls for secure data and workload deployments, and assess security and compliance posture |
| | [Hyper Protect Crypto Services](/docs/hs-crypto?topic=hs-crypto-get-started) | Hardware security mVodule (HSM) and Key Management Service |
| | [Virtual Network Function](/docs/vpc?topic=vpc-deploy-vnf) | Virtualized network services running on virtual machines |
| DevOps | [Continuous Integration (CI)](/docs/containers?topic=containers-cicd) | A pipeline that tests, scans and builds the deployable artifacts from the application repositories |
| | [Continuous Deployment (CD)](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started) | A pipeline that generates all of the evidence and change request summary content |
| | [Continuous Compliance (CC)](/docs/devsecops?topic=devsecops-tutorial-cc-toolchain) | A pipeline that continuously scans deployed artifacts and repositories |
| | [Container Registry](/apidocs/container-registry) | Highly available, and scalable private image registry |
| Resiliency | [VSI and Storage multiple zones in two regions](/docs/solution-tutorials?topic=solution-tutorials-vpc-multi-region) | Web, app, database high availability and disaster recovery |
| Service Management | [{{site.data.keyword.monitoringlong_notm}}](/docs/monitoring?topic=monitoring-about-monitor) | Applications and operational monitoring |
| | [{{site.data.keyword.logs_full_notm}}](/docs/cloud-logs?topic=cloud-logs-about-cl) | Operational and audit logs |
{: caption="Components in architecture" caption-side="bottom"}

### Compliance
{: #compliance}

The Continuous Integration (CI), Continuous Deployment (CD), and Continuous Compliance (CC) pipelines, referred to as DevSecOps Application Lifecycle Management are used to deploy the application, check for vulnerabilities, and ensure auditability. Below are some of important compliance features of DevSecOps Application Lifecycle Management:

   - **Vulnerability Scans** involve using specialized tools to look for security vulnerabilities in the code. This is crucial to identify and fix potential security issues before they become a problem in production.
   - **Sign Build Artifacts** The code is compiled and built into software or application artifacts (like executable files or libraries). These artifacts are then digitally signed to ensure their authenticity and integrity.
   - **Evidence Gathering** This involves collecting and storing evidence of the development process, such as commit logs, build logs, and other relevant data. It helps in tracing back and understanding what happened at different stages of development.
   - **Evidence Locker** This involves collecting and storing evidence of the development process, such as commit logs, build logs, and other relevant data. This helps in tracing back and understanding what happened at different stages of development.
