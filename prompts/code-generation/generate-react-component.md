---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [react, frontend, components, typescript, ui]
---

# Generate React Component from Design Spec

## Purpose
Generate production-ready React components with TypeScript, proper typing, styling, accessibility, and tests.

## Context
Use this prompt when:
- Implementing UI from design specs or mockups
- Building component library
- Need consistent component patterns
- Scaffolding new features

---

## Prompt

You are a frontend developer. Generate a production-ready React component with TypeScript, styling, accessibility, and best practices.

**INPUT**:
- Component description or design spec
- Props and behavior requirements
- Styling approach (CSS Modules, styled-components, Tailwind)
- Optional: State management needs

**GENERATE**:

1. **Component Structure**:
   - TypeScript interfaces for props
   - Functional component with hooks
   - Prop validation and defaults
   - Memoization if needed

2. **Styling**:
   - Responsive design
   - Theme support
   - CSS-in-JS or modules
   - Consistent spacing/colors

3. **Accessibility**:
   - ARIA labels and roles
   - Keyboard navigation
   - Screen reader support
   - Focus management

4. **Best Practices**:
   - Single responsibility
   - Composable/reusable
   - Error boundaries where needed
   - Loading/error states

5. **Testing**:
   - Unit tests (React Testing Library)
   - Accessibility tests
   - User interaction tests

**OUTPUT FORMAT**:
```
# React Component: [ComponentName]

## Component Code
```tsx
[complete component]
```

## Styles
```[css/ts]
[styling code]
```

## Tests
```tsx
[test file]
```

## Storybook Story (optional)
```tsx
[storybook story]
```

## Usage Example
```tsx
[how to use component]
```

## Notes
- [Design decisions]
- [Accessibility features]
- [Performance considerations]
```

**CONSTRAINTS**:
- Use TypeScript with strict types
- Include accessibility (ARIA, keyboard support)
- Handle loading and error states
- Responsive by default
- Include prop documentation
- Write tests for key interactions

---

## Example

**Input**:
```
Component: UserCard
Description: Display user profile with avatar, name, role, and action button
Props:
  - user: { id, name, email, avatar, role }
  - onContact: callback when "Contact" button clicked
  - loading: boolean
Styling: Tailwind CSS
```

**Output**:

```typescript
# React Component: UserCard

## Component Code

```tsx
// UserCard.tsx
import React, { memo } from 'react';
import { User } from './types';

export interface UserCardProps {
  /** User object with profile information */
  user: User;
  /** Callback when contact button is clicked */
  onContact: (userId: string) => void;
  /** Loading state for async operations */
  loading?: boolean;
  /** Custom CSS class name */
  className?: string;
}

export const UserCard = memo<UserCardProps>(({
  user,
  onContact,
  loading = false,
  className = '',
}) => {
  const handleContactClick = () => {
    if (!loading) {
      onContact(user.id);
    }
  };

  const handleKeyDown = (event: React.KeyboardEvent) => {
    if (event.key === 'Enter' || event.key === ' ') {
      event.preventDefault();
      handleContactClick();
    }
  };

  return (
    <article
      className={`flex items-center gap-4 p-4 rounded-lg border border-gray-200 bg-white shadow-sm hover:shadow-md transition-shadow ${className}`}
      role="article"
      aria-labelledby={`user-name-${user.id}`}
    >
      {/* Avatar */}
      <div className="flex-shrink-0">
        {user.avatar ? (
          <img
            src={user.avatar}
            alt=""
            className="w-16 h-16 rounded-full object-cover"
            loading="lazy"
            aria-hidden="true"
          />
        ) : (
          <div
            className="w-16 h-16 rounded-full bg-gradient-to-br from-blue-400 to-purple-500 flex items-center justify-center text-white text-xl font-semibold"
            aria-hidden="true"
          >
            {user.name.charAt(0).toUpperCase()}
          </div>
        )}
      </div>

      {/* User Info */}
      <div className="flex-1 min-w-0">
        <h3
          id={`user-name-${user.id}`}
          className="text-lg font-semibold text-gray-900 truncate"
        >
          {user.name}
        </h3>
        <p className="text-sm text-gray-600 truncate" aria-label="Email">
          {user.email}
        </p>
        <span
          className="inline-block mt-1 px-2 py-1 text-xs font-medium rounded-full bg-blue-100 text-blue-800"
          aria-label="Role"
        >
          {user.role}
        </span>
      </div>

      {/* Action Button */}
      <button
        onClick={handleContactClick}
        onKeyDown={handleKeyDown}
        disabled={loading}
        className={`
          px-4 py-2 rounded-md font-medium text-sm
          transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
          ${loading
            ? 'bg-gray-300 text-gray-500 cursor-not-allowed'
            : 'bg-blue-600 text-white hover:bg-blue-700 active:bg-blue-800'
          }
        `}
        aria-label={`Contact ${user.name}`}
        aria-busy={loading}
      >
        {loading ? (
          <span className="flex items-center gap-2">
            <svg
              className="animate-spin h-4 w-4"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              aria-hidden="true"
            >
              <circle
                className="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                strokeWidth="4"
              />
              <path
                className="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              />
            </svg>
            Loading...
          </span>
        ) : (
          'Contact'
        )}
      </button>
    </article>
  );
});

UserCard.displayName = 'UserCard';
```

## Types

```tsx
// types.ts
export interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
  role: string;
}
```

## Tests

```tsx
// UserCard.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { axe, toHaveNoViolations } from 'jest-axe';
import { UserCard, UserCardProps } from './UserCard';
import { User } from './types';

expect.extend(toHaveNoViolations);

const mockUser: User = {
  id: '123',
  name: 'John Doe',
  email: 'john@example.com',
  avatar: 'https://example.com/avatar.jpg',
  role: 'Developer',
};

const defaultProps: UserCardProps = {
  user: mockUser,
  onContact: jest.fn(),
};

describe('UserCard', () => {
  it('renders user information correctly', () => {
    render(<UserCard {...defaultProps} />);

    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
    expect(screen.getByText('Developer')).toBeInTheDocument();
  });

  it('displays avatar when provided', () => {
    render(<UserCard {...defaultProps} />);

    const avatar = screen.getByRole('img');
    expect(avatar).toHaveAttribute('src', mockUser.avatar);
    expect(avatar).toHaveAttribute('alt', '');
  });

  it('displays initial when no avatar provided', () => {
    const userWithoutAvatar = { ...mockUser, avatar: undefined };
    render(<UserCard {...defaultProps} user={userWithoutAvatar} />);

    expect(screen.getByText('J')).toBeInTheDocument();
    expect(screen.queryByRole('img')).not.toBeInTheDocument();
  });

  it('calls onContact with user id when button clicked', async () => {
    const onContact = jest.fn();
    render(<UserCard {...defaultProps} onContact={onContact} />);

    const button = screen.getByRole('button', { name: /contact john doe/i });
    await userEvent.click(button);

    expect(onContact).toHaveBeenCalledWith('123');
    expect(onContact).toHaveBeenCalledTimes(1);
  });

  it('does not call onContact when loading', async () => {
    const onContact = jest.fn();
    render(<UserCard {...defaultProps} onContact={onContact} loading />);

    const button = screen.getByRole('button', { name: /contact john doe/i });
    await userEvent.click(button);

    expect(onContact).not.toHaveBeenCalled();
  });

  it('shows loading state correctly', () => {
    render(<UserCard {...defaultProps} loading />);

    const button = screen.getByRole('button', { name: /contact john doe/i });
    expect(button).toBeDisabled();
    expect(button).toHaveAttribute('aria-busy', 'true');
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });

  it('supports keyboard interaction (Enter key)', async () => {
    const onContact = jest.fn();
    render(<UserCard {...defaultProps} onContact={onContact} />);

    const button = screen.getByRole('button', { name: /contact john doe/i });
    button.focus();
    fireEvent.keyDown(button, { key: 'Enter', code: 'Enter' });

    expect(onContact).toHaveBeenCalledWith('123');
  });

  it('supports keyboard interaction (Space key)', async () => {
    const onContact = jest.fn();
    render(<UserCard {...defaultProps} onContact={onContact} />);

    const button = screen.getByRole('button', { name: /contact john doe/i });
    button.focus();
    fireEvent.keyDown(button, { key: ' ', code: 'Space' });

    expect(onContact).toHaveBeenCalledWith('123');
  });

  it('has no accessibility violations', async () => {
    const { container } = render(<UserCard {...defaultProps} />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });

  it('applies custom className', () => {
    const { container } = render(
      <UserCard {...defaultProps} className="custom-class" />
    );
    expect(container.firstChild).toHaveClass('custom-class');
  });
});
```

## Storybook Story

```tsx
// UserCard.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { UserCard } from './UserCard';
import { User } from './types';

const meta: Meta<typeof UserCard> = {
  title: 'Components/UserCard',
  component: UserCard,
  tags: ['autodocs'],
  argTypes: {
    onContact: { action: 'contact clicked' },
  },
};

export default meta;
type Story = StoryObj<typeof UserCard>;

const mockUser: User = {
  id: '1',
  name: 'Jane Smith',
  email: 'jane.smith@example.com',
  avatar: 'https://i.pravatar.cc/150?img=1',
  role: 'Senior Engineer',
};

export const Default: Story = {
  args: {
    user: mockUser,
  },
};

export const Loading: Story = {
  args: {
    user: mockUser,
    loading: true,
  },
};

export const NoAvatar: Story = {
  args: {
    user: {
      ...mockUser,
      avatar: undefined,
    },
  },
};

export const LongName: Story = {
  args: {
    user: {
      ...mockUser,
      name: 'Dr. Elizabeth Alexandra Mary Windsor-Mountbatten',
      email: 'elizabeth.windsor@verylongdomainname.co.uk',
    },
  },
};

export const CustomClassName: Story = {
  args: {
    user: mockUser,
    className: 'border-2 border-purple-500',
  },
};
```

## Usage Example

```tsx
import { UserCard } from './components/UserCard';
import { User } from './types';

function UserList() {
  const [loading, setLoading] = useState<string | null>(null);

  const users: User[] = [
    { id: '1', name: 'John Doe', email: 'john@example.com', role: 'Developer' },
    { id: '2', name: 'Jane Smith', email: 'jane@example.com', role: 'Designer' },
  ];

  const handleContact = async (userId: string) => {
    setLoading(userId);
    try {
      await api.sendContactRequest(userId);
      toast.success('Contact request sent!');
    } catch (error) {
      toast.error('Failed to send request');
    } finally {
      setLoading(null);
    }
  };

  return (
    <div className="space-y-4">
      {users.map((user) => (
        <UserCard
          key={user.id}
          user={user}
          onContact={handleContact}
          loading={loading === user.id}
        />
      ))}
    </div>
  );
}
```

## Notes

**Design Decisions**:
- Chose `<article>` for semantic HTML (represents standalone content)
- Memo wrapper prevents unnecessary re-renders
- Gradual degradation: initials fallback if no avatar

**Accessibility Features**:
- ✅ ARIA labels for screen readers (`aria-label`, `aria-labelledby`)
- ✅ Keyboard support (Enter and Space keys)
- ✅ Focus indicators (ring on focus)
- ✅ Loading state communicated (`aria-busy`)
- ✅ Disabled state properly handled
- ✅ Alt text on avatar (empty since decorative)
- ✅ Passes axe accessibility audit

**Performance Considerations**:
- `memo` prevents re-renders when props unchanged
- `loading="lazy"` on avatar for performance
- Minimal re-renders with stable callbacks
- No inline object/array creation (stable references)

**Responsive Design**:
- Flexbox layout adapts to container width
- Text truncation prevents overflow
- Touch-friendly button size (min 44x44px)

**Testing Coverage**:
- ✅ Rendering with all prop variations
- ✅ User interactions (click, keyboard)
- ✅ Loading state behavior
- ✅ Accessibility audit
- ✅ Edge cases (long names, missing avatar)

---

## Usage Notes

- **When to use**: Building UI components from designs
- **When NOT to use**: For layout components (use composition)
- **Best practice**: Test accessibility with real screen readers
- **Iteration**: Gather user feedback and refine

---

## Quality Checklist

- [ ] TypeScript with strict types and interfaces
- [ ] Accessibility (ARIA, keyboard, screen reader)
- [ ] Loading and error states handled
- [ ] Responsive design (mobile, tablet, desktop)
- [ ] Unit tests with React Testing Library
- [ ] Accessibility tests (jest-axe)
- [ ] Storybook story for visual testing
- [ ] Prop documentation (JSDoc comments)
- [ ] Performance optimized (memo, lazy loading)
