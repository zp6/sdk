# Usage Guide

Complete guide to using the SoroSave SDK for building savings group applications.

## Initializing the Client

```typescript
import { SoroSaveClient } from '@sorosave/sdk';

const client = new SoroSaveClient({
  rpcUrl: 'https://soroban-testnet.stellar.org',
  contractId: 'CBQ...YOUR_CONTRACT_ID',
  networkPassphrase: 'Test SDF Network ; September 2015'
});
```

## Using with React

The SDK provides React hooks for easy frontend integration:

```tsx
import { SoroSaveProvider, useGroup, useContribute, useMemberGroups } from '@sorosave/react';

function App() {
  return (
    <SoroSaveProvider config={{
      contractId: 'CBQ...YOUR_CONTRACT_ID',
      rpcUrl: 'https://soroban-testnet.stellar.org',
      networkPassphrase: 'Test SDF Network ; September 2015'
    }}>
      <Dashboard />
    </SoroSaveProvider>
  );
}
```

### useGroup — Fetch Group Data

```tsx
function GroupCard({ groupId }: { groupId: number }) {
  const { group, loading, error, refetch } = useGroup(groupId);

  if (loading) return <Spinner />;
  if (error) return <ErrorBanner message={error.message} />;
  if (!group) return <EmptyState message="Group not found" />;

  return (
    <div className="card">
      <h2>{group.name}</h2>
      <p>Members: {group.members.length}/{group.maxMembers}</p>
      <p>Round: {group.currentRound}/{group.totalRounds}</p>
      <p>Status: {group.status}</p>
      <button onClick={refetch}>Refresh</button>
    </div>
  );
}
```

### useContribute — Make Contributions

```tsx
function ContributeButton({ groupId, member }: Props) {
  const { contribute, loading, error } = useContribute();

  const handleContribute = async () => {
    try {
      await contribute({ member, groupId });
      toast.success('Contribution successful!');
    } catch (err) {
      toast.error(err.message);
    }
  };

  return (
    <button onClick={handleContribution} disabled={loading}>
      {loading ? 'Contributing...' : 'Contribute'}
    </button>
  );
}
```

### useMemberGroups — List Member's Groups

```tsx
function MyGroups({ address }: { address: string }) {
  const { groupIds, loading, refetch } = useMemberGroups(address);

  return (
    <div>
      <h3>My Groups ({groupIds.length})</h3>
      {groupIds.map(id => <GroupCard key={id} groupId={id} />)}
      <button onClick={refetch}>Refresh</button>
    </div>
  );
}
```

## Wallet Integration

### Freighter Wallet

```typescript
import { SoroSaveClient } from '@sorosave/sdk';
import { FreighterAdapter } from '@sorosave/sdk';

const wallet = new FreighterAdapter();
const client = new SoroSaveClient(config, wallet);

// Transactions will be automatically signed via Freighter
const tx = await client.createGroup(params, sourceAddress);
```

### Custom Wallet Adapter

```typescript
import { WalletAdapter } from '@sorosave/sdk';

class MyWalletAdapter implements WalletAdapter {
  async signTransaction(tx: Transaction, passphrase: string): Promise<Transaction> {
    // Your custom signing logic
    return signedTx;
  }

  async getPublicKey(): Promise<string> {
    return 'G...';
  }
}
```

## Batch Operations

The SDK supports batching multiple operations into a single transaction:

```typescript
const batch = client.createBatchBuilder();

// Add multiple operations
batch.addJoinGroup(member1, groupId);
batch.addJoinGroup(member2, groupId);
batch.addJoinGroup(member3, groupId);

// Build and submit as one transaction
const tx = await client.buildBatchTransaction(sourceAddress, batch);
```

## Utility Functions

```typescript
import {
  formatAmount,
  parseAmount,
  getStatusLabel,
  shortenAddress,
  calculatePotSize,
  getPayoutRound
} from '@sorosave/sdk';

// Format token amounts for display
formatAmount(1000000000n, 7);  // "100"

// Parse user input to raw amount
parseAmount("100", 7);  // 1000000000n

// Get readable status
getStatusLabel(group.status);  // "Active"

// Shorten addresses for UI
shortenAddress('GABC...XYZ');  // "GABC...WXYZ"

// Calculate total pot
calculatePotSize(1000000000n, 5);  // 5000000000n

// Find when a member gets paid
getPayoutRound(group.payoutOrder, memberAddress);  // 3
```

## Error Handling

```typescript
try {
  const tx = await client.contribute(member, groupId, sourceAddress);
  const result = await server.submitTransaction(tx);
} catch (error) {
  if (error.message.includes('Simulation failed')) {
    console.error('Transaction simulation failed:', error);
  } else if (error.message.includes('insufficient')) {
    console.error('Insufficient balance');
  } else {
    console.error('Unexpected error:', error);
  }
}
```

## CLI Usage

The SDK includes a command-line interface:

```bash
# Install globally
npm install -g @sorosave/sdk

# Create a group
sorosave create-group --name "My Group" --amount 100 --members 5

# Join a group
sorosave join-group --group-id 1 --member "G..."

# Contribute
sorosave contribute --group-id 1 --member "G..."

# Query group info
sorosave get-group --group-id 1
```

## Next Steps

- [API Reference](/api/) — Complete method documentation
- [Configuration](/guide/configuration) — Advanced configuration
- [Tutorial: Group Lifecycle](/tutorial/group-lifecycle) — Full walkthrough
