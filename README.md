# Prisma DMMF Modifier - Architecture Documentation

## Overview

The `prisma-dmmf-modifier` is a TypeScript library designed to programmatically modify Prisma's DMMF (Datamodel Meta Format) - the internal representation of Prisma schema. This library provides a structured way to manipulate Prisma schemas through code instead of directly editing schema files, enabling developers to:

- Add, update, and remove models
- Add, update, and remove fields from models
- Add, update, and remove enums and enum values
- Manage relations between models (one-to-one, one-to-many, many-to-many)

## Core Architecture

The project follows a command pattern architecture with the following key components:

### 1. DMMF Modifier Core (`dmmfModifier.ts`)

The `DMMFModifier` class serves as the central coordinator for all schema modifications. It maintains:
- The current state of the datamodel
- A history of commands for undo functionality

All modifications go through this class, providing a consistent interface for schema changes.

### 2. Datamodel (`datamodel.ts`)

The `Datamodel` class encapsulates the actual DMMF structure and provides methods to directly modify it:
- `addModel`, `removeModel`
- `addField`, `updateField`, `removeField`
- `addEnum`, `removeEnum`, `addEnumField`, `updateEnumField`, `removeEnumField`

This class contains the implementation details for each modification operation.

### 3. Command Pattern Implementation (`commands/`)

The library implements the Command pattern to enable operations and their reversals (undo):
- Each command (like `AddFieldCommand`, `RemoveModelCommand`, etc.) extends the abstract `DMMFCommand` class
- Commands have `do()` and `undo()` methods for executing and reversing operations
- Commands are executed through the `DMMFModifier.do()` method and can be undone with `DMMFModifier.undo()`

Available commands:
- Model operations: `AddModelCommand`, `RemoveModelCommand`
- Field operations: `AddFieldCommand`, `UpdateFieldCommand`, `RemoveFieldCommand`
- Enum operations: `AddEnumCommand`, `RemoveEnumCommand`
- Enum field operations: `AddEnumFieldCommand`, `UpdateEnumFieldCommand`, `RemoveEnumFieldCommand`

### 4. Relation Management (`relationManager/`)

The `RelationManager` and its sub-modules handle the complex logic of creating and maintaining relationships between models:
- `RelationType` abstractions for different relation types
- Specialized handlers for each relation type: `OneToOne`, `OneToMany`, `ManyToMany`
- Support for creating the necessary fields and foreign keys for each relation type

## Directory Structure

```
src/
├── commands/                   # Command pattern implementations
│   ├── addFieldCommand.ts      # Add field to model
│   ├── addEnumCommand.ts       # Add enum
│   ├── removeFieldCommand.ts   # And other command implementations...
│   └── index.ts                # Exports all commands
├── relationManager/            # Relation handling
│   ├── index.ts                # Main RelationManager class
│   └── relationType/           # Specialized relation type handlers
│       ├── oneToOne.ts
│       ├── oneToMany.ts
│       ├── manyToMany.ts
│       └── relationType.ts     # Base relationType interface
├── datamodel.ts                # Core datamodel manipulation
├── dmmfModifier.ts             # Main modifier class implementing command pattern
├── helpers.ts                  # Utility functions
├── nativeTypesOptions.ts       # Prisma native type definitions
├── types.ts                    # Type definitions
└── index.ts                    # Public API exports
```

## Technical Design

### Command Pattern

The library uses the Command Pattern to:
1. Encapsulate each schema modification as a separate object
2. Track operation history for undo functionality
3. Separate the modification logic from the actual execution

### Type Safety

The library extensively uses TypeScript to ensure type safety:
- Leverages Prisma's own type definitions from `@prisma/generator-helper`
- Provides proper typing for DMMF structures and operations

### Dependencies

The library depends on several Prisma packages:
- `@prisma/engine-core`
- `@prisma/generator-helper`
- `@prisma/internals`

## Build and Development

The project uses:
- TypeScript for type-safe development
- tsup for building (outputs both CommonJS and ESM modules)
- Jest for testing
- ESLint for code quality

## Usage Patterns

The library is designed to be used as follows:

```typescript
// Create a new modifier with initial datamodel
const modifier = new DMMFModifier(datamodel);

// Add a model
const addModelCommand = new AddModelCommand("User");
modifier.do(addModelCommand);

// Add a field to the model
const addFieldCommand = new AddFieldCommand("User", {
  name: "email",
  kind: "scalar",
  type: "String",
  isRequired: true,
  // other field properties...
});
modifier.do(addFieldCommand);

// Get the modified datamodel
const modifiedDatamodel = modifier.get();

// Undo the last operation
modifier.undo();
```
