# Contributing to AI CNC Monitor

Thank you for your interest in contributing to AI CNC Monitor! This project helps CNC machine shops prevent costly downtime and safety hazards through intelligent monitoring.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Process](#development-process)
- [Contribution Guidelines](#contribution-guidelines)
- [Reporting Issues](#reporting-issues)
- [Feature Requests](#feature-requests)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Documentation](#documentation)

## Code of Conduct

This project and everyone participating in it is governed by our commitment to creating a welcoming and inclusive environment. Please be respectful and professional in all interactions.

## Getting Started

### Prerequisites

- Node.js 18.18.0 or higher
- PNPM 9.12.0 or higher
- Git
- Basic understanding of React, Next.js, and TypeScript
- Familiarity with CNC operations (helpful but not required)

### Development Setup

1. **Fork the repository**
   ```bash
   # Click "Fork" on GitHub, then clone your fork
   git clone https://github.com/yourusername/ai-cnc-monitor.git
   cd ai-cnc-monitor
   ```

2. **Add upstream remote**
   ```bash
   git remote add upstream https://github.com/originalowner/ai-cnc-monitor.git
   ```

3. **Install dependencies**
   ```bash
   pnpm install
   ```

4. **Set up environment**
   ```bash
   cd apps/web
   cp .env.example .env.local
   # Edit .env.local with your configuration
   ```

5. **Start development server**
   ```bash
   pnpm dev
   ```

6. **Verify setup**
   - Open http://localhost:3000
   - Check that dashboard loads with demo data
   - Verify all navigation links work

## Development Process

### Branching Strategy

- `main` - Production-ready code
- `develop` - Integration branch for features
- `feature/feature-name` - New features
- `fix/issue-description` - Bug fixes
- `docs/update-description` - Documentation updates

### Workflow

1. **Create a branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make changes**
   - Write clean, documented code
   - Follow existing patterns
   - Test your changes thoroughly

3. **Commit changes**
   ```bash
   git add .
   git commit -m "feat: add new alert filtering capability"
   ```

4. **Keep branch updated**
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

5. **Push and create PR**
   ```bash
   git push origin feature/your-feature-name
   # Create PR on GitHub
   ```

## Contribution Guidelines

### Types of Contributions

**🐛 Bug Fixes**
- Fix existing functionality issues
- Improve error handling
- Resolve performance problems

**✨ New Features**
- Add new monitoring capabilities
- Enhance user interface
- Integrate with new hardware/software

**📚 Documentation**
- Improve README and guides
- Add code comments
- Create tutorials

**🎨 UI/UX Improvements**
- Enhance visual design
- Improve accessibility
- Optimize mobile experience

**⚡ Performance Optimizations**
- Reduce bundle size
- Improve load times
- Optimize database queries

### Focus Areas

We're particularly interested in contributions for:

1. **AI/ML Integration**
   - Anomaly detection algorithms
   - Predictive maintenance features
   - Computer vision for defect detection

2. **Hardware Integration**
   - Support for different camera types
   - CNC controller integrations
   - IoT sensor connectivity

3. **Manufacturing Workflows**
   - Industry-specific features
   - Quality control automation
   - Production metrics tracking

4. **Enterprise Features**
   - Multi-user management
   - Role-based permissions
   - Advanced reporting

## Reporting Issues

### Before Reporting

1. **Search existing issues** to avoid duplicates
2. **Try latest version** to ensure issue still exists
3. **Check documentation** for known limitations

### Issue Template

When creating an issue, please include:

```markdown
**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Environment (please complete the following information):**
- OS: [e.g. Windows 10, macOS, Ubuntu]
- Browser [e.g. chrome, safari]
- Version [e.g. 22]
- Node.js version
- PNPM version

**Additional context**
Add any other context about the problem here.
```

## Feature Requests

### Feature Request Template

```markdown
**Is your feature request related to a problem? Please describe.**
A clear and concise description of what the problem is.

**Describe the solution you'd like**
A clear and concise description of what you want to happen.

**Describe alternatives you've considered**
A clear and concise description of any alternative solutions or features you've considered.

**Manufacturing Context**
How would this feature help in real CNC shop operations?

**Additional context**
Add any other context or screenshots about the feature request here.
```

## Pull Request Process

### PR Checklist

Before submitting a PR, ensure:

- [ ] Code follows project style guidelines
- [ ] All tests pass (`pnpm test`)
- [ ] TypeScript compiles without errors (`pnpm typecheck`)
- [ ] Linting passes (`pnpm lint`)
- [ ] Changes are documented
- [ ] PR description explains the change
- [ ] Screenshots included for UI changes
- [ ] Related issues are linked

### PR Description Template

```markdown
## Description
Brief description of changes made.

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## How Has This Been Tested?
Describe the tests that you ran to verify your changes.

## Screenshots (if applicable)
Add screenshots to help explain your changes.

## Checklist
- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] Any dependent changes have been merged and published
```

## Coding Standards

### TypeScript Guidelines

```typescript
// ✅ Good: Use descriptive interfaces
interface CNCMachine {
  id: string;
  name: string;
  status: 'error' | 'normal' | 'warning';
  lastUpdate: Date;
}

// ✅ Good: Use proper typing for functions
const handleMachineAlert = (machineId: string, alertType: AlertSeverity): void => {
  // Implementation
};

// ❌ Avoid: Any types
const data: any = fetchMachineData();
```

### React Component Guidelines

```tsx
// ✅ Good: Functional components with TypeScript
interface MachineCardProps {
  machine: CNCMachine;
  onStatusChange: (id: string, status: MachineStatus) => void;
}

export function MachineCard({ machine, onStatusChange }: MachineCardProps) {
  // Component implementation
}

// ✅ Good: Use proper state typing
const [machines, setMachines] = useState<CNCMachine[]>([]);
```

### File Structure

```
apps/web/app/[feature]/
├── _components/           # Feature-specific components
│   ├── feature-main.tsx
│   └── feature-detail.tsx
├── page.tsx              # Route page
├── layout.tsx            # Route layout
└── loading.tsx           # Loading UI
```

### Naming Conventions

- **Files**: kebab-case (`cnc-dashboard.tsx`)
- **Components**: PascalCase (`CNCDashboard`)
- **Functions**: camelCase (`handleMachineUpdate`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_RETRY_ATTEMPTS`)
- **Interfaces**: PascalCase with descriptive names (`CNCMachineStatus`)

## Testing

### Running Tests

```bash
# Run all tests
pnpm test

# Run tests in watch mode
pnpm test:watch

# Run specific test file
pnpm test cnc-dashboard.test.tsx
```

### Writing Tests

```typescript
// Example component test
import { render, screen, fireEvent } from '@testing-library/react';
import { MachineStatusCard } from './machine-status-card';

describe('MachineStatusCard', () => {
  it('displays machine status correctly', () => {
    const machine = {
      id: 'test-1',
      name: 'Test Machine',
      status: 'normal' as const,
      lastUpdate: new Date()
    };

    render(<MachineStatusCard machine={machine} />);
    
    expect(screen.getByText('Test Machine')).toBeInTheDocument();
    expect(screen.getByText('Normal')).toBeInTheDocument();
  });
});
```

## Documentation

### Code Comments

```typescript
/**
 * Calculates machine efficiency based on uptime and performance metrics
 * @param uptime - Machine uptime percentage (0-100)
 * @param performance - Performance rating (0-100)
 * @returns Overall efficiency score (0-100)
 */
function calculateMachineEfficiency(uptime: number, performance: number): number {
  // Weighted average favoring uptime (70%) over performance (30%)
  return Math.round(uptime * 0.7 + performance * 0.3);
}
```

### JSDoc for Components

```typescript
/**
 * CNC Machine status display component
 * 
 * @component
 * @example
 * ```tsx
 * <MachineStatusCard 
 *   machine={machineData} 
 *   onAlert={handleAlert}
 * />
 * ```
 */
export function MachineStatusCard({ machine, onAlert }: MachineStatusCardProps) {
  // Component implementation
}
```

## Questions and Support

- **GitHub Discussions**: For questions and general discussion
- **GitHub Issues**: For bug reports and feature requests
- **Code Review**: All contributions are reviewed by maintainers

## Recognition

Contributors will be recognized in:
- Project README
- Release notes for significant contributions
- Annual contributor highlights

Thank you for helping make CNC operations safer and more efficient! 🏭⚡ 