# API Reference

The SoroSave SDK exports the following modules:

## Modules

- [`SoroSaveClient`](/api/client) — Main client class for contract interaction
- [`Types`](/api/types) — TypeScript interfaces, types, and enums
- [`Utils`](/api/utils) — Utility functions for formatting and calculations

## Quick Import

```typescript
// Core client
import { SoroSaveClient } from '@sorosave/sdk';

// Types
import type { CreateGroupParams, SavingsGroup, RoundInfo, SoroSaveConfig } from '@sorosave/sdk';

// Enums
import { GroupStatus } from '@sorosave/sdk';

// Utilities
import { formatAmount, parseAmount, getStatusLabel } from '@sorosave/sdk';

// React hooks
import { SoroSaveProvider, useGroup, useContribute, useMemberGroups } from '@sorosave/react';
```

## Client Methods Overview

### Write Methods (Transactions)

| Method | Description | Access |
|--------|-------------|--------|
| `createGroup()` | Create a new savings group | Anyone |
| `joinGroup()` | Join an existing group | Anyone |
| `leaveGroup()` | Leave a group (while forming) | Members |
| `startGroup()` | Start the savings cycle | Admin |
| `contribute()` | Contribute to current round | Members |
| `distributePayout()` | Distribute pot to recipient | Anyone |
| `pauseGroup()` | Pause a group | Admin |
| `resumeGroup()` | Resume a paused group | Admin |
| `raiseDispute()` | Raise a dispute | Members |

### Read Methods (Queries)

| Method | Description | Returns |
|--------|-------------|---------|
| `getGroup()` | Get group details | `SavingsGroup` |
| `getRoundStatus()` | Get round information | `RoundInfo` |
| `getMemberGroups()` | Get all groups for a member | `number[]` |

### Batch & Utility Methods

| Method | Description |
|--------|-------------|
| `createBatchBuilder()` | Create a batch operation builder |
| `buildBatchTransaction()` | Build a transaction from batch operations |
| `setWalletAdapter()` | Set or change the wallet adapter |
