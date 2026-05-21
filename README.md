A Look into the Internal System behind Reservation Management at a Prominent California Based Retreat Center

This is a Brief Overview of the Functionalities and Capabilities of the Internal Reservation and Operations Management System I helped Design and Deploy at a Prominent California based Retreat Center. Due to the Confidentiality of the System and the fact that it is a Full-Scale Production in use, the underlying Source code cannot be shared, but the thought process behind designing the System and some of the Stack used to make it come alive will be touched on briefly, as well as some diagrams to showcase what the working product came to be.

I will Continuously Update this Read.me as more Features and logistics are adapted into the System.


This documentation walks through how our stack integrates to handle internal booking logistics, room assignments, and scheduling for staff among other functions. <br> <br>

System Flow & Integration <br>
To understand how the system functions, it helps to follow a standard request like an employee reserving a room from the UI down to the database. <br>
Next.JS UI -> (Amplify) -> API Gateway/JWT -> Go Lambda -> PostgreSQL

<br><br>

# The Tools of the Trade and the Significance Behind Each One

**The Entry Point: Next.js + AWS Amplify** <br>
Due to the requirement of administrative workflows to be fast and responsive we decided to Utilize Next.JS to build the Dashboard from the ground up. Everything starts with the client dashboard built in Next.js. When a staff member updates a reservation or views the room grid, the frontend triggers state changes and handles API requests.

To meet the heavy lifting demand of CI/CD AWS Amplify was an easy choice. We use AWS Amplify to host the frontend. It manages the build pipeline directly from our repository, handles SSR (Server-Side Rendering) where needed, and serves the static assets via a CDN.

Role-Based Access Control (RBAC) is enforced right at the UI layer, ensuring staff members only see the administrative modules required for their specific shift duties.

<br>

**The Gateway & Compute: Go (Golang) + AWS Lambda** <br>
When the frontend fires an API request, it hits an API Gateway that validates the user's authentication token. Once cleared, the request is routed to AWS Lambda.

We decided to write the Backend Logic using Golang Because Go compiles down to a single, highly efficient binary, the execution time is incredibly fast, keeping Lambda "cold starts" at a minimum.

We decoupled core workflows (such as bookings, inventory, and meal manifests) into distinct, isolated serverless functions. This architectural split ensures high performance and blast-radius isolation: a database-heavy kitchen schedule generation cannot bottleneck rapid room check-ins, and a bug in one operational service leaves the rest of the platform entirely unaffected.

<br>

**Data Persistence: PostgreSQL** <br>
The Go binaries process the business logic and talk directly to PostgreSQL to read or write data.

Because bookings involve overlapping dates, physical space constraints, and guest packages, a relational database was essential. Postgres handles the complex joins and uses strict transactions to guarantee that two staff members can't double-book a room simultaneously.

<br>

**Provisioning: Terraform** <br>
Instead of manually creating these resources in the AWS Console, the entire setup from the Lambda environments to the database subnets is defined declaratively using Terraform. This allows us to spin up, update, or tear down identical development and staging environments predictably.
