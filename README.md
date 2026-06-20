# neoClarity Claude Plugins

This repo is the **Claude plugin marketplace** for neoClarity.

Claude plugins are lightweight, shareable packages of tools and instructions that bundle custom commands, AI subagents, event hooks, and external data connectors (via MCP) into installable units.

## Available Plugins

| Name                 | Description                                                  | Location     |
| -------------------- | ------------------------------------------------------------ | ------------ |
| **Open AOS Factory** | The Open AOS Factory builds and maintains one or more Agentic Operating System (AOS) instances on your computer. | /aos-factory |

## Planning mode

When the user prompts "Planning mode", "P mode", or "pmode": do not create, edit, or delete any file unless the user prompts "Proceed". Planning mode means the session is for discussing and designing changes to the agent, not implementing them. Always display proposed changes to the user and ask to proceed. Automatically enter Planning Mode at the start of every session. Return to Planning Mode immediately after a "Proceed" action has completed.

## Writing style (strict): no em dashes.

Never use em dashes (—) in anything you write or edit, including documents, posts,
profile copy, code comments, and chat. This applies to brand-new deliverables and to
edits of existing files alike. Replace an em dash with the punctuation the sentence
needs: a period between two independent clauses, a comma or parentheses for an aside,
or a colon (or "for example:") before a list or illustration. When you edit a file at
my request, also remove any existing em dashes you come across.
