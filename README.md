SEREIN

# QR-Powered Smart Printing

Investor Product Brief

A scan-to-print experience designed to simplify document printing and collection.

CONCEPT + EARLY TECHNICAL PROTOTYPE

## 1. Executive Summary

Serein is a proposed QR-powered printing service that lets a customer begin a print job from a phone, submit a PDF through a web interface, and have the job routed to a local printer workflow. The product is designed around a simple customer journey: scan a QR code, upload a document, choose print options, pay according to the shop’s pricing rules, and collect the completed pages using a job or pickup code.

The current technical work is an early local prototype: a website-facing API, an Express-based print queue, and a local Node.js agent that checks the queue and submits PDFs to the Windows default printer through a PDF printing utility. The end-to-end product, payments, pickup verification, persistent storage, and production security still require implementation and testing.

## 2. Customer Problem

Traditional print-shop workflows can require customers to transfer files manually, wait while an operator handles each request, or explain settings in person.

A shop needs a clear way to associate each uploaded document with the right customer, print options, payment state, and completed pages.

For unattended or high-volume workflows, the business needs controlled job queuing and a reliable handoff from website to printer.

## 3. Proposed Solution

Place a QR code at the print location. The QR code opens the Serein website, where the customer uploads a PDF and selects options such as copies. The backend creates a job and places it in a queue. A local print agent retrieves queued work and submits it to the shop’s configured printer. The system can then expose job status and a pickup code for staff/customer verification.

### Target customer journey

Scan: Customer scans the shop’s QR code and opens the web page.

Configure: Customer uploads a PDF and chooses supported print options.

Quote and pay: The system calculates the price; printing should be authorized only after trusted payment confirmation.

Queue: Backend validates the submission and creates a print-job record.

Print: Local agent retrieves the job and submits the PDF to the printer.

Collect: Customer presents a pickup code or job reference; staff confirms the order is ready and paid.

## 4. Product Architecture

Prototype architecture (local development):

| Layer | Role |

| --- | --- |

| QR code + browser | Opens the customer website on a phone or computer. |

| React/Vite frontend | Collects the PDF and print settings; submits an HTTP request to a relative /api endpoint. |

| Vite proxy | Forwards /api requests from the website’s development server to the local Express queue service. |

| Express queue API | Validates the upload, creates a job ID and pickup code, and tracks queued/printing/completed/failed status. |

| Local Node.js print agent | Polls the queue, checks the local PDF path, sends it to the Windows default printer via SumatraPDF, and reports status. |

| Printer + Windows spooler | Receives the print command; physical output still needs operational verification. |

Important design point: the public website should not connect directly to a local printer or a customer’s localhost. The browser submits to the website origin; the backend queue mediates the handoff, and the local agent makes outbound requests to the queue. The present prototype assumes the website, queue, and printer agent run on the same computer.

## 5. Technology Stack

Frontend: React, Vite, TypeScript (existing project stack).

Backend queue: Node.js, Express, Multer for multipart PDF upload handling.

Local print worker: Node.js, polling API, SumatraPDF command-line printing utility.

QR access: QR code encodes the website URL; it is a link mechanism, not proof of payment or identity.

Temporary external testing: LocalTunnel or another HTTPS tunnel to the Vite frontend.

## 6. Business Model Hypothesis

The initial model is a per-job printing fee, with price calculated from pages, colour mode, paper size, copies, and any finishing services supported by the shop. Payment could be collected digitally before the job is released to the printer. A later B2B model could charge print shops a monthly software fee, a per-job platform fee, or a combination. These are hypotheses to validate, not established revenue or pricing results.

### Potential customer segments

College and university print/copy shops

Independent neighbourhood printing centres

Libraries, co-working spaces, and shared office facilities

High-footfall locations where customers need quick document printing

## 7. Potential Value Proposition

Reduce manual file-transfer steps between customer and print operator.

Provide a consistent job reference and queue status.

Support a more self-service customer experience from a phone.

Create a path toward payment-gated printing and verified collection.

Use a local print agent so existing Windows-connected printers can be used in the prototype.

These benefits are intended product outcomes. They have not yet been validated through customer research or measured pilots.

## 8. Current Prototype Status

| Component | Current state |

| --- | --- |

| QR-to-website entry | Conceptually defined; QR code can point to the public website URL. |

| Public access for testing | Temporary tunnel approach identified; host allow-list and frontend/backend routing must be configured. |

| Queue API | Prototype files prepared for PDF upload, job queueing, polling, and status updates. |

| Print agent | Prototype prepared to poll the queue and invoke SumatraPDF on Windows. |

| Existing website integration | Not yet merged into the original React UI; a standalone test page is supplied. |

| End-to-end printing | Not verified on the user’s actual computer or printer. |

| Payment integration | Not implemented in this prototype. |

| Persistent database / durable queue | Not implemented; current queue is in-memory. |

| Production deployment and security | Not ready; requires substantial hardening. |

## 9. Roadmap

Phase 1 — Local proof of concept: Verify QR access, PDF upload, queue creation, local agent polling, and one successful print on the target printer.

Phase 2 — Customer workflow: Integrate with the React UI; add order history/status, pricing calculation, cancellation rules, and pickup-code verification.

Phase 3 — Payments: Integrate a suitable payment provider, verify payments server-side using signed webhooks or provider status checks, and release jobs only when payment is confirmed.

Phase 4 — Reliability and security: Add database-backed queue, durable job claiming, retries, audit logs, file scanning/validation, retention policy, rate limits, staff/admin access, and secure secret management.

Phase 5 — Pilot: Run a controlled pilot at one print shop, measure completion time, failed jobs, reprints, user adoption, and operating cost.

Phase 6 — Commercial rollout: Validate pricing and shop economics, support multiple printer stations, add monitoring and support procedures, and deploy production infrastructure.

## 10. Risks and Mitigations

| Risk | Mitigation / next step |

| --- | --- |

| Unauthorized or excessive print submissions | Require proper user/order authorization, rate limits, quotas, and server-side validation. A demo access code is not sufficient for public production. |

| Payment spoofing | Use trusted provider verification on the backend; never rely on a browser success message. |

| Duplicate or lost jobs | Use a persistent database, idempotency keys, durable queue claims, and explicit retry handling. |

| Sensitive customer documents | Store files outside public web roots, encrypt transport/storage where appropriate, restrict access, and delete files under a documented retention policy. |

| Print status differs from physical output | Distinguish “print command accepted” from “pages physically collected”; add staff confirmation or hardware monitoring where feasible. |

| Tunnel/public test exposure | Use tunnels only for controlled demos; do not expose an unprotected development server as a production service. |

## 11. Investor Discussion: What We Need to Prove

Customer demand: Do users prefer QR-based self-service over the existing shop workflow?

Shop economics: Does the workflow reduce staff handling time or increase jobs per hour enough to justify the fee?

Operational reliability: What proportion of jobs print correctly on the first attempt?

Unit economics: Average order value, paper/ink cost, payment fees, support/reprint costs, and software cost per shop.

Repeat usage: Customer return rate and shop willingness to continue after a pilot.

## 12. Investor Ask

The immediate objective is to complete and validate a controlled pilot, not to claim product-market fit. Potential investment or pilot support would be directed toward end-to-end product engineering, payment integration, secure and persistent infrastructure, printer compatibility testing, and a measured pilot with one or more print shops.

## Closing

Serein aims to turn a fragmented print-shop interaction into a QR-initiated, trackable digital workflow. The concept and initial local technical bridge provide a starting point. The next milestone is a reliable, secure pilot that demonstrates customer demand, measurable operating benefits, and viable economics.
