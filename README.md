FlowSpec
===============================

A lightweight standardized JSON schema for defining and automating multi-step workflows.

This document is a first draft of a standardized schema for defining AI-driven automation workflows. It is designed to be both human-readable and machine-interpretable, allowing you to share, visualize, and execute workflows with ease.

Table of Contents
-----------------

-   Overview
-   Why a Standardized Schema?
-   Workflow-Level Properties
-   Step Object
-   Global Transitions
-   Example
-   Existing Schema-like Workflow Protocols
-   Extending the Schema
-   How to Contribute

Why a Standardized Schema?
--------

As I took on the challenge to make a 100% automated business ([ProfitSwarm.ai](https://profitswarm.ai)), I tried many of the AI/Automation workflow tools out there. I found each had it's own quirks and limitations. I soon realised that if I was to achieve my goal, I would need to fundamentally get to grips with the idea of an AI workflow.

Nearly all of these drag-and-drop workflow tools operate the same backbone. It's flow-chart like, with a series of nodes and edges. Despite that, there's no great standard for defining these workflows in a portable way.

As each platform has it's own take on how to run workflows, a workflow on one won't necessarily equate to the same workflow on another, but in essence they are the same.

For my challenge I need a way to express these workflows, so I've created this schema (FlowSpec).

I intend to rework this schema as I go, and I appreciate any and all feedback.


### Example Workflow Tools:
- [n8n](https://profitswarm.ai/n8n-review-workflow-automation-tools/)
- [Gumloop](https://profitswarm.ai/gumloop-review/)
- [Relevance.ai](https://profitswarm.ai/relevance-ai-review/)


Overview
--------

In this context, a workflow is a series of steps that define a process for automated or semi-automated tasks, often leveraging AI to perform the work. Each workflow consists of a title, description, and a sequence of steps. Each step contains details about what action to execute, its inputs, expected outputs, and the transitions based on the outcome.

Workflow-Level Properties
-------------------------

-   **workflowTitle**\
    A short, human-friendly title that identifies the workflow.

-   **workflowDescription**\
    A brief description of what the workflow does and its overall purpose.

-   **globalTransitions** (Optional)\
    An object that defines default transitions for steps. For example, defaults for:

    -   **rerun**: What to do if a step needs to be rerun.
    -   **human_needed**: What to do if human intervention is required.\
        These defaults will apply to any step that does not override them.

Step Object
-----------

Each workflow consists of an array of steps defined by the following properties:

-   **stepTitle**\
    A human-friendly name for the step. This is what is displayed to users.

-   **stepId**\
    A machine-friendly unique identifier (or slug) for the step. This is used to reference the step in transitions.

-   **stepDescription**\
    A brief explanation of what the step does.

-   **action**\
    The function, method, or service to be executed for this step.

-   **parameters**\
    The inputs required for the action. This is where you specify the data needed for the step to run.

-   **results**\
    The expected outputs from the action. This documents what data will be produced.

-   **transitions**\
    An object mapping outcomes (like "success" and "fail") to the next step's stepId or to special directives. The standard keys include:

    -   **success**: The next step if the action succeeds.
    -   **fail**: The directive or step to jump to if the action fails.
    -   **rerun** (Optional): Overrides the global default for rerunning a step.
    -   **human_needed** (Optional): Overrides the global default for adding the step to a human review queue.

Global Transitions
------------------

The globalTransitions property allows you to set default behaviours for certain transitions across all steps. This reduces duplication when many steps share the same logic for rerun or human intervention.

Example of globalTransitions:

```json
{
    "rerun": "default_rerun_logic",
    "human_needed": "default_human_review"
}
```

Any step that does not explicitly define its own "rerun" or "human_needed" transition will use these global defaults.

Example
-------

Below is a full example of a workflow defined using this schema:
```json
{
  "workflowTitle": "Prospector Agent Flow",
  "workflowDescription": "This workflow gathers and validates niche ideas and domain availability.",
  "globalTransitions": {
    "rerun": "default_rerun_logic",
    "human_needed": "default_human_review"
  },
  "steps": [
    {
      "stepTitle": "Idea Generation",
      "stepId": "idea_gen",
      "stepDescription": "Generate a list of potential ideas based on trends.",
      "action": "generateIdeas",
      "parameters": {
        "prompt": "Generate creative business ideas based on current trends."
      },
      "results": {
        "ideas": "array of idea strings"
      },
      "transitions": {
        "success": "trend_analysis",
        "fail": "report_failure"
        // "rerun" and "human_needed" will use global settings unless defined here.
      }
    },
    {
      "stepTitle": "Trend Analysis",
      "stepId": "trend_analysis",
      "stepDescription": "Analyze trending data for each idea.",
      "action": "analyzeTrends",
      "parameters": {
        "ideas": "previous step's ideas"
      },
      "results": {
        "trendData": "array of trend metrics"
      },
      "transitions": {
        "success": "finalize_results",
        "fail": "report_failure",
        "human_needed": "custom_human_review"  // This overrides the global default.
      }
    }
    // Additional steps as needed...
  ]
}

```

Existing Schema-like Workflow Protocols
---------------------------------------

-   **BPMN 2.0**: An industry-standard graphical notation with an XML-based specification for modeling business processes.
-   **BPEL**: An XML language for orchestrating web services, historically used in service-oriented architectures.
-   **AWS Step Functions**: A JSON-based state machine language designed for orchestrating AWS services in serverless architectures.
-   **Azure Logic Apps**: A JSON-based workflow definition tool integrated with Azure, featuring both visual and code views.
-   **WDL & CWL**: Workflow languages geared toward data-intensive pipelines and scientific computing, emphasizing reproducibility.
-   **Argo Workflows & Tekton**: Kubernetes-native, YAML-based orchestration engines that excel in containerized CI/CD pipelines.
-   **Custom DSLs**: Many teams opt to create lightweight JSON/YAML schemas tailored to their specific automation needs.

Extending the Schema
--------------------

This schema is designed to be modular and extendable. As your workflows evolve, you might want to include additional properties such as:

-   **timeout**: Specify maximum execution time per step.
-   **retries**: Define the number of retries on failure.
-   **metadata**: Any extra data or tags that may be useful for logging or analysis.
-   **conditions**: More complex branching logic beyond simple transitions.

... I intend to develop this schema as my challenge progresses.

Feel free to customize the schema to best fit your project needs while maintaining clarity and consistency.

Moving this forward
--------------------

I appreciate any and all feedback, please do raise issues, submit pull requests, or contact me directly via [ProfitSwarm.ai](https://profitswarm.ai) or [@woodyhayday](https://x.com/woodyhayday).

My aim with this is to write something we can all use, regardless of which [AI workflow tool](https://profitswarm.ai/category/ai-tool-reviews/) we're using. Let's make it work!
