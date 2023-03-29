> This is a **Sanity Studio v3** plugin.

## Installation

```sh
npm install sanity-plugin-workflow
```

# Sanity Workflow Demo Plugin Example

With Sanity Studio you can [customize your content tools to support arbitrary workflows like assignment and content pipelines](https://www.sanity.io/docs/custom-workflows).

This plugin is distributed as a **reference implementation** of these customization APIs and is not considered to be a feature-complete implementation of what workflow management requires in production. It is a starting point intended to be forked and customized to the needs of your organization and content creators.

![Screenshot 2023-03-21 at 12 11 24](https://user-images.githubusercontent.com/9684022/226602179-5bd3d91a-9c27-431e-be18-3c70f06c6ccb.png)

## Features

This work demonstrates how a single plugin can define:

- A unique schema to handle workflow metadata
- Document Actions to promote and demote documents through the workflow
- Document Badges for visual feedback about the current state of a document
- A custom Tool for drag-and-drop updating of a document's state

## Installation

```
npm install --save sanity-plugin-workflow@beta
```

or

```
yarn add sanity-plugin-workflow@beta
```

## Usage

Add it as a plugin in sanity.config.ts (or .js):

```js
 import {createConfig} from 'sanity'
 import {workflow} from 'sanity-plugin-workflow'

 export const createConfig({
    // all other settings ...
     plugins: [
         workflow({
            // Required, list of document type names
            // schemaTypes: ['article', 'product'],
            schemaTypes: [],
            // Optional, see below
            states: [],
         })
     ]
 })
```

## Configuring "States"

The plugin comes with a default set of "States". These are tracked by the plugin creating a separate "metadata" document for each document that has begun the Workflow. 

Documents can be promoted and demoted in the Workflow with the provided Document Actions as well as a drag-and-drop custom Tool. The settings below are not enforced by the API, custom access control rules could be used to do so. 

```ts
{
    // Required configuration
    id: 'inReview',
    title: 'In Review',
    // Optional settings:
    // Used for the color of the Document Badge
    color: 'success',
    // Will limit document actions and drag-and-drop for only users with these Role
    roles: ['publisher', 'administrator'],
    // Requires the user to be "assigned" in order to update to this State
    requireAssignment: true,
    // Requires the document to be valid before being promoted out of this State
    // Warning: With many documents in the Kanban view this can negatively impact performance 
    requireValidation: true,
    // Defines which States a document can be moved to from this one
    transitions: ['changesRequested', 'approved']
}
```

### Intended content operations with this plugin

A content creator composes a new document of one of the configured Schema types. The document is a "Draft", but not automatically added to the Workflow.

The creator clicks the "Begin Workflow" Document Action to create a new "metadata" document and add the document to the first State in the Workflow. Using the default States, the document is now "In Review".

The document is now visible in the Workflow Tool. The creator can drag and drop the document to the next State in the Workflow, "Changes Requested". Other users may be "assigned" to the document. In the default State configuration, only an assigned user can move the document into the final "Approved" state.

An administrator can move the document into Changes Requested or Approved.

With the document now Approved, a user may also return to the document and Publish it, by whatever means that make sense to your use case. Such as scheduled publishing or migrating it to a new dataset.

Once the Workflow is complete, the metadata can be removed by using the "Complete Workflow" document action.

### Differences from the Sanity Studio v2 Workflow Demo

This plugin is largely based on the original Workflow Demo built into a Sanity Studio v2 project. The major differences are:

* This plugin can be more easily installed and configured, not just code examples built into a Studio project
* Documents must "opt-in" to and removed from the Workflow, in the previous version all documents were in the workflow which would fill up the interface
* User Roles and Assignments can affect the Workflow. Set rules to enforce which States documents can move between and if being assigned to a document is required to move it to a new State
* Ability to filter Schema types and assigned Users

## License

[MIT](LICENSE) © Sanity.io

## Develop & test

This plugin uses [@sanity/plugin-kit](https://github.com/sanity-io/plugin-kit)
with default configuration for build & watch scripts.

See [Testing a plugin in Sanity Studio](https://github.com/sanity-io/plugin-kit#testing-a-plugin-in-sanity-studio)
on how to run this plugin with hot reload in the studio.

### Release new version

Run ["CI & Release" workflow](https://github.com/sanity-io/sanity-plugin-workflow/actions/workflows/main.yml).
Make sure to select the main branch and check "Release new version".

Semantic release will only release on configured branches, so it is safe to run the release on any branch.
