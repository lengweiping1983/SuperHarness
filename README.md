# SuperHarness

SuperHarness is a proactive coding agent for **one GitHub repository**. It listens to GitHub — and, optionally, Feishu — and, when someone allowed asks it for work, opens a work item, does the work in its own agent session and directory, and answers in the original conversation, for example with a pull request. A web panel inside DeepSeek Harness shows what needs a person.

Its guiding principle: **take as little human attention as possible, and give as much value as possible**. An agent that can act for a whole team can also waste a whole team's attention, so SuperHarness acts only when asked or when a written rule the team allows permits it, and speaks only when someone must act or can use a result.

It is built as a bundle for [DeepSeek Harness](https://www.deepseek.com/en/harness/).

## What it does

- **Listens.** It polls GitHub for requests addressed to the agent account — new Issues, comments and mentions, subscribed Issues and pull requests, CI changes and merged or closed pull requests. Feishu chats and direct messages are supported as an optional second source.
- **Decides.** Every event is evaluated against the team's Policy: who may ask for work, which bots may raise suggestions, and which written rules permit proactive work. A request from an allowed person becomes a work item with an explicit mandate; anything else is recorded, ignored, or held until a person decides.
- **Works.** Each accepted item runs in its own agent session with its own checkout, so several pieces of work proceed without sharing state. The worker may investigate, change code, run checks and open a pull request — and may reach a person with one concrete question when it needs a decision.
- **Reports.** The agent confirms a request once, stays quiet while it works, and writes again only when a result is ready, a decision is needed, or it cannot continue. Messages are short and go to the conversation where the request was made.
- **Stays honest.** Drafts are marked unfinished, failures are reported as failures, and a limit is never bypassed to avoid asking.

## Concepts

The domain model is deliberately small. Work moves from a **Source** (GitHub or Feishu) as a **Trigger**, along a **Thread**, into an **Eval** that applies the **Policy** and produces a **Decision**. An accepted Decision creates a **Ticket**: a unit of authorized work that holds a **Mandate**, is handed to an **Executor** as an **Assignment**, and reports back through its **Channels**. People can follow any piece of work from Trigger to Eval to Ticket in the panel.

| Term | Meaning |
| --- | --- |
| Source | A stream of events: a reusable kind (GitHub, Feishu) plus one configuration |
| Trigger | One immutable event from a Source, with its actor |
| Thread | Triggers in time order, such as one Issue and its comments |
| Policy | The versioned rules for accepting work, including each Source's allowed list |
| Eval | The judgement on a batch of Triggers that yields a Decision: accept, stop, ignore or unavailable |
| Mandate | What an accept grants: scope, strength and the Policy version it relied on |
| Ticket | A unit of authorized work, from open to ended, that resumes on a person's follow-up |
| Assignment | The binding of a Ticket to one executor session and its directory |
| Subscription | The relation that lets later discussion on a Thread reach its Ticket without a new mention |

## How work flows

1. You mention the agent on an Issue or in a comment, for example `@agent add input validation to the settings page`.
2. The agent acknowledges once in that conversation and turns the request into a Ticket with its mandate and scope.
3. A worker session prepares a checkout, investigates and implements the change, and opens a pull request.
4. The pull request arrives as the result in the original conversation, ready for review. Review comments and further discussion on the same threads reach the same worker, so follow-ups do not need to be explained again.
5. If the work needs a decision the worker cannot make — a design choice, a missing permission, a breaking change — it stops, says what is missing, and asks one concrete question.

## Panel

Open the **Superagent** page in the DeepSeek Harness Web sidebar to supervise the agent. It answers three questions in order:

1. **Does anything need me now?** Tickets awaiting an answer or a review, and setup or system problems.
2. **Is the system healthy and doing the right things?** Source state, filtered events, decisions and their reasons, and executor load.
3. **What happened to one piece of work?** A single item traced from Trigger through Eval to Ticket and its sessions.

Settings cover Sources, the Policy and allowed lists, execution (directories, concurrency, blocked commands), prompts and deployment limits.

## Getting started

SuperHarness needs:

- **DeepSeek Harness** with a model provider configured, run in a dedicated profile separate from the one you work in.
- An **agent GitHub account** with write access to the repository, plus a GitHub CLI signed in only as that account.
- Node.js 22.19+ or 24+, pnpm, git 2.32+ and the GitHub CLI 2.48+.
- Optionally, a **Feishu or Lark bot** app and its `lark-cli` configuration for chat requests.

Then, at a high level:

1. Create the dedicated DeepSeek Harness profile and install the SuperHarness bundle into it.
2. Configure the profile: the state directory and the panel port.
3. Start the service and open the panel.
4. In **Settings**, set the GitHub repository and the agent CLI, add the accounts allowed to request work, and configure the executor's directory root and prepare command.
5. Post an Issue or comment mentioning the agent account from an allowed account and watch the panel.

## Principles

- **Act only when asked, or when a written rule allows it.** Requests count only from people the team allowed, in that source's own identities. Proactive work needs a rule the team wrote and starts in a mode that only records what the agent would have done.
- **Speak only when a person must act or can use a result.** Fewer, better messages cost less attention than many explanations of the same thing.
- **Ask one concrete question at a time,** in a structured form, so the question reaches the person instead of hiding inside a long report.
- **Stop by impact, not by effort.** A local change goes as far as a reviewable result; a change that needs a design decision stops with a clear question.
- **Keep the model small.** A business need becomes an attribute of a Source or of a Ticket, or a prompt the agent follows — not a new concept.
- **Own what it says.** The agent comments, pushes and opens pull requests under accounts your team sees every day. Read what it posts in your name, and correct it in the prompts when it is wrong.

## Responsibility

Start narrow: a short allowed list and proactive work only recorded. Widen both once what the agent would have done looks right. When the agent is noisy, make its prompts shorter and stricter, or pause it — do not add more messages to explain the noise. Do not use it to produce volume: mass pull requests, broadcast messages and busywork cost the team more than they give.

## License

The project is distributed under the MIT License.