# ShadCN UI

## Overview

ShadCN UI is a collection of reusable components built with Radix UI primitives and styled with Tailwind CSS.

## Installation

```bash
# Initialize ShadCN (if not already done)
npx shadcn@latest init

# Add a component
npx shadcn@latest add button
npx shadcn@latest add dialog
npx shadcn@latest add dropdown-menu
```

## Component Location

All ShadCN components are located at:
```
~/saas/resources/js/components/ui/
```

## Common Components

| Component | Command |
|-----------|---------|
| Button | `npx shadcn@latest add button` |
| Dialog | `npx shadcn@latest add dialog` |
| Dropdown Menu | `npx shadcn@latest add dropdown-menu` |
| Input | `npx shadcn@latest add input` |
| Card | `npx shadcn@latest add card` |
| Form | `npx shadcn@latest add form` |
| Table | `npx shadcn@latest add table` |

## Usage Example

```tsx
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

export default function Example() {
    return (
        <Card>
            <CardHeader>
                <CardTitle>Title</CardTitle>
            </CardHeader>
            <CardContent>
                <Input placeholder="Enter text" />
                <Button>Submit</Button>
            </CardContent>
        </Card>
    );
}
```

## Radix Primitives

ShadCN uses `@radix-ui/react-*` packages:
- `@radix-ui/react-dialog`
- `@radix-ui/react-dropdown-menu`
- `@radix-ui/react-slot`
- `@radix-ui/react-tabs`
- etc.

These provide accessible, unstyled primitives that ShadCN styles with Tailwind.