# AI CNC Monitor

<div align="center">
  <img src="apps/web/public/images/dashboard.webp" alt="CNC Monitoring Dashboard" width="600">
  
  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
  [![TypeScript](https://img.shields.io/badge/TypeScript-4.9+-blue.svg)](https://www.typescriptlang.org/)
  [![Next.js](https://img.shields.io/badge/Next.js-15.1+-black.svg)](https://nextjs.org/)
  [![Supabase](https://img.shields.io/badge/Supabase-Ready-green.svg)](https://supabase.com/)
</div>

## Overview

AI CNC Monitor is a frontend mockup and prototype for a comprehensive real-time monitoring solution designed for busy CNC machine shops. In manufacturing environments where even small mistakes like tool breakage, part misalignment, or material feed issues can lead to costly downtime, damaged equipment, or safety hazards, this platform demonstrates how continuous oversight can be provided when shop owners and operators can't be physically present.

**Current Status**: This is a frontend demonstration with mock data and simulated functionality. The interface showcases the proposed user experience and core features for a future production system.

The system addresses the critical challenge faced by shop owners who must juggle multiple responsibilities beyond the shop floor - from managing logistics to personal commitments. With AI CNC Monitor, you never have to choose between running your business and ensuring your machines operate safely.

## Problem Statement

In busy CNC machine shops:
- **Tool breakage** can damage expensive workpieces and machinery
- **Part misalignment** leads to scrapped products and wasted materials  
- **Material feed issues** cause production delays and quality problems
- **Safety hazards** put operators and equipment at risk
- **Constant supervision** isn't practical for busy shop owners

Shop owners often need to handle logistics, customer relations, and personal responsibilities, making 24/7 machine supervision impossible.

## Solution

AI CNC Monitor provides:
- **Real-time monitoring** through multiple camera feeds
- **Intelligent alerts** for immediate issue detection  
- **Comprehensive logging** with video evidence
- **Remote access** from anywhere with internet connection
- **Professional dashboard** for quick status overview

## Key Features

### Real-time Dashboard
- Live machine status monitoring
- Alert metrics (active, resolved, ignored per hour)
- Machine health indicators
- Recent warnings with instant action buttons

### Multi-Camera Streaming
- HD/FHD video quality support
- Multiple camera feeds per machine
- Recording capabilities
- Full-screen monitoring mode
- Stream management and configuration

### Smart Alert System  
- Real-time anomaly detection
- Severity-based categorization (Low, Medium, High, Critical)
- Instant notifications for critical issues
- One-click resolve/ignore functionality
- Visual status indicators

### Comprehensive Logging
- Detailed incident tracking with timestamps
- Video evidence linking
- Advanced filtering by date, status, and severity
- Export capabilities for analysis
- Historical trend monitoring

### Machine Management
- Individual machine status tracking
- Performance metrics per machine
- Alert history and resolution tracking
- Customizable machine configurations

## Technical Architecture

### Built With
- **Frontend**: Next.js 15.1, React 19, TypeScript
- **Backend**: Supabase (PostgreSQL, Auth, Real-time)
- **UI Framework**: Tailwind CSS, Radix UI, Lucide Icons
- **State Management**: React Hooks, Local Storage
- **Build System**: Turbo (Monorepo), PNPM
- **Deployment**: Vercel-ready, Docker support

### Project Structure
```
saas-platform/
├── apps/
│   ├── web/                    # Main Next.js application
│   │   ├── app/
│   │   │   ├── dashboard/      # CNC monitoring dashboard
│   │   │   ├── streams/        # Camera stream management
│   │   │   ├── logs/           # System logs and warnings
│   │   │   └── (marketing)/    # Landing page
│   │   ├── components/         # Shared components
│   │   └── config/             # App configuration
│   └── e2e/                    # End-to-end tests
├── packages/
│   ├── ui/                     # UI component library
│   ├── auth/                   # Authentication system
│   ├── supabase/               # Database client
│   └── shared/                 # Shared utilities
└── tooling/                    # Development tools
```

## Quick Start

### Prerequisites
- Node.js 18.18.0+
- PNPM 9.12.0+
- Supabase account (for production)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/ai-cnc-monitor.git
   cd ai-cnc-monitor
   ```

2. **Install dependencies**
   ```bash
   pnpm install
   ```

3. **Set up environment variables**
   ```bash
   cd apps/web
   cp .env.example .env.local
   ```

4. **Configure Supabase** (Optional - works with demo data)
   ```bash
   # Start local Supabase instance
   pnpm supabase:web:start
   
   # Generate database types
   pnpm supabase:web:typegen
   ```

5. **Start development server**
   ```bash
   pnpm dev
   ```

6. **Open your browser**
   Navigate to `http://localhost:3000`

## Usage Guide

### Dashboard Overview
- **Monitor machine status** at a glance
- **View real-time metrics** for alerts, resolutions, and ignores
- **Quick actions** on recent warnings
- **Navigate** to detailed views

### Stream Management
- **Add new camera streams** with custom URLs
- **Configure stream settings** (quality, recording)
- **Monitor multiple feeds** simultaneously  
- **Full-screen mode** for detailed inspection

### Log Analysis
- **Filter logs** by date, status, or severity
- **Search specific incidents** by keyword
- **Export data** for external analysis
- **Track resolution patterns** over time

### Alert Handling
- **Resolve** issues that have been fixed
- **Ignore** false positives or known issues
- **Visual feedback** with color-coded status
- **Historical tracking** of all actions

## Development

### Available Scripts
```bash
# Development
pnpm dev              # Start development server
pnpm build            # Build for production
pnpm start            # Start production server

# Code Quality
pnpm lint             # Run ESLint
pnpm format           # Check code formatting
pnpm format:fix       # Fix code formatting
pnpm typecheck        # TypeScript type checking

# Database
pnpm supabase:start   # Start local Supabase
pnpm supabase:stop    # Stop local Supabase
pnpm supabase:reset   # Reset database

# Testing
pnpm test             # Run tests
```

### Adding New Features

1. **Create feature branch**
   ```bash
   git checkout -b feature/new-feature
   ```

2. **Develop with hot reload**
   ```bash
   pnpm dev
   ```

3. **Follow project structure**
   - Components in `apps/web/app/[section]/_components/`
   - Shared UI in `packages/ui/src/`
   - Types and schemas in respective feature folders

4. **Test thoroughly**
   - Manual testing in browser
   - TypeScript compilation
   - Linting and formatting

## Deployment

### Vercel (Recommended)
1. **Connect to Vercel**
   - Import repository in Vercel dashboard
   - Configure environment variables
   - Deploy automatically on git push

2. **Environment Variables**
   ```env
   NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
   SUPABASE_SERVICE_ROLE_KEY=your_service_role_key
   ```

### Docker (Alternative)
```bash
# Build container
docker build -t ai-cnc-monitor .

# Run container
docker run -p 3000:3000 ai-cnc-monitor
```

## Customization

### Branding
- Update logo in `apps/web/components/app-logo.tsx`
- Modify colors in `apps/web/styles/globals.css`
- Customize landing page in `apps/web/app/(marketing)/page.tsx`

### Features
- Add new dashboard widgets in `apps/web/app/dashboard/_components/`
- Extend alert types in warning interfaces
- Customize machine configurations
- Add new video stream providers

### UI Components
- All components are in `packages/ui/src/`
- Uses Tailwind CSS for styling
- Radix UI for accessible components
- Lucide React for icons

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Workflow
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Ensure all checks pass
6. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

- **Documentation**: Check this README and inline code comments
- **Issues**: Open a GitHub issue for bugs or feature requests
- **Discussions**: Use GitHub Discussions for questions and ideas

## Demo Data

The application comes with realistic demo data for immediate testing:
- 4 sample CNC machines with different statuses
- Historical warnings and logs
- Sample video streams (YouTube embeds for demo)
- Real-time metrics calculations

All demo data is stored in browser localStorage and can be reset anytime.

## Future Work

This frontend mockup demonstrates the potential for a comprehensive manufacturing monitoring platform. Future development phases would include:

### Phase 1: Backend Integration
- Real-time data processing and storage
- Authentication and user management
- Database integration for persistent data
- API development for frontend-backend communication

### Phase 2: Hardware Integration
- **Machine Power Control**: Integration with machine power systems to remotely stop equipment when critical issues are detected
- **Camera Integration**: Support for IP cameras, USB cameras, and RTSP streams
- **Sensor Networks**: IoT sensor integration for temperature, vibration, and other parameters
- **Machine Controllers**: Direct integration with CNC controllers for real-time status

### Phase 3: AI and Analytics
- Computer vision for anomaly detection
- Predictive maintenance algorithms
- Pattern recognition for quality control
- Machine learning models for failure prediction

### Phase 4: Multi-Machine Support
- **Expanded Equipment Types**: Support for 3D printers, robotic arms, injection molding machines, and other manufacturing equipment
- **Cross-Platform Monitoring**: Unified dashboard for diverse manufacturing environments
- **Industry-Specific Templates**: Pre-configured setups for different manufacturing sectors

### Phase 5: Enterprise Features
- Multi-user access and role-based permissions
- Advanced reporting and analytics
- Integration with ERP and MES systems
- Cloud deployment and scalability
- Mobile applications for on-the-go monitoring

### Phase 6: Safety and Compliance
- Emergency stop protocols and safety interlocks
- Compliance reporting for industry standards
- Audit trails and documentation
- Insurance integration for incident reporting

The modular architecture demonstrated in this prototype allows for incremental development and deployment, enabling manufacturers to start with basic monitoring and gradually expand capabilities as needs grow.


