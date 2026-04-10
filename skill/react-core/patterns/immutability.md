# Immutability in React State

Never mutate objects or arrays in state directly. Always create a new copy. Mutating state causes components not to re-render or causes unpredictable bugs.

## 1. Updating Objects

Don't do: `user.name = 'New Name'; setUser(user);`
Do: Create a new object with the spread operator.

```javascript
setUser({
  ...user,
  name: 'New Name'
});
```

### Nested Objects
You must spread every level of nesting that changes.
```javascript
setPerson({
  ...person,
  address: {
    ...person.address,
    city: 'New City'
  }
});
```

## 2. Updating Arrays

| Operation | Don't use (mutates) | Use (returns new) |
|---|---|---|
| Adding | `push`, `unshift` | `[...arr, item]`, `[item, ...arr]` |
| Removing | `splice`, `pop` | `filter` |
| Replacing | `arr[i] = ...` | `map` |
| Sorting | `sort`, `reverse` | `[...arr].sort()`, `toSorted()` |

### Examples
- **Adding**: `setItems([...items, newItem])`
- **Deleting**: `setItems(items.filter(i => i.id !== id))`
- **Updating**: `setItems(items.map(i => i.id === id ? { ...i, val } : i))`

## 3. Why Immutability?

- **Re-render trigger**: React uses shallow equality check (`===`). If the reference is the same, React thinks nothing changed.
- **Predictability**: Keeps the history of states clean (useful for undo/redo and debugging).
- **Concurrency**: Allows React to start and stop renders safely without working on "live" data.

---

### 🔗 Contexto & Relacionados

- **Patterns**: [State Management Patterns](../patterns/state-management.md)
- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
